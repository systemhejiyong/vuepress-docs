
# 前端准备

## 开源地址

- [https://github.com/luoyunchong/lin-cms-vue](https://github.com/luoyunchong/lin-cms-vue)

## 文档地址
- 介绍 [http://doc.cms.7yue.pro/](http://doc.cms.7yue.pro/)
- 前端 [http://doc.cms.7yue.pro/lin/client/](http://doc.cms.7yue.pro/lin/client/s)

## 官方预览地址

- [http://face.cms.7yue.pro/](http://face.cms.7yue.pro/)

## 扩展官方博客模块线上地址 
- 用户端 lin-cms-vvlog [http://47.106.80.39:8080/index](http://47.106.80.39:8080/index) 
  - 普通用户：710277267@qq.com
  - 密码：123qwe

- 管理员 lin-cms-vue [http://47.106.80.39:8081/#/](http://47.106.80.39:8081/#/)
  - 管理员： admin
  - 密码：123qwe

## 与dotnetcore配合部署地址

- 本项目swagger地址 [http://47.106.80.39:88/swagger/index.html](http://47.106.80.39:88/swagger/index.html)

## 快速上手

开发必备

- [Node.js 10+](https://nodejs.org/en/) 版本即可，我须安装12.7
- [yarn](https://yarnpkg.com/zh-Hant/docs/install#windows-stable)

如果以下命令有问题，请删除yarn.lock，node_modules文件夹后，重新执行yarn，yarn serve
```
# clone the project
git clone https://github.com/TaleLin/lin-cms-vue.git

# install dependency
yarn

# develop
yarn serve
```

## 配置项

1. 配置 api 地址： 打开配置文件 src/config/index.js 配置 baseUrl ，本地开发阶段配置本地虚拟域名(https://localhost:5001/)，线上部署生产域名。



<RightMenu />