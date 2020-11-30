---
title: 版本控制策略 - Azure Database for MySQL 单一服务器和灵活服务器（预览版）
description: 描述有关 Azure Database for MySQL 中 MySQL 主要版本和次要版本的策略
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: conceptual
origin.date: 11/03/2020
ms.date: 11/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 28f484bb345367fcdd09701222f2ff1494e6b21d
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978343"
---
# <a name="azure-database-for-mysql-versioning-policy"></a>Azure Database for MySQL 版本控制策略

此页面介绍 Azure Database for MySQL 版本控制策略。

## <a name="supported--mysql-versions"></a>支持的 MySQL 版本

Azure Database for MySQL 支持以下数据库版本。

| 版本 |
| ----- |
| MySQL 8 |
| MySQL 5.7 |
| MySQL 5.6|


## <a name="major-version-support"></a>主要版本支持
如[版本控制策略](https://www.mysql.com/support/eol-notice.html)中所述，从 Azure 开始支持该版本之日起，到 MySQL 社区停用该版本之日结束，在此期间，Azure Database for MySQL 将支持 MySQL 的每个主要版本。

## <a name="minor-version-support"></a>次要版本支持
在定期维护过程中，Azure Database for MySQL 自动执行次要版本升级到 Azure 首选的 MySQL 版本。 

## <a name="major-version-retirement-policy"></a>主要版本停用策略
下表提供了 MySQL 主要版本的停用详细信息。 日期遵循 [MySQL 版本控制策略](https://www.mysql.com/support/eol-notice.html)。

| 版本 | 新增功能 | Azure 支持开始日期 | 停用日期|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [功能](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 2018 年 3 月 20 日 | 2021 年 2 月
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [功能](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 2018 年 3 月 20 日 | 2023 年 10 月
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [功能](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html) | 2019 年 12 月 11 日 | 2026 年 4 月


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Azure Database for MySQL 不支持已停用的 MySQL 引擎版本

在停用每个 MySQL 数据库版本之后，如果继续运行已停用版本，需要注意以下限制：
- 由于社区将不会发布任何进一步的 bug 修复或安全修复，Azure Database for MySQL 将不会针对任何 bug 或安全问题修补已停用的数据库引擎，也不会针对已停用的数据库引擎采取安全防护措施。 但是，Azure 将继续对主机、OS、容器以及任何其他与服务相关的组件执行定期维护和修补。
- 如果可能遇到的任何支持问题与 MySQL 数据库有关，我们可能无法为你提供支持。 在这种情况下，必须升级数据库，以便我们为你提供任何支持。
- 无法为已停用的版本创建新的数据库服务器。 但能够执行时间点恢复并为现有服务器创建只读副本。
- Azure Database for MySQL 开发的新服务功能可能仅适用于受支持的数据库服务器版本。
- 运行时间 SLA 仅适用于与 Azure Database for MySQL 服务相关的问题，而不适用于与数据库引擎相关的 bug 导致的任何故障时间。  
- 在极端情况下，如果已停用的数据库版本中识别的 MySQL 数据库引擎漏洞对服务造成严重威胁，Azure 可能会选择停止数据库服务器的计算节点，以首先保护服务。 系统将要求你在使服务器联机之前升级服务器。 在升级过程中，将始终使用在服务上执行的自动备份来保护数据，该备份可用于按需还原到旧版本。 



## <a name="next-steps"></a>后续步骤
- 请参阅 Azure Database for MySQL 单一服务器[支持的版本](./concepts-supported-versions.md)
- 若要执行升级，请参阅 MySQL [转储和还原](./concepts-migrate-dump-restore.md)。
