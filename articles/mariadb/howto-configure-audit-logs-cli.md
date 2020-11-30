---
title: 访问审核日志 - Azure CLI - Azure Database for MariaDB
description: 本文介绍如何从 Azure CLI 配置和访问 Azure Database for MariaDB 中的审核日志。
author: WenJason
ms.author: v-jay
ms.service: mariadb
ms.topic: how-to
origin.date: 6/24/2020
ms.date: 11/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 26e9a65859d367e284122bd162bb450f5907908e
ms.sourcegitcommit: db15d6cc591211c0e531d636f45e9cbe24cfb15b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94908976"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>在 Azure CLI 中配置和访问 Azure Database for Maria DB 审核日志

可以从 Azure CLI 配置 [Azure Database for MariaDB 审核日志](concepts-audit-logs.md)。

## <a name="prerequisites"></a>先决条件

若要完成本指南：

- 需要 [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

> [!IMPORTANT]
> 本操作方法指南要求使用 Azure CLI 版本 2.0 或更高版本。 若要确认版本，请在 Azure CLI 命令提示符下输入 `az --version`。 若要安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。

## <a name="configure-audit-logging"></a>配置审核日志记录

>[!IMPORTANT]
> 建议仅记录审核所需的事件类型和用户，以确保服务器的性能不会受到严重影响。

使用以下步骤启用和配置审核日志记录： 

1. 通过将“audit_logs_enabled”参数设为“ON”来启用审核日志。 
    ```azurecli
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. 通过更新 **audit_log_events** 参数，选择要记录的 [事件类型](concepts-audit-logs.md#configure-audit-logging)。
    ```azurecli
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. 通过更新 **audit_log_exclude_users** 参数添加要从日志记录中排除的 MariaDB 用户。 通过提供 MariaDB 用户名来指定用户。
    ```azurecli
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. 通过更新“audit_log_include_users”参数，添加要包括在日志中的任何特定 MariaDB 用户。 通过提供 MariaDB 用户名来指定用户。
    ```azurecli
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure Database for MariaDB 中的[审核日志](concepts-audit-logs.md)
- 了解如何在 [Azure 门户](howto-configure-audit-logs-portal.md)中配置审核日志