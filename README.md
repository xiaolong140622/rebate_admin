<h1 style="text-align: center">mshop迈沃商城系统</h1>


#### 项目简介
mshop基于当前流行技术组合的前后端分离商城系统： SpringBoot2+MybatisPlus+SpringSecurity+jwt+redis+Vue的前后端分离的商城系统， 包含淘客系统、积分商城、会员、充值等功能，更适合企业或个人二次开发；

#### 官网地址：https://www.mailvor.com

#### 项目结构
mshop3.0项目采用分模块开发方式
- mshop-app       移动端API模块
- mshop-admin     管理后台API模块
- mshop-mp        微信模块
- mshop-store     商城通用模块
- mshop-shop      商城管理后台模块
- mshop-redis     消息模块
- mshop-system    系统公共模块
- mshop-log       日志模块
- mshop-tool      工具模块
- mshop-gen       代码生成模块
- mshop-db        mybatisPlus配置模块


## 技术选型
* 1 后端使用技术
  * 1.1 SpringBoot2
  * 1.2 MyBatis-Plus
  * 1.3 SpringSecurity
  * 1.5 Druid
  * 1.6 Slf4j
  * 1.7 Fastjson
  * 1.8 JWT
  * 1.9 Redis
  * 1.10 Quartz
  * 1.11 Mysql
  * 1.12 Swagger
  * 1.13 WxJava
  * 1.14 Lombok
  * 1.15 Hutool
  * 1.16 Mapstruct

* 前端使用技术
  * 2.1 Vue 全家桶
  * 2.2 Element
  * 2.3 Flutter

部署注意事项：
- mysql一定需要5.7含以上，需要支持json
- 部署完成后保存一次 分销管理->分销配置

# 本地安装
### 基本环境（必备）
- 1、JDK：8+
- 2、Redis 3.0+
- 3、Maven 3.0+
- 4、MYSQL 5.7+
- 5、Node v18.20.4
### 开发工具
Idea、Webstorm、vscode

### 后台系统工程（JAVA端） sfb_server

1、请确保redis已经安装启动

2、idea打开工程

3、配置开发环境数据库信息及其redis信息，文件路径如下：
- mshop-app\src\main\resources\config
- mshop-admin\src\main\resources\config

5、然后在父级pom.xml输入命令 mvn clean install 或者用idea工具操作

6、启动程序，启动程序路径如下：


### 后台前端工程（VUE端） sfb_server_qd
1、请确保本地已经安装node,建议node14或者node12

2、打开工程

3、在控制台输入命令：npm i

4、安装依赖失败（上一步成功跳过）
```
先确认node版本，node -v，12以下会有问题，

设置依赖包下载地址
npm config set registry https://registry.npm.taobao.org
配置后可通过下面方式来验证是否成功
npm config get registry

重新执行npm i
```
5、在控制台输入命令：npm run dev，控制台打印出如下画面，恭喜表示本项目启动成功拉。
![输入图片说明](https://images.gitee.com/uploads/images/2021/0811/163209_09ed1793_477893.png "test9.png")


5、打开浏览器输入地址：
默认用户名：admin 密码：123456
后台需要验证码登录，可以在登录接口设置默认密码，绕开验证码


# nginx线上部署

### 后台系统（Java端） sfb_server

1、mvn install 或者直接idea打成jar包

2、配置nginx 如下即可：

```
server{ 
    listen 80;
    server_name app.xxx.com;

    location /api {
        proxy_pass http://127.0.0.1:8008;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Port $server_port;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
   }
 
}
```



### 后台前端工程（VUE端） sfb_server_qd
1、根目录新建文件.env.production，加入如下内容
```
ENV = 'production'

# 接口地址 改成自己域名
VUE_APP_BASE_API  = 'http://admin.xxx.com/admin'
VUE_APP_WS_API = 'ws://admin.xxx.com/admin'
```
2、输入命令：npm run build 编译打包

3、把打包后的dist目录代码上传到服务器

4、配置nginx如下：
```
server
{
    listen 80;
    server_name admin.xxx.com;

    location / {
        try_files $uri $uri/ @router;
        index index.html;
    }
    location @router {
        rewrite ^.*$ /index.html last;
    } 

    location /admin {
        proxy_pass http://127.0.0.1:8001;
        proxy_set_header Host $Host:$server_port;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header REMOTE-HOST $remote_addr;
        add_header X-Cache $upstream_cache_status;
        proxy_set_header X-Host $host:$server_port;
        proxy_set_header X-Scheme $scheme;
        proxy_connect_timeout 30s;
        proxy_read_timeout 86400s;
        proxy_send_timeout 30s;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
   }
   
}

```
