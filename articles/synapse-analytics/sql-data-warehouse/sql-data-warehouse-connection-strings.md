---
title: 连接字符串
description: Synapse SQL 池的连接字符串
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
origin.date: 04/17/2018
ms.date: 01/11/2021
ms.author: v-jay
ms.reviewer: igorstan
ms.custom: azure-synapse, seo-lt-2019, devx-track-csharp
ms.openlocfilehash: d1d7ebbb5a68fef207b0d603f37665785dbc1573
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98021928"
---
# <a name="connection-strings-for-sql-pools-in-azure-synapse"></a>Azure Synapse 中 SQL 池的连接字符串

可以使用几种不同的应用程序协议（例如，[ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)、[ODBC](https://docs.microsoft.com/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[PHP](https://docs.microsoft.com/sql/connect/php/overview-of-the-php-sql-driver?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [JDBC](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)）连接到 Azure Synapse 中的 SQL 池。 下面是每个协议的连接字符串的一些示例。  可以使用 Azure 门户来生成连接字符串。  

若要使用 Azure 门户生成连接字符串，请导航到“SQL 池”边栏选项卡，在“概要”下单击“显示数据库连接字符串” 。

## <a name="sample-adonet-connection-string"></a>示例 ADO.NET 连接字符串

```csharp
Server=tcp:{your_server}.database.chinacloudapi.cn,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>示例 ODBC 连接字符串

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.chinacloudapi.cn,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>示例 PHP 连接字符串

```PHP
Server: {your_server}.database.chinacloudapi.cn,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.chinacloudapi.cn,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.chinacloudapi.cn,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>示例 JDBC 连接字符串

```Java
jdbc:sqlserver://yourserver.database.chinacloudapi.cn:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.chinacloudapi.cn;loginTimeout=30;
```

> [!NOTE]
> 请考虑将连接超时值设置为 300 秒，以便连接可以经受住短时间内不可用。

## <a name="next-steps"></a>后续步骤

若要开始使用 Visual Studio 和其他应用程序来查询 SQL 池，请参阅[使用 Visual Studio 进行查询](sql-data-warehouse-query-visual-studio.md)。
