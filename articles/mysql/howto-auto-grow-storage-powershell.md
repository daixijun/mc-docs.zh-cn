---
title: 自动增长存储 - Azure PowerShell - Azure Database for MySQL
description: 本文介绍如何使用 PowerShell 在 Azure Database for MySQL 中启用自动增长存储。
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: how-to
origin.date: 4/28/2020
ms.date: 11/09/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bd675d5d88d54fdd7ae0a24be61822405fc663f8
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94327809"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>使用 PowerShell 实现 Azure Database for MySQL 服务器存储的自动增长

本文介绍如何将 Azure Database for MySQL 服务器存储配置为在不影响工作负荷的情况下增长。

存储自动增长可防止服务器[达到存储限制](./concepts-pricing-tiers.md#reaching-the-storage-limit)并变为只读。 对于预配存储为 100 GB 或更低的服务器，当可用空间低于 10% 时，大小将增加 5 GB。 对于预配存储超过 100 GB 的服务器，当可用空间低于 10 GB 时，大小将增加 5%。 最大存储限制的应用方式遵循 [Azure Database for MySQL 定价层](./concepts-pricing-tiers.md#storage)的存储部分内容中所指定的方式。

> [!IMPORTANT]
> 请记住，存储只能增加，不能减少。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装了 [Az PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az.MySql PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.MySql -AllowPrerelease`。
> Az.MySql PowerShell 模块正式版推出后，它会包含在将来的 Az PowerShell 模块发行版中。

使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。

## <a name="enable-mysql-server-storage-auto-grow"></a>启用 MySQL 服务器存储自动增长

使用以下命令在现有服务器上启用服务器自动增长存储：

```azurepowershell
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

使用以下命令创建新服务器时启用服务器自动增长存储：

```azurepowershell
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location chinaeast2 -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何使用 PowerShell 在 Azure Database for MySQL 中创建和管理只读副本](howto-read-replicas-powershell.md)。
