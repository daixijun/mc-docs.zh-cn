---
title: 从 Azure 门户获取连接字符串
description: 从 Azure 门户获取连接字符串
keywords: sql 连接, 连接字符串
services: sql-database
author: WenJason
manager: digimobile
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
origin.date: 07/13/2018
ms.date: 01/04/2021
ms.author: v-jay
ms.openlocfilehash: 4e5de24644c50fd2a512bd85c46ed96e056cd38a
ms.sourcegitcommit: cf3d8d87096ae96388fe273551216b1cb7bf92c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830249"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>从 Azure 门户获取连接字符串
使用 [Azure 门户](https://portal.azure.cn/)获取客户端程序与 Azure SQL 数据库进行交互所需的连接字符串。 

1. 选择“所有服务”   > “SQL 数据库”  。

2. 在“SQL 数据库”边栏选项卡左上角附近的筛选器文本框中输入数据库的名称。 

3. 选择数据库所对应的行。

4. 显示数据库的边栏选项卡后，为了方便查看，请选择“最小化”按钮  ，以便折叠用于浏览和数据库筛选的边栏选项卡。

5. 在数据库边栏选项卡上，选择“显示数据库连接字符串”。 

6. 复制相应的连接字符串。 也就是说，如果想要使用 ADO.NET 连接库，则从“ADO.NET”  选项卡中复制相应的字符串。

    ![复制数据库的 ADO 连接字符串][20-CopyAdoConnectionString]

7. 根据需要编辑连接字符串。 即，向连接字符串中插入密码，或者在用户名或服务器名称太长的情况下，从用户名中删除“@&lt;servername&gt;”。

8. 通过这种或那种格式，将连接字符串信息粘贴到客户端程序代码中。

有关详细信息，请参阅[连接字符串和配置文件](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files)。

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->