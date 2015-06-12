---
layout: post
title: 搞清楚Rails中的eager loading
---
![](/images/bing_623.jpg "")

在Rails中eager loading这词比较抽象，其实就是preloading的意思。就是尽可能把后面需要的数据，通过最少的sql语句一起查询出来，从数据库的角度就是充分利用Join的功能，解决N+1查询的问题。

在Rails中常用下面三个方法来preload数据：

* `#includes`
* `#preload`
* `#eager_load`

实验的时候，建立了下面两个model：
{% highlight ruby %}
rails g model user name:string email:string
rails g model address country city postal_code street user:references
{% endhighlight %}

直接使用joins是不能preload关联的数据：
{% highlight ruby %}
#只能查出users，使用的是INNER JOIN
User.joins(:addresses).where("addresses.country = ?", "Poland")
{% endhighlight %}

Rails使用两种方法来提前加载数据

1. 使用多条单独的查询获取数据 （例如：`#preload`）
2. 使用一条LEFT JOIN语句获取数据 （例如：`#eager_load`)

那么`#includes`是怎么样的呢？

在Rails3中，`#includes`会自动根据查询的条件决定采用那种方式：

所以，下面两种情况产生的语句是一样的：
{% highlight ruby %}
User.includes(:addresses).where("addresses.country = ?", "Poland")
User.eager_load(:addresses).where("addresses.country = ?", "Poland")
{% endhighlight %}

实现的逻辑是：`#includes`通过判断where查询条件中的表名也是addresses，然后把查询代理给了`#eager_load`

一个特别的需求，如果我要查询出符合条件的user，同时附带该user所有的addresses，这个时候我们就要用到`#joins`：
{% highlight ruby %}
User.joins(:addresses).where("addresses.country = ?", "Poland").includes(:addresses)
{% endhighlight %}
这句的效果和上面两句是类似的，并没有查询出用户的所有地址，有一点区别就是使用的INNER JOIN
{% highlight ruby %}
User.joins(:addresses).where("addresses.country = ?", "Poland").preload(:addresses)
{% endhighlight %}
这条使用`#preload`会用另外一条单独的语句查询出user的所有addresses


在Rails4中又有什么不同呢？
{% highlight ruby %}
User.includes(:addresses).where("addresses.country = ?", "Poland")
{% endhighlight %}

会抛出SQLite3::SQLException: no such column: addresses.country 异常，所以rails4已经不会自动帮你使用`#eager_load`方法了。

Rails4中需要显式地用references指定要preload的关联表：
{% highlight ruby %}
User.includes(:addresses).where("addresses.country = ?", "Poland").references(:addresses)
{% endhighlight %}

在rails4中等同于：
{% highlight ruby %}
User.eager_load(:addresses).where("addresses.country = ?", "Poland")
{% endhighlight %}

所以用`#preload`就是明确指定，让rails单独再发送一条查询语句查询关联表里面的数据，而用`#eager_load`就是尽可能用一条LEFT JOIN语句搞定。
