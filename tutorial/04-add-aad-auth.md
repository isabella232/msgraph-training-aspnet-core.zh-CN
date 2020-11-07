---
ms.openlocfilehash: 6e6c476b4ff0901f50d8e35a17f584d73b48b533
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822362"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4ff45-101">在本练习中，你将扩展上一练习中的应用程序，以支持 Azure AD 的身份验证。</span><span class="sxs-lookup"><span data-stu-id="4ff45-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="4ff45-102">若要获取所需的 OAuth 访问令牌以调用 Microsoft Graph API，这是必需的。</span><span class="sxs-lookup"><span data-stu-id="4ff45-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="4ff45-103">在此步骤中，将配置 " [Microsoft. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) 库"。</span><span class="sxs-lookup"><span data-stu-id="4ff45-103">In this step you will configure the [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) library.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4ff45-104">为了避免在源中存储应用程序 ID 和密码，您将使用 [.Net 密钥管理器](/aspnet/core/security/app-secrets) 来存储这些值。</span><span class="sxs-lookup"><span data-stu-id="4ff45-104">To avoid storing the application ID and secret in source, you will use the [.NET Secret Manager](/aspnet/core/security/app-secrets) to store these values.</span></span> <span data-ttu-id="4ff45-105">机密管理器仅用于开发目的，而生产应用程序应使用受信任密钥管理器来存储机密信息。</span><span class="sxs-lookup"><span data-stu-id="4ff45-105">The Secret Manager is for development purposes only, production apps should use a trusted secret manager for storing secrets.</span></span>

1. <span data-ttu-id="4ff45-106">打开 **。/appsettings.js** 并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4ff45-106">Open **./appsettings.json** and replace its contents with the following.</span></span>

    :::code language="json" source="../demo/GraphTutorial/appsettings.json" highlight="2-6":::

1. <span data-ttu-id="4ff45-107">在 **GraphTutorial** 所在的目录中打开 CLI，并运行以下命令， `YOUR_APP_ID` 从 Azure 门户中的应用程序 ID 和 `YOUR_APP_SECRET` 应用程序机密中进行替换。</span><span class="sxs-lookup"><span data-stu-id="4ff45-107">Open your CLI in the directory where **GraphTutorial.csproj** is located, and run the following commands, substituting `YOUR_APP_ID` with your application ID from the Azure portal, and `YOUR_APP_SECRET` with your application secret.</span></span>

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="4ff45-108">实施登录</span><span class="sxs-lookup"><span data-stu-id="4ff45-108">Implement sign-in</span></span>

<span data-ttu-id="4ff45-109">首先将 Microsoft Identity platform 服务添加到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="4ff45-109">Start by adding the Microsoft Identity platform services to the application.</span></span>

1. <span data-ttu-id="4ff45-110">在 **./Graph** 目录中创建一个名为 **GraphConstants.cs** 的新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="4ff45-110">Create a new file named **GraphConstants.cs** in the **./Graph** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphConstants.cs" id="GraphConstantsSnippet":::

1. <span data-ttu-id="4ff45-111">打开 **./Startup.cs** 文件，并将以下 `using` 语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="4ff45-111">Open the **./Startup.cs** file and add the following `using` statements to the top of the file.</span></span>

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

1. <span data-ttu-id="4ff45-112">将现有的 `ConfigureServices` 函数替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4ff45-112">Replace the existing `ConfigureServices` function with the following.</span></span>

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

1. <span data-ttu-id="4ff45-113">在 `Configure` 函数中，在行上方添加以下行 `app.UseAuthorization();` 。</span><span class="sxs-lookup"><span data-stu-id="4ff45-113">In the `Configure` function, add the following line above the `app.UseAuthorization();` line.</span></span>

    ```csharp
    app.UseAuthentication();
    ```

1. <span data-ttu-id="4ff45-114">打开 **/Controllers/HomeController.cs** ，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4ff45-114">Open **./Controllers/HomeController.cs** and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="4ff45-115">保存所做的更改并启动项目。</span><span class="sxs-lookup"><span data-stu-id="4ff45-115">Save your changes and start the project.</span></span> <span data-ttu-id="4ff45-116">使用你的 Microsoft 帐户登录。</span><span class="sxs-lookup"><span data-stu-id="4ff45-116">Login with your Microsoft account.</span></span>

1. <span data-ttu-id="4ff45-117">检查同意提示。</span><span class="sxs-lookup"><span data-stu-id="4ff45-117">Examine the consent prompt.</span></span> <span data-ttu-id="4ff45-118">权限列表对应于在 **/Graph/GraphConstants.cs** 中配置的权限范围列表。</span><span class="sxs-lookup"><span data-stu-id="4ff45-118">The list of permissions correspond to list of permissions scopes configured in **./Graph/GraphConstants.cs**.</span></span>

    - <span data-ttu-id="4ff45-119">**维护对你已向其授予** 访问权限的数据的访问权限： (`offline_access`) 此权限由 MSAL 请求，以便检索刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="4ff45-119">**Maintain access to data you have given it access to:** (`offline_access`) This permission is requested by MSAL in order to retrieve refresh tokens.</span></span>
    - <span data-ttu-id="4ff45-120">**登录并阅读您的配置文件：** (`User.Read`) 此权限允许应用获取已登录用户的配置文件和个人资料照片。</span><span class="sxs-lookup"><span data-stu-id="4ff45-120">**Sign you in and read your profile:** (`User.Read`) This permission allows the app to get the logged-in user's profile and profile photo.</span></span>
    - <span data-ttu-id="4ff45-121">**读取邮箱设置：** (`MailboxSettings.Read`) 此权限允许应用读取用户的邮箱设置，包括时区和时间格式。</span><span class="sxs-lookup"><span data-stu-id="4ff45-121">**Read your mailbox settings:** (`MailboxSettings.Read`) This permission allows the app to read the user's mailbox settings, including time zone and time format.</span></span>
    - <span data-ttu-id="4ff45-122">**拥有对日历的完全访问权限：** (`Calendars.ReadWrite`) 此权限允许应用读取用户日历上的事件、添加新事件以及修改现有事件。</span><span class="sxs-lookup"><span data-stu-id="4ff45-122">**Have full access to your calendars:** (`Calendars.ReadWrite`) This permission allows the app to read events on the user's calendar, add new events, and modify existing ones.</span></span>

    ![Microsoft identity platform 同意提示的屏幕截图](./images/add-aad-auth-03.png)

    <span data-ttu-id="4ff45-124">有关同意的详细信息，请参阅 [了解 AZURE AD 应用程序同意体验](/azure/active-directory/develop/application-consent-experience)。</span><span class="sxs-lookup"><span data-stu-id="4ff45-124">For more information regarding consent, see [Understanding Azure AD application consent experiences](/azure/active-directory/develop/application-consent-experience).</span></span>

1. <span data-ttu-id="4ff45-125">同意请求的权限。</span><span class="sxs-lookup"><span data-stu-id="4ff45-125">Consent to the requested permissions.</span></span> <span data-ttu-id="4ff45-126">浏览器重定向到应用程序，并显示令牌。</span><span class="sxs-lookup"><span data-stu-id="4ff45-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="4ff45-127">获取用户详细信息</span><span class="sxs-lookup"><span data-stu-id="4ff45-127">Get user details</span></span>

<span data-ttu-id="4ff45-128">用户登录后，可以从 Microsoft Graph 获取其信息。</span><span class="sxs-lookup"><span data-stu-id="4ff45-128">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="4ff45-129">打开 **/Graph/GraphClaimsPrincipalExtensions.cs** ，并将其全部内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="4ff45-129">Open **./Graph/GraphClaimsPrincipalExtensions.cs** and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphClaimsPrincipalExtensions.cs" id="GraphClaimsExtensionsSnippet":::

1. <span data-ttu-id="4ff45-130">打开 " **./Startup.cs** "，并 `.AddMicrosoftIdentityWebApp(Configuration)` 将现有行替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="4ff45-130">Open **./Startup.cs** and replace the existing `.AddMicrosoftIdentityWebApp(Configuration)` line with the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddSignInSnippet":::

    <span data-ttu-id="4ff45-131">请考虑此代码执行的操作。</span><span class="sxs-lookup"><span data-stu-id="4ff45-131">Consider what this code does.</span></span>

    - <span data-ttu-id="4ff45-132">它添加事件的事件处理程序 `OnTokenValidated` 。</span><span class="sxs-lookup"><span data-stu-id="4ff45-132">It adds an event handler for the `OnTokenValidated` event.</span></span>
        - <span data-ttu-id="4ff45-133">它使用 `ITokenAcquisition` 接口获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="4ff45-133">It uses the `ITokenAcquisition` interface to get an access token.</span></span>
        - <span data-ttu-id="4ff45-134">它调用 Microsoft Graph 以获取用户的配置文件和照片。</span><span class="sxs-lookup"><span data-stu-id="4ff45-134">It calls Microsoft Graph to get the user's profile and photo.</span></span>
        - <span data-ttu-id="4ff45-135">它会将 Graph 信息添加到用户的标识中。</span><span class="sxs-lookup"><span data-stu-id="4ff45-135">It adds the Graph information to the user's identity.</span></span>

1. <span data-ttu-id="4ff45-136">在 `EnableTokenAcquisitionToCallDownstreamApi` 调用之后和调用之前，添加以下函数调用 `AddInMemoryTokenCaches` 。</span><span class="sxs-lookup"><span data-stu-id="4ff45-136">Add the following function call after the `EnableTokenAcquisitionToCallDownstreamApi` call and before the `AddInMemoryTokenCaches` call.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddGraphClientSnippet":::

    <span data-ttu-id="4ff45-137">这将使经过身份验证的 **GraphServiceClient** 通过依赖关系注入提供给控制器。</span><span class="sxs-lookup"><span data-stu-id="4ff45-137">This will make an authenticated **GraphServiceClient** available to controllers via dependency injection.</span></span>

1. <span data-ttu-id="4ff45-138">打开 **/Controllers/HomeController.cs** ，并将 `Index` 函数替换为以下函数。</span><span class="sxs-lookup"><span data-stu-id="4ff45-138">Open **./Controllers/HomeController.cs** and replace the `Index` function with the following.</span></span>

    ```csharp
    public IActionResult Index()
    {
        return View();
    }
    ```

1. <span data-ttu-id="4ff45-139">删除对 `ITokenAcquisition` **HomeController** 类中的所有引用。</span><span class="sxs-lookup"><span data-stu-id="4ff45-139">Remove all references to `ITokenAcquisition` in the **HomeController** class.</span></span>

1. <span data-ttu-id="4ff45-140">保存所做的更改，启动应用程序，并执行登录过程。</span><span class="sxs-lookup"><span data-stu-id="4ff45-140">Save your changes, start the app, and go through the sign-in process.</span></span> <span data-ttu-id="4ff45-141">您应该最后返回到主页，但 UI 应更改以指示您已登录。</span><span class="sxs-lookup"><span data-stu-id="4ff45-141">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![登录后主页的屏幕截图](./images/add-aad-auth-01.png)

1. <span data-ttu-id="4ff45-143">单击右上角的用户头像以访问 " **注销** " 链接。</span><span class="sxs-lookup"><span data-stu-id="4ff45-143">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="4ff45-144">单击 " **注销** " 重置会话并返回到主页。</span><span class="sxs-lookup"><span data-stu-id="4ff45-144">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![带有 "注销" 链接的下拉菜单的屏幕截图](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="4ff45-146">存储和刷新令牌</span><span class="sxs-lookup"><span data-stu-id="4ff45-146">Storing and refreshing tokens</span></span>

<span data-ttu-id="4ff45-147">此时，您的应用程序具有访问令牌，该令牌是在 `Authorization` API 调用的标头中发送的。</span><span class="sxs-lookup"><span data-stu-id="4ff45-147">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="4ff45-148">这是允许应用代表用户访问 Microsoft Graph 的令牌。</span><span class="sxs-lookup"><span data-stu-id="4ff45-148">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="4ff45-149">但是，此令牌的生存期较短。</span><span class="sxs-lookup"><span data-stu-id="4ff45-149">However, this token is short-lived.</span></span> <span data-ttu-id="4ff45-150">令牌在发出后会过期一小时。</span><span class="sxs-lookup"><span data-stu-id="4ff45-150">The token expires an hour after it is issued.</span></span> <span data-ttu-id="4ff45-151">这就是刷新令牌变得有用的地方。</span><span class="sxs-lookup"><span data-stu-id="4ff45-151">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="4ff45-152">刷新令牌允许应用在不要求用户重新登录的情况下请求新的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="4ff45-152">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="4ff45-153">由于应用程序使用的是 Microsoft. Web 库，因此您无需实现任何令牌存储或刷新逻辑。</span><span class="sxs-lookup"><span data-stu-id="4ff45-153">Because the app is using the Microsoft.Identity.Web library, you do not have to implement any token storage or refresh logic.</span></span>

<span data-ttu-id="4ff45-154">应用程序使用内存中的令牌缓存，这对于在应用程序重新启动时不需要保留令牌的应用程序是足够的。</span><span class="sxs-lookup"><span data-stu-id="4ff45-154">The app uses the in-memory token cache, which is sufficient for apps that do not need to persist tokens when the app restarts.</span></span> <span data-ttu-id="4ff45-155">生产应用程序可以改为使用 Microsoft. Web 库中的 [分布式缓存选项](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) 。</span><span class="sxs-lookup"><span data-stu-id="4ff45-155">Production apps may instead use the [distributed cache options](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) in the Microsoft.Identity.Web library.</span></span>

<span data-ttu-id="4ff45-156">该 `GetAccessTokenForUserAsync` 方法将为您处理令牌过期和刷新。</span><span class="sxs-lookup"><span data-stu-id="4ff45-156">The `GetAccessTokenForUserAsync` method handles token expiration and refresh for you.</span></span> <span data-ttu-id="4ff45-157">它首先检查缓存的令牌，如果它未过期，它将返回。</span><span class="sxs-lookup"><span data-stu-id="4ff45-157">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="4ff45-158">如果它已过期，则使用缓存的刷新令牌获取新的。</span><span class="sxs-lookup"><span data-stu-id="4ff45-158">If it is expired, it uses the cached refresh token to obtain a new one.</span></span>

<span data-ttu-id="4ff45-159">控制器通过依赖项注入获取的 **GraphServiceClient** 将预配置为使用的身份验证提供程序 `GetAccessTokenForUserAsync` 。</span><span class="sxs-lookup"><span data-stu-id="4ff45-159">The **GraphServiceClient** that controllers get via dependency injection will be pre-configured with an authentication provider that uses `GetAccessTokenForUserAsync` for you.</span></span>
