---
title: 重启服务器 - Azure PowerShell - Azure Database for PostgreSQL
description: 本文介绍了如何使用 PowerShell 重启 Azure Database for PostgreSQL 服务器。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: how-to
origin.date: 06/08/2020
ms.date: 01/11/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 092158f44005abb6beaf4846d835e7584f2b0113
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022570"
---
# <a name="restart-azure-database-for-postgresql-server-using-powershell"></a>使用 PowerShell 重启 Azure Database for PostgreSQL 服务器

本主题介绍如何重启 Azure Database for PostgreSQL 服务器。 出于维护原因，可能需要重启服务器，这会在操作期间导致短暂中断。

如果服务处于繁忙状态，则会阻止重启服务器。 例如，服务可能正在处理先前请求的操作（例如缩放 vCore）。

> [!NOTE] 
> 完成重启所需的时间取决于 PostgreSQL 恢复过程。 若要减少重启时间，建议在重启之前尽量减少服务器上发生的活动量。 你可能还需要增加检查点频率。 还可以调整与检查点相关的参数值，包括 `max_wal_size`。 还建议在重启服务器之前运行 `CHECKPOINT` 命令。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要：

- 在本地安装了 [Az PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)
- [Azure Database for PostgreSQL 服务器](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 尽管 Az.PostgreSql PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.PostgreSql -AllowPrerelease`。

使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。

## <a name="restart-the-server"></a>重启服务器

使用以下命令重启服务器：

```azurepowershell
Restart-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 创建 Azure Database for PostgreSQL 服务器](quickstart-create-postgresql-server-database-using-azure-powershell.md)
