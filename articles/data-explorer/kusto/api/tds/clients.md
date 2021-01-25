---
title: MS-TDS 客户端和 Kusto - Azure 数据资源管理器
description: 本文介绍了 Azure 数据资源管理器中的 MS-TDS 客户端和 Kusto。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
origin.date: 10/30/2019
ms.date: 01/22/2021
ms.openlocfilehash: d3cf2bb23bece3067f8bbf3bafac28b8d1ca12ce
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611594"
---
# <a name="ms-tds-clients-and-azure-data-explorer"></a>MS-TDS 客户端和 Azure 数据资源管理器

Azure 数据资源管理器为 MS-SQL 客户端实现了与 TDS 兼容的终结点。 兼容性在协议级别实现。 任何可以连接到采用 Azure Active Directory (Azure AD) 身份验证的 SQL Azure 数据库的库或应用程序，都可以与 Azure 数据资源管理器服务器一起使用。 因此，你可以使用服务器域名，就像它是 SQL Azure 服务器一样。

Azure 数据资源管理器不支持使用用户名和密码进行基本身份验证。 必须将 Azure Active Directory 与下面列出的所有客户端配合使用。 

Azure 数据资源管理器实现了 T-SQL 的一个子集和 SQL Server 模拟的一个子集。 有关详细信息，请参阅[已知问题](./sqlknownissues.md)，以了解 SQL Server 实现的 T-SQL 与 Azure 数据资源管理器实现的 T-SQL 之间的差异。

## <a name="net-sql-client"></a>.NET SQL 客户端

Azure 数据资源管理器支持对 SQL 客户端进行 Azure AD 身份验证。 有关详细信息，请参阅 [.NET SQL 客户端（用户身份验证）](./aad.md#net-sql-client-user)和 [.NET SQL 客户端（应用程序身份验证）](./aad.md#net-sql-client-application)

## <a name="jdbc"></a>JDBC

Microsoft JDBC 驱动程序可用于连接到采用 Azure AD 身份验证的 Azure 数据资源管理器。

创建一个应用程序来使用 *mssql-jdbc* JAR 和 *adal4j* JAR 的版本之一及其所有依赖项。
例如，

```s
mssql-jdbc-7.0.0.jre8.jar
adal4j-1.6.3.jar
accessors-smart-1.2.jar
activation-1.1.jar
asm-5.0.4.jar
commons-codec-1.11.jar
commons-lang3-3.5.jar
gson-2.8.0.jar
javax.mail-1.6.1.jar
jcip-annotations-1.0-1.jar
json-smart-2.3.jar
lang-tag-1.4.4.jar
nimbus-jose-jwt-6.5.jar
oauth2-oidc-sdk-5.64.4.jar
slf4j-api-1.7.21.jar
```

创建一个应用程序来使用 JDBC 驱动程序类 com.microsoft.sqlserver.jdbc.SQLServerDriver。

使用如下所示的连接字符串。

```s
jdbc:sqlserver://<cluster_name.region>.kusto.chinacloudapi.cn:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.chinacloudapi.cn;loginTimeout=30;authentication=ActiveDirectoryPassword
```

> [!NOTE]
> 如果要使用 Azure Active Directory 集成身份验证模式，请将 ActiveDirectoryPassword 替换为 ActiveDirectoryIntegrated。 有关详细信息，请参阅 [JDBC（用户身份验证）](./aad.md#jdbc-user)和 [JDBC（应用程序身份验证）](./aad.md#jdbc-application)。

## <a name="odbc"></a>ODBC

支持 ODBC 的应用程序可以连接到 Azure 数据资源管理器。

创建一个 ODBC 数据源：

1. 启动 ODBC 数据源管理器。
2. 选择“添加”以创建一个新的数据源，并设置“ODBC Driver 17 for SQL Server”。
3. 为数据源指定一个名称，并在“服务器”字段中指定 Azure 数据资源管理器群集名称。 例如“mykusto.kusto.chinacloudapi.cn”。
4. 对于身份验证选项，请设置“Active Directory 集成”。
5. 选择“下一步”以设置数据库。
7. 对于后面的选项卡中的所有其他设置，只需要保留默认值即可。
8. 选择“完成”以打开数据源摘要窗口，在该窗口中可以测试连接。

现在，你可以将该 ODBC 数据源与应用程序配合使用。

如果 ODBC 应用程序可以接受连接字符串来替代 DSN 或与其一起使用，则使用以下命令。

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.chinacloudapi.cn;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

某些 ODBC 应用程序不能很好地与 `NVARCHAR(MAX)` 类型配合使用。 有关详细信息，请参阅 https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver 。

常见的解决方法是将返回的数据强制转换为 *NVARCHAR(n)* ，用某个值替换 n。 例如 NVARCHAR(4000)。 但是，这种解决方法对于 Azure 数据资源管理器将不起作用，因为 Azure 数据资源管理器只有一个字符串类型，并且对于 SQL 客户端，它将编码为 NVARCHAR(MAX)。

Azure 数据资源管理器提供了另一种解决方法。 你可以通过连接字符串将 Azure 数据资源管理器配置为将所有字符串编码为 NVARCHAR(n)。 可以使用连接字符串中的 language 字段以“language@OptionName1:OptionValue1,OptionName2:OptionValue2”格式指定优化选项。

例如，以下连接字符串会指示 Azure 数据资源管理器将字符串编码为 NVARCHAR(8000)。

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.chinacloudapi.cn;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

下面是使用 ODBC 驱动程序的 PowerShell 脚本的示例。

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.chinacloudapi.cn;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()
```

## <a name="linqpad"></a>LINQPad

可以通过将 Linq 应用程序像 SQL Server 一样进行连接，将其与 Azure 数据资源管理器一起使用。
可以使用 LINQPad 探究 Linq 兼容性并浏览 Azure 数据资源管理器。 它还可以执行 SQL 查询。建议使用此工具来浏览 Azure 数据资源管理器 TDS (SQL) 终结点。

像往常一样连接到 Microsoft SQL Server。 LINQPad 支持 Active Directory 身份验证。

1. 选择“添加连接”。
2. 设置“自动生成数据上下文”。
3. 设置“默认值(LINQ to SQL)”，即 LINQPad 驱动程序。
4. 设置“SQL Azure”。
5. 对于“服务器”，请指定 Azure 数据资源管理器群集的名称。 例如“mykusto.kusto.chinacloudapi.cn”。
6. 为登录设置“Windows 身份验证(Active Directory)”。
7. 选择“测试”来验证连接性。
8. 选择“确定” 。 浏览器窗口将显示其中包含数据库的树状视图。
9. 你可以浏览数据库、表和列。
10. 你可以在查询窗口中运行 SQL 查询。 指定 SQL 语言，然后选择到数据库的连接。
11. 你还可以在查询窗口中运行 LINQ 查询。 例如，在浏览器窗口中选择一个表。 选择“计数”，并让它运行。

## <a name="azure-data-studio-134-and-above"></a>Azure Data Studio（1.3.4 及更高版本）

创建一个新连接。

1. 将连接类型设置为“Microsoft SQL Server”。
2. 指定 Azure 数据资源管理器群集的名称作为服务器名称。 例如“mykusto.kusto.chinacloudapi.cn”。
3. 设置身份验证类型“Azure Active Directory - 支持 MFA 的通用方法”。
4. 指定在 Azure AD 中预配的帐户。 例如， *myname@contoso.com* 。 首次添加该帐户。
5. 使用数据库选取器选择数据库。
6. 选择“连接”以转到数据库仪表板，并设置连接。
7. 选择“新建查询”以打开查询窗口，或者在仪表板上选择“新建查询”任务。

## <a name="power-bi-desktop"></a>Power BI Desktop

像往常一样连接到 SQL Azure 数据库。

1. 在“获取数据”下，选择“更多”。
2. 设置“Azure”，然后设置“Azure SQL 数据库”。 
3. 指定 Azure 数据资源管理器服务器名称。 例如“mykusto.kusto.chinacloudapi.cn”。
4. 选择“DirectQuery”。
5. 设置“Microsoft 帐户”身份验证（不是 **Windows**），然后选择“登录”。
6. 数据库选取器会显示可用的数据库。 继续像之前那样操作，但使用真实的 SQL Server。

## <a name="excel"></a>Excel

像往常一样连接到 SQL Azure 数据库。

1. 在“数据”选项卡下选择“获取数据”，然后设置“从 Azure”和“从 Azure SQL 数据库”。 
2. 指定 Azure 数据资源管理器服务器名称。 例如“mykusto.kusto.chinacloudapi.cn”。
3. 设置“Microsoft 帐户”身份验证（不是 **Windows**），然后选择“登录”。
5. 数据库选取器会显示可用的数据库。 继续像之前那样操作，但使用真实的 SQL Server。
4. 在登录后，选择“连接”。
5. 数据库选取器会显示可用的数据库。 继续像之前那样操作，但使用真实的 SQL Server。

## <a name="tableau"></a>Tableau

创建一个 ODBC 数据源。 有关详细信息，请参阅 [ODBC](./clients.md#odbc) 部分。

1. 通过“其他数据库(ODBC)”进行连接。
2. 在“DSN”中设置 ODBC 数据源。
3. 选择“连接”以建立连接。
4. 选择“登录”（在按钮可用后），并登录到 Azure 数据资源管理器。

## <a name="dbeaver-533-and-above"></a>DBeaver（5.3.3 及更高版本）

配置 DBeaver，以采用与 Azure 数据资源管理器兼容的方式处理结果集。

1. 在“窗口”菜单中选择“首选项”。
2. 在“编辑器”部分中选择“数据编辑器”。
3. 请确保选中“读取下个页面时刷新数据”。

创建到 Azure 数据资源管理器数据库的连接。

1. 在“数据库”菜单中选择“新建连接”。
2. 查找“Azure”，然后设置“Azure SQL 数据库”。  选择“**下一步**”。
3. 指定主机。 例如“mykusto.kusto.chinacloudapi.cn”。
4. 指定数据库。 例如“mydatabase”。

> [!WARNING]
> 不要使用“master”作为数据库名称。 Azure 数据资源管理器需要连接到特定数据库。

5. 对于“身份验证”，请设置“Active Directory - 密码”。
6. 指定 Active Directory 用户的凭据， 例如 myname@contoso.com，并为此用户设置相应的密码。
7. 选择“测试连接 …” 以验证连接详细信息是否正确。

## <a name="microsoft-sql-server-management-studio-v18x"></a>Microsoft SQL Server Management Studio (v18.x)

1. 选择“连接”，然后在“对象资源管理器”下选择“数据库引擎” 。
2. 指定 Azure 数据资源管理器群集的名称作为服务器名称。 例如“mykusto.kusto.chinacloudapi.cn”。
3. 对于身份验证，请设置“Active Directory - 集成”。
4. 选择“选项”。
5. 在“连接到数据库”下选择“浏览服务器”以浏览可用数据库。 
6. 选择“是”以继续浏览。
7. 窗口将显示一个树状视图，其中包含所有可用的数据库。 选择其中一个以连接到数据库。
8. 另一种可能的操作是，在“连接到数据库”下选择“默认”，然后选择“连接”。   对象资源管理器将显示所有数据库。

> [!NOTE]
> 尚不支持通过 SSMS 浏览数据库对象，因为 SSMS 使用关联子查询来浏览数据库架构。
> Azure 数据资源管理器不支持相关子查询。 有关详细信息，请参阅[相关子查询](./sqlknownissues.md#correlated-sub-queries)。

10. 选择“新建查询”以打开查询窗口并设置数据库。

你可以从查询窗口中运行自定义 SQL 查询。

## <a name="matlab-via-jdbc"></a>MATLAB（通过 JDBC）

通过在首选项目录中创建“javaclasspath.txt”文件，将所需的 JAR 文件添加到 MATLAB 的静态类路径的前面。

1. 在 Matlab 的命令窗口中运行以下命令。

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

2. 添加所需的 JAR 文件的完整路径。

``` s
<before>
c:\full\path\to\accessors-smart-1.2.jar
c:\full\path\to\activation-1.1.jar
c:\full\path\to\adal4j-1.6.3.jar
c:\full\path\to\asm-5.0.4.jar
c:\full\path\to\commons-codec-1.11.jar
c:\full\path\to\commons-lang3-3.5.jar
c:\full\path\to\gson-2.8.0.jar
c:\full\path\to\javax.mail-1.6.1.jar
c:\full\path\to\jcip-annotations-1.0-1.jar
c:\full\path\to\json-smart-2.3.jar
c:\full\path\to\lang-tag-1.4.4.jar
c:\full\path\to\mssql-jdbc-7.0.0.jre8.jar
c:\full\path\to\nimbus-jose-jwt-6.5.jar
c:\full\path\to\oauth2-oidc-sdk-5.64.4.jar
c:\full\path\to\slf4j-api-1.7.21.jar
```

> [!NOTE]
> 顶部需要有 <**before**>，以便将这些文件添加到类路径的前面。 另外，请将“c:\full\path\to”替换为这些文件的实际完整路径。

3. 重启 MATLAB 以确保加载这些类。

4. 尝试连接之前，请运行以下命令（MATLAB 命令窗口）。

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> [!NOTE]
> 这会将 **TransformerFactory** 重置为默认值（MATLAB 通常通过 **Saxon** 重载此项，但这与 **ADAL4J** 不兼容）。

5. 使用以下命令（MATLAB 命令窗口）连接到 Azure 数据资源管理器 TDS 终结点。

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.chinacloudapi.cn:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.chinacloudapi.cn;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> [!NOTE]
> 以 **"database="** 结尾是可以的，那样将没有任何值。 database 函数会自动将第一个输入（数据库名称）追加到此字符串。
> 如果要使用 Azure Active Directory 集成身份验证模式，请将 ActiveDirectoryPassword 替换为 ActiveDirectoryIntegrated。

6. 测试连接并运行示例查询。 提交以下命令（MATLAB 命令窗口）。

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> [!NOTE]
> 将 KUSTO_TABLE 替换为 Azure 数据资源管理器中的某个现有表。

## <a name="sending-t-sql-queries-over-the-rest-api"></a>通过 REST API 发送 T-SQL 查询

[Azure 数据资源管理器 REST API](../rest/index.md) 可以接受和执行 T-SQL 查询。

1. 将请求发送到查询终结点，并将 **csl** 属性设置为 T-SQL 查询的文本。
2. 将 **[请求属性](../netfx/request-properties.md)** **OptionQueryLanguage** 设置为 **sql**。
