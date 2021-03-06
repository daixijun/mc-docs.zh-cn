---
title: 重启服务器 - Azure CLI - Azure Database for MySQL
description: 本文介绍了如何使用 Azure CLI 重启 Azure Database for MySQL 服务器。
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: how-to
origin.date: 3/28/2020
ms.date: 11/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 402d7b0a856761ba692da628d74ede2948b6aa45
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94977327"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>使用 Azure CLI 重启 Azure Database for MySQL 服务器
本主题介绍如何重启 Azure Database for MySQL 服务器。 出于维护原因，可能需要重启服务器，这会在服务器执行操作时导致短暂中断。

如果服务处于繁忙状态，则会阻止重启服务器。 例如，服务可以处理缩放 vCores 等先前请求的操作。

完成重启所需的时间取决于 MySQL 恢复过程。 若要减少重启时间，建议在重启之前尽量减少服务器上发生的活动量。

## <a name="prerequisites"></a>必备条件

若要完成本操作说明指南：

- 需要 [Azure Database for MySQL 服务器](quickstart-create-server-up-azure-cli.md)。
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 

## <a name="restart-the-server"></a>重启服务器

使用以下命令重启服务器：

```azurecli
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤

了解[如何在 Azure Database for MySQL 中设置参数](howto-configure-server-parameters-using-cli.md)