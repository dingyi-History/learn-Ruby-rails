## Demo2 toy app

> Rails Tutorial第二章
> 源码：https://bitbucket.org/imedingyiming/demo2-toyapp


### 新建

```
rails new demo2-toyapp --skip-bundle

bundle install
```

### 快速新建用户操作

```
rails g scaffold User name:string email:string

#模型名:单数
#资源和控制器:复数

#Rake迁移
bundle exec rake db:migrate

rails s

访问localhost:3000/users
```


### MVC

![](https://github.com/dingyiming/learn-Ruby-rails/blob/master/pics/MVC.png?raw=true)

* 指定根路径

```
root `users#index`
```

* Rest架构路由

```
HTTP请求  URL             动作    作用
GET      /users          index   列出所用用户
GET      /users/1        show    显示ID为1的用户
GET      /users/new      new     显示创建用户的页面
POST     /users          create  创建新用户
GET      /users/1/edit   edit    显示编辑ID为1的用户页面
PATCH    /users/1        update  更新ID为1的用户
DELETE   /users/1        destroy 删除ID为1 的用户
```

* 显示所有用户

```
 def index
    @users = User.all
  end
```

* 用户模型

```
#app/models/user.rb
class User < ActiveRecord::Base
  has_many :microposts
  validates :name, presence: true
  validates :email, presence: true
end
```

* 视图中

```
#app/views/users/index.html.erb
<% @users.each do |user| %>
<tr>
  <td><%= user.name %></td>
  <td><%= user.email %></td>
  <td><%= link_to 'Show', user %></td>
  <td><%= link_to 'Edit', edit_user_path(user) %></td>
  <td><%= link_to 'Destroy', user, method: :delete,data: { confirm: 'Are you sure?'} %></td>
</tr>
<% end %>
```

### 微博资源

```
rails g scaffold Micropost content:text user_id:integer

bundle exec rake db:migrate
```

* 在模型类中添加长度验证与存在性验证

```
#app/models/micropost.rb
class Micropost < ActiveRecord::Base
  belongs_to :user # 一个微博属于一个用户
  validates :content,length: { maximum: 140 },presence: true
end
```

* 1对多

```
has_many :microposts
```

* 控制台 `rails console`

### 部署

```
git status

git add -A

git commit -m "finish toy app"

git push origin master

git push heroku

heroku run rake db:migrate
```

### 第二章小结

* 概览了MVC
* MVC
* Rest架构
* 使用数据模型
* 在heroku运行一个基于数据库的web应用

* 缺点
  * 没有布局样式
  * 没有静态页面
  * 没用户密码
  * 没头像
  * 没登录功能
  * 不安全
  * 没实现用户和微博之间的自动关联
  * 没实现关注和被关在功能
  * 没实现微博列表
  * 没编写测试
