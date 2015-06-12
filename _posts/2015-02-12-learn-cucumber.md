---
layout: post
title: 学习使用cucumber和相关技巧
---
### 参考资料
这次系统得学习一下cucumber，首先还是通过几个railscasts上的视频进行学习，虽然里面使用的是rails2，我还是用rails3及相关的gem重新实现了一遍。
实现代码：[Learn cucumber](https://github.com/bingxie/learn-cucumber)

Railscasts上相关视频教程：

* [Beginning with Cucumber](http://railscasts.com/episodes/155-beginning-with-cucumber)
* [More on Cucumber](http://railscasts.com/episodes/159-more-on-cucumber)
* [Pickle with Cucumber](http://railscasts.com/episodes/186-pickle-with-cucumber)


进一步深入学习的话还有两个资源可以参考：

* [Cucumber Backgrounder](https://github.com/cucumber/cucumber/wiki/Cucumber-Backgrounder)
* [The Rspec Book](https://pragprog.com/book/achbd/the-rspec-book)

### 技巧及经验总结
#### 命令行参数
cucumber features

* -r :用于指定在那个或者那些目录下面寻找匹配的step definition
* -t :用于指定scenario的标签。例如： -t @focus, 或者排除指定标签： -t ~@focus

#### 工具
Sublime可以安装`Sublime Gherkin Formatter`这个插件,可以用于格式化表格数据，快捷键是：`<Cmd> + <Shift> + |`

#### DRY
* 可以使用background定义一系列的scenario需要用到步骤
* 可以使用scenario outline定义类似的一组测试

#### 使用launchy gem调试
1. 可以使用step: `Then show me the page`
2. 可以在step的定义里面调用: `save_and_open_page`
3. 可以用下面的方法当测试失败时，自动打开页面调试：

新建`/features/support/debugging.rb`,
添加代码：
{% highlight ruby %}
After do |scenario|
  save_and_open_page if scenario.failed? and (ENV["debug"] == "open")
end
{% endhighlight %}
然后运行测试时加上debug环境变量
> cucumber features debug=open

#### 使用World扩展cucumber
* 使用Class进行扩展
例如，需要反复用到用户登录的功能，可以重构一个helper方法：
{% highlight ruby %}
# features/support/login_helpers.rb
class LoginHelpers

  def login_user(username)
    visit("/login")
    fill_in( "user name", :with => username )
    fill_in( "password", :with => "tester" )
    click_button( "login" )
    page.should have_content("welcome")
  end

  def another_logging_method . . .
end

World do
  LoginHelpers.new
end

# features/step_definitions/user/login.rb
When /^"(.?)" logs in do |username|
  login_user(username)
end
{% endhighlight %}

* 使用Module扩展
{% highlight ruby %}
# features/support/paths.rb
module NavigationHelpers
  def path_to(page_name)
    case page_name

    when /the homepage/
      root_path
    when /the list of (.+)/
      send "#{$1}_path"
    when /the show page for (.+)/
      polymorphic_path(model($1))
    else
      raise "Can't find mapping from \"#{page_name}\" to a path."
    end
  end
end
{% endhighlight %}




