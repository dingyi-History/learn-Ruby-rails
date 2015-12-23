## Demo3 simple app

> 来自 RailsTutorial 3rd 第三章
> 源码：https://bitbucket.org/imedingyiming/demo3-simpleapp

### 新建项目

```
rails new demo3-simpleapp --skip-bundle

# 可更改gem源到国内 https://ruby.taobao.org
# 添加gem包

bundle install --without production
```

### git

```
git init
git add -A
git commit -m "init simple app"
git remote add origin git@bitbucket.org:imedingyiming/demo3-simpleapp.git
git push -u origin --all
```

### 推送到heroku

```
heroku create demo3-simpleapp

git push heroku master

heroku logs

heroku open
```

### 切新分支

```
git checkout master

git checkout -b static-pages
```


### 静态页面控制器

```
rails g controller StaticPages home help
```

### rails命令简写

```
rails  server      rails s
rails  console     rails c
rails  generate    rails g
bundle install     bundle
rake test          rake
```

### 推送

```
git status
git add -A
git commit -m "add static pages"
git push -u origin static-pages
```

> Ruby文件名一般使用蛇底式,rails生成器使用`underscore`方法把驼峰式转换成蛇底式

### rails撤销操作

```
rails destroy controller StaticPages home help

rails destroy model User

bundle exec rake db:rollback

bundle exec rake db:migrate VERSION=0
```

### 开始测试 TDD

* 好处
  * 1.测试避免"回归",即由于某些原因之前能用的功能不能用了;
  * 2.有测试,重构(改变实现方式,但功能不变)时更有自信;
  * 3.测试是应用代码的客户

* 什么时候进行测试
  * 和应用代码相比,测试代码特别简短,倾向于先编写测试
  * 如果实现的功能不清楚,倾向于先写应用代码
  * 为安全先测试
  * 只要发现一个问题,就编写一个测试重现这个问题,以避免回归,然后再编写应用代码修正问题
  * 尽量不为以后可能修改的代码编写测试
  * 重构之前要编写测试,集中测试容易出错的代码
  * 一般先编写控制器和模型测试,再编写集成测试,如果代码经常变动就完全不测试

### 第一个测试

```
ls test/controllers/
static_pages_controller_test.rb

# test/controllers/static_pages_controller_test.rb

require 'test_helper'

class StaticPagesControllerTest < ActionController::TestCase
  test "should get home" do
    get :home
    assert_response :success
  end

  test "should get help" do
    get :help
    assert_response :success
  end
end

# 执行测试
bundle exec rake test
```

### TDD工作流

```
 RED -> GREEN -> REWORK
```

1.RED

```
#添加
test "should get home" do
  get :home
  assert_response :success
  assert_select "title","Home | Ruby on Rails Tutorial"
end

test "should get help" do
  get :help
  assert_response :success
  assert_select "title","Help | Ruby on Rails Tutorial"
end

test "should get about" do
  get :about
  assert_response :success
  assert_select "title","About | Ruby on Rails Tutorial" #html标签为什么内容
end
```

测试:`bundle exec rake test`

2.GREEN

```
#routes.rb
get 'static_pages/about'

#static_pages_controller.rb
def about
end

#static_pages/about.html.erb
<!DOCTYPE html>
<html>
<head>
  <title>About | Ruby on Rails Tutorial</title>
</head>
about
</html>

其他类似
```

测试:`bundle exec rake test`

3.REWORK

布局和嵌入式Ruby

* app/views/layouts/application.html.erb

```
<!DOCTYPE html>
<html>
<head>
  <title><%= yield(:title)%></title>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>
</head>
<body>

<%= yield %>

</body>
</html>

```

* about.html.erb

```
<% provide(:title, "about"%>
about

#其他类似
```

测试:`bundle exec rake test`

> 高级测试技术,MiniTest报告程序(下一遍添加)