---
title: RabbitMQ
tags: RabbitMQ
abbrlink: b543ced0
date: 2020-08-31 14:11:43
---
RabbitMQ配置
<!-- more -->
步骤1：前往官网下载，路径如下：
https://www.erlang.org/downloads

步骤2：对于一般的开发电脑，安装环境都是设置在系统变量，这样子设置的好处是不会随着系统用户变化而失效

Erlang在安装时候会检查环境的组件是否其齐全，如果不全会弹出Microsoft Visual C++，Redistributable 可再发行组件包要求用户修复，可以进行修复，不影响安装!

步骤3：环境变量的安装与验证

安装完成之后创建一个名为ERLANG_HOME的系统变量，其值指向erlang的安装目录，同时将%ERLANG_HOME%\bin加入到Path中，最后打开命令行，输入erl，如果出现erlang的版本信息就表示erlang语言环境安装成功；

注意：ERLANG_HOME的指向目录是bin的上以及目录

erlang_home配置：

![attr](https://img-blog.csdn.net/20180917112755917?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTA1OTQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Path路径配置：

![attr](https://img-blog.csdn.net/2018091711290296?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTA1OTQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



windows下安装RabbitMQ消息队列

 步骤1：下载RabbitMQ软件，大概10M左右，速度可能非常慢，需要等待

 Rabbitmq软件下载：http://www.rabbitmq.com/   

步骤2：安装

下载完成后双击xxxxxx.exe进行安装，rabbitmq的默认安装目录是系统盘的Program file文件夹，但是rabbitmq其内部设置是不允许文件夹有中文，有空格的，加之功能也不适合在系统盘中，需要另外选择目录进行安装!。

建议：一般情况下都是将erlang与安装同一个大目录!

步骤3：安装管理桌面的插件，可以通过浏览器登录查看消息队列状态和交换机的工作情况等信息的管理页面，类似于tomcat的管理页面；

在rabbitmq的安装目录下找到sbin目录，进入并在此目录打开cmd窗口(进入sbin的cmd窗口)。输入rabbitmq-plugins enable rabbitmq_management命令，会提示plugin安装成功!网上是默认安装6个插件，但是本身是提示了安装3个后面的使用也没有什么问题，不知道是不是版本问题，当前一般3个也能正常使用，场景图如下：

.\rabbitmq-plugins enable rabbitmq_management

## 启动方式
1、以应用方式启动

rabbitmq-server -detached 后台启动

Rabbitmq-server 直接启动，如果你关闭窗口或者需要在改窗口使用其他命令时应用就会停止

 关闭:rabbitmqctl stop

2、以服务方式启动（安装完之后在任务管理器中服务一栏能看到RabbtiMq）

rabbitmq-service install 安装服务

rabbitmq-service start 开始服务

Rabbitmq-service stop  停止服务

Rabbitmq-service enable 使服务有效

Rabbitmq-service disable 使服务无效

rabbitmq-service help 帮助

当rabbitmq-service install之后默认服务是enable的，如果这时设置服务为disable的话，rabbitmq-service start就会报错。

当rabbitmq-service start正常启动服务之后，使用disable是没有效果的

  关闭:rabbitmqctl stop

3、Rabbitmq 管理插件启动，可视化界面

rabbitmq-plugins enable rabbitmq_management 启动

rabbitmq-plugins disable rabbitmq_management 关闭

 

4、Rabbitmq节点管理方式

Rabbitmqctl
## 参考链接：

[RabbitMQ在windows下的安装](https://jingyan.baidu.com/article/a17d5285173ce68098c8f2e5.html)

[windows10环境下的RabbitMQ安装步骤（图文）](https://blog.csdn.net/weixin_39735923/article/details/79288578?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param)

[Window下安装RabbitMQ3.8.5](https://blog.csdn.net/doupengzp/article/details/106887362?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.channel_param#2.%E6%B7%BB%E5%8A%A0%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E5%B9%B6%E4%BF%AE%E6%94%B9%E7%AB%AF%E5%8F%A3%E5%8F%B7)

https://blog.csdn.net/qq_36505948/article/details/82734133

https://blog.csdn.net/seven_coder/article/details/50946562

[远程访问rabbitMQ](https://www.jianshu.com/p/e3af4cf97820)