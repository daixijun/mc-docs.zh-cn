---
title: 重启服务器 - Azure 门户 - Azure Database for MySQL 灵活服务器
description: 本文介绍了如何使用 Azure 门户重启 Azure Database for MySQL 灵活服务器。
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: how-to
origin.date: 10/26/2020
ms.date: 01/11/2021
ms.openlocfilehash: 8c84aade895dfd73479250f36f5bc9838d4c03e6
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023647"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>使用 Azure 门户重启 Azure Database for MySQL 灵活服务器
本主题介绍如何重启 Azure Database for MySQL 灵活服务器。 出于维护原因，可能需要重启服务器，这会在服务器执行操作时导致短暂中断。

如果服务处于繁忙状态，则会阻止重启服务器。 例如，服务可以处理缩放 vCores 等先前请求的操作。

完成重启所需的时间取决于 MySQL 恢复过程。 若要减少重启时间，建议在重启之前尽量减少服务器上发生的活动量。

## <a name="prerequisites"></a>先决条件
若要完成本操作指南，需要：
- [Azure Database for MySQL 灵活服务器](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>执行服务器重启

可通过以下步骤重启 MySQL 服务器：

1. 在 Azure 门户中，选择 Azure Database for MySQL 灵活服务器。

2. 在服务器“概述”页的工具栏中，单击“重启”。

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL -“概述”-“重启”按钮":::

3. 单击“是”以确认重启服务器。

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL - 重启确认":::

4. 观察到服务器状态更改为“正在重启”。

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL - 重启状态":::

5. 确认服务器重启成功。

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL - 重启成功":::

## <a name="next-steps"></a>后续步骤

[快速入门：使用 Azure 门户创建 Azure Database for MySQL 灵活服务器](quickstart-create-server-portal.md)
