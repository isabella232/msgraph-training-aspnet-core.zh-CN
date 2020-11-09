---
ms.openlocfilehash: f70714a0bc2588fc67d63096b4ab746380bb8e2c
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822295"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e2af0-101">在本节中，您将添加在用户日历上创建事件的功能。</span><span class="sxs-lookup"><span data-stu-id="e2af0-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-model"></a><span data-ttu-id="e2af0-102">创建模型</span><span class="sxs-lookup"><span data-stu-id="e2af0-102">Create model</span></span>

1. <span data-ttu-id="e2af0-103">在 **./Models** 目录中创建一个名为 **NewEvent.cs** 的新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="e2af0-103">Create a new file named **NewEvent.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

## <a name="create-view"></a><span data-ttu-id="e2af0-104">创建视图</span><span class="sxs-lookup"><span data-stu-id="e2af0-104">Create view</span></span>

1. <span data-ttu-id="e2af0-105">在 **/Views/Calendar** 目录中创建一个名为 " **new.** ." 的新文件，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="e2af0-105">Create a new file named **New.cshtml** in he **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/New.cshtml" id="NewFormSnippet":::

## <a name="add-controller-actions"></a><span data-ttu-id="e2af0-106">添加控制器操作</span><span class="sxs-lookup"><span data-stu-id="e2af0-106">Add controller actions</span></span>

1. <span data-ttu-id="e2af0-107">打开 **/Controllers/CalendarController.cs** ，并将以下操作添加到 `CalendarController` 类以呈现新的事件窗体。</span><span class="sxs-lookup"><span data-stu-id="e2af0-107">Open **./Controllers/CalendarController.cs** and add the following action to the `CalendarController` class to render the new event form.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewGetSnippet":::

1. <span data-ttu-id="e2af0-108">将以下操作添加到类中， `CalendarController` 以便在用户单击 " **保存** " 并使用 Microsoft Graph 将事件添加到用户的日历中时从表单接收新事件。</span><span class="sxs-lookup"><span data-stu-id="e2af0-108">Add the following action to the `CalendarController` class to receive the new event from the form when the user clicks **Save** and use Microsoft Graph to add the event to the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewPostSnippet":::

1. <span data-ttu-id="e2af0-109">启动应用程序，登录，然后单击 " **日历** " 链接。</span><span class="sxs-lookup"><span data-stu-id="e2af0-109">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="e2af0-110">单击 " **新建事件** " 按钮，填写窗体，然后单击 " **保存** "。</span><span class="sxs-lookup"><span data-stu-id="e2af0-110">Click the **New event** button, fill in the form, and click **Save**.</span></span>

    ![新事件表单的屏幕截图](./images/create-event-01.png)
