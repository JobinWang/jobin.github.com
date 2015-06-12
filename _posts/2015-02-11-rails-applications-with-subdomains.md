---
layout: post
title: 使用subdomain开发和测试rails应用(二)
---

基于上一篇blog[使用subdomain开发和测试rails应用(一)](http://localhost:4000/2015/02/10/dev-and-test-rails-applications-with-subdomains/)，已经知道如何在开发和测试环境中配置和使用subdomain。这篇blog主要结合实例代码，介绍如何在rails应用内处理subdomain。


### Blog应用实例
实例代码基于Railscasts上的[blog-before](https://github.com/railscasts/123-subdomains-revised/tree/master/blogs-before)，升级了一下gem的版本。

更新后的代码github地址：[blog-subdomain](https://github.com/bingxie/blogs-subdomains)

应用场景：一个Blog应用网站，用户可以有自己的blog，每个用户的blog有subdomain，例如：bing.blogs.dev

subdomain的值，在rails中时存放在request的实例中的。

配置routes：
{% highlight ruby %}
  match '', to: 'blogs#show', constraints: {subdomain: /.+/}
{% endhighlight %}

在Blog controller的show action中，使用subdomain来获取blog实例：
{% highlight ruby %}
  @blog = Blog.find_by_subdomain!(request.subdomain)
{% endhighlight %}

在首页上连接各个blog的地址:
{% highlight ruby %}
  <%= link_to blog.name, root_url(subdomain: blog.subdomain) %>
{% endhighlight %}

由于所有的articles都存在同一个表中，所以获取文章的时候，要先通过subdomain获取blog实例，然后通过blog获取articles
{% highlight ruby %}
  @blog = Blog.find_by_subdomain!(request.subdomain)
  @article = @blog.articles.find(params[:id])
{% endhighlight %}

### 支持www子域名访问
我们希望用户输入www.blogs.dev时访问blog的列表，而不是说找不到subdomain。
通过rails的constrains有两种方法实现：

* 方法一： 在constrains选项中用lamada
{% highlight ruby %}
  lambda { |request| request.subdomain.present? && r.subdomain != 'www' }
{% endhighlight %}
  另外一种使用正则表达式的方法：
{% highlight ruby %}
  constraints: { subdomain: /^(?!www$)(.+)$/i }
{% endhighlight %}

* 方法二： 定义专门的类，适合于逻辑比较复杂的情况
{% highlight ruby %}
  class Subdomain
    def self.matches?(request)
    request.subdomain.present? && request.subdomain != "www"
    end
  end
{% endhighlight %}
  在routes中：
{% highlight ruby %}
  constraints(Subdomain) do
    match '/' => 'blogs#show'
    # more ...
  end
{% endhighlight %}

连接到有www的主页：
{% highlight ruby %}
<%= link_to 'Home', root_url(subdomain: 'www') %>
{% endhighlight %}
连接到无www的主页：
{% highlight ruby %}
<%= link_to 'Home', root_url(subdomain: false) %>
{% endhighlight %}

可以配置默认不使用subdomian：
{% highlight ruby %}
config.action_controller.default_url_options = { subdomain: false }
{% endhighlight %}

### 总结rails constrains的使用
上面的几段代码，也展示了最常见的rails routes中constrains的使用方法：

还有一种常见的就是限制传入的id参数的格式：
resources :photos, :constraints => {:id => /[A-Z][A-Z][0-9]+/}

(完)

