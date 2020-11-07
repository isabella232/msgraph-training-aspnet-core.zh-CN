---
ms.openlocfilehash: 308938efbedc4618c7b0ca3ea6b2eebc0582da10
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822367"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9b2ec-101">首先创建 ASP.NET Core web app。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-101">Start by creating an ASP.NET Core web app.</span></span>

1. <span data-ttu-id="9b2ec-102">在要创建项目的目录中打开命令行界面 (CLI) 。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="9b2ec-103">运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-103">Run the following command.</span></span>

    ```Shell
    dotnet new mvc -o GraphTutorial
    ```

1. <span data-ttu-id="9b2ec-104">创建项目后，通过将当前目录更改为 **GraphTutorial** 目录并在 CLI 中运行以下命令来验证它是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

1. <span data-ttu-id="9b2ec-105">打开浏览器并浏览到 `https://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-105">Open your browser and browse to `https://localhost:5001`.</span></span> <span data-ttu-id="9b2ec-106">如果一切正常，您应该会看到一个默认的 ASP.NET Core 页面。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-106">If everything is working, you should see a default ASP.NET Core page.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9b2ec-107">如果您收到一条警告，指出 **localhost** 的证书不受信任，可以使用 .NET Core CLI 安装和信任开发证书。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-107">If you receive a warning that the certificate for **localhost** is un-trusted you can use the .NET Core CLI to install and trust the development certificate.</span></span> <span data-ttu-id="9b2ec-108">有关特定操作系统的说明，请参阅 [在 ASP.NET Core 中强制 HTTPS](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) 。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-108">See [Enforce HTTPS in ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.1) for instructions for specific operating systems.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="9b2ec-109">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="9b2ec-109">Add NuGet packages</span></span>

<span data-ttu-id="9b2ec-110">在继续之前，请安装稍后将使用的一些其他 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-110">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="9b2ec-111">用于请求和管理访问令牌的[Microsoft Web 标识。](https://www.nuget.org/packages/Microsoft.Identity.Web/)</span><span class="sxs-lookup"><span data-stu-id="9b2ec-111">[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="9b2ec-112">通过依赖关系注入添加 Microsoft Graph SDK 的[MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) 。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-112">[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) for adding the Microsoft Graph SDK via dependency injection.</span></span>
- <span data-ttu-id="9b2ec-113">用于登录和注销 UI 的[Microsoft. WEB 界面](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/)。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-113">[Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) for sign-in and sign-out UI.</span></span>
- <span data-ttu-id="9b2ec-114">用于调用 Microsoft Graph 的[microsoft](https://www.nuget.org/packages/Microsoft.Graph/) graph。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-114">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="9b2ec-115">用于处理时区标识符跨平台的[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) 。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-115">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for handling time zoned identifiers cross-platform.</span></span>

1. <span data-ttu-id="9b2ec-116">在 CLI 中运行以下命令来安装依赖项。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-116">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.1.0
    dotnet add package Microsoft.Identity.MicrosoftGraph --version 1.1.0
    dotnet add package Microsoft.Identity.Web.UI --version 1.1.0
    dotnet add package Microsoft.Graph --version 3.18.0
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a><span data-ttu-id="9b2ec-117">设计应用程序</span><span class="sxs-lookup"><span data-stu-id="9b2ec-117">Design the app</span></span>

<span data-ttu-id="9b2ec-118">在本节中，您将创建应用程序的基本 UI 结构。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-118">In this section you will create the basic UI structure of the application.</span></span>

### <a name="implement-alert-extension-methods"></a><span data-ttu-id="9b2ec-119">实现警报扩展方法</span><span class="sxs-lookup"><span data-stu-id="9b2ec-119">Implement alert extension methods</span></span>

<span data-ttu-id="9b2ec-120">在本节中，您将为 `IActionResult` 控制器视图返回的类型创建扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-120">In this section you will create extension methods for the `IActionResult` type returned by controller views.</span></span> <span data-ttu-id="9b2ec-121">此扩展将启用将临时错误或成功消息传递给视图。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-121">This extension will enable passing temporary error or success messages to the view.</span></span>

> [!TIP]
> <span data-ttu-id="9b2ec-122">可以使用任何文本编辑器编辑本教程的源文件。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-122">You can use any text editor to edit the source files for this tutorial.</span></span> <span data-ttu-id="9b2ec-123">但是， [Visual Studio Code](https://code.visualstudio.com/) 提供了其他功能，如调试和智能感知。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-123">However, [Visual Studio Code](https://code.visualstudio.com/) provides additional features, such as debugging and Intellisense.</span></span>

1. <span data-ttu-id="9b2ec-124">在名为 " **警报** " 的 **GraphTutorial** 目录中创建一个新目录。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-124">Create a new directory in the **GraphTutorial** directory named **Alerts**.</span></span>

1. <span data-ttu-id="9b2ec-125">在 **./Alerts** 目录中创建一个名为 **WithAlertResult.cs** 的新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-125">Create a new file named **WithAlertResult.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/WithAlertResult.cs" id="WithAlertResultSnippet":::

1. <span data-ttu-id="9b2ec-126">在 **./Alerts** 目录中创建一个名为 **AlertExtensions.cs** 的新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-126">Create a new file named **AlertExtensions.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/AlertExtensions.cs" id="AlertExtensionsSnippet":::

### <a name="implement-user-data-extension-methods"></a><span data-ttu-id="9b2ec-127">实现用户数据扩展方法</span><span class="sxs-lookup"><span data-stu-id="9b2ec-127">Implement user data extension methods</span></span>

<span data-ttu-id="9b2ec-128">在本节中，您将为 `ClaimsPrincipal` Microsoft 标识平台生成的对象创建扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-128">In this section you will create extension methods for the `ClaimsPrincipal` object generated by the Microsoft Identity platform.</span></span> <span data-ttu-id="9b2ec-129">这将允许您使用 Microsoft Graph 中的数据扩展现有的用户标识。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-129">This will allow you to extend the existing user identity with data from Microsoft Graph.</span></span>

> [!NOTE]
> <span data-ttu-id="9b2ec-130">此代码仅为占位符。现在，你将在后续部分中完成此代码。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-130">This code is just a placeholder for now, you will complete it in a later section.</span></span>

1. <span data-ttu-id="9b2ec-131">在名为 **Graph** 的 **GraphTutorial** 目录中创建一个新目录。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-131">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>

1. <span data-ttu-id="9b2ec-132">创建一个名为 **GraphClaimsPrincipalExtensions.cs** 的新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-132">Create a new file named **GraphClaimsPrincipalExtensions.cs** and add the following code.</span></span>

    ```csharp
    using System.Security.Claims;

    namespace GraphTutorial
    {
        public static class GraphClaimTypes {
            public const string DisplayName ="graph_name";
            public const string Email = "graph_email";
            public const string Photo = "graph_photo";
            public const string TimeZone = "graph_timezone";
            public const string DateTimeFormat = "graph_datetimeformat";
        }

        // Helper methods to access Graph user data stored in
        // the claims principal
        public static class GraphClaimsPrincipalExtensions
        {
            public static string GetUserGraphDisplayName(this ClaimsPrincipal claimsPrincipal)
            {
                return "Adele Vance";
            }

            public static string GetUserGraphEmail(this ClaimsPrincipal claimsPrincipal)
            {
                return "adelev@contoso.com";
            }

            public static string GetUserGraphPhoto(this ClaimsPrincipal claimsPrincipal)
            {
                return "/img/no-profile-photo.png";
            }
        }
    }
    ```

### <a name="create-views"></a><span data-ttu-id="9b2ec-133">创建视图</span><span class="sxs-lookup"><span data-stu-id="9b2ec-133">Create views</span></span>

<span data-ttu-id="9b2ec-134">在本节中，您将实现应用程序的 Razor 视图。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-134">In this section you will implement the Razor views for the application.</span></span>

1. <span data-ttu-id="9b2ec-135">在 **/Views/Shared** 目录中添加一个名为 **_LoginPartial** 的新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-135">Add a new file named **_LoginPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_LoginPartial.cshtml" id="LoginPartialSnippet":::

1. <span data-ttu-id="9b2ec-136">在 **/Views/Shared** 目录中添加一个名为 **_AlertPartial** 的新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-136">Add a new file named **_AlertPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_AlertPartial.cshtml" id="AlertPartialSnippet":::

1. <span data-ttu-id="9b2ec-137">打开 **./Views/Shared/_Layout cshtml** 文件，并将其全部内容替换为以下代码，以更新应用程序的全局布局。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-137">Open the **./Views/Shared/_Layout.cshtml** file, and replace its entire contents with the following code to update the global layout of the app.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_Layout.cshtml" id="LayoutSnippet":::

1. <span data-ttu-id="9b2ec-138">打开 **/wwwroot/css/site.css** ，并将以下代码添加到文件底部。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-138">Open **./wwwroot/css/site.css** and add the following code at the bottom of the file.</span></span>

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. <span data-ttu-id="9b2ec-139">打开 " **./Views/Home/index.cshtml** " 文件，并将其内容替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-139">Open the **./Views/Home/index.cshtml** file and replace its contents with the following.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Home/Index.cshtml" id="HomeIndexSnippet":::

1. <span data-ttu-id="9b2ec-140">在 **/wwwroot** 目录中创建一个名为 **img** 的新目录。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-140">Create a new directory in the **./wwwroot** directory named **img**.</span></span> <span data-ttu-id="9b2ec-141">在此目录中添加你选择命名 **no-profile-photo.png** 的图像文件。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-141">Add an image file of your choosing named **no-profile-photo.png** in this directory.</span></span> <span data-ttu-id="9b2ec-142">当用户在 Microsoft Graph 中没有照片时，此图像将用作用户的照片。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-142">This image will be used as the user's photo when the user has no photo in Microsoft Graph.</span></span>

    > [!TIP]
    > <span data-ttu-id="9b2ec-143">您可以从 [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png)下载这些屏幕截图中使用的图像。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-143">You can download the image used in these screenshots from [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png).</span></span>

1. <span data-ttu-id="9b2ec-144">保存所有更改，然后重新启动服务器 (`dotnet run`) 。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-144">Save all of your changes and restart the server (`dotnet run`).</span></span> <span data-ttu-id="9b2ec-145">现在，应用程序看起来应非常不同。</span><span class="sxs-lookup"><span data-stu-id="9b2ec-145">Now, the app should look very different.</span></span>

    ![重新设计的主页的屏幕截图](./images/create-app-01.png)
