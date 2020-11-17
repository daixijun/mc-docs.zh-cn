---
title: 即将推出的对 Azure 安全中心的重要更改
description: 即将推出的对 Azure 安全中心的更改，你可能需要了解这些更改并针对这些更改做好计划
services: security-center
documentationcenter: na
author: Johnnytechn
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2020
ms.author: v-johya
ms.openlocfilehash: 43a088a5ca7036e48a1fcf9ffad9ed9d413caff3
ms.sourcegitcommit: d30cf549af09446944d98e4bd274f52219e90583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94638269"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>与要添加的 Azure 安全基准相关的建议（预览版）

| 方面 | 详细信息 |
|---------|---------|
|公布日期 | 2020 年 10 月 26 日  |
|进行此更改的日期  |  2020 年 11 月 |
|影响     | 可能需要查看更多建议。<br>不会立即对安全功能分数产生影响 - 预览版建议不会影响安全功能分数。<br>不会立即对资源的运行状态产生影响 - 预览版建议不会显示资源“运行不正常”。|
|  |  |

Azure 安全基准是由 Microsoft 创作的特定于 Azure 的一组准则，适用于基于常见合规框架的安全与合规最佳做法。 [详细了解 Azure 安全基准](../security/benchmarks/introduction.md)。

以下 29 条新建议将添加到安全中心，以扩大基准的覆盖范围。

预览版建议不会显示资源运行不正常，并且在计算安全功能分数时不会包含这些建议。 请尽量修正这些建议，以便在预览期结束之后，借助这些建议提高安全功能分数。 如需详细了解如何响应这些建议，请参阅[修正 Azure 安全中心的建议](security-center-remediate-recommendations.md)。

- 应为虚拟机启用 Azure 备份
- 应将 SQL Server 的审核保留设置为至少 90 天
- 应在应用服务中启用诊断日志 
- 应为 MySQL 数据库服务器启用“强制 SSL 连接”
- 应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”
- 应仅在 API 应用中要求使用 FTPS
- 应仅在函数应用中要求使用 FTPS
- 应仅在 Web 应用中要求使用 FTPS
- 应为 Azure Database for MariaDB 启用异地冗余备份
- 应为 Azure Database for MySQL 启用异地冗余备份
- 应为 Azure Database for PostgreSQL 启用异地冗余备份
- 应将 Java 更新为 API 应用的最新版本
- 应将 Java 更新为函数应用的最新版本
- 应将 Java 更新为 Web 应用的最新版本
- 应在 API 应用中使用托管标识
- 应在函数应用中使用托管标识
- 应在 Web 应用中使用托管标识
- 应将 PHP 更新为 API 应用的最新版本
- 应将 PHP 更新为 Web 应用的最新版本
- 应为 MariaDB 服务器启用专用终结点
- 应为 MySQL 服务器启用专用终结点
- 应为 PostgreSQL 服务器启用专用终结点
- 应将 Python 更新为 API 应用的最新版本
- 应将 Python 更新为函数应用的最新版本
- 应将 Python 更新为 Web 应用的最新版本
- 应将 TLS 更新为 API 应用的最新版本
- 应将 TLS 更新为函数应用的最新版本
- 应将 TLS 更新为 Web 应用的最新版本
- Web 应用应请求一个用于所有传入请求的 SSL 证书

相关链接：

- [详细了解 Azure 安全基准](../security/benchmarks/introduction.md)
- [详细了解 Azure API 应用](../app-service/app-service-web-tutorial-rest-api.md)
- [详细了解 Azure 函数应用](../azure-functions/functions-overview.md)
- [详细了解 Azure Web 应用](../app-service/overview.md)
- [详细了解 Azure Database for MariaDB](../mariadb/overview.md)
- [详细了解 Azure Database for MySQL](../mysql/overview.md)
- [详细了解 Azure Database for PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>后续步骤

有关产品的所有最新更改，请参阅 [Azure 安全中心的新增功能](release-notes.md)。

