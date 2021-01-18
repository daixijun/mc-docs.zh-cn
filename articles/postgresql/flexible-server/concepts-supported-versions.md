---
title: 支持的版本 - Azure Database for PostgreSQL 灵活服务器
description: 介绍 Azure Database for PostgreSQL 灵活服务器中支持的 PostgreSQL 主版本和次要版本。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/16/2020
ms.date: 01/11/2021
ms.openlocfilehash: 61368d8b02758e40047615cc37c4737a23a36993
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023396"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 灵活服务器中支持的 PostgreSQL 主版本

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 灵活服务器目前支持以下主版本：

## <a name="postgresql-version-12"></a>PostgreSQL 版本 12

当前次要版本为 12.4。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/12/static/release-12-4.html)。

## <a name="postgresql-version-11"></a>PostgreSQL 版本 11

当前次要版本为 11.9。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/11/static/release-11-9.html)。

## <a name="postgresql-version-10-and-older"></a>PostgreSQL 版本 10 及更早版本

对于 Azure Database for PostgreSQL 灵活服务器，我们不支持 PostgreSQL 版本 10 及更早版本。 如果需要较旧版本，请使用[单一服务器](../concepts-supported-versions.md)部署选项。

## <a name="managing-upgrades"></a>管理升级

PostgreSQL 项目定期发布次要版本来修复已报告的 bug。 Azure Database for PostgreSQL 在服务的每月部署期间会自动使用次要版本修补服务器。

尚不支持自动主版本升级。 例如，当前没有从 PostgreSQL 11 到 PostgreSQL 12 的自动升级。<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->