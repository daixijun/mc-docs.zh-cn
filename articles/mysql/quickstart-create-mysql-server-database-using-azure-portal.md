---
title: 快速入门：创建服务器 - Azure 门户 - Azure Database for MySQL
description: 本文介绍如何使用 Azure 门户在大约五分钟内创建示例 Azure Database for MySQL 服务器。
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
origin.date: 11/04/2020
ms.date: 01/11/2021
ms.openlocfilehash: 9c82c68e05eb1264f17274f0a9e14afd3bcb3775
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022417"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建 Azure Database for MySQL 服务器

> [!NOTE]
> 将要查看的是 Azure Database for MySQL 的新服务。 若要查看经典 MySQL Database for Azure 的文档，请访问[此页](https://docs.azure.cn/zh-cn/mysql-database-on-azure/)。

Azure Database for MySQL 是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 MySQL 数据库。 本快速入门介绍如何使用 Azure 门户创建 Azure Database for MySQL 单一服务器， 以及如何连接到该服务器。

## <a name="prerequisites"></a>先决条件
需要一个 Azure 订阅。 如果没有 Azure 订阅，可在开始前创建一个 [Azure 试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="create-an-azure-database-for-mysql-single-server"></a>创建 Azure Database for MySQL 单一服务器
1. 转到 [Azure 门户](https://portal.azure.cn/)创建 MySQL 单一服务器数据库。 搜索并选择“Azure Database for MySQL”：

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="查找 Azure Database for MySQL":::

1. 选择 **添加** 。

2. 在”选择 Azure Database for MySQL 部署选项”页上，选择“单一服务器”：
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="显示“单一服务器”选项的屏幕截图。":::

3. 输入新的单一服务器的基本设置：

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="显示“创建 MySQL 服务器”页的屏幕截图。":::

   **设置** | **建议的值** | **说明**
   ---|---|---
   订阅 | 你的订阅 | 选择所需的 Azure 订阅。
   资源组 |  myresourcegroup | 输入新资源组，或订阅中的现有资源组。
   服务器名称 | **mydemoserver** | 输入唯一名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。
   数据源 |**无** | 选择“无”，从头开始创建新的服务器  。 仅当从现有服务器的异地备份进行还原时，才选择“备份”。
   位置 |你所需的位置 | 从列表中选择一个位置。
   版本 | 最新主版本| 使用最新主版本。 请参阅[所有受支持的版本](../mysql/concepts-supported-versions.md)。
   计算 + 存储 | 使用默认值| 默认定价层是“常规用途”，其中包含 4 个 vCore 和 100 GB 存储  。 备份保留期设置为 7 天，并提供“异地冗余”备份选项 。<br/>查看[定价](https://azure.cn/pricing/details/mysql/)页，根据需要更新默认值。
   管理员用户名 | **mydemoadmin** | 输入服务器管理员用户名。 不能将“azure_superuser”、“admin”、“administrator”、“root”、“guest”或“public”用作管理员用户名     。
   Password | 密码 | 服务器管理员用户的新密码。 密码长度必须为 8 到 128 个字符，并包含大写字母或小写字母、数字和非字母数字字符（！、$、#、% 等）的组合。
  

   > [!NOTE]
   > 如果轻量级计算和 I/O 足以满足工作负荷要求，请考虑使用“基本”定价层。 请注意，在“基本”定价层中创建的服务器以后不能扩展到“常规用途”或“内存优化”定价层。

4. 选择“查看 + 创建”  以预配服务器。

5. 等待门户页显示“你的部署已完成”消息。 选择“转到资源”，转到新建服务器页：

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="显示“部署已完成”消息的屏幕截图。":::

## <a name="configure-a-server-level-firewall-rule"></a>配置服务器级防火墙规则

默认情况下，新服务器受防火墙保护。 若要进行连接，必须通过完成以下步骤提供对 IP 的访问权限：

1. 转到服务器资源左侧窗格中的“连接安全性”。 如果不知道如何找到资源，请参阅[如何打开资源](../azure-resource-manager/management/manage-resources-portal.md#open-resources)。

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="显示“连接安全性”>“防火墙规则”页的屏幕截图。":::

2. 选择“添加当前客户端 IP 地址”，然后选择“保存” 。

   > [!NOTE]
   > 若要避免连接问题，请检查网络是否允许通过端口 3306 送出出站流量，该端口由 Azure Database for MySQL 使用。 

可以添加更多 IP 或提供一个 IP 范围，以便从这些 IP 连接到服务器。 有关详细信息，请参阅[如何在 Azure Database for MySQL 服务器上管理防火墙规则](./concepts-firewall-rules.md)。

## <a name="connect-to-the-server-by-using-mysqlexe"></a>通过 mysql.exe 连接到服务器
可以使用 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md) 从本地环境连接到服务器。 在本快速入门中，我们将使用 shell 中的 mysql.exe 连接到服务器。

1. 将此处所示的值替换为实际的服务器名称和管理员用户名。 对于 Azure Database for MySQL，需要为管理员用户名添加 `@\<servername>`，如下所示： 

      ```azurecli
      mysql --host=mydemoserver.mysql.database.chinacloudapi.cn --user=myadmin@mydemoserver -p
      ```

2. 创建名为 `guest` 的数据库：
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
3. 切换到 `guest` 数据库：
      ```
      mysql> USE guest;
      Database changed
      ```
4. 输入 `quit`，然后选择 Enter 退出 mysql。

## <a name="clean-up-resources"></a>清理资源
现在已在资源组中创建 Azure Database for MySQL 服务器。  如果将来不再需要这些资源，可以通过删除资源组来删除它们，也可以直接删除 MySQL 服务器。 若要删除资源组，请完成以下步骤：
1. 在 Azure 门户中，搜索并选择“资源组”。
2. 在资源组列表中，选择资源组的名称。
3. 在资源组的“概述”页中，选择“删除资源组” 。
4. 在确认对话框中，键入资源组的名称，然后选择“删除”。

若要删除服务器，可以在服务器的“概述”页中选择“删除”，如下所示 ：
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="显示服务器概述页中的“删除”按钮的屏幕截图。":::

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
>[使用 MySQL 在 Windows 上生成 PHP 应用](../app-service/tutorial-php-mysql-app.md)

