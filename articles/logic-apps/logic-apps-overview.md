---
title: 自动执行企业集成任务 - Azure 逻辑应用
description: 了解如何使用 Azure 逻辑应用以最少的代码自动执行集成应用、数据、服务和系统的工作流，以实现企业集成。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: overview
ms.custom: mvc
origin.date: 06/29/2018
ms.author: v-yiso
ms.date: 11/11/2019
ms.openlocfilehash: df21043c93ebc6f9444b864ab96d135bd07f28eb
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508002"
---
# <a name="overview---what-is-azure-logic-apps"></a>概述 - 什么是 Azure 逻辑应用？

[Azure 逻辑应用](/logic-apps)是一项云服务，用于在需要跨企业或组织集成应用、数据、系统和服务时计划、自动执行和协调任务、业务流程和[工作流](#logic-app-concepts)。 逻辑应用可简化可缩放解决方案的设计和构建方式，适用于应用集成、数据集成、系统集成、企业应用程序集成 (EAI) 和企业到企业 (B2B) 通信，不管是在云中还是在本地。

例如，下面就是一些可以通过逻辑应用自动完成的工作负荷：

* 跨本地系统和云服务处理并路由订单。
* 当各种系统、应用和服务中发生活动时，使用 Office 365 发送电子邮件通知。
* 将上传的文件从 SFTP 或 FTP 服务器移至 Azure 存储。 
* 监视推文中的特定主题，分析观点，针对需要查看的项目创建警报或任务。

若要使用 Azure 逻辑应用生成企业集成解决方案，可以从一个不断扩充的库中进行选择。该库包含[上百个可使用的连接器](../connectors/apis-list.md)，包括各种服务，例如 Azure 服务总线、Functions 和存储；SQL、Office 365、Dynamics、Salesforce、BizTalk、SAP、Oracle DB、文件共享等。 [连接器](#logic-app-concepts)提供[触发器](#logic-app-concepts)和/或[操作](#logic-app-concepts)，所创建的逻辑应用可以安全地对数据进行实时访问和处理。


## <a name="how-does-logic-apps-work"></a>逻辑应用的工作原理 

每个逻辑应用工作流都从触发器开始，在发生特定事件或新的可用数据符合特定条件的情况下触发。 许多触发器包括基本的计划功能，用于指定工作负荷的运行频率。 若要获取更多自定义计划方案，请使用计划触发器启动工作流。 

<!--Not Available on [how to build schedule-based workflows](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)-->

每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例来运行工作流中的操作。 这些操作也可包括数据转换和流控制，如条件语句、开关语句、循环和分支。 例如，以下逻辑应用通过 Dynamics 365 触发器启动，带有内置的条件“当更新记录时”。 触发器在检测到与此条件匹配的事件时，会触发并运行工作流的操作。 在这里，这些操作包括 XML 转换、数据更新、决策分支和电子邮件通知。

![逻辑应用设计器 - 示例逻辑应用](./media/logic-apps-overview/azure-logic-apps-overview.png)

可以使用逻辑应用设计器直观地生成逻辑应用。该设计器可通过浏览器在 Azure 门户中获取，也可在 Visual Studio 中获取。 若要获取更多的自定义逻辑应用，可以使用“代码视图”编辑器以 JavaScript 对象表示法 (JSON) 创建或编辑逻辑应用定义。 也可对选定的任务使用 Azure PowerShell 命令和 Azure 资源管理器模板。 逻辑应用部署和运行在 Azure 云中。 

## <a name="why-use-logic-apps"></a>为什么使用逻辑应用？

随着企业逐渐转向数字化，逻辑应用应运而生。它可以提供预生成的 API 作为 Microsoft 托管的连接器，从而可以更轻松快捷地连接旧系统和新式、前沿的系统。 因此，你可以专注于应用的业务逻辑和功能， 不需担心应用的生成、托管、缩放、管理、维护和监视。 逻辑应用为你解决这一切。 另外，只需根据使用情况付费，具体取决于使用量[定价模型](../logic-apps/logic-apps-pricing.md)。 

在许多情况下，无需编写代码。 但如果必须编写一些代码，则可使用 [Azure Functions](../azure-functions/functions-overview.md) 创建代码片段，然后通过逻辑应用按需运行该代码。 另外，如果逻辑应用需要与来自 Azure 服务、自定义应用或其他解决方案的事件交互，则可将 [Azure 事件网格](../event-grid/overview.md)与逻辑应用配合使用，以便进行事件监视、路由和发布。

逻辑应用、Functions 和事件网格由 Azure 全权托管，因此不必担心解决方案的生成、托管、缩放、管理、监视和维护。 由于能够创建[“无服务器”应用和解决方案](../logic-apps/logic-apps-serverless-overview.md)，因此只需关注业务逻辑。 这些服务可以按需自动缩放，加快集成速度，使用最少的代码生成可靠的云应用。 另外，只需根据使用情况付费，具体取决于使用量[定价模型](../logic-apps/logic-apps-pricing.md)。 

若要了解公司如何将逻辑应用与其他 Azure 服务和 Microsoft 产品配合使用，以便增强敏捷性并更加专注于核心业务，请查看这些[客户案例](https://aka.ms/logic-apps-customer-stories)。

下面更详细地介绍逻辑应用的功能和好处：

### <a name="visually-build-workflows-with-easy-to-use-tools"></a>使用易用的工具直观地生成工作流

使用可视化设计工具，既节省时间，又能简化复杂的流程。 从头至尾使用逻辑应用设计器来生成逻辑应用，不管是通过浏览器在 Azure 门户中使用，还是在 Visual Studio 中使用。 使用触发器启动工作流，并从[连接器库](../connectors/apis-list.md)添加任意数量的操作。

### <a name="get-started-faster-with-logic-app-templates"></a>使用逻辑应用模板加快入门速度

从[模板库](../logic-apps/logic-apps-create-logic-apps-from-templates.md)选择预定义的工作流时，可以更快速地创建常用解决方案。 模板既有适用于软件即服务 (SaaS) 应用的简单连接，也有高级 B2B 解决方案，还有“兴趣型”模板。 了解如何[从预生成的模板创建逻辑应用](../logic-apps/logic-apps-create-logic-apps-from-templates.md)。

### <a name="connect-disparate-systems-across-different-environments"></a>跨不同的环境连接不同的系统

某些模式和工作流描述起来容易，但难以在代码中实现。 逻辑应用可用于跨本地环境和云环境无缝连接不同的系统。 例如，可以将云营销解决方案连接到本地计费系统，也可以使用企业服务总线集中进行跨 API 和系统的消息传送。 可以通过逻辑应用快速、可靠且一致地为这些方案提供可重复使用和重新配置的解决方案。


### <a name="write-once-reuse-often"></a>编写一次即可多次重复使用

将逻辑应用作为模板创建，然后即可跨多个环境和区域[部署和重新配置应用](../logic-apps/logic-apps-create-deploy-template.md)。

### <a name="built-in-extensibility"></a>内置的扩展性

如果找不到所需的连接器，或者需要运行自定义代码，则可通过 [Azure Functions](../azure-functions/functions-overview.md) 根据需要创建和调用自己的代码片段，从而扩展逻辑应用。 创建自己的 [API](../logic-apps/logic-apps-create-api-app.md) 和[自定义连接器](../logic-apps/custom-connector-overview.md)，以便通过逻辑应用对其进行调用。

### <a name="pay-only-for-what-you-use"></a>只需为使用的服务付费
  
逻辑应用使用基于使用情况的[定价和计费](../logic-apps/logic-apps-pricing.md)，除非该逻辑应用是以前使用应用服务计划创建的。


<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>关键术语

* **工作流**：以一系列步骤的方式完成业务流程的可视化、设计、生成、自动化和部署操作。

* **托管连接器**：逻辑应用需要访问数据、服务和系统。 可以使用预生成的 Microsoft 托管连接器，这些连接器旨在连接、访问和使用数据。 请参阅[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)

* **触发器**：许多 Microsoft 托管连接器提供的触发器可以在事件或新数据符合指定条件时触发。 例如，某个事件可能正在获取电子邮件或检测 Azure 存储帐户中的更改。 每当触发器触发时，逻辑应用引擎就会创建一个新的逻辑应用实例来运行工作流。

* **操作**：操作是在触发器之后发生的所有步骤。 每个操作通常都会映射到由托管连接器、自定义 API 或自定义连接器定义的操作。


## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-flow"></a>逻辑应用与 Functions、WebJobs 及 Flow 的区别在哪里？

所有这些服务都可以用来将不同的系统“粘贴”和连接到一起。 每项服务都有其优点和优势，因此若要快速生成可缩放且功能完备的集成系统，最好的方法是将这些服务的功能组合到一起。 


## <a name="get-started"></a>入门 

逻辑应用是托管在 Microsoft Azure 上的许多服务之一。 因此在开始之前，你需要一个 Azure 订阅。 如果没有订阅，可以<a href="https://www.microsoft.com/china/azure/index.html?fromtype=cn" target="_blank">注册 Azure 试用帐户</a>。 

如果有 Azure 订阅，可以尝试此[创建第一个逻辑应用的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)。该逻辑应用通过 RSS 源监视网站上的新内容，在新内容出现时发送电子邮件。

## <a name="next-steps"></a>后续步骤

<!--Not Available on * [Check traffic with a schedule-based logic app](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)-->

* 详细了解 [Azure 的无服务器解决方案](../logic-apps/logic-apps-serverless-overview.md)
