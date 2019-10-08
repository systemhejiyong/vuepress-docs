# 后端准备

## Server 端必备环境
* 安装软件开发包 [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)   
* 安装开发工具  [Visual Studio 2019](https://visualstudio.microsoft.com/zh-hans/vs/?rr=https%3A%2F%2Fcn.bing.com%2F)  
* 安装MySQL（version： 5.6+,别装8.0+，未测试）


## 获取工程项目
```bash
git clone https://github.com/luoyunchong/lin-cms-dotnetcore.git
```
## 安装依赖包

打开lin-cms-dotnetcore项目目录

右击build-all.ps1,使用powershell运行。即可安装好相应的依赖。或打开powershell，cd到lin-cms-dotnetcore目录 ，输入如 下命令，运行此脚本。
```powershell
.\build-all.ps1
```
双击lin-cms-dotnetcore.sln即可使用vs2019打开项目。


## 数据库配置

src/LinCms.Web目录中appsettings.json
**请务必根据自己的实际情况修改此配置项**
```json
"ConnectionStrings": {
    "Default": "Data Source=127.0.0.1;Port=3306;User ID=root;Password=123456;Initial Catalog=LinCms;Charset=utf8;SslMode=none;Max pool size=10"
}
```
## 数据迁移
该项目使用[FreeSql](https://github.com/2881099/FreeSql)，默认自动迁移数据表结构，无须用户操作，但无数据，而且只有访问到表时才会创建某个表，所以用户可将[备份SQL](https://github.com/luoyunchong/lin-cms-dotnetcore/blob/master/docs/sql/lincms.sql)放到Mysql中生成，还原表结构及数据。

## 运行
用户可使用vs2019，单击LinCms .Web，即可自动启动后台服务。

![](https://ae01.alicdn.com/kf/H70086026eaca4dc8ab4806ee1d07443bP.jpg)

会打开浏览器，访问[https://localhosst:5001/swagger/index.html](https://localhosst:5001/swagger/index.html)，会看到swagger的文档。

![](https://ae01.alicdn.com/kf/He52bc4d3708242d2995419bb584e1f53Q.jpg)



## 部署至linux(Ubuntu16.06)

- [https://blog.igeekfan.cn/2019/06/09/dotnetcore/ASP.NET-Core-Deploy-To-Ubuntu/](https://blog.igeekfan.cn/2019/06/09/dotnetcore/ASP.NET-Core-Deploy-To-Ubuntu/)

## 部署至Docker

- [https://blog.igeekfan.cn/2019/06/09/dotnetcore/ASP.NET-Core-Deploy-To-Docker-Ubuntu/](https://blog.igeekfan.cn/2019/06/09/dotnetcore/ASP.NET-Core-Deploy-To-Docker-Ubuntu/)

<RightMenu />