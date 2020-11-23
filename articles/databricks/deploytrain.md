---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/04/2020
title: 部署计划 - Azure Databricks
description: 了解 Azure Databricks 部署流程和计划性维护时段。
ms.openlocfilehash: f7464f3ea192603086a6721e5296df773af9f6a0
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589646"
---
# <a name="deployment-schedule"></a>部署计划

为尽量减少对 Azure Databricks 用户的干扰，版本将定期进行部署。 在部署过程中，Azure Databricks 服务可能会暂时中断。 你该做好计划来应对这些计划性维护时段，避免在这些时段长时间运行的作业。

版本部署通常分为三个阶段，第 1 个阶段发生在星期二，第 2 阶段发生在星期四，第 3 阶段发生在下一个星期二。

版本部署计划在其所在区域的正常工作时间之外进行。

> [!NOTE]
>
> 部署阶段基于 Azure Databricks 工作区的区域。


## <a name="notification-types"></a>通知类型

部署开始前，会有多种类型的通知，具体取决于部署模型。

以下各部分重点介绍了最常见的方法。

### <a name="status-page"></a>“状态”页

在部署开始前，会更新 Azure Databricks [状态页面](https://status.azuredatabricks.net/)来指出受影响的区域。

部署结束时，会更新 Azure Databricks [状态页面](https://status.azuredatabricks.net/)来显示正常操作。

### <a name="status-page-automatic-notification"></a>状态页面自动通知

如果你[已订阅](/databricks/status#subscribe)自动通知，则将在部署开始前收到一条警报，然后在部署结束时再收到一条警报。 这些警报可通过电子邮件或 Webhook 发送，具体取决于你配置的订阅类型。

### <a name="example-email-notification"></a>电子邮件通知示例

下面是在计划的部署开始时发送给订阅者的示例电子邮件通知。

```
Title: Maintenance Notification from Databricks

Affected Infrastructure
Components: Authentication Service, Compute Service, Jobs Service, ODBC/JDBC Service, User Interface,API Service
Locations: ap: ap-northeast-1: AP Northeast 1: Asia Pacific (Tokyo), ap: ap-northeast-2: AP Northeast 2: Asia Pacific (Seoul), ap: ap-south-1: AP South 1: Asia Pacific (Mumbai), ap: ap-southeast-1: AP Southeast 1: Asia Pacific (Singapore), ap: ap-southeast-2: AP Southeast 2: Asia Pacific (Sydney), ca: ca-central-1: Canada Central: Canada (Central), eu: eu-central-1: Central Europe 1: EU (Frankfurt), eu: eu-west-1: West Europe 1: EU (Ireland), sa: sa-east-1: SA East 1: South America (Sao Paulo), us: us-east-1: East US 1: US East (Northern Virginia), us: us-west-2: West US 2: US West (Oregon),us: us-west-1: West US 1: US West (Northern California)

Update:
Databricks has initiated scheduled maintenance in the following regions. An additional notification will be sent when maintenance is completed.
```