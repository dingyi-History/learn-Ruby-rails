## Demo1 - HelloWorld

> 来自 rails tutorial 3rd
> 源码：https://bitbucket.org/imedingyiming/demo1-helloworld


### 新建项目

```
rails new demo1 --skip-bundle

bundle install
```
> gem源可改为 https://ruby.taobao.org

### 1.添加方法

```
vim application_controller.rb

def hello
  render text: "Hello world"
end
```

### 2.添加路由

```
vim routes.rb

root 'application#hello'
```

### 3.启动

```
rails s

localhost:3000
```

> OK

## git基础使用

* 查看公钥

```
cat ~/.ssh/id_rsa.pub
```

* git初始化  `git init`
* git查看暂存区状态 `git status`
* git添加全文件 `git add .` 或 `git add -A`
* 保留改动 `git commit -m "说明" `
* 查看提交历史 `git log`
* 撤销改动 `git checkout -f`
* 添加源:

```
git remote add origin  git@bitbucket.org:imedingyiming/demo1-helloworld.git
```

* 第一次推送到远端

```
git push -u origin --all
```

* 显示分支 `git branch`
* 创建新分支 `git checkout -b modify-README`
* 查看改动 `git diff`
* 提交当前改动 `git commit -a -m "xxx" `
* 切换分支 `git checkout master`
* 合并分支 `git merge modify-README`
* 推送 `git push origin master`
* 删除分支 `git branch -d modify-README`

## 搭建HeroKu部署环境

### 添加生产环境配置

```
group :production do
  gem 'pg'
  gem 'rails_12factor'
end

bundle install --without production
```

### mac上通过homebrew安装heroku工具

```
brew search heroku

brew install heroku-toolbelt

heroku version
```

### 使用heroku

```
heroku login

heroku keys:add

heroku create  #创建一个新应用

git push heroku master #部署到heroku

heroku open #查看部署的应用
```

### heroku命令

```
heroku rename rails-tutorial-hello #重命名
```

## 读完第一章章学到了

* Ruby on Rails 是一个使用Ruby编程语言开发的web开发框架
* 在预先配置好的云端环境中安装Rails,新建应用,以及编辑文件都很简单
* Rails提供了命令行命令rails,可用于新建应用和启动本地服务器
* 为了避免丢失数据和协作,使用了git和bithucket
* 使用heroku把应用部署到生产环境中