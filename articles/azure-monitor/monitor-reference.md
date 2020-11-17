---
title: Azure Monitor 监视哪些内容
description: 有关 Azure Monitor 监视的所有服务和其他资源的参考信息。
ms.subservice: ''
ms.topic: conceptual
author: Johnnytechn
ms.author: v-johya
ms.date: 11/02/2020
ms.openlocfilehash: d4239d5cb547a75d0c6bf9721ed4a1c1e1157076
ms.sourcegitcommit: d30cf549af09446944d98e4bd274f52219e90583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94638219"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure Monitor 监视哪些内容？
本文介绍了由 Azure Monitor 监视的不同应用程序和服务。 

## <a name="insights-and-core-solutions"></a>见解和核心解决方案
核心见解和解决方案被视为 Azure Monitor 的一部分，并遵循 Azure 的支持和服务级别协议。 它们在可使用 Azure Monitor 的所有 Azure 区域中均受支持。

### <a name="insights"></a>洞察力

见解为特定的应用程序和服务提供自定义监视体验。 它们收集和分析日志和指标。

| 见解 | 说明 |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 可扩展应用程序性能管理 (APM) 服务，用于监视任何平台上的实时 web 应用程序。 |
| [用于容器的 Azure Monitor](insights/container-insights-overview.md) | 监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 |
| [用于 Cosmos DB 的 Azure Monitor](insights/cosmosdb-insights-overview.md) | 提供了关于所有 Azure Cosmos DB 资源的整体性能、故障、容量和运行状况的统一交互式体验视图。 |
| [用于网络的 Azure Monitor（预览版）](insights/network-insights-overview.md) | 提供所有网络资源的运行状况和指标的综合视图。 高级搜索功能通过便捷搜索网站名称，帮助你识别资源依赖关系，从而能够支持多种方案，比如识别用于托管网站的资源等。 |
[用于资源组的 Azure Monitor（预览版）](insights/resource-group-insights.md) |  分类和诊断各资源出现的任何问题，同时提供有关资源组的运行状况和性能的整体上下文。 |
| [用于存储的 Azure Monitor](insights/storage-insights-overview.md) | 通过提供 Azure 存储服务性能、容量和可用性的统一视图，提供对 Azure 存储帐户的全面监视。 |
| [Key Vault 的 Azure Monitor（预览版）](./insights/key-vault-insights-overview.md) | 提供 Key Vault 请求、性能、失败和延迟的统一视图，在其中可以全面监视密钥保管库。 |
| [适用于 Azure Cache for Redis 的 Azure Monitor（预览版）](insights/redis-cache-insights-overview.md) |  提供有关整体性能、故障、容量和运行状况的统一交互式视图。 |


### <a name="core-solutions"></a>核心解决方案

解决方案基于为特定应用程序或服务自定义的日志查询和视图。 它们只收集和分析日志，并随着时间推移而不再被使用，取而代之的是见解。



## <a name="azure-services"></a>Azure 服务
下表列出了 Azure 服务及其收集到 Azure Monitor 中的数据。 

- 指标 - 服务自动将指标收集到 Azure Monitor 指标中。 
- 日志 - 该服务支持诊断设置，可将平台日志和指标收集到 Azure Monitor 日志。
- 见解 - 有一个可用于服务的见解，它为服务提供自定义的监视体验。

| 服务 | 指标 | 日志 | 见解 | 说明 |
|:---|:---|:---|:---|:---|
|Active Directory | 否 | 是 | [是](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | 否 | 否 | 否 |  |
|Active Directory 域服务 | No | 是 | 否 |  |
|活动日志 | No | 是 | 否 | |
|高级威胁防护 | 否 | 否 | 否 |  |
|顾问 | 否 | 否 | 否 |  |
|AI Builder | 否 | 否 | 否 |  |
|Analysis Services | 是 | 是 | 否 |  |
|API for FHIR | 否 | 否 | 否 |  |
|API 管理 | 是 | 是 | 否 |  |
|应用服务 | 是 | 是 | 否 |  |
|AppConfig | 否 | 否 | 否 |  |
|应用程序网关 | 是 | 是 | 否 |  |
|证明服务 | 否 | 否 | 否 |  |
|自动化 | 是 | 是 | 否 |  |
|Azure 服务管理器 (RDFE) | 否 | 否 | 否 |  |
|备份 | 否 | 是 | 否 |  |
|Bastion | 否 | 否 | 否 |  |
|Batch | 是 | 是 | 否 |  |
|Batch AI | 否 | 否 | 否 |  |
|区块链服务 | 否 | 是 | 否 |  |
|蓝图 | 否 | 否 | 否 |  |
|Bot 服务 | 否 | 否 | 否 |  |
|云服务 | 是 | 是 | 否 | 监视来宾操作系统和工作流时所需的代理。  |
|认知服务 | 是 | 是 | 否 |  |
|容器实例 | 是 | 否 | 否 |  |
|容器注册表 | 是 | 是 | 否 |  |
|内容分发网络 (CDN) | 否 | 是 | 否 |  |
|Cosmos DB | 是 | 是 | [是](insights/cosmosdb-insights-overview.md) |  |
|成本管理 | 否 | 否 | 否 |  |
|Data Box | 否 | 否 | 否 |  |
|数据目录 Gen2 | 否 | 否 | 否 |  |
|数据资源管理器 | 是 | 是 | 否 |  |
|数据工厂 | 是 | 是 | 否 |  |
|数据工厂 v2 | 否 | 是 | 否 |  |
|数据共享 | 否 | 否 | 否 |  |
|Database for MariaDB | 是 | 是 | 否 |  |
|Database for MySQL | 是 | 是 | 否 |  |
|Database for PostgreSQL | 是 | 是 | 否 |  |
|数据库迁移服务 | 否 | 否 | 否 |  |
|Databricks | 否 | 是 | 否 |  |
|DDoS 保护 | 是 | 是 | 否 |  |
|DevOps | 否 | 否 | 否 |  |
|DNS | 是 | 否 | 否 |  |
|域名 | 否 | 否 | 否 |  |
|DPS | 否 | 否 | 否 |  |
|Dynamics 365 Customer Engagement | 否 | 否 | 否 |  |
|Dynamics 365 Finance and Operations | 否 | 否 | 否 |  |
|事件网格 | 是 | 否 | 否 |  |
|事件中心 | 是 | 是 | 否 |  |
|ExpressRoute | 是 | 是 | 否 |  |
|防火墙 | 是 | 是 | 否 |  |
|Front Door | 是 | 是 | 否 |  |
|函数 | 是 | 是 | 否 |  |
|HDInsight | 否 | 是 | 否 |  |
|HPC 缓存 | 否 | 否 | 否 |  |
|信息保护 | 否 | 是 | 否 |  |
|Intune | 否 | 是 | 否 |  |
|IoT Central | 否 | 否 | 否 |  |
|IoT 中心 | 是 | 是 | 否 |  |
|Key Vault | 是 | 是 | [是](./insights/key-vault-insights-overview.md) |  |
|Kubernetes 服务 (AKS) | 否 | 否 | [是](insights/container-insights-overview.md)  |  |
|负载均衡器 | 是 | 否 | 否 |  |
|逻辑应用 | 是 | 是 | 否 |  |
|机器学习服务 | 否 | 否 | 否 |  |
|托管应用程序  | 否 | 否 | 否 |  |
|地图  | 否 | 否 | 否 |  |
|媒体服务 | 是 | 是 | 否 |  |
|Microsoft Flow | 否 | 否 | 否 |  |
|Microsoft 托管桌面 | 否 | 否 | 否 |  |
|Microsoft PowerApps | 否 | 否 | 否 |  |
|Microsoft 社交参与 | 否 | 否 | 否 |  |
|Microsoft Stream | 是 | 是 | 否 |  |
|迁移 | 否 | 否 | 否 |  |
|多重身份验证 | 否 | 是 | 否 |  |
|网络观察程序 | 是 | 是 | 否 |  |
|通知中心 | 是 | 否 | 否 |  |
|开放数据集 | 否 | 否 | 否 |  |
|策略 | 否 | 否 | 否 |  |
|Power BI Embedded | 是 | 是 | 否 |  |
|专用链接 | 否 | 否 | 否 |  |
|项目假脱机通信平台 | 否 | 否 | 否 |  |
|Red Hat OpenShift | 否 | 否 | 否 |  |
|Redis 缓存 | 是 | 是 | [是](insights/redis-cache-insights-overview.md) | |
|Resource Graph | 否 | 否 | 否 |  |
|资源管理器 | 否 | 否 | 否 |  |
|零售搜索 - 通过必应 | 否 | 否 | 否 |  |
|搜索 | 是 | 是 | 否 |  |
|服务总线 | 是 | 是 | 否 |  |
|Service Fabric | 否 | 是 | 否 | 监视来宾操作系统和工作流时所需的代理。  |
|注册门户 | 否 | 否 | 否 |  |
|Site Recovery | 否 | 是 | 否 |  |
|Spring Cloud 服务 | 否 | 否 | 否 |  |
|Azure Synapse Analytics | 是 | 是 | 否 |  |
|SQL 数据库 | 是 | 是 | 否 |  |
|SQL Server Stretch Database | 是 | 是 | 否 |  |
|堆栈 | 否 | 否 | 否 |  |
|存储 | 是 | 否 | [是](insights/storage-insights-overview.md) |  |
|存储缓存 | 否 | 否 | 否 |  |
|存储同步服务 | 否 | 否 | 否 |  |
|流分析 | 是 | 是 | 否 |  |
|时序见解 | 是 | 是 | 否 |  |
|TINA | 否 | 否 | 否 |  |
|流量管理器 | 是 | 是 | 否 |  |
|通用打印 | 否 | 否 | 否 |  |
|虚拟网络 | 是 | 是 | [是](insights/network-insights-overview.md) |  |
|虚拟网络 - NSG 流日志 | 否 | 是 | 否 |  |
|VPN 网关 | 是 | 是 | 否 |  |
|Windows 虚拟桌面 | 否 | 否 | 否 |  |

## <a name="product-integrations"></a>产品集成
下表中的服务和解决方案将其数据存储在 Log Analytics 工作区中，以便可以将这些数据与使用 Azure Monitor 收集的其他日志数据一起进行分析。

| 产品/服务 | 说明 |
|:---|:---|
| [Azure 自动化](../automation/index.yml) |[更新管理](../automation/update-management/update-mgmt-overview.md)。 |
| [Azure 安全中心](../security-center/index.yml) | 收集和分析安全事件并执行威胁分析。 请参阅 [Azure 安全中心中的数据收集](../security-center/security-center-enable-data-collection.md) |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | 创建诊断设置以将日志发送到 Azure Monitor。 请参阅[将日志数据发送到 Intune 中的存储、事件中心或日志分析（预览版）](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)。  |
| 网络  | [网络性能监视器](insights/network-performance-monitor.md) - 监视与服务和应用程序终结点的网络连接和其性能。<br>[流量分析](../network-watcher/traffic-analytics.md) - 可以分析网络观察程序网络安全组 (NSG) 流日志，帮助洞察 Azure 云中的流量流。 |
| [Microsoft 团队聊天室](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Microsoft 团队聊天室设备的集成、端到端管理。 |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | 构建、测试和分发应用程序，然后监视它们的状态和使用情况。 请参阅[开始使用 App Center 和 Application Insights 分析移动应用](learn/mobile-center-quickstart.md)。 |
| Windows | [Windows 更新符合性](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) - 评估 Windows 桌面升级。<br>[桌面分析](https://docs.microsoft.com/configmgr/desktop-analytics/overview) - 与配置管理器集成，提供见解和情报，帮助你制定关于 Windows 客户端更新就绪性的更明智决策。 |
<!--Customized in MC-->



## <a name="other-solutions"></a>其他解决方案
有可用于监视其他应用程序和服务的其他解决方案，但当前没有相关开发处于活动状态，并且不一定能所有区域中使用。 Azure Log Analytics 数据引入服务级别协议中涵盖了这些解决方案。

| 解决方案 | 说明 |
|:---|:---|
| [活动日志分析](platform/activity-log.md#activity-log-analytics-monitoring-solution) | 查看活动日志条目。 |
| [容器](insights/containers.md) | 查看和管理 Docker 和 Windows 容器主机。 |
| [按需评估](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | 评估和优化本地、混合和云 Microsoft 技术环境的可用性、安全性和性能。 |

<!--Not available in MC: ITSM-->
## <a name="resources-outside-of-azure"></a>Azure 以外的资源
Azure Monitor 可以使用下表中列出的方法从 Azure 以外的资源收集数据。

| 资源 | 方法 |
|:---|:---|
| 应用程序 | 使用 Application Insights 监视 Azure 以外的 web 应用程序。 请参阅[什么是 Application Insights？](./app/app-insights-overview.md)。 |
| 虚拟机 | 使用代理从其他云环境中的或本地的虚拟机的来宾操作系统收集数据。 请参阅 [Azure Monitor 代理概述](platform/agents-overview.md)。 |
| REST API 客户端 | 可使用单独的 API 将数据从任何 REST API 客户端写入 Azure Monitor 日志和指标。 请参阅[使用 HTTP 数据收集器 API 将日志数据发送到 Azure Monitor](platform/data-collector-api.md) 以获取日志。 |



## <a name="next-steps"></a>后续步骤

- 阅读和深入了解[存储由见解和解决方案收集的日志和指标的 Azure Monitor数据平台](platform/data-platform.md)。
- 完成[有关监视 Azure 资源的教程](learn/tutorial-resource-logs.md)。
- 完成[编写日志查询以分析 Azure Monitor 日志中的数据的相关教程](learn/tutorial-resource-logs.md)。
- 完成[创建指标图表以分析 Azure Monitor 指标中的数据的相关教程](learn/tutorial-metrics-explorer.md)。

 

