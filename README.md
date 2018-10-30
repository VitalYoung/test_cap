## 使用Capistrano部署Rails项目

ruby 2.3.5

Rails 4.0.13


### 1.Gemfile

```
group :development do
  # ...

  # 其中`capistrano-rails`包含了以下三个插件。
  # gem 'capistrano/bundler'
  # gem 'capistrano/rails/assets'
  # gem 'capistrano/rails/migrations'
  # 你也可以分别一个个加进去，但是何必呢？这些基本都是`rails`部署必须的。
  # 直接用`gem 'capistrano-rails'`这一个就好了。
  gem 'capistrano-rails'

  # 对`passenger`与`rbenv`的支持
  gem 'capistrano-passenger'
  gem 'capistrano-rbenv'
end
```


### 2.生成capistrano的相关配置文件

```
cap install
```

### 3.编辑 Capfile（项目的根目录下）

```
# 加上这行
require "capistrano/rails"

# 去掉这两行前面的`#`号 
require "capistrano/rvm"
require "capistrano/passenger"
```

### 4.编辑 config/deploy.rb

```
# 最顶上加这行，注意是「`」号而不是单引号「'」
# 如果你对ssh-add有兴趣，你可以去读这一篇。https://ihower.tw/blog/archives/7837
`ssh-add`

# 项目名称
set :application, "test_cap"

# git仓库地址
set :repo_url, "git@github.com:VitalYoung/test_cap.git"

# 需要部署到服务器的位置
set :deploy_to, "/opt/app/test_cap"

# 去掉注释，并加上 "config/master.key"
append :linked_files, "config/database.yml"

# 去掉注释
append :linked_dirs, 'log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'public/system'
```


### 5.编辑config/deploy/production.rb

```
# server-based syntax
# ======================
# Defines a single server with a list of roles and multiple properties.
# You can define all roles on a single server, or split them:

server "47.52.249.6", user: "nick.zhu", roles: %w{app db web}, my_property: :my_value
# server "example.com", user: "deploy", roles: %w{app web}, other_property: :other_value
# server "db.example.com", user: "deploy", roles: %w{db}
```

```
# Custom SSH Options
# ==================
# You may pass any option but keep in mind that net/ssh understands a
# limited set of options, consult the Net::SSH documentation.
# http://net-ssh.github.io/net-ssh/classes/Net/SSH.html#method-c-start
#
# Global options
# --------------
set :ssh_options, {
  keys: %w(~/.ssh/id_rsa),
  forward_agent: true,
  auth_methods: %w(publickey)
}
```

### 6.部署

```
# 本地执行命令
cap production deploy
```
