---
layout: post
title: "Learning ROR - Active Record Migrations"
description: ""
category: Geek 
tags: []
---

## Creating a Migration

* Creating a Standalone Migration

{% highlight bash %}
$ rails generate migration AddPartNumberToProducts
{% endhighlight %}

{% highlight ruby %}
class AddPartNumberToProducts < ActiveRecord::Migration
  def change
  end
end
{% endhighlight %}

If the migration name is of the form "AddXXXToYYY" or "RemoveXXXFromYYY" and is followed by a list of column names and types then a migration containing the appropriate *add_column* and *remove_column* statements will be created.

{% highlight bash %}
$ rails generate migration AddPartNumberToProducts part_number:string
{% endhighlight %}

{% highlight ruby %}
class AddPartNumberToProducts < ActiveRecord::Migration
  def change
    add_column :products, :part_number, :string
  end
end
{% endhighlight %}

if you'd like to add an index on the new column:

{% highlight bash %}
$ rails generate migration AddPartNumberToProducts part_number:string:index
{% endhighlight %}

{% highlight ruby %}
class AddPartNumberToProducts < ActiveRecord::Migration
  def change
    add_column :products, :part_number, :string
    add_index :products, :part_number
  end
end
{% endhighlight %}

Similarly:

{% highlight bash %}
$ rails generate migration RemovePartNumberFromProducts part_number:string
{% endhighlight %}

{% highlight ruby %}
class RemovePartNumberFromProducts < ActiveRecord::Migration
  def change
    remove_column :products, :part_number, :string
  end
end
{% endhighlight %}

If the migration name is of the form "CreateXXX" and is followed by a list of column names and types then a migration creating the table XXX with the columns listed will be generated.

{% highlight ruby %}
class CreateProducts < ActiveRecord::Migration
  def change
    create_table :products do |t|
      t.string :name
      t.string :part_number
    end
  end
end
{% endhighlight %}

*references* as well as *belongs_to*:

{% highlight bash %}
$ rails generate migration AddUserRefToProducts user:references
{% endhighlight %}

{% highlight ruby %}
class AddUserRefToProducts < ActiveRecord::Migration
  def change
    add_reference :products, :user, index: true
  end
end
{% endhighlight %}

This migration will create a *user_id* column and appropriate index.

To produce join tables if *JoinTable* is part of the name:

{% highlight bash %}
$ rails g migration CreateJoinTableCustomerProduct customer product
{% endhighlight %}

{% highlight ruby %}
class CreateJoinTableCustomerProduct < ActiveRecord::Migration
  def change
    create_join_table :customers, :products do |t|
      # t.index [:customer_id, :product_id]
      # t.index [:product_id, :customer_id]
    end
  end
end
{% endhighlight %}

{% include JB/setup %}
