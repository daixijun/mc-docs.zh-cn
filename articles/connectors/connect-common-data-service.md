---
title: 连接到 Common Data Service (Microsoft Dataverse)
description: 使用 Azure 逻辑应用创建和管理 Common Data Service (Microsoft Dataverse) 记录
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
origin.date: 12/11/2020
author: rockboyfor
ms.date: 01/11/2021
ms.testscope: yes|no
ms.testdate: 01/11/2021null
ms.author: v-yeche
tags: connectors
ms.openlocfilehash: 3d60c282993edf5056ca2c4be352eac3d3235b73
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023709"
---
<!--Pending for VM Review-->
<!--Common Data Service connect exists on Mooncake-->
# <a name="create-and-manage-records-in-common-data-service-microsoft-dataverse-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用在 Common Data Service (Microsoft Dataverse) 中创建和管理记录

> [!NOTE]
> 2020 年 11 月，Common Data Service 已重命名为 Microsoft Dataverse。

<!--CORRECT ON Microsoft Dataverse-->

使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和 [Common Data Service 连接器](https://docs.microsoft.com/connectors/commondataservice/)，你可以构建自动化工作流，用于管理 [Common Data Service（现在的 Microsoft Dataverse）](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro)数据库中的记录。 这些工作流可以创建记录、更新记录，以及执行其他操作。 你还可以从 Common Data Service 数据库中获取信息，使输出可供逻辑应用中的其他操作使用。 例如，在 Common Data Service 数据库中更新记录时，可以使用 Office 365 Outlook 连接器发送电子邮件。

本文介绍如何构建一个逻辑应用，用于在每次创建新的潜在顾客记录时创建一个任务记录。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册试用版 Azure 订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

* [Common Data Service 环境](https://docs.microsoft.com/power-platform/admin/environments-overview)，这是你的组织在其中存储、管理以及共享业务数据和 Common Data Service 数据库的空间。 有关详细信息，请参阅以下资源：<p>

    * [Learn：Common Data Service 入门](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
    * [Power Platform - 环境概述](https://docs.microsoft.com/power-platform/admin/environments-overview)

* 关于[逻辑应用创建方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)以及特定逻辑应用（你需要从其访问 Common Data Service 数据库中的记录）的基本知识。 若要通过 Common Data Service 触发器启动逻辑应用，需要一个空白逻辑应用。 如果你不熟悉 Azure 逻辑应用，请查看[快速入门：使用 Azure 逻辑应用创建你的第一个工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-common-data-service-trigger"></a>添加 Common Data Service 触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

在此示例中，请添加在创建新记录时触发的 Common Data Service 触发器。

1. 在 [Azure 门户](https://portal.azure.cn)上的逻辑应用设计器中打开空白逻辑应用（如果尚未打开）。

1. 在搜索框中输入 `common data service`。 对于本示例，请在触发器列表中选择以下触发器：“创建记录时”

    ![选择“创建记录时”触发器](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. 如果出现系统提示，请登录到 Common Data Service。

1. 在触发器中，提供要在其中监视新“潜在顾客”记录的环境的相关信息，例如：

    :::image type="content" source="./media/connect-common-data-service/when-record-created-trigger-details.png" alt-text="要监视的环境的触发器信息":::

    | 属性 | 必须 | 说明 |
    |----------|----------|-------------|
    | **环境** | 是 | 要监视的环境，例如“Fabrikam Sales Production”。 有关详细信息，请参阅 [Power Platform - 环境概述](https://docs.microsoft.com/power-platform/admin/environments-overview)。 |
    | **实体名称** | 是 | 要监视的实体，例如“潜在顾客” |
    | **范围** | 是 | 创建了新记录的源，例如，业务部门中的用户或组织中的任何用户。 此示例使用了“业务部门”。 |
    ||||

## <a name="add-common-data-service-action"></a>添加 Common Data Service 操作

现在添加一个 Common Data Service 操作，该操作为新的“潜在顾客”记录创建任务记录。

1. 在“创建记录时”触发器下，选择“新建步骤”。

1. 在搜索框中输入 `common data service`。 在操作列表中选择以下操作：“创建新记录”

    ![选择“创建新记录”操作](./media/connect-common-data-service/select-create-new-record-action.png)

1. 在操作中，提供要在其中创建新任务记录的环境的相关信息。 系统还会根据你为此操作选择的实体显示其他属性（如果可用），例如：

    :::image type="content" source="./media/connect-common-data-service/create-new-record-action-details.png" alt-text="要在其中创建记录的环境的操作信息":::

    | 属性 | 必须 | 说明 |
    |----------|----------|-------------|
    | **组织名称** | 是 | 要在其中创建记录的环境，不一定是触发器中的同一环境，在本示例中为“Fabrikam Sales Production” |
    | **实体名称** | 是 | 要在其中创建记录的实体，例如“Tasks” |
    | **主题** | 是，具体取决于此示例中选择的实体 | 有关此任务的目标的简短说明 |
    ||||

    1. 对于“主题”属性，请输入带尾随空格的以下文本：

        `Follow up with new lead:`

    1. 将鼠标指针置于“主题”框中，使动态内容列表保持可见状态。

    1. 在列表中，从“创建记录时”部分选择要包括在任务记录中的触发器输出，例如：

        :::image type="content" source="./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png" alt-text="选择要在任务记录中使用的触发器输出":::

        | 触发器输出 | 说明 |
        |----------------|-------------|
        | **名字** | 潜在顾客记录中的名字，将用作任务记录中的主要联系人 |
        | **姓氏** | 潜在顾客记录中的姓氏，将用作任务记录中的主要联系人 |
        | **说明** | 要包括在任务记录中的其他输出，例如电子邮件地址和业务电话号码 |
        |||

    完成后，此操作可能如以下示例所示：

    ![完成的“创建新记录”操作](./media/connect-common-data-service/finished-create-record-action-details.png)

1. 保存逻辑应用。 在设计器工具栏上选择“保存”。 

1. 若要手动启动逻辑应用，请在设计器工具栏中选择“运行”。 若要测试逻辑应用，请创建新的“潜在顾客”记录。

## <a name="trigger-only-on-updated-attributes"></a>仅在更新的特性上触发

对于在记录更新时运行的触发器（例如“更新记录时”操作），你可以使用筛选器特性，以便仅在指定的特性更新时才运行逻辑应用。 此功能有助于防止不必要的逻辑应用运行。

1. 在触发器中，从“添加新参数”列表选择“特性筛选器”。

    ![屏幕截图显示了“更新记录时”操作和已打开的“添加新参数”列表（选中了“特性筛选器”属性）。](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. 对于每个“特性筛选器项”，请选择要监视更新的特性，例如：

    ![添加“特性筛选器”属性](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>基于筛选器列出记录

对于返回记录的操作（例如“列出记录”操作），你可以使用一个基于指定筛选器返回记录的 ODATA 查询。 例如，你可以让操作仅列出活动帐户的记录。

1. 在操作中，打开“添加新参数”列表，然后选择“筛选器查询”属性。 

    ![添加“筛选器查询”属性](./media/connect-common-data-service/list-records-action-filter-query.png)

1. 在目前显示在操作中的“筛选器查询”属性中，输入此 ODATA 筛选器查询：`statuscode eq 1`

    :::image type="content" source="./media/connect-common-data-service/list-records-action-filter-query-value.png" alt-text="输入用于筛选记录的 ODATA 筛选器查询":::

有关 `$filter` 系统查询选项的详细信息，请参阅 [Common Data Service - 筛选结果](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results)。

## <a name="list-records-based-on-an-order"></a>基于某个顺序列出记录

对于返回记录的操作（例如“列出记录”操作），可以使用一个按指定顺序（因操作返回的记录而异）返回记录的 ODATA 查询。 例如，可以让操作根据帐户名称列出记录。

1. 在操作中，打开“添加新参数”列表，然后选择“排序依据”属性。 

    ![添加“排序依据”属性](./media/connect-common-data-service/list-records-action-order-by.png)

1. 在目前显示在操作中的“排序依据”属性中，输入以下 ODATA 筛选器查询：`name`

    :::image type="content" source="./media/connect-common-data-service/list-records-action-order-by-value.png" alt-text="输入用于对记录排序的 ODATA 筛选器查询":::

有关 `$orderby` 系统查询选项的详细信息，请参阅 [Common Data Service - 对结果排序](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results)。

## <a name="field-data-types"></a>字段数据类型

对于触发器或操作中的字段，不管是手动输入值，还是从动态内容列表中选择值，该值的数据类型都必须与字段的所需数据类型相匹配。

下表描述了一些字段类型以及这些字段的值需要使用的数据类型。

| 字段 | 数据类型 | 说明 |
|-------|-----------|-------------|
| 文本字段 | 单个文本行 | 需要单行文本或具有文本数据类型的动态内容，例如以下属性： <p><p>- **说明** <br />- 类别 |
| 整数字段 | 整数 | 需要一个整数或具有整数数据类型的动态内容，例如以下属性： <p><p>- 完成百分比 <br />- 持续时间 |
| 日期字段 | 日期和时间 | 需要一个 MM/DD/YYY 格式的日期或具有日期数据类型的动态内容，例如以下属性： <p><p>- 创建日期 <br />- 开始日期 <br />- 实际开始日期 <br />- 实际结束日期 <br />- 截止日期 |
| 引用其他实体记录的字段 | 主密钥 | 需要一个记录 ID（例如 GUID）和一个查找类型，这意味着来自动态内容列表的值将不起作用，例如以下属性： <p><p>- 所有者：必须是有效的用户 ID 或团队记录 ID。 <br />- 所有者类型：必须是一个查找类型，例如 `systemusers` 或 `teams`。 <p><p>- 相关：必须是有效的记录 ID，例如帐户 ID 或联系人记录 ID。 <br />- 相关类型：必须是一个查找类型，例如 `accounts` 或 `contacts`。 <p><p>- 客户：必须是有效的记录 ID，例如帐户 ID 或联系人记录 ID。 <br />- 客户类型：必须是一个查找类型，例如 `accounts` 或 `contacts`。 |
||||

此示例展示了“创建新记录”操作如何创建一个新的“任务”记录，该记录与其他实体记录相关联，具体而言，是一个用户记录和一个帐户记录。 该操作通过使用与相关属性的预期数据类型匹配的值来指定这些实体记录的 ID 和查找类型。

* 该操作根据“所有者”属性（指定用户 ID）和“所有者类型”属性（指定 `systemusers` 查找类型）将新的“任务”记录与特定用户相关联。

* 该操作根据“相关”属性（指定记录 ID）和“相关类型”属性（指定 `accounts` 查找类型）将新的“任务”记录与特定帐户相关联。

![创建与 ID 和查找类型关联的“任务”记录](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>连接器参考

如需基于连接器的 Swagger 说明的技术信息，例如触发器、操作、限制和其他详细信息，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/commondataservice/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)

<!-- Update_Description: new article about connect common data service -->
<!--NEW.date: 01/11/2021-->