---
layout: post
title: "Create a blog app with ROR"
description: ""
category: Geek 
tags: []
---

## 环境

之前都是直接从官网上下源码来安装ruby的，导致用gem或者bundle时总是要sudo，非常不爽。终于发现可以使用rvm……

{% highlight bash %}
$ curl -L http://get.rvm.io | bash -s stable
$ rvm requirements
$ source ~/.rvm/scripts/rvm
{% endhighlight %}

在某些shell下可能要设置`Run command as a login shell`之类的。比如ubuntu自带的shell。

rvm好在可以给不同的工程创建不同的环境。ruby版本或者gem什么的都可以不一样。

{% highlight bash %}
$ rvm install 2.0.0
$ rvm use 2.0.0 --default
$ gem install rails
{% endhighlight %}

然后是蛋疼的数据库支持。`Synaptic Package Manager`可以把一个东西相关连的东西一起下载下来。为了支持`PostgreSQL`装上`PostgreSQL`以及`postgresql-server-dev-9.1`，最好装个`pgAdmin`，因为终端操纵数据库不是件很愉快的事情。

……不过配这个蛋疼的数据库还是得用终端……

{% highlight bash %}
$ sudo -u postgres psql

postgres=# alter user postgres with password 'postgres';
postgres=# \q

$ sudo passwd -d postgres
$ sudo -u postgres passwd
{% endhighlight %}

然后在`pgAdmin`里面折腾一下就好了……

{% highlight bash %}
$ cd ~/rails_projects
$ rvm use --create 2.0.0@demo_blog
$ gem install rails
$ rails new demo_blog -d postgresql
$ cd my_app
$ rvm --rvmrc 2.0.0@demo_blog
{% endhighlight %}

这样每次切进这个文件夹就会进入`2.0.0@demo_blog`环境了。

最后关于gem。由于身在天朝所以先得处理一下网络问题：

{% highlight bash %}
$ gem source --remove https://rubygems.org/
$ gem source -a http://ruby.taobao.org/
{% endhighlight %}

淘宝还做这种好事情呢……

每次用rails创建新项目时也会卡住，break掉进去改`Gemfile`的source再执行`bundle install`就好了。

不对还有个东西会出问题……数据库……

在`database.yml`里面把数据库用户的帐号密码设置好，把`host: localhost`加进去……毕竟是本地测试嘛。

{% highlight bash %}
$ rake db:create
{% endhighlight %}

终于完工了～

哦，最好把时区改对。在`config/application.rb`里面设置

{% highlight ruby %}
config.time_zone = 'Chongqing'
{% endhighlight %}

## 后台

在`Gemfile`末尾加

{% highlight bash %}
gem 'devise'
gem 'activeadmin', github: 'gregbell/active_admin'
{% endhighlight %}

因为最近rails更新过，所以很多库还没修改好……直接从github上获取会比较好。当然也会碰到各种奇怪的问题。

{% highlight bash %}
$ rails g active_admin:install
$ mv app/assets/stylesheets/active_admin.css.scss vendor/assets/stylesheets/
{% endhighlight %}

这么搞的原因是`activeadmin`的css在普通的页面中也能用到，这个就不是很爽了……

然后在migration里面把`t.timestamps`注释掉。最好把创建comment的那个migration删掉。还得修改一下`activeadmin`的initializer。把comment给disable掉就好。

因为这个comment看上去太废柴了……

{% highlight bash %}
$ rails s
{% endhighlight %}

打开`http://localhost:3000/admin`，用户名`admin@example.com`，密码`password`，进去新建一个用户，再把这个默认的删掉。

## 模块

当然博客得有个首页吧。

{% highlight bash %}
$ rails g controller home index
{% endhighlight %}

把routes里面的`home/index`去掉，加上`root 'home#index'`，这样主页就不是那个rails创建成功的页面了。

接下来把各种model都弄上去。

{% highlight bash %}
$ rails g model category title:string slug:string description:string
$ rails g model tag title:string slug:string 
$ rails g model post title:string slug:string blurb:string content:text
$ rake db:migrate
$ rails g active_admin:resource category
$ rails g active_admin:resource tag
$ rails g active_admin:resource post
{% endhighlight %}

然后处理一下它们之间的关系。

{% highlight bash %}
$ rails g migration AddCategoryToPosts category:belongs_to
$ rails g model posts_tags post:belongs_to tag:belongs_to
$ rake db:migrate
{% endhighlight %}

在migrate之前把posts_tags这个model里面的timestamps去掉，注意要加上`id: false`。migrate后把`posts_tags.rb`这个文件删掉。当然高兴的话把所有和这个有关的文件都删掉也是可以的。果然最简单的方法还是自己写个migration……

在`category.rb`里面加上

{% highlight ruby %}
has_many :posts
{% endhighlight %}

在`post.rb`里面加上

{% highlight ruby %}
belongs_to :category
has_and_belongs_to_many :tags
{% endhighlight %}

在`tag.rb`里面加上

{% highlight ruby %}
has_and_belongs_to_many :posts
{% endhighlight %}

然后是修改admin界面。在`category.rb`里面加上

{% highlight ruby %}
permit_params :title, :slug, :description

index do
  column :title
  column :slug
  column :description
  
  default_actions
end
{% endhighlight %}

在`tag.rb`里面加上

{% highlight ruby %}
permit_params :title, :slug

index do
  column :title
  column :slug

  default_actions
end
{% endhighlight %}

在`post.rb`里面加上

{% highlight ruby %}
permit_params :title, :slug, :blurb, :content, :category_id, :tag_ids => []

index do
  column :title
  column :slug
  column :blurb
  column :category
  column :created_at

  default_actions
end

form do |f|
  f.inputs 'Details' do
    f.input :title
    f.input :slug
    f.input :blurb
    f.input :category
    f.input :tags, as: :check_boxes
    f.input :content
  end

  f.actions
end
{% endhighlight %}

顺便把dashboard也改改

{% highlight ruby %}
columns do
  column do
    panel 'Recent Posts' do
      table_for Post.order('created_at desc').limit(5) do
        column(:title) { |post| link_to(post.title, admin_post_path(post)) }
        column :created_at
      end
    end
  end

  column do
    panel 'Info' do
      para 'Welcome to ActiveAdmin.'
    end
  end
end
{% endhighlight %}

现在再来给model添加一些限制。比如slug仅由字母数字等组成之类的……在model里面新建一个`argv_validator.rb`

{% highlight ruby %}
module ArgvValidator
  def validates_title_slug
    validates :title, presence: true
    validates :slug,  presence: true, format: { with: /\A[a-zA-Z0-9_-]+\Z/, 
      message: 'only letters, numbers, underscore and dash allowed' }
  end
end
{% endhighlight %}

然后在需要的model里面（貌似是所有的= =）添加

{% highlight ruby %}
extend ArgvValidator
validates_title_slug
{% endhighlight %}

模块差不多到这里就可以了……但是写博文纯文本怎么能行呢（虽然可以直接手写HTML）……为了代码高亮、插入图片或者什么的，我想还是弄个`markdown`来处理博文～

{% include JB/setup %}
