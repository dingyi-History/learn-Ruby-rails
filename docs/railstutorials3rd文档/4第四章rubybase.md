## demo4 simple-app 2 (Rails背后的Ruby)

> 源码为demo3的demo4分支

### 布局文件

```
# app/views/layouts/application.html.erb

<%= stylesheet_link_tag "application", media:"all","data-turbolinks-track" => true %>

# 调用函数时可以省略括号
stylesheet_link_tag('application', media: 'all', 'data-turbolinks-track' => true)

# 哈希是最后一个参数,可以省略花括号
stylesheet_link_tag 'application', { media: 'all', 'data-turbolinks-track' => true }

# 符号中不能使用连字符
data-turbolinks-track: true 无效,

# 换一行的ruby

调用了stylesheet_link_tag函数,并且传入两个参数: 一个是字符串,指明样式表的路径;另一个是哈希,包含两个元素,
第一个指明媒介类型,第二个启用Rails4.0中添加的Turbolink功能.
因为使用的是<%= %> 函数的执行结果会通过ERb插入模板中

显示的源码: <link data-turbolinks-track="true" href="/assets/application.css" media="all" rel="stylesheet" />

<%= yield(:title) %>
||
<% provide(:title,"Home")%>
```

### full_title辅助方法

```
# app/helpers/application_helper.rb
module ApplicationHelper
 #根据所在的页面返回完整的标题
 def full_title(page_title = '')
   base_title = 'Ruby on Rails Tutorial'
   if page_title.empty?
     base_title
   else
     "#{page_title} | #{base_title}"
   end
 end

# app/views/layouts/application.html.erb
 <title><%= full_title(yield(:title))%></title>
```

### Ruby基础

* 控制台 `rails console`
* 注释 `#`
* 字符串/插值 ` "#{name} " + '' `
* 打印字符串 `puts 或 print`
* 转义 `\`
* 换行 `\n`
* 对象和消息传送 `"foot".length   "".empty?"`
* 空 nil
* `!!nil  => false`  `!!0 => true`
* 数组 ["foo","bar","baz"]
* `.split()` 拆分字符串成数组
* `.first` 数组一个值
* `.second` 数组第二个值
* `.last` 数组最后一个值
* `a.last == a[-1] => true`
* `.empty?`
* `.include?(xx)`
* `.sort`
* `.reverse`
* `.shuffle` 打乱数组
* `.shuffle[0..7]` 取前8个数
* 修改原值 需要加 ! 如 `a.sort!`
* `a.push(7)` 同 `a << 7` 加到数组结尾
* `a.join(',')` 数组连成字符串
* `0..9` 包含`9`  `0...9` 不包含 `9`
* `(0..9).to_a` 用括号包住值域,组成数组
* 块 `(1..5)each { |i| puts 2*i}`
* 块

```
(1..5)each do |i|
puts 2*i
end
```

* ` %w[a b c] ` 用于创建元素为字符串的数组
* `3.times{ #code }` 表示次数
* `(1..5).map { |i|**2 } ` **表示幂运算
* `%w[a b c].map { |char| char.upcase }  等同于  %w[A B C].map(&:downcase)`
* `('a'..'z').to_a.shuffle[0..7].join`

```
('a'..'z').to_a  # 由全部英文字母组成的数组
.shuffle  # 打乱数组
.shuffle[0..7] # 取出前8个元素
.join # 把取出的元素合并成字符串
```

* 空哈希 `{}`

```
user["first_name"] = "name"
user = { "first_name" => "name" }
user = { :name => "name"}
user[:name]

h1 = { :name => "name" }
h2 = { name => "name" }
h1 == h2  => true

#嵌套哈希
params[:user][:email]

flash = { success: "It worked", danger: "It failed" }
flash.each do |key,value|
  puts "key #{key.inspect} has value #{value.inspect}"
end
```

* `.inspect` 输出数组的字面量形式 快捷方式 p 方法 `p :name 等价于 puts :name.inspect `
* Ruby中一切皆对象
* 构造方法

```
s = "footer" # 使用双引号字面构造方法
s.class # 返回字符串所属类
s = String.new("footer") # 字符串的具名构造方法

a = Array.new([1,3,2])
h = Hash.new   => {}
h[:foo]  => nil
h = Hash.new(0)  => {} #让不存在的键返回0而不是nil
h[:foo] => 0
```

* 类的继承

```
s.class.superclass # 找父类

self代表这个字符串本身
```

### 定义用户类

```
# example_user.rb
class User
  attr_accessor :name,:email
  def initialize(attributes = {} )
    @name = attributes[:name]
    @email = attributes[:email]
  end
  def formatted_email
    "#{@name} < #{@email}"
  end
end
```

### 提交代码

```
git status
git add -A
git commit -m "add file"
git push origin master
bundle exec rake test
git push heroku
heroku open
```

### 第四章小结

* Ruby提供了很多处理字符串的方法
* Ruby中一切皆对象
* Ruby 定义方法 def
* Ruby 定义类 class
* Ruby 内建支持的数据类型有数组,值域和哈希
* Ruby块是一种灵活的语言接口,可遍历可枚举的数据类型
* 符号式一种标记,和字符串类似,但没有额外的束缚
* Ruby支持对象继承
* 可以打开并修改Ruby内建的类
* deified是回文
