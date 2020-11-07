---
ms.openlocfilehash: ed1bb3d97791ecfdd3b63a1bb4a1dfd63f2f03bf
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822383"
---
<!-- markdownlint-disable MD002 MD041 -->

本教程向您介绍如何构建使用 Microsoft Graph API 检索用户的日历信息的 ASP.NET Core web 应用程序。

> [!TIP]
> 如果您只想下载已完成的教程，可以下载或克隆 [GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-aspnet-core)。 有关使用应用 ID 和密码配置应用程序的说明，请参阅 **demo** 文件夹中的自述文件。

## <a name="prerequisites"></a>必备条件

在开始本教程之前，您的开发计算机上应安装 [.Net CORE SDK](https://dotnet.microsoft.com/download) 。 如果没有 SDK，请访问 "下载选项" 的上一个链接。

您还应具有一个个人的 Microsoft 帐户，其中包含 Outlook.com 上的邮箱，或 Microsoft 工作或学校帐户。 如果你没有 Microsoft 帐户，可以使用以下几种方法获取免费帐户：

- 你可以 [注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- 你可以 [注册 office 365 开发人员计划](https://developer.microsoft.com/office/dev-program) 以获取免费的 office 365 订阅。

> [!NOTE]
> 本教程是使用 .NET Core SDK 版本3.1.201 编写的。 本指南中的步骤可能适用于其他版本，但尚未经过测试。

## <a name="feedback"></a>反馈

请在 [GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-aspnet-core)中提供有关本教程的任何反馈。
