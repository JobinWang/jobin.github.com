---
layout: post
title: 在Ruby的Case表达式中使用Lambda/Proc
---

大部分的Ruby开发者都知道在`case`表达式中的`when`后面可以使用直接量，类，范围(Range)和正则表达式：

{% highlight ruby %}
case something
when Array then ...
when 1..100 then ...
when /some_regexp/ then ...
end
{% endhighlight %}

你可能也知道在`case`中实际是使用`===`方法来进行比较。Ruby会将上面的代码象下面一样执行：

{% highlight ruby %}
case something
when Array === something then ...
when 1..100 === something then ...
when /some_regexp/ === something then...
end
{% endhighlight %}

当然你可能不知道`Proc`类中也定义了`===`方法，用于执行`proc`或者`lambda`。相当于把`===`右边的参数作为参数传递给`Proc#call`方法，例如：
{% highlight ruby %}
is_even = ->(n) { n.even? }

is_even === 5 # => false

# same as
is_even.call(5)
{% endhighlight %}

这样我们就可以在`when`的后面使用`proc`或`lambda`，下面是一个非常简单的例子：
{% highlight ruby %}
def even?
  ->(n) { n.even? }
end

def odd?
  ->(n) { n.odd? }
end

case x
when even? then puts 'even'
when odd? then puts 'odd'
else puts 'Impossible!'
end
{% endhighlight %}

也可以直接在`when`的后面定义`lambda`，例如：
{% highlight ruby %}
case x
when ->(n) { n.even? } then puts 'even'
when ->(n) { n.odd? } then puts 'odd'
else puts 'Impossible!'
end
{% endhighlight %}

因为`proc`或`lambda`的闭包特性，所以可以给`lambda`的定义方法传递参数，进行更强大的比较功能：
{% highlight ruby %}
def response_code?(code)
  ->(response) { response.code == code }
end

case response
when response_code?(200) then 'OK'
when response_code?(404) then 'Not found'
else 'Unknown code'
end
{% endhighlight %}

下面是一个在具体项目中使用的用于对`Array`进行模式匹配的方法：
{% highlight ruby %}
def matches(expected)
  ->(actual) {
    expected.zip(actual).all? do |(exp, act)|
      exp === act
    end
  }
end

def __
  ->(x) { true }
end
{% endhighlight %}

使用时就可以像下面这样对`Array`进行匹配：
{% highlight ruby %}
case [a, b]
when matches([true])
 "matches when a is `true`"
when matches([true, true])
 "matches when a and b are `true`"
when matches([__, true])
 "matches when b is `true`"
when matches([false, (4..10)])
 "matches when a is `false` and b is number in range 4-10"
when matches([Array, Hash])
 "matches when a is an `Array` and b is a `Hash`"
{% endhighlight %}

本文参考：
[Lambdas/Procs in Case Expressions](http://batsov.com/articles/2013/09/24/lambdas-slash-procs-in-case-expressions/)
