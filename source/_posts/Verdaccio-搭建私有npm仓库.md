---
title: Verdaccio 搭建私有npm仓库
tags:
  - 私有npm仓库
  - Verdaccio
categories: 前端
abbrlink: 27f9f710
date: 2021-03-30 14:29:27
---

## Verdaccio 是一个跨平台的 Web 应用程序，轻量级的
[Verdaccio官网地址：](https://verdaccio.org/docs/zh-CN/installation) https://verdaccio.org/docs/zh-CN/installation

### 安装步骤

使用 npm

`npm install -g verdaccio`

或使用 yarn

`yarn global add verdaccio`

输入`verdaccio`运行 

```
    $> verdaccio
    warn --- config file  - /home/.config/verdaccio/config.yaml
    warn --- http address - http://localhost:4873/ - verdaccio/4.8.1
```
yaml 是配置文件，
<!-- more -->

```
#
# This is the default config file. It allows all users to do anything,
# so don't use it on production systems.
#
# Look here for more config file examples:
# https://github.com/verdaccio/verdaccio/tree/master/conf
#

# path to a directory with all packages
storage: ./storage
# path to a directory with plugins to include
plugins: ./plugins

web:
  title: npm 私有仓库
  # comment out to disable gravatar support
  # gravatar: false
  # by default packages are ordercer ascendant (asc|desc)
  # sort_packages: asc
  # convert your UI to the dark side
  # darkMode: true

# translate your registry, api i18n not available yet
# i18n:
# list of the available translations https://github.com/verdaccio/ui/tree/master/i18n/translations
#   web: en-US

auth:
  htpasswd:
    file: ./htpasswd
    # Maximum amount of users allowed to register, defaults to "+inf".
    # You can set this to -1 to disable registration.
    # max_users: 1000

# a list of other known repositories we can talk to
uplinks:
  npmjs:
    url: https://registry.npm.taobao.org/

packages:
  '@*/*':
    # scoped packages
    access: $all
    publish: $authenticated
    unpublish: $authenticated
    proxy: npmjs

  '**':
    # allow all users (including non-authenticated users) to read and
    # publish all packages
    #
    # you can specify usernames/groupnames (depending on your auth plugin)
    # and three keywords: "$all", "$anonymous", "$authenticated"
    access: $all

    # allow all known users to publish/publish packages
    # (anyone can register by default, remember?)
    publish: $authenticated
    unpublish: $authenticated

    # if package is not available locally, proxy requests to 'npmjs' registry
    proxy: npmjs

# You can specify HTTP/1.1 server keep alive timeout in seconds for incoming connections.
# A value of 0 makes the http server behave similarly to Node.js versions prior to 8.0.0, which did not have a keep-alive timeout.
# WORKAROUND: Through given configuration you can workaround following issue https://github.com/verdaccio/verdaccio/issues/301. Set to 0 in case 60 is not enough.
server:
  keepAliveTimeout: 60

middlewares:
  audit:
    enabled: true
   
# 监听的端口，IP,重点,不配置这个,只能本机能访问
listen: 0.0.0.0:4873
# log settings
logs:
  - { type: stdout, format: pretty, level: http }
  #- {type: file, path: verdaccio.log, level: info}
#experiments:
#  # support for npm token command
#  token: false
#  # support for the new v1 search endpoint, functional by incomplete read more on ticket 1732
#  search: false
#  # disable writing body size to logs, read more on ticket 1912
#  bytesin_off: false

# This affect the web and api (not developed yet)
#i18n:
#web: en-US

```

### 使用 nrm 设置 Verdaccio 的源 

`nrm add verdaccio http://localhost:4873/`

### 注册用户 

`npm adduser --registry http://localhost:4873`

### 登陆私有npm ，输入注册的用户名，密码，邮箱

`npm login–-registry http://localhost:4873`

### 发布包：需要去掉 package 里的私有字段

`npm publish --registry http://localhost:4873`