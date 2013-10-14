---
layout: post
title: "Learning ROR - Active Record Basics"
description: ""
category: Geek 
tags: []
---

## Rails philosophy

* DRY - "Don't Repeat Yourself"
* Convention Over Configuration

## Convention over Configuration in Active Record

* Naming Conventions

<table>
<thead><tr>
<th>Model / Class</th>
<th>Table / Schema</th>
</tr></thead>
<tbody>
<tr>
<td>Post</td>
<td>posts</td>
</tr>
<tr>
<td>LineItem</td>
<td>line_items</td>
</tr>
<tr>
<td>Deer</td>
<td>deer</td>
</tr>
<tr>
<td>Mouse</td>
<td>mice</td>
</tr>
<tr>
<td>Person</td>
<td>people</td>
</tr>
</tbody>
</table>

* Schema Conventions

1. Foreign keys: *singularized_table_name_id*
2. Primary keys: *id*
3. *created_at*
4. *updated_at*
5. *lock_version*
6. *type*
7. *(association_name)_type*
8. *(table_name)_count*

## Creating Active Record Models

{% highlight ruby %}
class Product < ActiveRecord::Base
end
{% endhighlight %}

{% highlight sql %}
CREATE TABLE products (
   id int(11) NOT NULL auto_increment,
   name varchar(255),
   PRIMARY KEY  (id)
);
{% endhighlight %}

{% highlight ruby %}
p = Product.new
p.name = "Some Book"
puts p.name # "Some Book"
{% endhighlight %}

## Overriding the Naming Conventions

{% highlight ruby %}
class Product < ActiveRecord::Base
  self.table_name = "PRODUCT"
end
{% endhighlight %}

If you do so, you will have to define manually the class name that is hosting the fixtures (class_name.yml) using the *set_fixture_class* method in your test definition:

{% highlight ruby %}
class FunnyJoke < ActiveSupport::TestCase
  set_fixture_class funny_jokes: 'Joke'
  fixtures :funny_jokes
  ...
end
{% endhighlight %}

It's also possible:

{% highlight ruby %}
class Product < ActiveRecord::Base
  set_primary_key "product_id"
end
{% endhighlight ruby %}

## CRUD: Reading and Writing Data

* Create

The *new* method will return a new object while *create* will return the object and save it to the database.

{% highlight ruby %}
user = User.create(name: "David", occupation: "Code Artist")
{% endhighlight %}

{% highlight ruby %}
user = User.new
user.name = "David"
user.occupation = "Code Artist"
{% endhighlight %}

If a block is provided, both *create* and *new* will yield the new object to that block for initialization:

{% highlight ruby %}
user = User.new do |u|
  u.name = "David"
  u.occupation = "Code Artist"
end
{% endhighlight %}

* Read

{% highlight ruby %}
# return a collection with all users
users = User.all

# return the first user
user = User.first

# return the first user named David
david = User.find_by_name('David')

# find all userd named David who are Code Artists and sort by
# created_at in reverse chronological order
users = User.where(name: 'David', occupation: 'Code Artists').order('created_at DESC')
{% endhighlight %}

* Update

{% highlight ruby %}
user = User.find_by_name('David')
user.update(name: 'Dave')
{% endhighlight %}

{% highlight ruby %}
User.update_all "max_login_attempts = 3, must_change_password = 'true'"
{% endhighlight %}

* Delete

{% highlight ruby %}
user = User.find_by_name('David')
user.destroy
{% endhighlight %}

## Validations

{% highlight ruby %}
class User < ActiveRecord::Base
  validates :name, presence: true
end

User.create  # => false
User.create! # => ActiveRecord::RecordInvalid: Validation failed: Name can't be blank
{% endhighlight %}

## Migrations

{% highlight bash %}
$ rake db:migrate
$ rake db:rollback
{% endhighlight %}

{% include JB/setup %}
