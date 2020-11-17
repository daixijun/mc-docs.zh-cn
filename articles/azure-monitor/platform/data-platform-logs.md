---
title: Azure Monitor 中的日志 | Azure Docs
description: 介绍 Azure Monitor 中用于对监视数据进行高级分析的日志。
documentationcenter: ''
author: Johnnytechn
ms.topic: conceptual
ms.tgt_pltfrm: na
origin.date: 03/26/2019
ms.date: 11/02/2020
ms.author: v-johya
ms.openlocfilehash: bcf3d69660f63fab5abfd333e52242947643b4cd
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94327548"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor 日志概览
Azure Monitor 日志是 Azure Monitor 的一项功能，用于收集和组织来自各种源的日志数据，并使其可用于使用复杂的查询语言进行分析。 可以将来自不同源的数据合并到一个工作区中并一起进行分析，以执行诸如任务和趋势分析、警报和可视化等操作。

## <a name="relationship-to-azure-monitor-metrics"></a>与 Azure Monitor 指标的关系
Azure Monitor 指标将数值数据存储在时序数据库中，使这些数据比 Azure Monitor 日志更为轻量，并且能够支持准实时场景，因此特别适用于对问题发出警报并快速进行检测。 不过，指标只能将数值数据存储在特定的结构中，而日志可以存储各种不同的数据类型，每个数据类型都有自己的结构。 还可使用日志查询对日志数据执行复杂的分析，只是无法分析指标数据。

除指标之外，数值数据通常从数据源发送到日志。 虽然在日志中收集和保留此数据会产生额外费用，但这使你可以在日志查询中包含指标数据，并将其与其他监视数据一起进行分析。

## <a name="relationship-to-azure-data-explorer"></a>与 Azure 数据资源管理器的关系
Azure Monitor 日志基于 Azure 数据资源管理器。 Log Analytics 工作区大致相当于 Azure 数据资源管理器中的数据库，表的结构相同，并且都使用相同的 Kusto 查询语言 (KQL)。 在 Azure 门户中使用 Log Analytics 来处理 Azure Monitor 查询的体验与使用 Azure 数据资源管理器 Web UI 的体验类似。 你甚至可以[将来自 Log Analytics 工作区的数据包含在 Azure 数据资源管理器查询中](/data-explorer/query-monitor-data)。 


## <a name="structure-of-data"></a>数据的结构
由 Azure Monitor 日志收集的数据存储在 [Log Analytics 工作区](./design-logs-deployment.md)中，该工作区包含多个表，每个表都存储来自特定源的数据。 工作区定义数据的地理位置、访问权限（定义哪些用户可以访问数据），以及配置设置（例如定价层和数据保留）。 可以对所有监视数据使用一个工作区，也可以根据需要创建多个工作区。 有关创建多个工作区的注意事项，请参阅[设计 Azure Monitor 日志部署](design-logs-deployment.md)。

每个工作区都包含多个表，这些表被组织成具有多行数据的单独列。 每个表由一组唯一的列定义，这些列由数据源提供的数据行共享。 

[![Azure Monitor 日志结构](./media/data-platform-logs/logs-structure.png)](./media/data-platform-logs/logs-structure.png#lightbox)


来自 Application Insights 的日志数据也存储在 Azure Monitor 日志中，但根据应用程序的配置方式不同，存储的方式也不同。 对于基于工作区的应用程序，数据存储在 Log Analytics 工作区中的一组标准表中，以保存诸如应用程序请求、异常和页面视图之类的数据。 多个应用程序可以使用同一个工作区。 对于经典应用程序，数据不会存储在 Log Analytics 工作区中。 它使用相同的查询语言，并且你可以在 Azure 门户中使用相同的 Log Analytics 工具创建和运行查询。 不过，经典应用程序的数据是分开存储的。 
<!--Not available in MC: workspace-based -->
## <a name="log-queries"></a>日志查询
可使用[日志查询](../log-query/log-query-overview.md)从 Log Analytics 工作区检索数据，该查询是对处理数据和返回结果的只读请求。 日志查询采用 [Kusto 查询语言 (KQL)](/data-explorer/kusto/query/) 编写，这种语言是 Azure 数据资源管理器使用的查询语言。 使用 Log Analytics，这是 Azure 门户中的一个工具，用于编辑和运行日志查询并交互式地分析其结果。 然后，你可以使用你创建的查询来支持 Azure Monitor 中的其他功能，例如日志查询警报和工作簿。


## <a name="sources-of-data-for-azure-monitor-logs"></a>Azure Monitor 日志的数据源
Azure Monitor 从各种源收集日志数据，包括 Azure Monitor 中的资源和在虚拟机中运行的代理。 有关将数据发送到 Log Analytics 工作区的数据源的完整列表，请参阅 [Azure Monitor 监视哪些内容？](../monitor-reference.md)。



## <a name="next-steps"></a>后续步骤

- 了解[日志查询](../log-query/log-query-overview.md)，以从 Log Analytics 工作区检索和分析数据。
- 了解 [Azure Monitor 中的指标](data-platform-metrics.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。


