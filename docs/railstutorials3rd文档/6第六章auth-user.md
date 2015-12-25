## demo6 auth-user 用户与认证(注册)

> rails tutorial 3rd 第六章 用户模型
> 托管: https://bitbucket.org/imedingyiming/demo6-auth-user
> 省略初始化了,每次都会练一遍,文档中就不描述了,顺当多了,开始记录些重点吧.

###  用户模型

* 生成

```
//控制器生成
rails g controller Users new

//模型类生成
rails g model User name:string email:string
```

* 迁移文件

```
db/migrate/[timestamp]_create_users.rb

class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name
      t.string :email

      t.timstamps null: false
    end
  end
end

// 表名复数,模型名单数
```

* 向上迁移

```
bundle exec rake db:migrate

//回滚
bundle exec db:rollback
```

* 模型文件

```
app/models/user.rb
  class User < ActiveRecord::Base
  end
```

* 模型对象的操作

![](https://github.com/dingyiming/learn-Ruby-rails/blob/master/pics/railscurd.png?raw=true)

```
rails console --sandbox

//新建
user = User.new(name: "xxx", email: "xxx")
user.valid?  => true
user.save  => true
user  =>  结果集
user.name  => xxx
foo = User.create(name: "xx", email:"xx") => 结果集

//删除
foo.destroy   => 结果集
foo  => 结果集,销毁的对象还在内存中

// 查找
User.find(1) => 结果集
User.find_by(email: "xxx")  => 结果集
User.first   => 第一条结果集
User.all     => 全部结果集

//更新
user.email = "xxxx"  => 赋新值
user.reload.email  => 使用数据库中数据重新加载对象
//更新的第二种方法
user.update_attributes(name: "xxx",email: "xx")  => true
```

### 用户数据验证

* 有效性测试

```
//test/models/user_test.rb
def setup
  @user = User.new(name: "xxx",email: "xxx")
end
test "should be valid" do
  assert @user.valid?
end

bundle exec rake test:models  //只运行模型测试
```

* 存在性验证

```
//test/models/user_test.rb
...
test "name should be present"
  @user.name = " "
  assert_not @user.valid?
end

//app/models/user.rb
validates :name,presence: true
```

* 长度验证

```
test "name should not be too long" do
  @user.email = "a" * 51
  assert_not @user.valid?
end

test "email should not be too long" do
  @user.email = "a"*256
  assert_not @user.valid?
end

validates :name,presence:true,length: { maximum:50 }
validates :email,presences:true,length: { maximum:255 }
```

* 格式验证

```
test "email validation should accepte valid addresses" do
  valid_addresses = %w[user@exampel.com USER@foo.COM A_US-ER@foo.bar.org
                       first.last@foo.jp alice+bob@baz.cn]
  valid_addresses.each do |valid_address|
    @user.email = valid_address
    assert @user.valid?, "#{valid_address.inspect} should be valid"
  end
end
```

* 邮箱正则

```
/\A[\w+\-.]+@[a-z\d\-.]+\.[a-z]+\z/i        完整的正则表达式
/                                           正则表达式开始
\A                                          匹配字符串的开头
[\w+\-.]+                                   一个或多个字母,加好,连字符,或点号
@                                           匹配@符号
[a-z\d\-.]+                                 一个或多个字母,数字,连字符或点号
\.                                          匹配点号
[a-z]+                                      一个或多个字母
\z                                          匹配字符串结尾
/                                           结束正则表达式
i                                           不区分大小写
```

> Rubular  ruby regular expression editor 练习正则

```
bundle exec rake test:models
```

* 唯一性验证

```
test "拒绝重复电子邮件地址" do
  duplicate_user = @user.dup  //创建重复对象
  duplicate_user.email = @user.email.upcase //不区分大小写
  @user.save
  assert_not duplicate_user.valid?
end

validates :email,uniqueness: true  //唯一性
validates :email,uniqueness: { case_sensitive: false } //不区分大小写的唯一性验证,自动为true
```

### 数据库索引

* 创建迁移文件

```
rails g migration add_index_to_users_email

// db/migrate/[timestamp]_add_index_to_users_email.rb
def change
  add_index :users, :email, unique: true
end

bundle exec rake db:migrate
```

* eamil保存前存为小写

```
before_save { self.email = email.downcase }
```


### 添加安全密码

```
class User < ActiveRecord::Base
  ...
  has_secure_password
end
```

* `has_secure_password`方法,会添加如下功能:
  * 在数据库中的`password_digest` 列存储安全的密码哈希值
  * 获得一对"虚拟属性",password和password_confirmation,而且创建对象时会执行存在下验证和匹配验证
  * 获得authenticate方法,如果密码正确,返回对应的用户对象,否则返回false

* `has_secure_password`发挥功效的唯一要求是,模型中字段名为`password_digest`属性

* 迁移命名

```
rails g migration add_password_digest_to_users password_digest:string

//生成
def change
  add_column :users, :password_digest, :string
end

bundle exec rake db:migrate
```

* 添加`bcrypt` 哈希算法计算密码

```
//Gemfile
gem 'bcrypt', '3.1.7'

bundle install
```

* 添加密码测试

```
  def setup
    @user = User.new(name: "user",
                     email: "user@example.com",
                     password: "foobar",
                     password_confirmation: "foobar")
  end
```

* 密码的最短长度

```
//双重赋值
test "密码最短长度" do
  @user.password = @user.password_confirmation = "a" * 5
  assert_not @user.valid?
end

validates :password,length: { minimum: 6 }
```

### 创建并认证用户

```
rails c
User.create(name:"xxx",email: "wode@example.com",password: "111111",password_confirmation: "111111")
user = User.find_by(name:"xxx")
user.password_digest
user.authenticate("111111")  => 结果集,不对则为false
!!user.authenticate("111111") => true
```

### 推送

```
bundle exec rake test
git add -A
git commit -m "finish demo6"
git checkout master
git push origin master
git push heroku master
heroku run rake db:migrate
heroku run console --sandbox
User.create(...)
```

### 第六章小结

* 使用迁移可以修改应用的数据模型
* ActiveRecord 提供了很多创建和处理数据模型的方法
* 使用ActiveRecord验证可以在模型的数据上添加约束条件
* 常见的验证有存在性,长度,格式
* 正则表达式
* 数据库索引可以提升查询效率,而且能在数据库层实现唯一性约束
* 可以使用内置的`has_secure_password` 方法在模型中添加一个安全的密码

### 练习

* 切分支

```
git checkout -b demo6-exe
```

* 邮箱小写形式测试

```
test "email addresses should be saved as lower-case" do
  mixed_case_email = "Foo@ExAMPle.CoM"
  @user.email = mixed_case_email
  @user.save
  assert_equal mixed_case_email.downcase,@user.reload.email
end
```

* `before_save`回调的另一种实现方式

```
before { email.downcase! }
```

* 不允许电子邮件多个点号的正则

```
/\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
```