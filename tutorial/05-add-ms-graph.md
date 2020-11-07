---
ms.openlocfilehash: 17394dd6283464eabcbea1f60c48640412b55431
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822379"
---
<!-- markdownlint-disable MD002 MD041 -->

在本节中，将向应用程序中加入 Microsoft Graph。 对于此应用程序，您将使用 [Microsoft Graph 客户端库进行 .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 以调用 microsoft graph。

## <a name="get-calendar-events-from-outlook"></a>从 Outlook 获取日历事件

首先，为日历视图创建新的控制器。

1. 在 **./Controllers** 目录中添加一个名为 **CalendarController.cs** 的新文件，并添加以下代码。

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using TimeZoneConverter;

    namespace GraphTutorial.Controllers
    {
        public class CalendarController : Controller
        {
            private readonly GraphServiceClient _graphClient;
            private readonly ILogger<HomeController> _logger;

            public CalendarController(
                GraphServiceClient graphClient,
                ILogger<HomeController> logger)
            {
                _graphClient = graphClient;
                _logger = logger;
            }
        }
    }
    ```

1. 将以下函数添加到 `CalendarController` 类中，以获取用户的日历视图。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetCalendarViewSnippet":::

    考虑代码的功能 `GetUserWeekCalendar` 。

    - 它使用用户的时区获取每周的 UTC 开始时间和结束日期/时间值。
    - 它查询用户的 " [日历" 视图](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) ，以获取介于开始和结束日期/时间之间的所有事件。 使用日历视图（而不是 [列出事件](/graph/api/user-list-events?view=graph-rest-1.0) ）可展开定期事件，并返回在指定时间范围内发生的任何事件。
    - 它使用 `Prefer: outlook.timezone` 标头获取用户的时区中的结果。
    - 它用于 `Select` 限制返回到仅应用程序所使用的字段。
    - 它用于 `OrderBy` 按时间顺序对结果进行排序。
    - 它使用的 "" `PageIterator` 指向 ["" 事件集合](/graph/sdks/paging)中的 "" 页。 这将处理用户其日历上的事件数超过请求的页面大小的情况。

1. 将以下函数添加到 `CalendarController` 类中，以实现返回数据的临时视图。

    ```csharp
    // Minimum permission scope needed for this view
    [AuthorizeForScopes(Scopes = new[] { "Calendars.Read" })]
    public async Task<IActionResult> Index()
    {
        try
        {
            var userTimeZone = TZConvert.GetTimeZoneInfo(
                User.GetUserGraphTimeZone());
            var startOfWeek = CalendarController.GetUtcStartOfWeekInTimeZone(
                DateTime.Today, userTimeZone);

            var events = await GetUserWeekCalendar(startOfWeek);

            // Return a JSON dump of events
            return new ContentResult {
                Content = _graphClient.HttpProvider.Serializer.SerializeObject(events),
                ContentType = "application/json"
            };
        }
        catch (ServiceException ex)
        {
            if (ex.InnerException is MicrosoftIdentityWebChallengeUserException)
            {
                throw ex;
            }

            return new ContentResult {
                Content = $"Error getting calendar view: {ex.Message}",
                ContentType = "text/plain"
            };
        }
    }
    ```

1. 启动应用程序，登录，然后单击导航栏中的 " **日历** " 链接。 如果一切正常，应在用户的日历上看到一个 JSON 转储的事件。

## <a name="display-the-results"></a>显示结果

现在，您可以添加一个视图，以对用户更友好的方式显示结果。

### <a name="create-view-models"></a>创建视图模型

1. 在 **./Models** 目录中创建一个名为 **CalendarViewEvent.cs** 的新文件，并添加以下代码。

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewEvent.cs" id="CalendarViewEventSnippet":::

1. 在 **./Models** 目录中创建一个名为 **DailyViewModel.cs** 的新文件，并添加以下代码。

    :::code language="csharp" source="../demo/GraphTutorial/Models/DailyViewModel.cs" id="DailyViewModelSnippet":::

1. 在 **./Models** 目录中创建一个名为 **CalendarViewModel.cs** 的新文件，并添加以下代码。

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewModel.cs" id="CalendarViewModelSnippet":::

### <a name="create-views"></a>创建视图

1. 在 **/Views** 目录中创建一个名为 **Calendar** 的新目录。

1. 在 **/Views/Calendar** 目录中创建一个名为 **_DailyEventsPartial** 的新文件，并添加以下代码。

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/_DailyEventsPartial.cshtml" id="DailyEventsPartialSnippet":::

1. 在 **/Views/Calendar** 目录中创建一个名为 " **" 的新** 文件，并添加以下代码。

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/Index.cshtml" id="CalendarIndexSnippet":::

### <a name="update-calendar-controller"></a>更新日历控制器

1. 打开 **/Controllers/CalendarController.cs** ，并将现有 `Index` 函数替换为以下项。

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="IndexSnippet":::

1. 启动应用程序，登录，然后单击 " **日历** " 链接。 应用现在应呈现一个事件表。

    ![事件表的屏幕截图](./images/add-msgraph-01.png)
