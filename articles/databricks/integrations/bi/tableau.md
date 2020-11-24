---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/11/2020
title: Tableau - Azure Databricks
description: 了解如何将 Tableau 与 Azure Databricks 配合使用。
ms.openlocfilehash: 23147d5cfd8f0dbe88b0a8ff612fc07b6659b01b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589947"
---
# <a name="tableau"></a>Tableau

将 Azure Databricks 作为数据源与 Tableau 配合使用时，你可以提供强大的交互式分析功能，通过 Databricks Runtime 将数据集扩展为巨大的数据集，使业务分析师受益于数据科学家和数据工程师的贡献。 可以通过两个选项将 Tableau 连接到 Azure Databricks 群集，具体取决于 Tableau 版本：

* [Tableau 2019.3 及更高版本](#tableau-20193)：Databricks 建议使用此版本。 请使用由 Databricks 通过 Tableau 连接器 SDK 开发的 Databricks 连接器。 Databricks 连接器完全符合 Tableau 数据源验证工具 (TDVT) 测试套件的要求，为你提供一个更简单的用于连接到 Azure Databricks 群集的用户界面。 此连接器还包含若干内置的连接优化。
* [Tableau 2019.2 及更低版本](#tableau-20192)：使用由 Tableau 提供并维护的 Spark 连接器。

Tableau 提供四种工具：

* **Tableau Desktop**：Tableau Desktop 在本地计算机上运行，可用于连接到原始数据源并将该数据操作到视图中、仪表板中，等等。
* **Tableau Server**：Tableau Server 类似于 Tableau Desktop，只不过它允许多个用户访问集中管理的 Tableau 部署并启用数据源治理。
* **Tableau Online**：Tableau Server 的云版本。
* **Tableau Prep**：一种工具，用于连接到各种数据源并为 Tableau Desktop 组合、整理和清除数据。

有关 Tableau 的详细信息，请观看这些 [Tableau 教程视频](https://www.tableau.com/learn/tutorials/on-demand/introduction-tableau)。

本文介绍如何将 Tableau 与 Azure Databricks 配合使用。

## <a name="connect-tableau-20193-and-above-with-azure-databricks"></a><a id="connect-tableau-20193-and-above-with-azure-databricks"> </a><a id="tableau-20193"> </a>将 Tableau 2019.3 及更高版本与 Azure Databricks 连接

### <a name="step-1-download-and-install-software"></a>步骤 1：下载并安装软件

下载并安装以下项：

* Tableau 2019.3（Desktop、Server 或 Online）或更高版本
* Simba [Spark ODBC 驱动程序](https://databricks.com/spark/odbc-driver-download) 2.6.4 或更高版本。 如果你未运行受支持的驱动程序，Tableau 会在连接对话框中显示一条消息，其中包含指向驱动程序下载页的链接，你可以在该页中找到驱动程序链接和安装说明。

### <a name="step-2-get-azure-databricks-connection-information"></a>步骤 2：获取 Azure Databricks 连接信息

1. 获取[个人访问令牌](../../dev-tools/api/latest/authentication.md#token-management)。
2. 按照[服务器主机名、端口、HTTP 路径和 JDBC URL](jdbc-odbc-bi.md#jdbc-odbc-params) 中的说明获取服务器主机名、端口和 HTTP 路径。

### <a name="step-3-configure-connection-in-tableau-to-an-azure-databricks-cluster"></a>步骤 3：在 Tableau 中配置与 Azure Databricks 群集的连接

1. 启动 Tableau。
2. 请转到“连接”>“连接到服务器”菜单，选择 Databricks 连接器。

   > [!div class="mx-imgBorder"]
   > ![选择 Databricks 连接器](../../_static/images/third-party-integrations/tableau/tableau-databricks-connector.png)

3. 在“Databricks”对话框中，输入来自步骤 2 的服务器主机名和 HTTP 路径。
4. 使用 `token` 作为用户名并使用步骤 2 中的令牌作为密码进行身份验证。
5. 单击“登录”  。

## <a name="connect-tableau-20192-and-below-with-azure-databricks"></a><a id="connect-tableau-20192-and-below-with-azure-databricks"> </a><a id="tableau-20192"> </a>将 Tableau 2019.2 及更低版本与 Azure Databricks 连接

### <a name="step-1-download-and-install-software"></a>步骤 1：下载并安装软件

* Tableau 2019.2（Desktop、Server 或 Online）或更低版本
* Simba [Spark ODBC 驱动程序](https://databricks.com/spark/odbc-driver-download) 1.2.0 或更高版本

默认情况下，Tableau 在 Tableau 2018.1 及更高版本中安装此驱动程序。 如果计算机上未安装正确的驱动程序，Tableau 会在连接对话框中显示一条消息，其中包含指向驱动程序下载页的链接，你可以在该页中找到驱动程序链接和安装说明。

> [!NOTE]
>
> 如果你同时使用 Tableau Desktop 和 Tableau Server，那么它们必须使用相同的 Spark ODBC 驱动程序版本。

### <a name="step-2-get-azure-databricks-connection-information"></a>步骤 2：获取 Azure Databricks 连接信息

1. 获取[个人访问令牌](../../dev-tools/api/latest/authentication.md#token-management)。
2. 按照[服务器主机名、端口、HTTP 路径和 JDBC URL](jdbc-odbc-bi.md#jdbc-odbc-params) 中的说明获取服务器主机名、端口和 HTTP 路径。

### <a name="step-3-configure-connection-in-tableau-to-an-azure-databricks-cluster"></a>步骤 3：在 Tableau 中配置与 Azure Databricks 群集的连接

1. 启动 Tableau。
2. 转到“连接”菜单，单击“连接到服务器”>“更多...”，然后选择“Spark SQL”。
3. 输入你的服务器主机名和端口。
4. 在“类型”下拉列表中选择“SparkThriftServer”。 
5. 在“身份验证”下拉列表中，选择“用户名和密码”。
6. 在“传输”下拉列表中，选择“HTTP”。 
7. 使用 `token` 作为用户名并使用步骤 2 中的令牌作为密码进行身份验证。
8. 输入步骤 2 中的 HTTP 路径。
9. 选择“需要 SSL”。
10. 单击“登录”。

    > [!div class="mx-imgBorder"]
    > ![Tableau](../../_static/images/third-party-integrations/tableau/tableau-azure.png)

## <a name="tableau-server-on-linux"></a>Linux 上的 Tableau Server

编辑 `/etc/odbcinst.ini`，使之包括以下内容：

```
[Simba Spark ODBC Driver 64-bit]
Description=Simba Spark ODBC Driver (64-bit)
Driver=/opt/simba/spark/lib/64/libsparkodbc_sb64.so
```

> [!NOTE]
>
> Linux 上的 Tableau Server 建议使用 64 位处理体系结构。

## <a name="best-practices-and-troubleshooting"></a>最佳做法和故障排除

### <a name="in-this-section"></a>本部分内容：

* [启用性能记录以调试任何 Tableau 问题](#enable-performance-recording-to-debug-any-tableau-issue)
* [Tableau Server 与 Tableau Desktop 的性能对比](#performance-on-tableau-server-versus-tableau-desktop)
* [配置](#configuration)
* [优化图表（工作表）](#optimize-charts-worksheets)
* [优化仪表板](#optimize-dashboards)
* [缓存](#caching)

用于优化 Tableau 查询的两个基本操作是：

* 减少在单个图表或仪表板中要查询和可视化的记录数。
* 减少在单个图表或仪表板中要由 Tableau 发送的查询数。

确定先尝试哪一个取决于你的仪表板。 如果有很多用于各用户的不同图表全都在同一个仪表板中，则可能是 Tableau 向 Azure Databricks 发送过多的查询。 如果只有几个图表，但它们需要很长时间才能加载，则 可能是 Azure Databricks 返回太多记录，导致无法高效地进行加载。

在 Tableau Desktop 和 Tableau Server 上均可使用的 Tableau 性能记录可以用来在你运行特定工作流或仪表板时识别导致延迟的进程，帮助你了解性能瓶颈的位置。

### <a name="enable-performance-recording-to-debug-any-tableau-issue"></a>启用性能记录以调试任何 Tableau 问题

例如，如果查询执行存在问题，则你知道它与数据引擎进程有关或者与正在查询的数据源有关。 如果视觉对象布局的执行速度缓慢，则你知道 VizQL 是问题所在。

如果性能记录表明延迟发生在执行查询的过程中，则可能是 Azure Databricks 返回结果花费了太长时间，或者是 ODBC/连接器覆盖在将数据处理成适用于 VizQL 的 SQL 时花费了太长时间。 出现这种情况时，应分析返回的内容，并尝试更改分析模式，使每个组、段或项目都有一个仪表板，而不是尝试将所有内容都塞到一个仪表板中并依赖于快速筛选器。

如果是排序或视觉对象布局导致性能不佳，则问题可能出在仪表板尝试返回的标记数。 Azure Databricks 可以快速返回 1 百万条记录，但 Tableau 可能无法计算布局并对结果排序。 如果这是一个问题，请聚合查询并深化到更低的级别。 你还可以尝试更大的计算机，因为 Tableau 仅受运行它的计算机上的物理资源的限制。

有关性能记录器的深入教程，请参阅[创建性能记录](https://help.tableau.com/current/server-linux/en-us/perf_record_create_server.htm)。

### <a name="performance-on-tableau-server-versus-tableau-desktop"></a>Tableau Server 与 Tableau Desktop 的性能对比

通常，在 Tableau Desktop 上运行的工作流的速度不会快于 Tableau Server 上的。 不在 Tableau Desktop 上执行的仪表板不会在 Tableau Server 上执行。 这一点需牢记在心。

事实上，在 Desktop 上工作是一种好得多的故障排除方法，因为在进行故障排除时 Tableau Server 有更多需要考虑的进程。 如果可以在 Tableau Desktop 中工作，但不能在 Tableau Server 中工作，则可以安全地将问题范围缩小到那些在 Tableau Server 中而不在 Tableau Desktop 中的进程。

### <a name="configuration"></a>配置

默认情况下，连接 URL 中的参数会替代 Simba ODBC DSN 中的参数。 可以通过两种方式自定义 Tableau 中的 ODBC 配置：

* 单个数据源的 `.tds` 文件：
  1. 按照[保存数据源](https://help.tableau.com/current/pro/desktop/en-us/export_connection.htm)中的说明导出数据源的 `.tds` 文件。
  2. 查找 `.tds` 文件中的属性行 `odbc-connect-string-extras=''` 并设置参数。 例如，若要启用 `AutoReconnect` 和 `UseNativeQuery`，可以将行更改为 `odbc-connect-string-extras='AutoReconnect=1,UseNativeQuery=1'`。
  3. 通过对此连接进行重新连接，重新加载 `.tds` 文件。

  群集驱动程序经过优化，可使用较少的堆内存来收集大型结果，因此与 Simba ODBC 的默认值相比，可以为每个提取块处理更多行。 将 `RowsFetchedPerBlock=100000'` 追加到 `odbc-connect-string-extras` 属性的值。

* 所有数据源的 `.tdc` 文件：
  1. 如果从未创建过 `.tdc` 文件，则可将 [TableauTdcExample.tdc](../../_static/examples/TableauTdcExample.tdc) 添加到文件夹 `Document/My Tableau Repository/Datasources`。
  2. 将此文件添加到所有开发人员的 Tableau Desktop 安装，以便在共享仪表板时此文件可以发挥作用。

### <a name="optimize-charts-worksheets"></a>优化图表（工作表）

可以通过很多战术性图表优化来提高 Tableau 工作表的性能。

对于不经常更改且不应与之交互的筛选器，请使用上下文筛选器，这会缩短执行时间。
根据经验，另一种好的做法是在查询中使用 `if/else` 语句而非 `case/when` 语句。

Tableau 可以将筛选器向下推送到数据源中，这可以显著提高查询速度。 请参阅[使用参数跨多个数据源进行筛选](https://kb.tableau.com/articles/howto/filter-multiple-data-sources-using-parameter)和[跨多个数据源筛选数据](https://help.tableau.com/current/pro/desktop/en-us/filter_across_datasources.htm)，详细了解数据源向下推送筛选器。

最好是尽可能避免表计算，因为表计算需要扫描完整数据集。 有关表计算的详细信息，请参阅[通过表计算转换值](https://help.tableau.com/current/pro/desktop/en-us/calculations_tablecalculations.htm)。

### <a name="optimize-dashboards"></a>优化仪表板

下面是一些提示和故障排除练习，你可以应用这些练习来改善 Tableau 仪表板的性能。

连接到 Azure Databricks 的 Tableau 仪表板的常见问题来源是，在各个仪表板上使用了快速筛选器为许多不同的用户、函数或段提供服务。 可以将全局快速筛选器附加到仪表板上的所有图表。 这是一项很好的功能，但它引发问题的速度也很快。 如果一个仪表板有五个图表和一个全局快速筛选器，则会导致至少 10 个查询发送到 Azure Databricks。 添加更多筛选器后，此数字可能会螺旋式上升，导致巨大的性能问题，因为 Spark 不适合处理多个同一时刻开始的并发查询。 如果使用的群集不够大，无法处理大量查询，则问题会更严重。

建议你首先使用 Tableau 性能记录来排查问题原因。

如果是排序或视觉对象布局导致性能不佳，则问题可能出在仪表板尝试返回的标记数。  Azure Databricks 可以快速返回 1 百万条记录，但 Tableau 可能无法计算布局并对结果排序。 如果这是一个问题，请聚合查询并深化到更低的级别。 你还可以尝试更大的计算机，因为 Tableau 仅受运行它的计算机上的物理资源的限制。

若要了解 Tableau 中的向下钻取，请参阅[向下钻取详细信息](https://help.tableau.com/current/guides/get-started-tutorial/en-us/get-started-tutorial-drilldown.htm)。

通常情况下，如果看到很多粒度标记，则表明分析模式不太好，因为它不提供见解。 从更高的聚合级别向下钻取会更有意义，可减少需要处理和可视化的记录数。

#### <a name="use-actions-to-optimize-dashboards"></a>使用操作来优化仪表板

若要从组钻取到段再钻取到项目以获取与进行详尽分析的仪表板相同的分析和信息，可以使用 Tableau 操作。 操作时可以单击一个标记（例如，地图上的状态），将其发送到另一个仪表板，该仪表板会根据你单击的状态进行筛选。 这可以减少在一个仪表板上使用多个筛选器的需求，以及减少需要生成的记录数，因为你可以设置一个操作，使其在获取用作筛选标准的谓词之前不会生成记录。

有关详细信息，请参阅[操作](https://help.tableau.com/current/pro/desktop/en-us/actions.htm)和[提高仪表板性能的 6 大诀窍](https://www.tableau.com/about/blog/2016/1/5-tips-make-your-dashboards-more-performant-48574)。

### <a name="caching"></a>缓存

缓存数据是提高工作表或仪表板性能的好办法。

#### <a name="caching-in-tableau"></a>Tableau 中的缓存

Tableau 在返回到数据之前有四层缓存，无论该数据是在实时连接中还是在提取中：

* **磁贴**：如果有人在加载完全相同的仪表板，不进行任何更改，则 Tableau 会尝试对图表重复使用相同的磁贴。 这类似于 Google Maps 磁贴。
* **模型**：无法使用磁贴时，可以通过数学计算生成可视化效果。 Tableau Server 会尝试使用相同的模型。
* **抽象**：查询的聚合结果也会进行存储。 这是第三个“防御”级别。 如果以前的某个查询返回 Sum(Sales)、Count(orders)、Sum(Cost)，而未来的某个查询只需要 Sum(Sales)，则 Tableau 会获取该结果并使用它。
* **本机缓存**：如果查询与另一个查询完全相同，则 Tableau 会使用相同的结果。 这是最后一级别的缓存。 如果此操作失败，Tableau 会转到数据。

##### <a name="caching-frequency"></a>缓存频率

Tableau 的管理设置可用于管理缓存频率。 如果将服务器设置为“降低刷新频率”，则 Tableau 会将数据保留在缓存中长达 12 小时。 如果将其设置为“提高刷新频率”，则 Tableau 会在每次刷新页面时返回到数据。

如果客户需要反复使用同一仪表板（例如，“周一早晨的管道报表”），则应将其所在的服务器设置为“降低刷新频率”，使仪表板全都使用同一缓存。

##### <a name="cache-warming"></a>缓存预热

可以在 Tableau 中预热缓存，方法是：设置要在查看仪表板之前发送的仪表板订阅。
这是因为，需要先呈现面板，然后才能为订阅电子邮件生成图像。 请参阅[使用订阅预热 Tableau Server 缓存](https://kb.tableau.com/articles/HowTo/warming-the-tableau-server-cache-using-subscriptions)。

#### <a name="caching-in-azure-databricks"></a>Azure Databricks 中的缓存

可以在用户使用 Tableau 仪表板之前缓存常用表或关键表，以减少 Azure Databricks 将结果返回到 Tableau 所用的时间。

可以在早上运行脚本，以便对那些在已进行缓存优化的虚拟机上进行 [Delta 缓存](../../delta/optimizations/delta-cache.md#use-delta-caching)的特定表执行 `SELECT CACHE`。

甚至可以缓存 Azure Databricks 从驱动程序获取的特定查询。