---
title: 快速入门：在 Azure 数据资源管理器 Web UI 中查询数据
description: 本快速入门介绍如何在 Azure 数据资源管理器 Web UI 中查询和共享数据。
author: orspod
ms.author: v-tawe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
origin.date: 11/22/2020
ms.date: 01/22/2021
ms.localizationpriority: high
ms.openlocfilehash: 6453bad65105db1bfd5239b159b31df2cc10e55c
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611563"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>快速入门：在 Azure 数据资源管理器 Web UI 中查询数据

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，用于对大量数据进行实时分析。 Azure 数据资源管理器提供了一种 Web 体验，使你能够连接到 Azure 数据资源管理器群集并编写、运行和共享 Kusto 查询语言命令和查询。 该 Web 体验可在 Azure 门户中使用，也可作为独立的 Web 应用程序（即 [Azure 数据资源管理器 Web UI](https://dataexplorer.azure.cn)）使用。 Azure 数据资源管理器 Web UI 还可以由 HTML iframe 中的其他 Web 门户托管。 有关如何托管 Web UI 和所用 Monaco 编辑器的详细信息，请参阅 [Monaco IDE 集成](kusto/api/monaco/monaco-kusto.md)。
本快速入门将在独立的 Azure 数据资源管理器 Web UI 中进行操作。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，请在开始之前创建一个[免费 Azure 帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn#azurefreeform)。
* 包含数据的群集和数据库。 [创建自己的群集](create-cluster-database-portal.md)或使用 Azure 数据资源管理器 help 群集。

## <a name="sign-in-to-the-application"></a>登录应用程序

登录到[应用程序](https://dataexplorer.azure.cn/)。

## <a name="add-clusters"></a>添加群集

首次打开应用程序时，不存在任何群集连接。

![添加群集](media/web-query-data/add-cluster.png)

开始运行查询前，必须先将连接添加到群集中。 本部分会将连接添加到 Azure 数据资源管理器 help 群集以及在[先决条件](#prerequisites)中创建的测试群集中（可选）。

### <a name="add-help-cluster"></a>添加 help 群集

1. 在应用程序的左上角，选择“添加群集”。

1. 在“添加群集”对话框中，输入 URI https://help.kusto.chinacloudapi.cn ，然后选择“添加” 。
   
1. 在左侧窗格中，现在应看到 help 群集。 展开“示例”数据库并打开“Tables”文件夹，查看有权访问的示例表 。

    :::image type="content" source="media/web-query-data/help-cluster.png" alt-text="在 help 群集中查找表":::

我们在本快速入门后面部分以及其他 Azure 数据资源管理器文章中使用 StormEvents 表。 

### <a name="add-your-cluster"></a>添加群集

现在添加创建的测试群集。

1. 选择“添加群集”。

1. 在“添加群集”对话框中，以 `https://<ClusterName>.<Region>.kusto.chinacloudapi.cn/` 格式输入测试群集 URL，然后选择“添加” 。 例如，`https://mydataexplorercluster.chinaeast2.kusto.chinacloudapi.cn`，如下图所示：

    :::image type="content" source="media/web-query-data/server-uri.png" alt-text="输入测试群集 URL":::
    
    在以下示例中，会看到 help 群集和一个新群集 docscluster.chinaeast2（完整 URL 为 `https://docscluster.chinaeast2.kusto.chinacloudapi.cn/`）   。

    ![测试群集](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>运行查询

你现在可以在两个群集上运行查询（假定测试群集中包含数据）。 考虑到本文的目的，我们将重点介绍 help 群集。

1. 在左窗格中的 help 群集下，选择示例数据库 。

1. 将以下查询复制并粘贴到查询窗口中。 在窗口顶部，选择“运行”。

    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    
    此查询返回 StormEvents 表中的 10 条最新记录。 结果应如下表所示。

    :::image type="content" source="media/web-query-data/result-set-take-10.png" alt-text="表的屏幕截图，其中列出了 10 个风暴事件的数据。" border="false":::

    下图显示了应用程序的状态，其中添加了群集以及带有结果的查询。

    :::image type="content" source="media/web-query-data/webui-take10.png" alt-text="全屏":::

1. 将以下查询复制并粘贴到第一个查询下方的查询窗口中。 请注意，它没有像第一个查询那样在单独行上进行格式化。

    ```kusto
    StormEvents | sort by StartTime desc 
    | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. 选择新查询。 按 Shift+Alt+F 设置查询格式，如以下查询所示。

    ![已格式化查询](media/web-query-data/formatted-query.png)

1. 选择“运行”或按 Shift + Enter 以运行查询。 此查询返回与第一条记录相同的记录，但仅包括 `project` 语句中指定的列。 结果应如下表所示。

    :::image type="content" source="media/web-query-data/result-set-project.png" alt-text="表的屏幕截图，其中列出了 10 个风暴事件的开始时间、结束时间、州、事件类型、损伤特性和事件叙述。" border="false":::

    > [!TIP]
    > 选择查询窗口顶部的“撤回”以显示第一个查询的结果集，而无需重新运行该查询。 通常在分析期间，会运行多个查询，通过“撤回”，你可以检索先前查询的结果。

1. 让我们再运行一个查询来查看不同类型的输出。

    ```kusto
    StormEvents
    | summarize event_count=count(), mid = avg(BeginLat) by State
    | sort by mid
    | where event_count > 1800
    | project State, event_count
    | render columnchart
    ```

    结果应如下图所示。

    ![柱形图](media/web-query-data/column-chart.png)

    > [!NOTE]
    > 查询表达式中的空白行可能影响执行查询的那一部分。
    > * 如果未选定文本，则假定以空行分隔查询或命令。
    > * 如果选定了文本，则运行选定的文本。

## <a name="work-with-the-table-grid"></a>使用网格型

现在你已了解了基本查询的工作原理，接下来我们来看看如何使用表网格来自定义结果并进行进一步分析。

1. 重新运行第一个查询。 将鼠标悬停在“州”列上，选择菜单，然后选择“按州分组” 。

    ![按州分组](media/web-query-data/group-by.png)

1. 在网格中，双击“加利福尼亚州”，展开并查看该州的记录。 进行探索性分析时，此类型的分组可能会有所帮助。

    :::image type="content" source="media/web-query-data/group-expanded.png" alt-text="查询结果网格的屏幕截图，其中已展开“加利福尼亚州”组" border="false":::

1. 将鼠标悬停在“组”列上，然后选择“重置列” 。 此设置将网格返回到其原始状态。

    ![重置列](media/web-query-data/reset-columns.png)

1. 运行以下查询。

    ```kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. 在结果网格中，选择一些数值单元格。 使用表网格，你可以选择多个行、列和单元格，并计算它们的聚合。 Web UI 当前支持以下数值函数：Average、Count、Min、Max 和 Sum    。

    :::image type="content" source="media/web-query-data/select-stats.png" alt-text="选择函数"::: 

1. 在网格的右侧，选择“列”以查看表工具面板。 此面板的功能类似于 Excel 中的数据透视表字段列表，可在网格中执行更多分析。

    ![表工具面板](media/web-query-data/tool-panel.png)

1. 选择“透视模式”，然后按以下方式拖动列：将“状态”拖动到“行组”；将“DamageProperty”拖动到“值”，并将“EventType”拖动到“列标签”     。  

    ![透视模式](media/web-query-data/pivot-mode.png)

    结果应如以下数据透视表所示。

    ![数据透视表](media/web-query-data/pivot-table.png)

    请注意佛蒙特州和阿拉巴马州各有两个属于同一类别的事件，而德克萨斯州有两个不同类别的事件。 数据透视表是一种很好的快速分析工具，因为该表使你可以快速发现这些差异。

## <a name="search-in-the-results-table"></a>在结果表中搜索

你可以在结果表中查找特定表达式。

1.  运行以下查询：

    ```Kusto
    StormEvents
    | where DamageProperty > 5000
    | take 1000
    ```

1. 单击右侧的“搜索”按钮，然后键入“Wabash”

    :::image type="content" source="media/web-query-data/search.png" alt-text="在表中搜索":::

1. 所有提到的搜索表达式现在都在表中突出显示。 可以通过单击 Enter 向前移动或按 Shift + Enter 向后移动来在它们之间导航，也可以使用搜索框旁边的向上和向下按钮进行导航   。

    :::image type="content" source="media/web-query-data/search-results.png" alt-text="在搜索结果中导航":::

## <a name="share-queries"></a>共享查询

很多时候，你希望共享创建的查询。 

1. 在查询窗口中，选择复制的第一个查询。

1. 在查询窗口顶部，选择“共享”。 

:::image type="content" source="media/web-query-data/share-menu.png" alt-text="共享菜单":::

下拉列表中提供了以下选项：
* 链接到剪贴板
* [将查询链接到剪贴板](#provide-a-deep-link)
* 将查询结果链接到剪贴板
* [固定到仪表板](#pin-to-dashboard)
* [Power BI 查询](power-bi-imported-query.md)

### <a name="provide-a-deep-link"></a>提供深层链接

可提供深层链接，以便有权访问群集的其他用户可运行查询。

1. 在“共享”中，选择“将查询链接到剪贴板” 。

1. 将链接和查询复制到文本文件。

1. 将链接粘贴到新的浏览器窗口中。 结果应如下所示

    :::image type="content" source="media/web-query-data/shared-query.png" alt-text="共享查询深层链接":::

### <a name="pin-to-dashboard"></a>固定到仪表板

在 Web UI 中通过查询完成数据探索并找到所需的数据后，可将其固定到仪表板中以便持续监视。 

若要固定查询：

1. 在“共享”中，选择“固定到仪表板” 。

1. 在“固定到仪表板”窗格中：
    1. 提供查询名称。
    1. 选择“使用现有查询”或“创建新查询” 。
    1. 提供仪表板名称
    1. 选择“创建后查看仪表板”复选框（如果是新仪表板）。
    1. 选择“固定”

    :::image type="content" source="media/web-query-data/pin-to-dashboard.png" alt-text="“固定到仪表板”窗格":::

> [!NOTE]
> “固定到仪表单”选项仅固定所选的查询。 要创建仪表板数据源并将显示命令转换为仪表板中的视觉对象，必须在数据库列表中选择相关数据库。

## <a name="export-query-results"></a>导出查询结果

要将查询结果导出到 CSV 文件，请选择“文件” > “导出到 CSV” 。

:::image type="content" source="media/web-query-data/export-results.png" alt-text="将结果导出到 CSV 文件":::

## <a name="settings"></a>设置

在“设置”选项卡中，你可以：

* [导出环境设置](#export-environment-settings)
* [导入环境设置](#import-environment-settings)
* [突出显示错误级别](#highlight-error-levels)
* [清除本地状态](#clean-up-resources)

选择右上方的设置图标 :::image type="icon" source="media/web-query-data/settings-icon.png" border="false":::，打开“设置”窗口。

:::image type="content" source="media/web-query-data/settings.png" alt-text="“设置”窗口":::

### <a name="export-and-import-environment-settings"></a>导出和导入环境设置

导出和导入操作有助于保护工作环境，并将其重新定位到其他浏览器和设备。 导出操作可将所有设置、群集连接和查询选项卡导出到 JSON 文件，该文件可导入到其他浏览器或设备。

#### <a name="export-environment-settings"></a>导出环境设置

1. 在“设置” > “常规”窗口中，选择“导出”  。
1. 此操作会将 adx-export.json 文件下载到本地存储中。
1. 选择“清除本地状态”，将环境还原到其原始状态。 此设置将删除所有群集连接并关闭打开的选项卡。

> [!NOTE]
> “导出”选项仅导出查询相关数据。 不会在 adx-export.json 文件中导出任何仪表板数据。

#### <a name="import-environment-settings"></a>导入环境设置

1. 在“设置” > “常规”窗口中，选择“导入”  。 然后在“警告”弹出窗口中，选择“导入” 。

    :::image type="content" source="media/web-query-data/import.png" alt-text="导入警告":::

1. 从本地存储中找到 adx-export.json 文件，然后将其打开。
1. 以前的群集连接和打开的选项卡现在可用。

> [!NOTE]
> “导入”选项将替代任何现有的环境设置和数据。

### <a name="highlight-error-levels"></a>突出显示错误级别

Kusto 会尝试解释结果面板中每行的严重性或详细级别，并对其进行相应的着色。 它通过将每列的非重复值与一组已知模式（“警告”、“错误”等）进行匹配来实现此目的。 

若要启用错误级别突出显示，请执行以下操作：

1. 选择用户名旁边的“设置”图标。
1. 选择“外观”选项卡，然后将“启用错误级别突出显示”选项切换到右侧。  

:::image type="content" source="media/web-query-data/enable-error-highlighting.gif" alt-text="动画 GIF 显示了如何在设置中启用错误级别突出显示":::

浅色模式下的错误级别配色方案 | 深色模式下的错误级别配色方案
|---|---|
:::image type="content" source="media/web-query-data/light-mode.png" alt-text="浅色模式下颜色图例的屏幕截图"::: | :::image type="content" source="media/web-query-data/dark-mode.png" alt-text="深色模式下颜色图例的屏幕截图":::

## <a name="provide-feedback"></a>提供反馈

1. 在应用程序的右上角，选择反馈图标 :::image type="icon" source="media/web-query-data/icon-feedback.png" border="false":::。

1. 输入反馈，然后选择“提交”。

## <a name="clean-up-resources"></a>清理资源

未在此快速入门中创建任何资源，但如果要从应用程序中删除一个或两个群集，请右键单击群集并选择“删除连接”。
另一种选择是从“设置” > “常规”选项卡中选择“清除本地状态”  。此操作将删除所有群集连接并关闭所有打开的查询选项卡。

## <a name="next-steps"></a>后续步骤

[Azure 数据资源管理器的编写查询](write-queries.md)
