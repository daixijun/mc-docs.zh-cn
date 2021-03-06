---
title: 概述 - Azure Database for MySQL 单一服务器
description: 了解 Azure Database for MySQL 单一服务器，这是 Microsoft 云中基于 MySQL 社区版的关系数据库服务。
author: WenJason
ms.service: mysql
ms.author: v-jay
ms.custom: mvc
ms.topic: overview
origin.date: 8/20/2020
ms.date: 11/09/2020
ms.openlocfilehash: 019869395f9ae6bf24405fb133c3885b16d92d16
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94328536"
---
# <a name="azure-database-for-mysql-single-server"></a>Azure Database for MySQL 单一服务器

本文概述和介绍了单一服务器部署模型的核心概念。

## <a name="overview"></a>概述

单一服务器是一项完全托管的数据库服务，对数据库自定义的要求最低。 单一服务器平台旨在以最少的用户配置和控制来处理大多数数据库管理功能，例如修补、备份、高可用性、安全性。 此体系结构经过优化，可在单个区域提供 99.99% 的可用性。 它支持 MySQL 社区版 5.6、5.7 和 8.0。 目前，该服务已在各种 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?regions=china-non-regional,china-east,china-east-2,china-north,china-north-2&products=all)中正式发布。 

单一服务器最适合用于云原生应用程序，这些应用程序旨在处理自动修补，而无需对修补计划和自定义 MySQL 配置设置进行精细控制。 

## <a name="high-availability"></a>高可用性

单一服务器部署模型针对内置高可用性和弹性进行了优化，并且降低了成本。 该体系结构将计算和存储分开。 数据库引擎在专有的计算容器上运行，而数据文件位于 Azure 存储上。 该存储维护数据库文件的三个本地冗余同步副本，以确保数据持续性。 

在计划内或计划外的故障转移事件期间，如果服务器发生故障，该服务将使用以下自动化过程来维持服务器的高可用性：

1. 预配新的计算容器
2. 具有数据文件的存储映射到新的容器 
3. MySQL 数据库引擎在新的计算容器上处于联机状态
4. 网关服务可确保故障转移透明化，从而确保无需更改应用程序端。 
  
典型的故障转移时间范围为 60 - 120 秒。 单一服务器服务的云原生设计使其支持 99.99% 的可用性，从而消除了被动热备用服务器的成本。

Azure 行业领先的 99.99% 可用性服务级别协议 (SLA) 有助于保持应用程序全天候运行。

:::image type="content" source="media/single-server-overview/1-singleserverarchitecture.png" alt-text="Azure Database for MySQL 单一服务器体系结构概念图"::: 

## <a name="automated-patching"></a>自动修补 

该服务执行基础硬件、OS 和数据库引擎的自动修补。 修补包括安全更新和软件更新。 对于 MySQL 引擎，次要版本的升级自动进行，并作为修补周期的一部分包含在内。 无需任何用户操作或配置设置即可进行修补。 修补频率根据有效负载的重要性由服务管理。 一般来说，作为持续集成和发布的一部分，该服务遵循每月发布计划。 用户可以订阅[计划内维护通知](concepts-monitoring.md)，在维护开始前 72 小时接收有关该维护事件的通知。

## <a name="automatic-backups"></a>自动备份

单一服务器服务可自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 备份可用于将服务器还原到备份保持期内的任何时间点。 默认的备份保留期为七天。 保持期可选择配置为最多 35 天。 所有备份都使用 AES 256 位加密进行加密。 有关详细信息，请参阅[备份](concepts-backup.md)。

## <a name="adjust-performance-and-scale-within-seconds"></a>几秒钟内调整性能和规模

单一服务器服务在三个 SKU 层中提供：“基本”、“常规用途”和“内存优化”。 基本层最适合用于低成本开发和低并发工作负载。 常规用途和内存优化更适用于需要高并发性、缩放性和可预测性能的生产工作负载。 可以在一个月内花费很少的费用基于小型数据库构建第一个应用，然后根据解决方案的需求调整缩放。 存储缩放是联机的，支持存储自动增长。 动态可伸缩性使得数据库能够以透明方式对不断变化的资源需求做出响应。 只需为所使用的资源付费。 有关详细信息，请参阅[定价层](./concepts-pricing-tiers.md)。

## <a name="enterprise-grade-security-compliance-and-governance"></a>企业级安全性、合规性和治理

单一服务器服务使用 FIPS 140-2 验证的加密模块对静态数据进行存储加密。 在运行查询时创建的数据（包括备份）和临时文件都会进行加密。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥可由系统进行管理（默认）。 该服务使用默认实施的传输层安全性 (SSL/TLS) 对动态数据进行加密。 该服务支持 TLS 版本 1.2、1.1 和 1.0，并可强制执行[最低 TLS 版本](concepts-ssl-connection-security.md)。 

该服务允许使用[专用链接](concepts-data-access-security-private-link.md)对服务器进行专用访问。

除了本机身份验证外，单一服务器服务还支持 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 身份验证。 Azure AD 身份验证是一种使用 Azure AD 中定义和管理的标识连接到 MySQL 服务器的机制。 通过 Azure AD 身份验证，可以在一个中心位置集中管理数据库用户标识和其他 Azure 服务，从而简化和集中访问控制。

[审核日志记录](concepts-audit-logs.md)可用于跟踪所有数据库级别活动。 

单一服务器服务符合所有行业领先的认证（如 FedRAMP、HIPAA、PCI DSS）。 有关 Azure 平台安全性的信息，请访问 [Azure 信任中心](https://www.trustcenter.cn/)。 

有关 Azure Database for MySQL 安全功能的详细信息，请参阅[安全概述](concepts-security.md)。

## <a name="monitoring-and-alerting"></a>监视和警报

单一服务器服务配备了内置的性能监视和警报功能。 所有 Azure 指标的频率都是一分钟，每个指标提供 30 天的历史记录。 可针对指标配置警报。 该服务允许配置慢查询日志，并附带差异化的[查询存储](concepts-query-store.md)功能。 通过帮助快速查找运行时间最长且资源最密集的查询，查询存储可简化性能故障排除。 使用这些工具，可快速优化工作负载并配置服务器以获得最佳性能。 有关详细信息，请参阅[监视](concepts-monitoring.md)。

## <a name="migration"></a>迁移

该服务运行 MySQL 的社区版本。 这样可以实现完全的应用程序兼容性，并且只需最小的重构成本就能将在 MySQL 引擎上开发的现有应用程序迁移到单一服务器服务。 可以使用以下任一选项来执行向单一服务器的迁移：

- 转储和还原 - 对于脱机迁移（在此情况下，用户可承受一定的故障时间），使用社区工具（如 mysqldump/mydumper）的转储和还原可以提供最快的迁移方式。 有关详细信息，请参阅[使用转储和还原进行迁移](concepts-migrate-dump-restore.md)。 
- Azure 数据库迁移服务 - 为了在最短的故障时间内实现向单一服务器的无缝简化迁移，可以利用 [Azure 数据库迁移服务](../dms/tutorial-mysql-azure-mysql-online.md)。 
- **数据传入复制** - 为在最短的停机时间内进行迁移，还可以利用数据传入复制，它依靠基于 binlog 的复制。 对于希望对迁移进行更多控制的实操专业人员来说，数据传入复制是实现停机时间最短的迁移的首选。 有关详细信息，请参阅[数据传入复制](concepts-data-in-replication.md)。

## <a name="contacts"></a>联系人
- 若要联系 Azure 支持，请[从 Azure 门户提交票证](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要修复帐户问题，请提交[支持请求](https://support.azure.cn/support/support-azure/)。

## <a name="next-steps"></a>后续步骤
现在，你已阅读 Azure Database for MySQL 单一服务器部署模式简介，接下来可以：

- 创建你的第一个服务器。 
  - [使用 Azure 门户创建用于 MySQL 服务器的 Azure 数据库](quickstart-create-mysql-server-database-using-azure-portal.md)
  - [使用 Azure CLI 创建用于 MySQL 服务器的 Azure 数据库](quickstart-create-mysql-server-database-using-azure-cli.md)
  - [用于 MySQL 的 Azure 数据库的 Azure CLI 示例](sample-scripts-azure-cli.md)

- 使用首选语言生成首个应用：
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
