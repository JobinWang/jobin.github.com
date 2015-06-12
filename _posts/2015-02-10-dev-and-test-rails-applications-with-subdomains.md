---
layout: post
title: 使用subdomain开发和测试rails应用(一)
---

这篇博客总结和展示在开发和测试rails应用的时候如何使用子域名subdomains

## 开发环境 Developing

### 使用lvh.me

如果在开发环境中使用的是webrick或者Puma服务器，可以通过lvh.me来访问子域名。例如：

> http://sub.lvh.me:3000

`lvh.me`会被解析到localhost，这样就可以通过默认的3000端口访问子域名（Puma的默认端口是9292）。但是这种方法可能会慢一点。

### 修改hosts文件
为了减少来回的跳转，可以通过修改hosts文件来访问子域名，在Mac下是`/etc/hosts`. 例如：

> 127.0.0.1 sub.virtual.local

然后你还是可以使用这个域名加默认的端口来访问应用：

> http://sub.virtural.local:3000

### 使用Pow（仅限Mac）
Pow本身非常好得支持子域名：[安装Pow](http://pow.cx/manualhtml#section_1), 连接应用，然后就可以通过子域名访问应用：

> sub.app-name.dev

你可以使用[powder gem](https://github.com/Rodreegez/powder), 方便安装和管理pow。

## 测试环境 Testing
Capybara在测试应用时需要访问真实的服务器，所以在测试环境下也需要解决子域名的问题。

### CI
最简单的方法就是使用`lvh.me`

添加几个helper方法，例如：<cite>`spec/support/subdomains.rb`</cite>:

{% highlight ruby %}
def switch_to_subdomain(subdomain)
  # lvh.me总是解析到127.0.0.1
  Capybara.app_host = "http://#{subdomain}.lvh.me"
end

def switch_to_main_domain
  # Capybara.app_host = "http://lvh.me"
end
{% endhighlight %}

然后在feature测试中就可以使用：
{% highlight ruby %}
switch_to_subdomain('sub')
{% endhighlight %}

### 在本机测试
虽然上面的方法在本机也是可行的，但是还是会浪费时间在来回跳转上,下面的方法更加好用。
首先还是跟上面一样修改`hosts`文件：

> 127.0.0.1 sub.virtual.local

在本机设置环境变量 (例如：LOCALVIRTUALHOST，推荐使用dotenv-rails gem)
在`.env`中添加：

> LOCAL_VIRTUAL_HOST  = virtual.local

然后修改<cite>`spec/support/subdomains.rb`</cite>：
{% highlight ruby %}
def local_virtual_host
    ENV['LOCAL_VIRTUAL_HOST'] || 'lvh.me'
end

def switch_to_subdomain(subdomain)
    Capybara.app_host = "http://#{subdomain}.#{local_virtual_host}"
end

def switch_to_main_domain
    Capybara.app_host = "http://#{local_virtual_host}"
end
{% endhighlight %}

这样可以使得本机的测试更快一些，还有一个需要注意的地方是，如果你有多个子域名，你不能在`hosts`文件中使用通配符，例如：`*.virtual.local`。你需要一个一个得指定子域名。

参考：
[Developing and testing Rails applications with subdomains](https://reinteractive.net/posts/199-developing-and-testing-rails-applications-with-subdomains)
