---                
layout: post            
title: "Windows Authentication in Website"                
date:   2022-4-29 10:30:00                 
categories: "Web"                
catalog: true                
tags:                 
    - Web                
---      

# NTLM
Windows New Technology LAN Manager (NTLM) 是 Microsoft 提供的一套安全协议，用于验证用户身份并保护其活动的完整性和机密性。NTLM 的核心是一个单点登录 (SSO)工具，它依靠质询-响应协议来确认用户，而无需他们提交密码。   
NTLM 凭据基于在交互式登录过程中获取的数据，由域名、用户名和用户密码的单向哈希组成。 NTLM使用加密的质询/响应协议对用户进行身份验证，而不会通过网络发送用户密码。 请求身份验证的系统必须执行一项计算来证明它有权访问受保护的NTLM凭据。  
NTLM 非交互身份验证的步骤：  
1. (交互式身份验证仅) 用户访问客户端计算机并提供域名、用户名和密码。 客户端计算密码的加密哈希 ，并丢弃实际密码。  
2. 客户端以 纯文本将用户名发送到服务器。  
3. 服务器生成一个 8 字节随机数，称为 质询 或 nonce，并将其发送到客户端。  
4. 客户端使用用户密码的哈希加密此质询，并将结果返回到服务器。 这称为 响应。  
5. 服务器将以下三项发送到域控制器：  

        用户名
        发送到客户端的质询
        从客户端接收的响应

6. 域控制器使用用户名从安全帐户管理器数据库中检索用户密码的哈希。 它使用此密码哈希来加密质询。  
7. 域控制器将步骤 6 中计算的加密质询与步骤 4 中客户端计算的响应进行比较。 如果相同，身份验证会成功。  
   
尽管 Microsoft 仍支持 NTLM，但它已被 Kerberos 取代，成为 Windows 2000 和后续 Active Directory (AD) 域中的默认身份验证协议。  
# Kerberos
Kerberos 是由麻省理工学院 (MIT) 的研究人员在 1980 年代开发的。名字来源于希腊神话人物Kerberos，守护冥界的三头犬。  
Kerberos 协议定义客户端如何与网络身份验证服务交互。 客户端从 Kerberos 密钥分发中心 (KDC) 获取票证，并在建立连接时向服务器出示这些票证。 Kerberos 票证表示客户端的网络 凭据。
Kerberos 身份验证的十二步流程：  

1. 用户与客户端共享其用户名、密码和域名。  
2. 客户端组装一个包（或身份验证器），其中包含有关客户端的所有相关信息，包括用户名、日期和时间。除用户名外，验证器中包含的所有信息都使用用户密码进行加密。  
3. 客户端将加密的身份验证器发送到 KDC(票务服务或密钥分发中心)。  
4. KDC 检查用户名以确定客户端的身份。KDC 然后检查 AD 数据库中的用户密码。然后它会尝试使用密码解密身份验证器。如果 KDC 能够解密验证器，则验证客户端的身份。  
5. 一旦验证了客户端的身份，KDC 就会创建一个票证或会话密钥，它也会被加密并发送给客户端。  
6. 票证或会话密钥存储在客户端的 Kerberos 托盘中；票证可用于在设定的时间段内访问服务器，通常为 8 小时。  
7. 如果客户端需要访问另一台服务器，它会将原始票证连同访问新资源的请求一起发送到 KDC。  
8. KDC 使用其密钥解密票证。（客户端不需要对用户进行身份验证，因为 KDC 可以使用票证来验证用户的身份之前已被确认）。  
9. KDC 为客户端生成更新的票证或会话密钥以访问新的共享资源。此票证也由服务器的密钥加密。然后 KDC 将此票证发送给客户端。  
10. 客户端将此新会话密钥保存在其 Kerberos 托盘中，并将副本发送到服务器。  
11. 服务器使用自己的密码来解密票证。  
12. 如果服务器成功解密会话密钥，则票证是合法的。然后，服务器将打开票证并查看访问控制列表 (ACL) 以确定客户端是否具有访问资源的必要权限。  

# NTLM VS Kerberos
NTLM 和 Kerberos 之间的主要区别在于这两种协议如何管理身份验证。NTLM 依赖于客户端和服务器之间的三次握手来对用户进行身份验证。Kerberos 使用一个由两部分组成的过程，该过程利用票证授予服务或密钥分发中心。  
另一个主要区别是密码是散列还是加密。NTLM 依赖于密码散列，这是一种基于输入文件生成文本字符串的单向函数；Kerberos 利用加密，这是一种双向功能，分别使用加密密钥和解密密钥对信息进行加扰和解锁。  
为什么 NTLM 被 Kerberos 取代？  
1. 单一身份验证。NTLM 是一种单一的身份验证方法。它依靠质询-响应协议来建立用户。它不支持多因素身份验证 (MFA)，即使用两条或更多条信息来确认用户身份的过程.  
2. NTLM 存在几个与密码散列和加盐相关的已知安全漏洞。在 NTLM 中，存储在服务器和域控制器上的密码不是“加盐”的——这意味着不会将随机字符串添加到散列密码中，以进一步保护其免受破解技术的侵害。这意味着拥有密码哈希的攻击者不需要底层密码来验证会话。因此，系统很容易受到暴力攻击，即攻击者试图通过多次登录尝试破解密码。如果用户选择了弱密码或普通密码，他们特别容易受到这种策略的影响。  
3. 过时的密码学。NTLM 没有利用算法思维或加密的最新进展来使密码更安全。    

# 创建一个网站
首先用.net core web api + Angular生成一个简单的网站。
1. Visual Studio 2022 新建 .net core web api 工程，Framework选.net 5.0
2. 直接运行工程F5，在swagger测试页面可以获取天气信息
   ![img](https://github.com/kerwenzhang/kerwenzhang.github.io/blob/master/_posts/image/Auth1.png?raw=true)
3. 新建Angular工程  
   
        ng new Client  

4. 添加request代码  
   app.module.ts里添加HttpClientModule引用  

        import { HttpClientModule } from '@angular/common/http';
        imports: [
            HttpClientModule,
            BrowserModule
        ],

    app.component.ts里调用http get请求数据  

        export class AppComponent {
            public values: Observable<any>|undefined; 
            title = 'Client';
            constructor(private httpClient:HttpClient){    
            }

            ngOnInit(): void {
                this.getData();    
            }

            getData()
            {
                this.values = this.httpClient.get<string>("https://localhost:44371/WeatherForecast");
            }
        }
    
    app.component.html中显示天气  

        Get weather information from server!

        <div *ngFor="let value of values | async">
            { {value.date} }
            <br>
            { {value.temperatureC} }
            <br>
            { {value.temperatureF} }
            <br>
            { {value.summary} }
        </div>


5. 运行Client端
   
        npm run start

    如果遇到端口4200被占用，修改package.json中的默认端口

        "start": "ng serve -o --port 4201",

6. 打开浏览器之后没有天气数据返回，F12 console里会有如下输出：

        Access to XMLHttpRequest at 'https://localhost:44371/WeatherForecast' from origin 'http://localhost:4201' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.

7. 在Server端添加CORS
   
        private readonly string MyAllowSpecificOrigins = "_MyOriginPolicy";
        services.AddCors(options =>
        {
            options.AddPolicy(MyAllowSpecificOrigins, builder =>
            {
                builder.WithOrigins("http://localhost:4201").AllowCredentials();
            });
        });
        app.UseCors(MyAllowSpecificOrigins);

8. 重新run Server端，刷新Client，能获取到数据了。
![img](https://github.com/kerwenzhang/kerwenzhang.github.io/blob/master/_posts/image/Auth2.png?raw=true)

至此一个简单的.net core web api + Angular client就搭建完成了，api没有集成任何认证，任何人的请求都会被响应。  

# 集成windows认证
## Server端
1. 新建web.config，添加`forwardWindowsAuthToken="true"`配置windows认证  
   
        <system.webServer>
            <handlers>
                <remove name="aspNetCore" />
                <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
            </handlers>
            <aspNetCore processPath="%LAUNCHER_PATH%" arguments="%LAUNCHER_ARGS%" stdoutLogEnabled="false" stdoutLogFile=".\logs\stdout" forwardWindowsAuthToken="true" hostingModel="InProcess" />
        </system.webServer>
   
2. 在`WeatherForecastController`中添加`Authorize`头  
   
        [Authorize]
        [ApiController]
        [Route("[controller]")]
        public class WeatherForecastController : ControllerBase
3. 安装新的nuget package：  
   
        Microsoft.AspNetCore.Server.IISIntegration

4. 配置startup  
   
        services.AddAuthentication(IISDefaults.AuthenticationScheme);
    
5. 工程属性-debug里将匿名登录去掉，勾选windows认证。如果将server托管到IIS上，则需要配置IIS。  
   ![img](https://github.com/kerwenzhang/kerwenzhang.github.io/blob/master/_posts/image/Auth7.png?raw=true)
6. F5 重新起服务，通过swagger测试，发现还是可以拿到天气数据，是我们的windows配置没有生效？  
7. 我们可以将request的用户名打印出来    
   WeatherForecastController.cs 中  

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            Console.WriteLine("----------------" + HttpContext.User.Identity.Name);
            ...
        }

8. 重新请求数据，在console里能打印出我们当前window的用户名  
![img](https://github.com/kerwenzhang/kerwenzhang.github.io/blob/master/_posts/image/Auth3.png?raw=true)
猜测是swagger在请求数据的时候已经把Authentication加进去了。  
8. 我们换成Postman再次请求数据,请求失败了返回401未授权。   
   ![img](https://github.com/kerwenzhang/kerwenzhang.github.io/blob/master/_posts/image/Auth4.png?raw=true)
9. 我们修改一下Postman请求的头，添加Authentication，输入一个本地的用户名和密码，这里我没有用当前登录的用户。发现可以返回数据。  
     ![img](https://github.com/kerwenzhang/kerwenzhang.github.io/blob/master/_posts/image/Auth6.png?raw=true)
10. 查看server console，打印出来的就是我们输入的用户名。  
    ![img](https://github.com/kerwenzhang/kerwenzhang.github.io/blob/master/_posts/image/Auth5.png?raw=true)

Server端的配置到这就结束了。  

## Client端
1. 修改`app.component.ts`，在请求里夹带windows账户信息  
   
        getData()
        {
            this.values = this.httpClient.get<string>("https://localhost:44371/WeatherForecast",{withCredentials:true});
        }
    
2. 重新启动client端，能获取到天气数据，server console里打印出当前登录用户名  
   
实验到这里就结束了，但我还是有个疑问。 Client端其实是自动把当前登录的账户信息发给Server端了，整个认证的过程用户是感觉不到的。那有没有可能像在Postman里做的那样，输入另外一个windows的账号和密码呢？
有没有可能让浏览器自动弹出这样的登录窗口呢？留着以后再研究吧。。。  
![img](https://github.com/kerwenzhang/kerwenzhang.github.io/blob/master/_posts/image/Auth8.png?raw=true)
   
[NTLM EXPLAINED](https://www.crowdstrike.com/cybersecurity-101/ntlm-windows-new-technology-lan-manager/#:~:text=Windows%20New%20Technology%20LAN%20Manager,and%20confidentiality%20of%20their%20activity.)  
[Windows Authentication](https://docs.microsoft.com/en-us/iis/configuration/system.webserver/security/authentication/windowsauthentication/)   
[Authentication with Angular](https://newspark.nl/authentication-with-angular/)  
[Windows Authentication with .NET Core API and Angular on IIS](https://lukelindner.medium.com/windows-authentication-with-net-core-api-and-angular-project-on-iis-ae16a573902e)  
[Angular 使用 Windows 验证](https://blog.kevinyang.net/2019/04/11/ng-windows-auth/)  
[在 ASP.NET Core 中配置 Windows 身份验证](https://docs.microsoft.com/zh-cn/aspnet/core/security/authentication/windowsauth?view=aspnetcore-6.0&tabs=netcore-cli)  
