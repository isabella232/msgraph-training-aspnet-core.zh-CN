---
ms.openlocfilehash: ed1bb3d97791ecfdd3b63a1bb4a1dfd63f2f03bf
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822383"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="76f8e-101">本教程向您介绍如何构建使用 Microsoft Graph API 检索用户的日历信息的 ASP.NET Core web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="76f8e-101">This tutorial teaches you how to build an ASP.NET Core web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="76f8e-102">如果您只想下载已完成的教程，可以下载或克隆 [GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-aspnet-core)。</span><span class="sxs-lookup"><span data-stu-id="76f8e-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnet-core).</span></span> <span data-ttu-id="76f8e-103">有关使用应用 ID 和密码配置应用程序的说明，请参阅 **demo** 文件夹中的自述文件。</span><span class="sxs-lookup"><span data-stu-id="76f8e-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="76f8e-104">必备条件</span><span class="sxs-lookup"><span data-stu-id="76f8e-104">Prerequisites</span></span>

<span data-ttu-id="76f8e-105">在开始本教程之前，您的开发计算机上应安装 [.Net CORE SDK](https://dotnet.microsoft.com/download) 。</span><span class="sxs-lookup"><span data-stu-id="76f8e-105">Before you start this tutorial, you should have the [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="76f8e-106">如果没有 SDK，请访问 "下载选项" 的上一个链接。</span><span class="sxs-lookup"><span data-stu-id="76f8e-106">If you do not have the SDK, visit the previous link for download options.</span></span>

<span data-ttu-id="76f8e-107">您还应具有一个个人的 Microsoft 帐户，其中包含 Outlook.com 上的邮箱，或 Microsoft 工作或学校帐户。</span><span class="sxs-lookup"><span data-stu-id="76f8e-107">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="76f8e-108">如果你没有 Microsoft 帐户，可以使用以下几种方法获取免费帐户：</span><span class="sxs-lookup"><span data-stu-id="76f8e-108">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="76f8e-109">你可以 [注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。</span><span class="sxs-lookup"><span data-stu-id="76f8e-109">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="76f8e-110">你可以 [注册 office 365 开发人员计划](https://developer.microsoft.com/office/dev-program) 以获取免费的 office 365 订阅。</span><span class="sxs-lookup"><span data-stu-id="76f8e-110">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="76f8e-111">本教程是使用 .NET Core SDK 版本3.1.201 编写的。</span><span class="sxs-lookup"><span data-stu-id="76f8e-111">This tutorial was written with .NET Core SDK version 3.1.201.</span></span> <span data-ttu-id="76f8e-112">本指南中的步骤可能适用于其他版本，但尚未经过测试。</span><span class="sxs-lookup"><span data-stu-id="76f8e-112">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="76f8e-113">反馈</span><span class="sxs-lookup"><span data-stu-id="76f8e-113">Feedback</span></span>

<span data-ttu-id="76f8e-114">请在 [GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-aspnet-core)中提供有关本教程的任何反馈。</span><span class="sxs-lookup"><span data-stu-id="76f8e-114">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnet-core).</span></span>
