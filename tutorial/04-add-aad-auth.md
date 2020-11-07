---
ms.openlocfilehash: 6e6c476b4ff0901f50d8e35a17f584d73b48b533
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822362"
---
<!-- markdownlint-disable MD002 MD041 -->

在本练习中，你将扩展上一练习中的应用程序，以支持 Azure AD 的身份验证。 若要获取所需的 OAuth 访问令牌以调用 Microsoft Graph API，这是必需的。 在此步骤中，将配置 " [Microsoft. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) 库"。

> [!IMPORTANT]
> 为了避免在源中存储应用程序 ID 和密码，您将使用 [.Net 密钥管理器](/aspnet/core/security/app-secrets) 来存储这些值。 机密管理器仅用于开发目的，而生产应用程序应使用受信任密钥管理器来存储机密信息。

1. 打开 **。/appsettings.js** 并将其内容替换为以下内容。

    :::code language="json" source="../demo/GraphTutorial/appsettings.json" highlight="2-6":::

1. 在 **GraphTutorial** 所在的目录中打开 CLI，并运行以下命令， `YOUR_APP_ID` 从 Azure 门户中的应用程序 ID 和 `YOUR_APP_SECRET` 应用程序机密中进行替换。

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a>实施登录

首先将 Microsoft Identity platform 服务添加到应用程序中。

1. 在 **./Graph** 目录中创建一个名为 **GraphConstants.cs** 的新文件，并添加以下代码。

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphConstants.cs" id="GraphConstantsSnippet":::

1. 打开 **./Startup.cs** 文件，并将以下 `using` 语句添加到文件顶部。

    ```csharp
    using Microsoft.AspNetCore.Authentication.OpenIdConnect;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc.Authorization;
    using Microsoft.Identity.Web;
    using Microsoft.Identity.Web.UI;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.Graph;
    using System.Net;
    using System.Net.Http.Headers;
    ```

1. 将现有的 `ConfigureServices` 函数替换为以下内容。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services
            // Use OpenId authentication
            .AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
            // Specify this is a web app and needs auth code flow
            .AddMicrosoftIdentityWebApp(Configuration)
            // Add ability to call web API (Graph)
            // and get access tokens
            .EnableTokenAcquisitionToCallDownstreamApi(options => {
                Configuration.Bind("AzureAd", options);
            }, GraphConstants.Scopes)
            // Use in-memory token cache
            // See https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization
            .AddInMemoryTokenCaches();

        // Require authentication
        services.AddControllersWithViews(options =>
        {
            var policy = new AuthorizationPolicyBuilder()
                .RequireAuthenticatedUser()
                .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
        })
        // Add the Microsoft Identity UI pages for signin/out
        .AddMicrosoftIdentityUI();
    }
    ```

1. 在 `Configure` 函数中，在行上方添加以下行 `app.UseAuthorization();` 。

    ```csharp
    app.UseAuthentication();
    ```

1. 打开 **/Controllers/HomeController.cs** ，并将其内容替换为以下内容。

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using System.Diagnostics;
    using System.Threading.Tasks;

    namespace GraphTutorial.Controllers
    {
        public class HomeController : Controller
        {
            ITokenAcquisition _tokenAcquisition;
            private readonly ILogger<HomeController> _logger;

            // Get the ITokenAcquisition interface via
            // dependency injection
            public HomeController(
                ITokenAcquisition tokenAcquisition,
                ILogger<HomeController> logger)
            {
                _tokenAcquisition = tokenAcquisition;
                _logger = logger;
            }

            public async Task<IActionResult> Index()
            {
                // TEMPORARY
                // Get the token and display it
                try
                {
                    string token = await _tokenAcquisition
                        .GetAccessTokenForUserAsync(GraphConstants.Scopes);
                    return View().WithInfo("Token acquired", token);
                }
                catch (MicrosoftIdentityWebChallengeUserException)
                {
                    return Challenge();
                }
            }

            public IActionResult Privacy()
            {
                return View();
            }

            [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
            public IActionResult Error()
            {
                return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
            }

            [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
            [AllowAnonymous]
            public IActionResult ErrorWithMessage(string message, string debug)
            {
                return View("Index").WithError(message, debug);
            }
        }
    }
    ```

1. 保存所做的更改并启动项目。 使用你的 Microsoft 帐户登录。

1. 检查同意提示。 权限列表对应于在 **/Graph/GraphConstants.cs** 中配置的权限范围列表。

    - **维护对你已向其授予** 访问权限的数据的访问权限： (`offline_access`) 此权限由 MSAL 请求，以便检索刷新令牌。
    - **登录并阅读您的配置文件：** (`User.Read`) 此权限允许应用获取已登录用户的配置文件和个人资料照片。
    - **读取邮箱设置：** (`MailboxSettings.Read`) 此权限允许应用读取用户的邮箱设置，包括时区和时间格式。
    - **拥有对日历的完全访问权限：** (`Calendars.ReadWrite`) 此权限允许应用读取用户日历上的事件、添加新事件以及修改现有事件。

    ![Microsoft identity platform 同意提示的屏幕截图](./images/add-aad-auth-03.png)

    有关同意的详细信息，请参阅 [了解 AZURE AD 应用程序同意体验](/azure/active-directory/develop/application-consent-experience)。

1. 同意请求的权限。 浏览器重定向到应用程序，并显示令牌。

### <a name="get-user-details"></a>获取用户详细信息

用户登录后，可以从 Microsoft Graph 获取其信息。

1. 打开 **/Graph/GraphClaimsPrincipalExtensions.cs** ，并将其全部内容替换为以下内容。

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphClaimsPrincipalExtensions.cs" id="GraphClaimsExtensionsSnippet":::

1. 打开 " **./Startup.cs** "，并 `.AddMicrosoftIdentityWebApp(Configuration)` 将现有行替换为以下代码。

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddSignInSnippet":::

    请考虑此代码执行的操作。

    - 它添加事件的事件处理程序 `OnTokenValidated` 。
        - 它使用 `ITokenAcquisition` 接口获取访问令牌。
        - 它调用 Microsoft Graph 以获取用户的配置文件和照片。
        - 它会将 Graph 信息添加到用户的标识中。

1. 在 `EnableTokenAcquisitionToCallDownstreamApi` 调用之后和调用之前，添加以下函数调用 `AddInMemoryTokenCaches` 。

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddGraphClientSnippet":::

    这将使经过身份验证的 **GraphServiceClient** 通过依赖关系注入提供给控制器。

1. 打开 **/Controllers/HomeController.cs** ，并将 `Index` 函数替换为以下函数。

    ```csharp
    public IActionResult Index()
    {
        return View();
    }
    ```

1. 删除对 `ITokenAcquisition` **HomeController** 类中的所有引用。

1. 保存所做的更改，启动应用程序，并执行登录过程。 您应该最后返回到主页，但 UI 应更改以指示您已登录。

    ![登录后主页的屏幕截图](./images/add-aad-auth-01.png)

1. 单击右上角的用户头像以访问 " **注销** " 链接。 单击 " **注销** " 重置会话并返回到主页。

    ![带有 "注销" 链接的下拉菜单的屏幕截图](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>存储和刷新令牌

此时，您的应用程序具有访问令牌，该令牌是在 `Authorization` API 调用的标头中发送的。 这是允许应用代表用户访问 Microsoft Graph 的令牌。

但是，此令牌的生存期较短。 令牌在发出后会过期一小时。 这就是刷新令牌变得有用的地方。 刷新令牌允许应用在不要求用户重新登录的情况下请求新的访问令牌。

由于应用程序使用的是 Microsoft. Web 库，因此您无需实现任何令牌存储或刷新逻辑。

应用程序使用内存中的令牌缓存，这对于在应用程序重新启动时不需要保留令牌的应用程序是足够的。 生产应用程序可以改为使用 Microsoft. Web 库中的 [分布式缓存选项](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) 。

该 `GetAccessTokenForUserAsync` 方法将为您处理令牌过期和刷新。 它首先检查缓存的令牌，如果它未过期，它将返回。 如果它已过期，则使用缓存的刷新令牌获取新的。

控制器通过依赖项注入获取的 **GraphServiceClient** 将预配置为使用的身份验证提供程序 `GetAccessTokenForUserAsync` 。
