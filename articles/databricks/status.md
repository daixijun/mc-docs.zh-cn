---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/11/2020
title: 状态页 - Azure Databricks
description: 了解 Azure Databricks 状态页。
ms.openlocfilehash: f983012c79a17bcf7cb8da2e86d6aa3df062dbde
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589781"
---
# <a name="status-page"></a>“状态”页

Azure Databricks [状态页](https://status.azuredatabricks.net/)提供了所有核心 Azure Databricks 服务的概述。 可以通过查看状态页轻松查看特定服务的状态。 （可选）还可以订阅各个服务组件上的状态更新，每当订阅的状态发生更改时，就会发送警报。

> [!div class="mx-imgBorder"]
> ![Azure 状态页](_static/images/status/azure-status-page.png)

状态页按 Azure 区域细分。 选择四个主地区中的一个，以显示所选地区中的所有活动区域。

服务状态按区域进行跟踪。 服务在不同地区可能具有不同的状态。

除了 Azure Databricks 服务之外，状态页还显示整个 Azure 的状态。 Azure 的状态由 Microsoft 提供。 它位于 Azure Databricks 的外部。

## <a name="service-icons"></a>服务图标

服务状态由颜色编码图标指示。

这些图标用于单个服务以及整个地区和外部服务。

  ![可操作服务](_static/images/status/green-so.png) 所有服务都是可操作的。

  ![服务已降级](_static/images/status/yellow-sd.png) 一个或多个服务正在降级状态下运行。

  ![服务关闭](_static/images/status/red-sd.png) 一个或多个服务当前已关闭。

  ![计划内维护](_static/images/status/blue-pm.png) 一个或多个服务因计划内维护而关闭。

将鼠标悬停在状态页的图标上，以显示当前状态的摘要。

## <a name="incident-page"></a>事件页

选择黄色、红色或蓝色的服务状态图标来显示详细的事件页。

事件页突出显示“事件状态”、受影响的“组件”，以及受影响的“位置”  。 解决事件后，事件页将在几分钟内显示事件的持续时间。

详细信息包含在事件页的“更新”部分中。 你可以在此处找到有关事件的最新信息以及任何已知的缓解措施。

## <a name="subscribe"></a>订阅

除了查看状态页外，你还可以选择通过以下一种（或多种）方法订阅更新：

* [Email](#email)
* [SMS（短信）](#sms-text-messages)
* [Webhook](#webhook)
* [Slack](#slack)

> [!NOTE]
>
> 你可以订阅每个区域中的各个服务。 这可确保你只收到与你相关的服务的警报。

### <a name="email"></a>电子邮件

> [!div class="mx-imgBorder"]
> ![电子邮件订阅](_static/images/status/subscribe-email.png)

通过电子邮件进行订阅：

1. 单击“订阅更新”（位于状态页的右上角）。
2. 单击“电子邮件”。
3. 输入电子邮件地址。
4. 单击 **“订阅”**。
5. 在“管理订阅”页上，选择要跟踪的所有组件。
6. 单击“保存订阅”以确认所做的选择。

管理现有订阅：

1. 单击“订阅更新”（位于状态页的右上角）。
2. 单击“电子邮件”。
3. 单击“管理现有订阅”。
4. 输入具有活动订阅的电子邮件地址。
5. 单击“管理订阅”，系统会通过电子邮件向你发送用于管理订阅的链接。
6. 打开电子邮件，并单击链接。
7. 在“管理订阅”页上，选择要跟踪的所有组件，然后取消选择要删除的所有组件。
8. 单击“保存订阅”以确认所做的选择。

### <a name="sms-text-messages"></a>SMS（短信）

> [!div class="mx-imgBorder"]
> ![SMS 订阅](_static/images/status/subscribe-sms.png)

> [!NOTE]
>
> 大多数主要移动运营商支持短信通知。 短信通知在 VoIP 线路上不保证传输成功。

订阅短信通知：

1. 单击“订阅更新”（位于状态页的右上角）。
2. 单击“短信”。
3. 从列表中选择你所在的国家/地区。
4. 输入你的移动电话号码。
5. 单击 **“订阅”**。
6. 检查你的手机是否收到确认短信。 `Thanks for signing up to receive status updates. Reply 1 to confirm your subscription.`
7. 向确认短信回复数字 1。 你将收到另一条确认短信。 `Your subscription is confirmed.`
8. 返回状态页，然后按照步骤管理现有订阅。

管理现有订阅：

1. 单击“订阅更新”（位于状态页的右上角）。
2. 单击“短信”。
3. 单击“管理现有订阅”。
4. 从列表中选择你所在的国家/地区。
5. 输入具有活动订阅的移动电话号码。
6. 单击“管理订阅”，系统会通过短信向你发送用于管理订阅的链接。
7. 打开短信，并单击链接。
8. 在“管理订阅”页上，选择要跟踪的所有组件，然后取消选择要删除的所有组件。
9. 单击“保存订阅”以确认所做的选择。

### <a name="webhook"></a>Webhook

> [!div class="mx-imgBorder"]
> ![Webhook 订阅](_static/images/status/subscribe-webhook.png)

通过 Webhook 进行订阅：

1. 单击“订阅更新”（位于状态页的右上角）。
2. 单击“WEBHOOK”。
3. 输入 Webhook 的 URL。
4. 输入电子邮件地址。
5. 单击 **“订阅”**。
6. 在“管理订阅”页上，选择要跟踪的所有组件。
7. 单击“保存订阅”以确认所做的选择。

管理现有订阅：

1. 单击“订阅更新”（位于状态页的右上角）。
2. 单击“WEBHOOK”。
3. 单击“管理现有订阅”。
4. 输入用于 Webhook 的 URL。
5. 输入具有活动订阅的电子邮件地址。
6. 单击“管理订阅”，系统会通过电子邮件向你发送用于管理订阅的链接。
7. 打开电子邮件，并单击链接。
8. 在“管理订阅”页上，选择要跟踪的所有组件，然后取消选择要删除的所有组件。
9. 单击“保存订阅”以确认所做的选择。

#### <a name="example-webhook"></a>示例 Webhook

下面是可通过 Webhook 发布的示例 JSON 有效负载。

```json
{
  "id":"552adb8331a9553b11000008",
  "message_id":"542adb8331a9553b11000008",
  "title":"Server Upgrades",
  "datetime":"2015-04-03T18:38:57.326Z",
  "current_status":"Planned Maintenance",
  "infrastructure_affected":[
    {"component":"551ed627b556f14210000005", "container":"551ed5ac590f5a3b10000006"},
    {"component":"551ed627b556f14210000005", "container":"551ed5b1c9f9404110000005"}
  ],
  "components":[
    {"name":"Chat Service",
    "_id":"551ed627b556f14210000005"}
  ],
  "containers":[
    {"name":"US East",
    "_id":"551ed5ac590f5a3b10000006"},
    {"name":"US West",
    "_id":"551ed5b1c9f9404110000005"}
  ],
  "details":"We've completed upgrades for all US East servers. No issues so far. Moving on to US West next. Updates to follow.",
  "maintenance_url":"https://status.io/pages/maintenance/5516e01e2e55e4e917000005/5116e01e2e33e4e413000001",
  "status_page_url":"https://status.io/pages/5516e01e2e55e4e917000005"
}
```

### <a name="slack"></a>Slack

> [!div class="mx-imgBorder"]
> ![Slack 订阅](_static/images/status/subscribe-slack.png)

订阅 Slack 通知：

1. 单击“订阅更新”（位于状态页的右上角）。
2. 单击“SLACK”。
3. 单击“添加到 Slack”。
4. 输入要接收通知的 Slack 工作区的名称。
5. 单击“继续” 。
6. 登录到 Slack 工作区。 如果 Slack 实例需要，你可能需要使用 2FA 进行验证。
7. 选择现有通道以接收状态页消息。
8. 单击“允许”。
9. 在“管理订阅”页上，输入电子邮件地址，选择要跟踪的所有组件，然后取消选择要删除的所有组件。
10. 单击“保存订阅”以确认所做的选择。

管理现有订阅：

1. 单击“订阅更新”（位于状态页的右上角）。
2. 单击“SLACK”。
3. 单击“管理现有订阅”。
4. 输入接收状态更新的 Slack 通道 ID。
5. 输入在 Slack 通道中注册的电子邮件地址。
6. 单击“管理订阅”，系统会通过电子邮件向你发送用于管理订阅的链接。
7. 打开电子邮件，并单击链接。
8. 在“管理订阅”页上，选择要跟踪的所有组件，然后取消选择要删除的所有组件。
9. 单击“保存订阅”以确认所做的选择。