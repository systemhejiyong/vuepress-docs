## GitHub第三方授权登录
使用SPA+.NET Core3.1实现 GitHub第三方授权登录 类似使用AspNet.Security.OAuth.GitHub，前端使用如下：VUE+Vue-Router+axios

## AspNet.Security.OAuth.GitHub
- GitHub [https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers)

## GitHub授权登录
什么配置的过程不说了。。有一推。

- [GitHub 第三方登录](https://www.jianshu.com/p/78d186aeb526)
- [给你的网站添加第三方登录以及短信验证功能](https://juejin.im/post/5dfb04cee51d45583a66c2f3)


下面为示例

```
client_id:0be6b05fc717bfc4fb67
client_secret:dcaced9f176afba64e89d88b9b06ffc4a887a609
```
Get
```
https://github.com/login/oauth/authorize?client_id=0be6b05fc717bfc4fb67&redirect_uri=https://localhost:5001/signin-github
```
会重定向到

[https://localhost:5001/signin-github?code=07537a84d12bbae08361](https://localhost:5001/signin-github?code=07537a84d12bbae08361)

这个code放到下面的请求中，获取access_token
POST方式（PostMan去请求）
```
https://github.com/login/oauth/access_token?client_id=0be6b05fc717bfc4fb67&client_secret=dcaced9f176afba64e89d88b9b06ffc4a887a609&code=07537a84d12bbae08361
```

Get方式
```
https://api.github.com/user?access_token=787506afa3271d077b98f18af56d7cfdc8db43b4
```

然后就能获取用户信息

 ```
{
    "login": "luoyunchong",
    "id": 18613266,
    "node_id": "MDQ6VXNlcjE4NjEzMjY2",
    "avatar_url": "https://avatars1.githubusercontent.com/u/18613266?v=4",
    "gravatar_id": "",
    "url": "https://api.github.com/users/luoyunchong",
    "html_url": "https://github.com/luoyunchong",
    "followers_url": "https://api.github.com/users/luoyunchong/followers",
    "following_url": "https://api.github.com/users/luoyunchong/following{/other_user}",
    "gists_url": "https://api.github.com/users/luoyunchong/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/luoyunchong/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/luoyunchong/subscriptions",
    "organizations_url": "https://api.github.com/users/luoyunchong/orgs",
    "repos_url": "https://api.github.com/users/luoyunchong/repos",
    "events_url": "https://api.github.com/users/luoyunchong/events{/privacy}",
    "received_events_url": "https://api.github.com/users/luoyunchong/received_events",
    "type": "User",
    "site_admin": false,
    "name": "IGeekFan",
    "company": null,
    "blog": "https://blog.igeekfan.cn",
    "location": null,
    "email": "luoyunchong@foxmail.com",
    "hireable": null,
    "bio": "学习之路漫漫无期。",
    "public_repos": 14,
    "public_gists": 0,
    "followers": 16,
    "following": 11,
    "created_at": "2016-04-22T10:33:44Z",
    "updated_at": "2019-12-21T14:49:33Z"
}
  ```
  

## .NET Core3.1
以下代码为主要代码，完整代码看下面的DEMO链接。

使用WebApi时，看了一些项目，全是基于MVC结构的，都不是我想要的。看了一些博客上面介绍 ,步骤都是千篇一律，都是配合前后端分离的。

- 前端运行在:http://localhost:8081
- 后端运行在:https://localhost:5001
### 前后端分离的SPA 配合第三方授权登录流程如下

本地测试时，gitHub回调地址设置 http(s)://ip:端口/signin-github
- 如: https://localhost:5001/signin-github。

#### 1. 上面这个明明填写的后端的地址，那后端怎么把结果通知前端呢？

前端请求**https://localhost:5001/signin?provider=GitHub&redirectUrl=http://localhost:8080/login-result**
- 提供参数provider为GitHub，
- redirectUrl为GitHub授权登录后，回调signin-github后，后端再去重定向的地址，这里填前端的一个路由。
    
#### 2. 后端只提供了signin，signin-callback路由，没有signin-github，那github上配置的路由是怎么回调回来呢？
[google-登录，微软文档](https://docs.microsoft.com/zh-cn/aspnet/core/security/authentication/social/google-logins?view=aspnetcore-3.1)，其中有一个**更改默认回调 URI**,通过 AddGitHub中的CallbackPath属性配置。

介绍了回调地址应配置signin-google,所以这里应该是signin-github，他是可以配置的，不需要自己写程序处理signin-google这个路由，内部有中间件已经处理了。

#### 3. 回调到signin-github后，后端怎么处理，才能让前端刷新。获取登录后的信息呢。

具体上面的根据code获取access_token，根据access_token获取用户的信息的过程，这些处理的过程，都不需要我们自己处理。我们可以用直接获取用户信息。

一个方法SignIn,只要**return Challenge(properties, provider);**，
- provider 为 GitHub，
- properties  var properties = new AuthenticationProperties { RedirectUri = url };

这个url为另一个获取用户信息的路由，只要拼接好地址即可。

```
var authenticateResult = await _contextAccessor.HttpContext.AuthenticateAsync(provider);
string email = authenticateResult.Principal.FindFirst(ClaimTypes.Email)?.Value;
string name = authenticateResult.Principal.FindFirst(ClaimTypes.Name)?.Value;
```
需要注入
```
private readonly IHttpContextAccessor _contextAccessor;
public AuthenticationController( IHttpContextAccessor contextAccessor)
{
    _contextAccessor = contextAccessor;
}
```
### 代码部署（简化）

打开NuGet包管理,安装包
```
Install-Package AspNet.Security.OAuth.GitHub
```

appSettings.json
```
"Authentication": {
    "GitHub": {
      "ClientId": "0be6b05fc717bfc4fb67",
      "ClientSecret": "dcaced9f176afba64e89d88b9b06ffc4a887a609"
    }
}
```

add扩展方法 

```
public static class JwtConfiguration
{
    public static void AddJwtConfiguration(this IServiceCollection services, IConfiguration configuration)
    {

        services.AddAuthentication(opts =>
            {
                opts.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
                opts.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            }).AddCookie(options =>
        {
            options.LoginPath = "/signin";
            options.LogoutPath = "/signout";
        }).AddGitHub(options =>
        {
            options.ClientId = configuration["Authentication:GitHub:ClientId"];
            options.ClientSecret = configuration["Authentication:GitHub:ClientSecret"];
        });
    }
}
```
默认情况下，如头像，email，是没有获取的。
```
.AddGitHub(options =>
{
    options.ClientId = configuration["Authentication:GitHub:ClientId"];
    options.ClientSecret = configuration["Authentication:GitHub:ClientSecret"];
    //options.CallbackPath = new PathString("~/signin-github");//与GitHub上的回调地址相同，默认即是/signin-github
    options.Scope.Add("user:email");
    //authenticateResult.Principal.FindFirst(LinConsts.Claims.AvatarUrl)?.Value;  得到GitHub头像
    options.ClaimActions.MapJsonKey(LinConsts.Claims.AvatarUrl, "avatar_url");
    options.ClaimActions.MapJsonKey(LinConsts.Claims.BIO, "bio");
    options.ClaimActions.MapJsonKey(LinConsts.Claims.BlogAddress, "blog");
});

#其中LinConsts类为静态常量
public static class LinConsts
{
    public static class Claims
    {
        public const string BIO = "urn:github:bio";
        public const string AvatarUrl = "urn:github:avatar_url";
        public const string BlogAddress = "urn:github:blog";
    }
}
```
startup.cs

ConfigureServices中配置此服务
```
    services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();
    services.AddJwtConfiguration(Configuration);
```

创建AuthenticationController.cs
增加SignIn，用于处理用户授权成功后，重定回signin-callback,并将参数带回。
```
        private readonly IHttpContextAccessor _contextAccessor;
        private readonly IConfiguration _configuration;

        public AuthenticationController(IHttpContextAccessor contextAccessor, IConfiguration configuration)
        {
            _contextAccessor = contextAccessor;
            _configuration = configuration;
        }
        
        [HttpGet("~/signin")]
        public async Task<IActionResult> SignIn(string provider, string redirectUrl)
        {
            var request = _contextAccessor.HttpContext.Request;
            var url =
                $"{request.Scheme}://{request.Host}{request.PathBase}{request.Path}-callback?provider={provider}&redirectUrl={redirectUrl}";
            var properties = new AuthenticationProperties { RedirectUri = url };
            properties.Items["LoginProviderKey"] = provider;
            return Challenge(properties, provider);

        }
```

在signin方法中，用户点击授权后（第一次），会根据其传递的URL，重定向到这个地址，signin-callback,参数也会一同携带。provider为GitHub,redirectUrl为：http://localhost:8081/login-result.
```
[HttpGet("~/signin-callback")]
public async Task<IActionResult> Home(string provider = null, string redirectUrl = "")
{
    var authenticateResult = await _contextAccessor.HttpContext.AuthenticateAsync(provider);
    if (!authenticateResult.Succeeded) return Redirect(redirectUrl);
    var openIdClaim = authenticateResult.Principal.FindFirst(ClaimTypes.NameIdentifier);
    if (openIdClaim == null || string.IsNullOrWhiteSpace(openIdClaim.Value))
        return Redirect(redirectUrl);

    //TODO 记录授权成功后的信息 

    string email = authenticateResult.Principal.FindFirst(ClaimTypes.Email)?.Value;
    string name = authenticateResult.Principal.FindFirst(ClaimTypes.Name)?.Value;
    string gitHubName = authenticateResult.Principal.FindFirst(GitHubAuthenticationConstants.Claims.Name)?.Value;
    string gitHubUrl = authenticateResult.Principal.FindFirst(GitHubAuthenticationConstants.Claims.Url)?.Value;
    //startup 中 AddGitHub配置项  options.ClaimActions.MapJsonKey(LinConsts.Claims.AvatarUrl, "avatar_url");
    string avatarUrl = authenticateResult.Principal.FindFirst(LinConsts.Claims.AvatarUrl)?.Value;

    return Redirect($"{redirectUrl}?openId={openIdClaim.Value}");
}
```

## 参考 
- [.net Core2.2 WebApi通过OAuth2.0实现微信登录](https://www.cnblogs.com/rsls/p/10522649.html)
- [AspNetCore3.0 和 JWT](https://blog.csdn.net/weixin_30414305/article/details/101389325)
- [用户系统设计：第三方授权、账号绑定及解绑（下）](http://www.woshipm.com/pd/509712.html)

## Demo  示例
- GitHub [https://github.com/luoyunchong/dotnetcore-examples/tree/master/dotnetcore3.1/VoVo.AspNetCore.OAuth2](https://github.com/luoyunchong/dotnetcore-examples/tree/master/dotnetcore3.1/VoVo.AspNetCore.OAuth2)
