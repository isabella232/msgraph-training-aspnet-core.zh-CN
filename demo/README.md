---
ms.openlocfilehash: 30398bafbf47aaa374a200dd1834c9f2003e967f
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822309"
---
# <a name="how-to-run-the-completed-project"></a>如何运行已完成的项目

## <a name="prerequisites"></a>必备条件

若要在此文件夹中运行已完成的项目，您需要以下各项：

- 在开发计算机上安装的 [.Net CORE SDK](https://dotnet.microsoft.com/download) 。  ( **注意：** 本教程是使用 .NET Core SDK 版本3.1.201 编写的。 本指南中的步骤可能适用于其他版本，但尚未经过测试。 ) 
- 使用 Outlook.com 上的邮箱的个人 Microsoft 帐户，或者是 Microsoft 工作或学校帐户。

如果你没有 Microsoft 帐户，可以使用以下几种方法获取免费帐户：

- 你可以 [注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- 你可以 [注册 office 365 开发人员计划](https://developer.microsoft.com/office/dev-program) 以获取免费的 office 365 订阅。

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>向 Azure Active Directory 管理中心注册 web 应用程序

1. 打开浏览器，并转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。 使用 **个人帐户** （亦称为“Microsoft 帐户”）或 **工作或学校帐户** 登录。

1. 选择左侧导航栏中的“ **Azure Active Directory** ”，再选择“ **管理** ”下的“ **应用注册** ”。

    ![应用注册的屏幕截图 ](../tutorial/images/aad-portal-app-registrations.png)

1. 选择“新注册”。 在“注册应用”页上，按如下方式设置值。

    - 将“名称”设置为“`ASP.NET Core Graph Tutorial`”。
    - 将“受支持的帐户类型”设置为“任何组织目录中的帐户和个人 Microsoft 帐户”。
    - 在“重定向 URI”下，将第一个下拉列表设置为“`Web`”，并将值设置为“`https://localhost:5001/`”。

    !["注册应用程序" 页的屏幕截图](../tutorial/images/aad-register-an-app.png)

1. 选择“ **注册** ”。 在 **ASP.NET Core Graph 教程** 页面上，将应用程序的值复制 **(客户端) ID** 并保存它，在下一步中将需要它。

    ![新应用注册的应用程序 ID 的屏幕截图](../tutorial/images/aad-application-id.png)

1. 选择“管理”下的“身份验证”。 在 " **重定向 uri** " 下，添加值为的 URI `https://localhost:5001/signin-oidc` 。

1. 将 **注销 URL** 设置为 `https://localhost:5001/signout-oidc` 。

1. 找到“隐式授予”部分，并启用“ID 令牌”。 选择“ **保存** ”。

    ![Azure 门户中的 Web 平台设置的屏幕截图](../tutorial/images/aad-web-platform.png)

1. 选择“管理”下的“证书和密码”。 选择“新客户端密码”按钮。 在 " **说明** " 中输入一个值，然后选择 " **过期** " 选项之一，然后选择 " **添加** "。

    !["添加客户端密码" 对话框的屏幕截图](../tutorial/images/aad-new-client-secret.png)

1. 离开此页前，先复制客户端密码值。 将在下一步中用到它。

    > [!IMPORTANT]
    > 此客户端密码不会再次显示，所以请务必现在就复制它。

    ![新添加的客户端密码的屏幕截图](../tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a>配置示例

1. 在 **GraphTutorial** 的目录中打开命令行界面 (CLI) ，并运行以下命令， `YOUR_APP_ID` 从 Azure 门户中的应用程序 ID 和 `YOUR_APP_SECRET` 应用程序机密中进行替换。

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="run-the-sample"></a>运行示例

在 CLI 中，运行以下命令以启动应用程序。

```Shell
dotnet run
```
