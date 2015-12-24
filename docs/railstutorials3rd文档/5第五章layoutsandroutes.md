## demo5 filling-in-layout

> 来自 RailsTutorial 3rd 第五章 完善布局
> heroku项目地址:https://demo5-filling-in-layout.herokuapp.com/
> 源码地址: https://bitbucket.org/imedingyiming/demo5-filling-in-layout

### 添加

* Bootstrap
* 局部视图
* Rails路由
* Asset Pipeline

### 新建项目

```
rails new demo5-filling-in-layout --skip-bundle

# 拷贝demo3中gemfile内容并添加
gem 'bootstrap-sass','3.2.0.0'

source 'https://rubygems.org'

gem 'rails', '4.2.5'
gem 'sass-rails', '~> 5.0'

gem 'bootstrap-sass'
gem 'uglifier', '>= 1.3.0'
gem 'coffee-rails', '~> 4.1.0'

gem 'jquery-rails'
gem 'turbolinks'
gem 'jbuilder', '~> 2.0'
gem 'sdoc', '~> 0.4.0', group: :doc

group :development, :test do
  gem 'byebug'
  gem 'sqlite3'
  gem 'web-console', '2.0.0.beta3'
  gem 'spring'
end

group :test do
  gem 'minitest-reporters', '1.0.5'
  gem 'mini_backtrace', '0.1.3'
  gem 'guard-minitest', '2.3.1'
end

group :production do
  gem 'pg', '0.17.1'
  gem 'rails_12factor', '0.0.2'
end
```

* 安装gem依赖
```
bundle install
```

### 推送代码

* 推送到Bitbucket

```
git init
git add -A
git commit -m 'init demo5'
git  remote add origin git@bitbucket.org:imedingyiming/demo5-filling-in-layout.git
git push origin master
```

* 推送到HeroKu

```
heroku create demo5-filling-in-layout
git push heroku master
heroku open
```

### 重现demo3与4

> 感觉不熟练,所以在此自己再打一遍

* 生成控制器与一些列方法

```
rails g controller StaticPages home help contact about
```

* TDD
  * 写测试 RED
  要做的是三个页面标题区分

```
# test/controllers/static_pages_controller_test.rb
  def setup
    @base_title = 'Rails Tutorial'
  end

  test "should get home" do
    get :home
    assert_response :success
    assert_select "title", "#{@base_title}"
  end

  test "should get help" do
    get :help
    assert_response :success
    assert_select "title", "Help | #{@base_title}"
  end

  test "should get contact" do
    get :contact
    assert_response :success
    assert_select "title", "Contact | #{@base_title}"
  end

  test "should get about" do
    get :about
    assert_response :success
    assert_select "title", "About | #{@base_title}"
  end
end
```

  * 写应用代码 GREEN
  给每个页面添加各自标题

  * 重构
  使用辅助方法灵活改变标题

```
# app/helpers/application_helper.rb
def full_title(page_title = '')
  base_title = 'Rails Tutorial'
  if page_title.empty?
    base_title
  else
    "#{page_title} | #{base_title}"
  end
end

# 然后到布局文件中直接用 app/views/layouts/application.html.erb
<%= full_title(:title) %>

# 子页面中按需填充
<% provide(:title,'Help') %>
```

### 添加元素与样式

* 导航
* 首页链接

```
# 关键代码
<%= link_to "Sign up now!", '#', class: "btn btn-lg btn-primary" %>

# 加载的图片位于 app/assets/images/文件夹中
<%= link_to image_tag("rails.png", alt: "Rails logo"),'http://rubyonrails.org/' %>
||
<img alt="Rails logo" src="/assets/rails-9308b8f92fea4c19a3a0d8385b494526.png" />

# 获取图片
curl -O http://rubyonrails.org/images/rails.png
mv rails.png app/assets/images/
``

### 添加bootstrap的样式

```
# 新建app/assets/stylesheets/custom.css.scss
@import "bootstrap-sprockets";
@import "bootstrap";
...
```

### 局部视图

下划线开头

```
+ app/views/layouts/
  - _shim.html.erb
  - _header.html.erb
```

布局中使用局部视图

```
<%= render 'layouts/shim' %>
<%= render 'layouts/header' %>
```

### Sass和Asset Pipeline

* 静态资源三个标准文件夹中的用途
  * app/assets : 当前应用的资源文件
  * lib/assets : 开发团队自己开发的代码库使用的资源文件
  * vendor/assets : 第三方代码库使用的资源文件

* 清单文件
  * app/assets/stylesheets/application.css

* 预处理引擎

* Sass
  * 嵌套
  * 变量

  ```
  $gray : #777;
  footer {
    &:hover {
     color : $gray;
    }
  }
  ```

  ### 布局中的链接

  ```
  <%= link_to "About", about_path %>

 #网站中链接的路由和URL地址的映射关系
 页面  URL       具名路由
 首页  /         root_path
 关于  /about    about_path
 帮助  /help     help_path
 联系  /contact  contact_path
 注册  /signup   signup_path
 登录  /login    login_path
  ```

### Rails具名路由

对根路由来说,创建的具名路由是`root_path`和`root_url`

```
get 'static_pages/help'
改为
get 'help' => 'static_pages#help'
...其他类似
```

### 布局中链接的测试

* 生成测试模板 test/integration/site_layout_test.rb

```
rails g integration_test site_layout
```

* 要测试的内容
  * 1.访问根路由
  * 2.确认使用正确的模板渲染
  * 3.检查指向首页,帮助页面,关于页面和联系页面\

* 测试布局中的链接

```
test "layout links" do
    get root_path
    assert_template 'static_pages/home'
    assert_select "a[href=?]", root_path,count: 2
    assert_select "a[href=?]", help_path
    assert_select "a[href=?]", about_path
    assert_select "a[href=?]", contact_path
  end
```

* assert_select 一些用法

```
代码                                  匹配的HTML
assert_select "div"                  <div></div>
assert_select "div","foobar"         <div>foobar</div>
assert_select "div.nav"              <div class="nav"></div>
assert_select "div#profile"          <div id="profile"></div>
assert_select "div[name=yo]"         <div name="yo"></div>
assert_select "a[href=?]",'/',count:1  <a href="/"></a>
assert_select "a[href=?]",'/',text:"foo"  <a href="/">foo</a>
```

* 测试
  * `bundle exec rake test:integration`
  * `bundle exec rake test`

### 加练一波推送

> 在之前一些基础配置下进行推送

```
git status
git add -A
git commit -m 'layouts'
git push origin master
git push heroku master
heroku open
```

### 用户注册第一步

* 生成用户控制器

```
rails generate controller Users new
# 生成一系列内容
bundle exec rake test
```

* 生成的一系列内容,很丰富,需要清晰认识

```
 create  app/controllers/users_controller.rb
       route  get 'users/new' #添加路由
      invoke  erb
      create    app/views/users #新增文件夹
      create    app/views/users/new.html.erb  #生成模板文件
      invoke  test_unit
      create    test/controllers/users_controller_test.rb #控制器测试文件
      invoke  helper
      create    app/helpers/users_helper.rb #辅助方法
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/users.coffee  #coffee js
      invoke    scss
      create      app/assets/stylesheets/users.scss #scss文件
```

* 修改路由

```
get 'signup' => 'users#new'  #添加 'signup'可以得到具名路由`signup_path`
```

* 添加注册链接

```
# home.html.erb
<%= link_to "Sign up now!", signup_path, class: "btn btn-lg btn-primary"%>
```

* 添加注册标题

```
# app/views/users/new.html.erb
<% provide(:title,'Sign up') %>
```

> 推送

### 第五章小结

* 定义布局
* Rails局部视图
* Sass和Asset Pipeline
* 具名路由
* 集成测试链接

### 练习

* 切分支

```
git checkout -b demo5-exe

git branch
```

* 在测试中引入应用的辅助方法

```
#test/test_helper.rb
...
include ApplicationHelper
...
```

* 测试中使用full_title方法

```
#test/integration/site_layout_test.rb
...
get signup_path
assert_select "title", full_title("Sign up")
...
```

* 测试full_title辅助方法

> 使用assert_equal方法通过 == 操作符检查两个值是否相等

```
# test/helpers/application_helper_test.rb
require 'test_helper'
class ApplicationHelperTest < ActionView::TestCase
  test "full title helper" do
    assert_equal full_title, "Rails Tutorial"
    assert_equal full_title("Help"), "Help | Rails Tutorial"
  end
end
```

> ok! git