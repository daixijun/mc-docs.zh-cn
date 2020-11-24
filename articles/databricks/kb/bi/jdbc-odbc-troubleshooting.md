---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 07/13/2020
title: 排查 JDBC 和 ODBC 连接问题 - Azure Databricks
description: 了解如何排查 Azure Databricks JDBC 和 ODBC 的连接错误。
ms.openlocfilehash: 04d58c7bd48abed4a5c4ca39b5f98e83c0221fb9
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589813"
---
# <a name="troubleshooting-jdbc-and-odbc-connections"></a><a id="troubleshoot-jdbc-odbc"> </a><a id="troubleshooting-jdbc-and-odbc-connections"> </a>排查 JDBC 和 ODBC 连接问题

本文提供的信息可帮助你排查 Databricks JDBC/ODBC 服务器和 BI 工具与数据源之间的连接问题。

## <a name="fetching-result-set-is-slow-after-statement-execution"></a>语句执行后获取结果集缓慢

执行查询后，你可以通过对返回的 `ResultSet` 重复调用 `next()` 方法来获取结果行。 此方法触发对驱动程序 Thrift 服务器的请求，以便在缓冲的行用尽时取回一批行。 我们发现批处理的大小会明显影响性能。 大多数 JDBC/ODBC 驱动程序中的默认值都过于保守，我们建议将其设置为至少 100,000。
如果你无法访问此配置，请与 BI 工具提供商联系。

## <a name="timeoutexception-when-creating-the-connection"></a>创建连接时出现超时/异常

获得服务器主机名后，可以从终端运行以下测试来检查与终结点的连接情况。

```bash
curl https://<server-hostname>:<port>/sql/protocolv1/o/0/<cluster-id> -H "Authorization: Basic $(echo -n 'token:<personal-access-token>' | base64)"
```

如果连接超时，请检查此连接的网络设置是否正确。

## <a name="ttransportexception"></a>TTransportException

如果响应中包含如下所示的 `TTransportException`（此错误是预料中的），则表示网关正常工作，并且你传入了有效凭据。 如果无法使用相同的凭据进行连接，请检查你使用的客户端是否已正确配置，以及是否在使用[最新 Simba 驱动程序（版本 > = 1.2.0）](https://databricks.com/spark/odbc-driver-download/)：

```console
<h2>HTTP ERROR: 500</h2>
<p>Problem accessing /cliservice. Reason:
<pre> javax.servlet.ServletException: org.apache.thrift.transport.TTransportException</pre></p>
```

## <a name="referencing-temporary-views"></a>引用临时视图

如果响应中包含消息“`Table or view not found: SPARK..temp_view`”，则意味着在客户端应用程序中没有正确引用某个临时视图。 Simba 有一个名为 `UseNativeQuery` 的内部配置参数，该参数决定了在将查询提交到 Thrift 服务器之前是否转换查询。 该参数默认设置为 0，在这种情况下，Simba 可以修改查询。 具体而言，Simba 会为临时视图创建一个自定义 `#temp` 架构，并期望客户端应用程序通过此架构引用临时视图。
可以通过设置 `UseNativeQuery=1` 避免使用此特殊别名，这会阻止 Simba 修改查询。 在这种情况下，Simba 会将查询直接发送到 Thrift 服务器。 但是，客户端需要确保使用 Spark 预期的方言（即 HiveQL）编写查询。

总之，你可以使用以下选项通过 Simba 和 Spark 处理临时视图：

* `UseNativeQuery=0`，并通过在视图名称前加上 `#temp` 来引用该视图。
* `UseNativeQuery=1`，并确保使用 Spark 预期的方言来编写查询。

## <a name="other-errors"></a>其他错误

* 如果出现错误“`401 Unauthorized`”，请检查你使用的凭据：

  ```console
  <h2>HTTP ERROR: 401</h2>
  <p>Problem accessing /sql/protocolv1/o/0/test-cluster. Reason:
  <pre>    Unauthorized</pre></p>
  ```

  验证用户名是 `token`（而不是你的用户名），并且密码是[个人访问令牌](/databricks/dev-tools/api/latest/authentication#generate-a-token)（它应当以 `dapi` 开头）。

* “`404, Not Found`”之类的响应通常指示查找指定群集时出现的问题：

  ```console
  <h2>HTTP ERROR: 404</h2>
  <p>Problem accessing /sql/protocolv1/o/0/missing-cluster. Reason:
  <pre>    RESOURCE_DOES_NOT_EXIST: No cluster found matching: missing-cluster</pre></p>
  ```

* 如果在应用程序 log4j 日志中看到以下错误：

  ```console
  log4j:ERROR A "org.apache.log4j.FileAppender" object is not assignable to a "com.simba.spark.jdbc42.internal.apache.log4j.Appender" variable.
  ```

  可以忽略这些错误。 Simba 内部 log4j 库已改装，以避免与应用程序中的 log4j 库冲突。 但是，Simba 仍可加载应用程序的 log4j 配置，并尝试使用某些自定义 log4j 追加器。 此尝试在改装的库中失败。 相关信息仍然捕获在日志中。