---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/24/2020
title: Databricks Runtime 维护更新 - Azure Databricks
description: 为 Databricks Runtime 版本发布的维护更新。
ms.openlocfilehash: c869e3ba844ca2223fd6efd60ad5be0547aed3ad
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329093"
---
# <a name="databricks-runtime-maintenance-updates"></a>Databricks 运行时维护更新

此页列出了为 Databricks Runtime 版本发布的维护更新。 要向现有群集添加维护更新，请重新启动群集。

## <a name="databricks-runtime-72"></a><a id="72"> </a><a id="databricks-runtime-72"> </a>Databricks Runtime 7.2

请参阅 [Databricks Runtime 7.2](7.2.md)。

* 2020 年 9 月 24 日
  * [[SPARK-32764]](https://issues.apache.org/jira/browse/SPARK-32764)[SQL] - 0.0 应等于 0.0
  * [[SPARK-32753]](https://issues.apache.org/jira/browse/SPARK-32753)[SQL] 仅在转换计划时才会将标记复制到没有标记的节点
  * [[SPARK-32659]](https://issues.apache.org/jira/browse/SPARK-32659)[SQL] 修复非原子类型上插入动态分区修剪的数据问题
  * 操作系统安全更新。
* 2020 年 9 月 8 日
  * 为 Azure Synapse Analytics 创建了一个新参数 `maxbinlength`。 此参数用于控制 BinaryType 列的列长度，并转换为 `VARBINARY(maxbinlength)`。 它可以使用 `.option("maxbinlength", n)` 进行设置，其中 0 < n <= 8000。

## <a name="databricks-runtime-71"></a><a id="71"> </a><a id="databricks-runtime-71"> </a>Databricks Runtime 7.1

请参阅 [Databricks Runtime 7.1](7.1.md)。

* 2020 年 9 月 24 日
  * [[SPARK-32764]](https://issues.apache.org/jira/browse/SPARK-32764)[SQL] - 0.0 应等于 0.0
  * [[SPARK-32753]](https://issues.apache.org/jira/browse/SPARK-32753)[SQL] 仅在转换计划时才会将标记复制到没有标记的节点
  * [[SPARK-32659]](https://issues.apache.org/jira/browse/SPARK-32659)[SQL] 修复非原子类型上插入动态分区修剪的数据问题
  * 操作系统安全更新。
* 2020 年 9 月 8 日
  * 为 Azure Synapse Analytics 创建了一个新参数 `maxbinlength`。 此参数用于控制 BinaryType 列的列长度，并转换为 `VARBINARY(maxbinlength)`。 它可以使用 `.option("maxbinlength", n)` 进行设置，其中 0 < n <= 8000。
* 2020 年 8 月 25 日
  * [[SPARK-32159]](https://issues.apache.org/jira/browse/SPARK-32159)[SQL] 修复 `Aggregator[Array[_], _, _]` 和 `UnresolvedMapObjects` 之间的集成
  * [[SPARK-32559]](https://issues.apache.org/jira/browse/SPARK-32559)[SQL] 修复 `UTF8String.toInt/toLong` 中的剪裁逻辑，该逻辑未正确处理非 ASCII 字符
  * [[SPARK-32543]](https://issues.apache.org/jira/browse/SPARK-32543)[R] 删除 SparkR 中的 `arrow::as_tibble` 用法
  * [[SPARK-32091]](https://issues.apache.org/jira/browse/SPARK-32091)[CORE] 删除丢失的执行程序上的块时忽略超时错误
  * 修复了 MSI 凭据影响 Azure Synapse 连接器的问题
  * 修复了自合并中不明确的属性解析
* 2020 年 8 月 18 日
  * [[SPARK-32594]](https://issues.apache.org/jira/browse/SPARK-32594)[SQL] 修复被插入到 Hive 表的日期序列化
  * [[SPARK-32237]](https://issues.apache.org/jira/browse/SPARK-32237)[SQL] 解决 CTE 中的提示
  * [[SPARK-32431]](https://issues.apache.org/jira/browse/SPARK-32431)[SQL] 检查从内置数据源读取的重复嵌套列
  * [[SPARK-32467]](https://issues.apache.org/jira/browse/SPARK-32467)[UI] 避免在 https 重定向时对 URL 进行两次编码
  * 修复了使用 Trigger.Once 时 AQS 连接器中的争用条件。
* 2020 年 8 月 11 日
  * [[SPARK-32280]](https://issues.apache.org/jira/browse/SPARK-32280)[[SPARK-32372]](https://issues.apache.org/jira/browse/SPARK-32372)[SQL] ResolveReferences.dedupRight 只应重写冲突计划的上级节点的属性
  * [[SPARK-32234]](https://issues.apache.org/jira/browse/SPARK-32234)[SQL] Spark SQL 命令在选择 ORC 表时失败
* 2020 年 8 月 3 日
  * 现在可以在启用了传递的群集上使用 LDA 转换函数。

## <a name="databricks-runtime-70"></a><a id="70"> </a><a id="databricks-runtime-70"> </a>Databricks Runtime 7.0

请参阅 [Databricks Runtime 7.0](7.0.md)。

* 2020 年 9 月 24 日
  * [[SPARK-32764]](https://issues.apache.org/jira/browse/SPARK-32764)[SQL] - 0.0 应等于 0.0
  * [[SPARK-32753]](https://issues.apache.org/jira/browse/SPARK-32753)[SQL] 仅在转换计划时才会将标记复制到没有标记的节点
  * [[SPARK-32659]](https://issues.apache.org/jira/browse/SPARK-32659)[SQL] 修复非原子类型上插入动态分区修剪的数据问题
  * 操作系统安全更新。
* 2020 年 9 月 8 日
  * 为 Azure Synapse Analytics 创建了一个新参数 `maxbinlength`。 此参数用于控制 BinaryType 列的列长度，并转换为 `VARBINARY(maxbinlength)`。 它可以使用 `.option("maxbinlength", n)` 进行设置，其中 0 < n <= 8000。
* 2020 年 8 月 25 日
  * [[SPARK-32159]](https://issues.apache.org/jira/browse/SPARK-32159)[SQL] 修复 `Aggregator[Array[_], _, _]` 和 `UnresolvedMapObjects` 之间的集成
  * [[SPARK-32559]](https://issues.apache.org/jira/browse/SPARK-32559)[SQL] 修复 `UTF8String.toInt/toLong` 中的剪裁逻辑，该逻辑未正确处理非 ASCII 字符
  * [[SPARK-32543]](https://issues.apache.org/jira/browse/SPARK-32543)[R] 删除 SparkR 中的 `arrow::as_tibble` 用法
  * [[SPARK-32091]](https://issues.apache.org/jira/browse/SPARK-32091)[CORE] 删除丢失的执行程序上的块时忽略超时错误
  * 修复了 MSI 凭据影响 Azure Synapse 连接器的问题
  * 修复了自合并中不明确的属性解析
* 2020 年 8 月 18 日
  * [[SPARK-32594]](https://issues.apache.org/jira/browse/SPARK-32594)[SQL] 修复被插入到 Hive 表的日期序列化
  * [[SPARK-32237]](https://issues.apache.org/jira/browse/SPARK-32237)[SQL] 解决 CTE 中的提示
  * [[SPARK-32431]](https://issues.apache.org/jira/browse/SPARK-32431)[SQL] 检查从内置数据源读取的重复嵌套列
  * [[SPARK-32467]](https://issues.apache.org/jira/browse/SPARK-32467)[UI] 避免在 https 重定向时对 URL 进行两次编码
  * 修复了使用 Trigger.Once 时 AQS 连接器中的争用条件。
* 2020 年 8 月 11 日
  * [[SPARK-32280]](https://issues.apache.org/jira/browse/SPARK-32280)[[SPARK-32372]](https://issues.apache.org/jira/browse/SPARK-32372)[SQL] ResolveReferences.dedupRight 只应重写冲突计划的上级节点的属性
  * [[SPARK-32234]](https://issues.apache.org/jira/browse/SPARK-32234)[SQL] Spark SQL 命令在选择 ORC 表时失败
  * 现在可以在启用了传递的群集上使用 LDA 转换函数。

## <a name="databricks-runtime-66"></a><a id="66"> </a><a id="databricks-runtime-66"> </a>Databricks Runtime 6.6

请参阅 [Databricks Runtime 6.6](6.6.md)。

* 2020 年 9 月 24 日
  * 操作系统安全更新。
* 2020 年 9 月 8 日
  * 为 Azure Synapse Analytics 创建了一个新参数 `maxbinlength`。 此参数用于控制 BinaryType 列的列长度，并转换为 `VARBINARY(maxbinlength)`。 它可以使用 `.option("maxbinlength", n)` 进行设置，其中 0 < n <= 8000。
  * 将 Azure 存储 SDK 更新到版本 8.6.4，并在 WASB 驱动程序建立的连接上启用 TCP keep-alive
* 2020 年 8 月 25 日
  * 修复了自合并中不明确的属性解析
* 2020 年 8 月 18 日
  * [[SPARK-32431]](https://issues.apache.org/jira/browse/SPARK-32431)[SQL] 检查从内置数据源读取的重复嵌套列
  * 修复了使用 Trigger.Once 时 AQS 连接器中的争用条件。
* 2020 年 8 月 11 日
  * [[SPARK-28676]](https://issues.apache.org/jira/browse/SPARK-28676)[CORE] 避免从 ContextCleaner 过度记录日志
  * [[SPARK-31967]](https://issues.apache.org/jira/browse/SPARK-31967)[UI] 降级到 vis.js 4.21.0 以修复作业 UI 加载时间回归
* 2020 年 8 月 3 日
  * 现在可以在启用了传递的群集上使用 LDA 转换函数。
  * 操作系统安全更新。

## <a name="databricks-runtime-65"></a><a id="65"> </a><a id="databricks-runtime-65"> </a>Databricks Runtime 6.5

请参阅 [Databricks Runtime 6.5](6.5.md)。

* 2020 年 9 月 24 日
  * 修复了之前的限制：标准群集上的直通仍然会限制用户使用的文件系统实现。 现在用户可以不受限制地访问本地文件系统。
  * 操作系统安全更新。
* 2020 年 9 月 8 日
  * 为 Azure Synapse Analytics 创建了一个新参数 `maxbinlength`。 此参数用于控制 BinaryType 列的列长度，并转换为 `VARBINARY(maxbinlength)`。 它可以使用 `.option("maxbinlength", n)` 进行设置，其中 0 < n <= 8000。
  * 将 Azure 存储 SDK 更新到版本 8.6.4，并在 WASB 驱动程序建立的连接上启用 TCP keep-alive
* 2020 年 8 月 25 日
  * 修复了自合并中不明确的属性解析
* 2020 年 8 月 18 日
  * [[SPARK-32431]](https://issues.apache.org/jira/browse/SPARK-32431)[SQL] 检查从内置数据源读取的重复嵌套列
  * 修复了使用 Trigger.Once 时 AQS 连接器中的争用条件。
* 2020 年 8 月 11 日
  * [[SPARK-28676]](https://issues.apache.org/jira/browse/SPARK-28676)[CORE] 避免从 ContextCleaner 过度记录日志
* 2020 年 8 月 3 日
  * 现在可以在启用了传递的群集上使用 LDA 转换函数。
  * 操作系统安全更新。
* 2020 年 7 月 7 日
  * 已将 Java 版本从 1.8.0_242 升级到 1.8.0_252。
* 2020 年 4 月 21 日
  * [[SPARK-31312]](https://issues.apache.org/jira/browse/SPARK-31312)[SQL] HiveFunctionWrapper 中 UDF 实例的缓存类实例

## <a name="databricks-runtime-64"></a><a id="64"> </a><a id="databricks-runtime-64"> </a>Databricks Runtime 6.4

请参阅 [Databricks Runtime 6.4](6.4.md)。

* 2020 年 9 月 24 日
  * 修复了之前的限制：标准群集上的直通仍然会限制用户使用的文件系统实现。 现在用户可以不受限制地访问本地文件系统。
  * 操作系统安全更新。
* 2020 年 9 月 8 日
  * 为 Azure Synapse Analytics 创建了一个新参数 `maxbinlength`。 此参数用于控制 BinaryType 列的列长度，并转换为 `VARBINARY(maxbinlength)`。 它可以使用 `.option("maxbinlength", n)` 进行设置，其中 0 < n <= 8000。
  * 将 Azure 存储 SDK 更新到版本 8.6.4，并在 WASB 驱动程序建立的连接上启用 TCP keep-alive
* 2020 年 8 月 25 日
  * 修复了自合并中不明确的属性解析
* 2020 年 8 月 18 日
  * [[SPARK-32431]](https://issues.apache.org/jira/browse/SPARK-32431)[SQL] 检查从内置数据源读取的重复嵌套列
  * 修复了使用 Trigger.Once 时 AQS 连接器中的争用条件。
* 2020 年 8 月 11 日
  * [[SPARK-28676]](https://issues.apache.org/jira/browse/SPARK-28676)[CORE] 避免从 ContextCleaner 过度记录日志
* 2020 年 8 月 3 日
  * 现在可以在启用了传递的群集上使用 LDA 转换函数。
  * 操作系统安全更新。
* 2020 年 7 月 7 日
  * 已将 Java 版本从 1.8.0_232 升级到 1.8.0_252。
* 2020 年 4 月 21 日
  * [[SPARK-31312]](https://issues.apache.org/jira/browse/SPARK-31312)[SQL] HiveFunctionWrapper 中 UDF 实例的缓存类实例
* 2020 年 4 月 7 日
  * 为了解决 pandas UDF 无法在 PyArrow 0.15.0 及更高版本中运行的问题，我们添加了一个环境变量 (`ARROW_PRE_0_15_IPC_FORMAT=1`) 来启用对这些 PyArrow 版本的支持。 请参阅 [[SPARK-29367]](https://issues.apache.org/jira/browse/SPARK-29367) 中的说明。
* 2020 年 3 月 10 日
  * 现在默认在 [Azure Databricks 高级计划](https://databricks.com/product/azure-pricing)中的全用途群集上使用优化后的自动缩放。
  * Databricks Runtime 中包含的 Snowflake 连接器 (`spark-snowflake_2.11`) 已更新到版本 2.5.9。 `snowflake-jdbc` 已更新到版本 3.12.0。

## <a name="databricks-runtime-63-unsupported"></a><a id="63"> </a><a id="databricks-runtime-63-unsupported"> </a>Databricks Runtime 6.3（不受支持）

请参阅 [Databricks Runtime 6.3（不支持）](6.3.md)。

* 2020 年 7 月 7 日
  * 已将 Java 版本从 1.8.0_232 升级到 1.8.0_252。
* 2020 年 4 月 21 日
  * [[SPARK-31312]](https://issues.apache.org/jira/browse/SPARK-31312)[SQL] HiveFunctionWrapper 中 UDF 实例的缓存类实例
* 2020 年 4 月 7 日
  * 为了解决 pandas UDF 无法在 PyArrow 0.15.0 及更高版本中运行的问题，我们添加了一个环境变量 (`ARROW_PRE_0_15_IPC_FORMAT=1`) 来启用对这些 PyArrow 版本的支持。 请参阅 [[SPARK-29367]](https://issues.apache.org/jira/browse/SPARK-29367) 中的说明。
* 2020 年 3 月 10 日
  * Databricks Runtime 中包含的 Snowflake 连接器 (`spark-snowflake_2.11`) 已更新到版本 2.5.9。 `snowflake-jdbc` 已更新到版本 3.12.0。
* 2020 年 2 月 18 日
  * 启用 ADLS 客户端预提取时，由于线程本地处理不正确，ADLS Gen2 的凭据传递性能降低。 此版本在启用凭证传递时禁用 ADLS Gen2 预提取，直到我们提供合适的修补程序。
* 2020 年 2 月 11 日
  * [[SPARK-24783]](https://issues.apache.org/jira/browse/SPARK-24783)[SQL] spark.sql.shuffle.partitions=0 应引发异常
  * [[SPARK-30447]](https://issues.apache.org/jira/browse/SPARK-30447)[SQL] 常数传播为 Null 性问题
  * [[SPARK-28152]](https://issues.apache.org/jira/browse/SPARK-28152)[SQL] 为旧的 MsSqlServerDialect 数字映射添加旧配置文件
  * 将重写函数列入了允许列表，以便 MLModels 扩展 MLWriter 可以调用该函数。

## <a name="databricks-runtime-62-unsupported"></a><a id="62"> </a><a id="databricks-runtime-62-unsupported"> </a>Databricks Runtime 6.2（不受支持）

请参阅 [Databricks Runtime 6.2（不受支持）](6.2.md)

* 2020 年 4 月 21 日
  * [[SPARK-31312]](https://issues.apache.org/jira/browse/SPARK-31312)[SQL] HiveFunctionWrapper 中 UDF 实例的缓存类实例
* 2020 年 4 月 7 日
  * 为了解决 pandas UDF 无法在 PyArrow 0.15.0 及更高版本中运行的问题，我们添加了一个环境变量 (`ARROW_PRE_0_15_IPC_FORMAT=1`) 来启用对这些 PyArrow 版本的支持。 请参阅 [[SPARK-29367]](https://issues.apache.org/jira/browse/SPARK-29367) 中的说明。
* 2020 年 3 月 25 日
  * 作业输出（如发送到 stdout 的日志输出）的大小限制为 20MB。 如果总输出的大小较大，则将取消运行并标记为失败。 若要避免出现此限制，可以通过将 `spark.databricks.driver.disableScalaOutput` Spark 配置设置为 `true` 来阻止从驱动程序返回 stdout。 （默认情况下，标志值为 `false`） 该标志控制 Scala JAR 作业和 Scala 笔记本的单元格输出。 如果启用该标志，Spark 不会将作业执行结果返回给客户端。 该标志不影响写入群集日志文件中的数据。 建议只对 JAR 作业的自动群集设置此标志，因为这将禁用笔记本结果。
* 2020 年 3 月 10 日
  * Databricks Runtime 中包含的 Snowflake 连接器 (`spark-snowflake_2.11`) 已更新到版本 2.5.9。 `snowflake-jdbc` 已更新到版本 3.12.0。
* 2020 年 2 月 18 日
  * [[SPARK-24783]](https://issues.apache.org/jira/browse/SPARK-24783)[SQL] spark.sql.shuffle.partitions=0 应引发异常
  * 启用 ADLS 客户端预提取时，由于线程本地处理不正确，ADLS Gen2 的凭据传递性能降低。 此版本在启用凭证传递时禁用 ADLS Gen2 预提取，直到我们提供合适的修补程序。
* 2020 年 1 月 28 日
  * 对于启用凭据传递的群集，将 ML 模型编写器的重写函数列入了允许列表，以便 model save 可以在凭据传递群集上使用重写模式。
  * [[SPARK-30447]](https://issues.apache.org/jira/browse/SPARK-30447)[SQL] 常数传播为 Null 性问题。
  * [[SPARK-28152]](https://issues.apache.org/jira/browse/SPARK-28152)[SQL] 为旧的 MsSqlServerDialect 数字映射添加旧配置文件。
* 2020 年 1 月 14 日
  * 已将 Java 版本从 1.8.0_222 升级到 1.8.0_232。
* 2019 年 12 月 10 日
  * [[SPARK-29904]](https://issues.apache.org/jira/browse/SPARK-29904)[SQL] 通过 JSON/CSV 数据源以微秒精度解析时间戳。

## <a name="databricks-runtime-61-unsupported"></a><a id="61"> </a><a id="databricks-runtime-61-unsupported"> </a>Databricks Runtime 6.1（不受支持）

请参阅 [Databricks Runtime 6.1（不支持）](6.1.md)。

* 2020 年 4 月 7 日
  * 为了解决 pandas UDF 无法在 PyArrow 0.15.0 及更高版本中运行的问题，我们添加了一个环境变量 (`ARROW_PRE_0_15_IPC_FORMAT=1`) 来启用对这些 PyArrow 版本的支持。 请参阅 [[SPARK-29367]](https://issues.apache.org/jira/browse/SPARK-29367) 中的说明。
* 2020 年 3 月 25 日
  * 作业输出（如发送到 stdout 的日志输出）的大小限制为 20MB。 如果总输出的大小较大，则将取消运行并标记为失败。 若要避免出现此限制，可以通过将 `spark.databricks.driver.disableScalaOutput` Spark 配置设置为 `true` 来阻止从驱动程序返回 stdout。 （默认情况下，标志值为 `false`） 该标志控制 Scala JAR 作业和 Scala 笔记本的单元格输出。 如果启用该标志，Spark 不会将作业执行结果返回给客户端。 该标志不影响写入群集日志文件中的数据。 建议只对 JAR 作业的自动群集设置此标志，因为这将禁用笔记本结果。
* 2020 年 3 月 10 日
  * Databricks Runtime 中包含的 Snowflake 连接器 (`spark-snowflake_2.11`) 已更新到版本 2.5.9。 `snowflake-jdbc` 已更新到版本 3.12.0。
* 2020 年 2 月 18 日
  * [[SPARK-24783]](https://issues.apache.org/jira/browse/SPARK-24783)[SQL] spark.sql.shuffle.partitions=0 应引发异常
  * 启用 ADLS 客户端预提取时，由于线程本地处理不正确，ADLS Gen2 的凭据传递性能降低。 此版本在启用凭证传递时禁用 ADLS Gen2 预提取，直到我们提供合适的修补程序。
* 2020 年 1 月 28 日
  * [[SPARK-30447]](https://issues.apache.org/jira/browse/SPARK-30447)[SQL] 常数传播为 Null 性问题。
  * [[SPARK-28152]](https://issues.apache.org/jira/browse/SPARK-28152)[SQL] 为旧的 MsSqlServerDialect 数字映射添加旧配置文件。
* 2020 年 1 月 14 日
  * 已将 Java 版本从 1.8.0_222 升级到 1.8.0_232。
* 2019 年 11 月 7 日
  * [[SPARK-29743]](https://issues.apache.org/jira/browse/SPARK-29743)[SQL] 示例应将 needCopyResult 设置为 true（如果其子级的 needCopyResult 为 true）。
  * 在公共预览版中从 Spark 配置属性和环境变量引用的机密。 请参阅 [Spark 配置属性和环境变量中的机密路径](../../security/secrets/secrets.md#spark-conf-env-var)。
* 2019 年 11 月 5 日
  * 修复了 DBFS FUSE 中处理装入点时在其路径中使用 `//` 的 bug。
  * [[SPARK-29081]](https://issues.apache.org/jira/browse/SPARK-29081) 使用更快的实现替换对属性的 SerializationUtils.clone 的调用
  * [[SPARK-29244]](https://issues.apache.org/jira/browse/SPARK-29244)[CORE] 防止 BytesToBytesMap 中已释放页再次释放
  * **(6.1 ML)** 库 mkl 2019.4 版本是无意安装的。 我们已将其降级到 mkl 版本 2019.3，以匹配 Anaconda 发行版2019.03。

## <a name="databricks-runtime-60-unsupported"></a><a id="60"> </a><a id="databricks-runtime-60-unsupported"> </a>Databricks Runtime 6.0（不受支持）

请参阅 [Databricks Runtime 6.0（不支持）](6.0.md)。

* 2020 年 3 月 25 日
  * 作业输出（如发送到 stdout 的日志输出）的大小限制为 20MB。 如果总输出的大小较大，则将取消运行并标记为失败。 若要避免出现此限制，可以通过将 `spark.databricks.driver.disableScalaOutput` Spark 配置设置为 `true` 来阻止从驱动程序返回 stdout。 （默认情况下，标志值为 `false`） 该标志控制 Scala JAR 作业和 Scala 笔记本的单元格输出。 如果启用该标志，Spark 不会将作业执行结果返回给客户端。 该标志不影响写入群集日志文件中的数据。 建议只对 JAR 作业的自动群集设置此标志，因为这将禁用笔记本结果。
* 2020 年 2 月 18 日
  * 启用 ADLS 客户端预提取时，由于线程本地处理不正确，ADLS Gen2 的凭据传递性能降低。 此版本在启用凭证传递时禁用 ADLS Gen2 预提取，直到我们提供合适的修补程序。
* 2020 年 2 月 11 日
  * [[SPARK-24783]](https://issues.apache.org/jira/browse/SPARK-24783)[SQL] spark.sql.shuffle.partitions=0 应引发异常
* 2020 年 1 月 28 日
  * [[SPARK-30447]](https://issues.apache.org/jira/browse/SPARK-30447)[SQL] 常数传播为 Null 性问题。
  * [[SPARK-28152]](https://issues.apache.org/jira/browse/SPARK-28152)[SQL] 为旧的 MsSqlServerDialect 数字映射添加旧配置文件。
* 2020 年 1 月 14 日
  * 已将 Java 版本从 1.8.0_222 升级到 1.8.0_232。
* 2019 年 11 月 19 日
  * [[SPARK-29743]](https://issues.apache.org/jira/browse/SPARK-29743)[SQL] 示例应将 needCopyResult 设置为 true（如果其子级的 needCopyResult 为 true）
* 2019 年 11 月 5 日
  * `dbutils.tensorboard.start()` 现在支持 TensorBoard 2.0（如果手动安装）。
  * 修复了 DBFS FUSE 中处理装入点时在其路径中使用 `//` 的 bug。
  * [[SPARK-29081]](https://issues.apache.org/jira/browse/SPARK-29081) 使用更快的实现替换对属性的 SerializationUtils.clone 的调用
* 2019 年 10 月 23 日
  * [[SPARK-29244]](https://issues.apache.org/jira/browse/SPARK-29244)[CORE] 防止 BytesToBytesMap 中已释放页再次释放
* 2019 年 10 月 8 日
  * 服务器端更改为允许 Simba Apache Spark ODBC 驱动程序在获取结果时连接失败后重新连接并继续操作（需要 [Simba Apache Spark ODBC 驱动程序版本 2.6.10](https://databricks.com/spark/odbc-driver-download)）。
  * 修复了在启用表 ACL 的群集中影响使用 `Optimize` 命令的问题。
  * 修复了在启用表 ACL 和凭据传递的群集上由于 Scala UDF 禁止错误而导致 `pyspark.ml` 库失败的问题。
  * 将用于凭据传递的 SerDe/SerDeUtil 方法列入了允许列表。
  * 修复了在 WASB 客户端中检查错误代码时发生的 NullPointerException。
  * 修复了用户凭据未转发到 `dbutils.notebook.run()` 创建的作业的问题。

## <a name="databricks-runtime-55-lts"></a><a id="55"> </a><a id="databricks-runtime-55-lts"> </a>Databricks Runtime 5.5 LTS

请参阅 [Databricks Runtime 5.5 LTS](5.5.md)。

* 2020 年 9 月 24 日
  * 操作系统安全更新。
* 2020 年 9 月 8 日
  * 为 Azure Synapse Analytics 创建了一个新参数 `maxbinlength`。 此参数用于控制 BinaryType 列的列长度，并转换为 `VARBINARY(maxbinlength)`。 它可以使用 `.option("maxbinlength", n)` 进行设置，其中 0 < n <= 8000。
* 2020 年 8 月 18 日
  * [[SPARK-32431]](https://issues.apache.org/jira/browse/SPARK-32431)[SQL] 检查从内置数据源读取的重复嵌套列
  * 修复了使用 Trigger.Once 时 AQS 连接器中的争用条件。
* 2020 年 8 月 11 日
  * [[SPARK-28676]](https://issues.apache.org/jira/browse/SPARK-28676)[CORE] 避免从 ContextCleaner 过度记录日志
* 2020 年 8 月 3 日
  * 操作系统安全更新
* 2020 年 7 月 7 日
  * 已将 Java 版本从 1.8.0_232 升级到 1.8.0_252。
* 2020 年 4 月 21 日
  * [[SPARK-31312]](https://issues.apache.org/jira/browse/SPARK-31312)[SQL] HiveFunctionWrapper 中 UDF 实例的缓存类实例
* 2020 年 4 月 7 日
  * 为了解决 pandas UDF 无法在 PyArrow 0.15.0 及更高版本中运行的问题，我们添加了一个环境变量 (`ARROW_PRE_0_15_IPC_FORMAT=1`) 来启用对这些 PyArrow 版本的支持。 请参阅 [[SPARK-29367]](https://issues.apache.org/jira/browse/SPARK-29367) 中的说明。
* 2020 年 3 月 25 日
  * Databricks Runtime 中包含的 Snowflake 连接器 (`spark-snowflake_2.11`) 已更新到版本 2.5.9。 `snowflake-jdbc` 已更新到版本 3.12.0。
* 2020 年 3 月 10 日
  * 作业输出（如发送到 stdout 的日志输出）的大小限制为 20MB。 如果总输出的大小较大，则将取消运行并标记为失败。 若要避免出现此限制，可以通过将 `spark.databricks.driver.disableScalaOutput` Spark 配置设置为 `true` 来阻止从驱动程序返回 stdout。 （默认情况下，标志值为 `false`） 该标志控制 Scala JAR 作业和 Scala 笔记本的单元格输出。 如果启用该标志，Spark 不会将作业执行结果返回给客户端。 该标志不影响写入群集日志文件中的数据。 建议只对 JAR 作业的自动群集设置此标志，因为这将禁用笔记本结果。
* 2020 年 2 月 18 日
  * [[SPARK-24783]](https://issues.apache.org/jira/browse/SPARK-24783)[SQL] spark.sql.shuffle.partitions=0 应引发异常
  * 启用 ADLS 客户端预提取时，由于线程本地处理不正确，ADLS Gen2 的凭据传递性能降低。 此版本在启用凭证传递时禁用 ADLS Gen2 预提取，直到我们提供合适的修补程序。
* 2020 年 1 月 28 日
  * [[SPARK-30447]](https://issues.apache.org/jira/browse/SPARK-30447)[SQL] 常数传播为 Null 性问题。
* 2020 年 1 月 14 日
  * 已将 Java 版本从 1.8.0_222 升级到 1.8.0_232。
* 2019 年 11 月 19 日
  * [[SPARK-29743]](https://issues.apache.org/jira/browse/SPARK-29743)[SQL] 示例应将 needCopyResult 设置为 true（如果其子级的 needCopyResult 为 true）
  * R 版本从 3.6.0 意外升级到了 3.6.1。 我们已将其降级回 3.6.0。
* 2019 年 11 月 5 日
  * 已将 Java 版本从 1.8.0_212 升级到 1.8.0_222。
* 2019 年 10 月 23 日
  * [[SPARK-29244]](https://issues.apache.org/jira/browse/SPARK-29244)[CORE] 防止 BytesToBytesMap 中已释放页再次释放
* 2019 年 10 月 8 日
  * 服务器端更改为允许 Simba Apache Spark ODBC 驱动程序在获取结果时连接失败后重新连接并继续操作（需要 [Simba Apache Spark ODBC 驱动程序版本 2.6.10](https://databricks.com/spark/odbc-driver-download)）。
  * 修复了在启用表 ACL 的群集中影响使用 `Optimize` 命令的问题。
  * 修复了在启用表 ACL 和凭据传递的群集上由于 Scala UDF 禁止错误而导致 `pyspark.ml` 库失败的问题。
  * 将用于凭据传递的 SerDe/SerDeUtil 方法列入了允许列表。
  * 修复了在 WASB 客户端中检查错误代码时发生的 NullPointerException。
* 2019 年 9 月 24 日
  * 提高了 Parquet 编写器的稳定性。
  * 修复了在开始执行之前被取消的 Thrift 查询可能会停留在“已启动”状态的问题。
* 2019 年 9 月 10 日
  * 将线程安全迭代器添加到 BytesToBytesMap
  * [[SPARK-27992]](https://issues.apache.org/jira/browse/SPARK-27992)[[SPARK-28881]](https://issues.apache.org/jira/browse/SPARK-28881)允许 Python 与连接线程联接以传播错误
  * 修复了影响某些全局聚合查询的 bug。
  * 改进了凭据修订。
  * [[SPARK-27330]](https://issues.apache.org/jira/browse/SPARK-27330)[SS] 在 foreach 编写器中支持任务中止
  * [[SPARK-28642]](https://issues.apache.org/jira/browse/SPARK-28642)在 SHOW CREATE TABLE 中隐藏凭据
  * [[SPARK-28699]](https://issues.apache.org/jira/browse/SPARK-28699)[SQL] 禁止在重新分区情况下对 ShuffleExchangeExec 使用基数排序
* 2019 年 8 月 27 日
  * [[SPARK-20906]](https://issues.apache.org/jira/browse/SPARK-20906)[SQL] 允许在具有架构注册表的 API to_avro 中使用用户指定的架构
  * [[SPARK-27838]](https://issues.apache.org/jira/browse/SPARK-27838)[SQL] 支持用户为无任何空记录的可空 catalyst 架构提供不可为 null 的 avro 架构
  * 对 Delta Lake 按时间顺序查看的改进
  * 修复了影响某个 `transform` 表达式的问题
  * 启用进程隔离时支持广播变量
* 2019 年 8 月 13 日
  * 增量流式处理源应检查表的最新协议
  * [[SPARK-28260]](https://issues.apache.org/jira/browse/SPARK-28260)将“CLOSED”状态添加到 ExecutionState
  * [[SPARK-28489]](https://issues.apache.org/jira/browse/SPARK-28489)[SS] 修复了 KafkaOffsetRangeCalculator.getRanges 可能会删除偏移量的 bug
* 2019 年 7 月 30 日
  * [[SPARK-28015]](https://issues.apache.org/jira/browse/SPARK-28015)[SQL] 检查 stringToDate() 使用 yyyy 和 yyyy-[m]m 格式的全部输入
  * [[SPARK-28308]](https://issues.apache.org/jira/browse/SPARK-28308)[CORE] 解析 CalendarInterval 之前应填充其子部分
  * [[SPARK-27485]](https://issues.apache.org/jira/browse/SPARK-27485)EnsureRequirements.reorder 应妥善处理重复表达式
  * [[SPARK-28355]](https://issues.apache.org/jira/browse/SPARK-28355)[CORE] [PYTHON] 将 Spark conf 用作广播压缩 UDF 的阈值

## <a name="databricks-runtime-54-ml-unsupported"></a><a id="54ml"> </a><a id="databricks-runtime-54-ml-unsupported"> </a>Databricks Runtime 5.4 ML（不受支持）

请参阅 [Databricks Runtime 5.4 ML（不受支持）](5.4ml.md)。

* 2019 年 6 月 18 日
  * 改进了 Hyperopt 集成中对 MLflow 活动运行的处理
  * 改进了 Hyperopt 中的消息
  * 已将包 `markdown` 从 3.1 更新到 3.1.1

## <a name="databricks-runtime-54-unsupported"></a><a id="54"> </a><a id="databricks-runtime-54-unsupported"> </a>Databricks Runtime 5.4（不受支持）

请参阅 [Databricks Runtime 5.4（不支持）](5.4.md)。

* 2019 年 11 月 19 日
  * [[SPARK-29743]](https://issues.apache.org/jira/browse/SPARK-29743)[SQL] 示例应将 needCopyResult 设置为 true（如果其子级的 needCopyResult 为 true）
* 2019 年 10 月 8 日
  * 服务器端更改为允许 Simba Apache Spark ODBC 驱动程序在获取结果时连接失败后重新连接并继续操作（需要 Simba Apache Spark ODBC 驱动程序更新到版本 2.6.10）。
  * 修复了在 WASB 客户端中检查错误代码时发生的 NullPointerException。
* 2019 年 9 月 10 日
  * 将线程安全迭代器添加到 BytesToBytesMap
  * 修复了影响某些全局聚合查询的 bug。
  * [[SPARK-27330]](https://issues.apache.org/jira/browse/SPARK-27330)[SS] 在 foreach 编写器中支持任务中止
  * [[SPARK-28642]](https://issues.apache.org/jira/browse/SPARK-28642)在 SHOW CREATE TABLE 中隐藏凭据
  * [[SPARK-28699]](https://issues.apache.org/jira/browse/SPARK-28699)[SQL] 禁止在重新分区情况下对 ShuffleExchangeExec 使用基数排序
  * [[SPARK-28699]](https://issues.apache.org/jira/browse/SPARK-28699)[CORE] 修复了中止不确定阶段的个别案例
* 2019 年 8 月 27 日
  * 修复了影响某个 `transform` 表达式的问题
* 2019 年 8 月 13 日
  * 增量流式处理源应检查表的最新协议
  * [[SPARK-28489]](https://issues.apache.org/jira/browse/SPARK-28489)[SS] 修复了 KafkaOffsetRangeCalculator.getRanges 可能会删除偏移量的 bug
* 2019 年 7 月 30 日
  * [[SPARK-28015]](https://issues.apache.org/jira/browse/SPARK-28015)[SQL] 检查 stringToDate() 使用 yyyy 和 yyyy-[m]m 格式的全部输入
  * [[SPARK-28308]](https://issues.apache.org/jira/browse/SPARK-28308)[CORE] 解析 CalendarInterval 之前应填充其子部分
  * [[SPARK-27485]](https://issues.apache.org/jira/browse/SPARK-27485)EnsureRequirements.reorder 应妥善处理重复表达式
* 2019 年 7 月 2 日
  * 已将 snappy-java 从 1.1.7.1 升级到 1.1.7.3。
* 2019 年 6 月 18 日
  * 改进了 MLlib 集成中 MLflow 活动运行的处理
  * 改进了与使用 Delta 缓存相关的 Databricks 顾问消息
  * 修复了影响使用高阶函数的 bug
  * 修复了影响增量元数据查询的 bug

## <a name="databricks-runtime-53-unsupported"></a><a id="53"> </a><a id="databricks-runtime-53-unsupported"> </a>Databricks Runtime 5.3（不受支持）

请参阅 [Databricks Runtime 5.3（不支持）](5.3.md)。

* 2019 年 11 月 7 日
  * [[SPARK-29743]](https://issues.apache.org/jira/browse/SPARK-29743)[SQL] 示例应将 needCopyResult 设置为 true（如果其子级的 needCopyResult 为 true）
* 2019 年 10 月 8 日
  * 服务器端更改为允许 Simba Apache Spark ODBC 驱动程序在获取结果时连接失败后重新连接并继续操作（需要 Simba Apache Spark ODBC 驱动程序更新到版本 2.6.10）。
  * 修复了在 WASB 客户端中检查错误代码时发生的 NullPointerException。
* 2019 年 9 月 10 日
  * 将线程安全迭代器添加到 BytesToBytesMap
  * 修复了影响某些全局聚合查询的 bug。
  * [[SPARK-27330]](https://issues.apache.org/jira/browse/SPARK-27330)[SS] 在 foreach 编写器中支持任务中止
  * [[SPARK-28642]](https://issues.apache.org/jira/browse/SPARK-28642)在 SHOW CREATE TABLE 中隐藏凭据
  * [[SPARK-28699]](https://issues.apache.org/jira/browse/SPARK-28699)[SQL] 禁止在重新分区情况下对 ShuffleExchangeExec 使用基数排序
  * [[SPARK-28699]](https://issues.apache.org/jira/browse/SPARK-28699)[CORE] 修复了中止不确定阶段的个别案例
* 2019 年 8 月 27 日
  * 修复了影响某个 `transform` 表达式的问题
* 2019 年 8 月 13 日
  * 增量流式处理源应检查表的最新协议
  * [[SPARK-28489]](https://issues.apache.org/jira/browse/SPARK-28489)[SS] 修复了 KafkaOffsetRangeCalculator.getRanges 可能会删除偏移量的 bug
* 2019 年 7 月 30 日
  * [[SPARK-28015]](https://issues.apache.org/jira/browse/SPARK-28015)[SQL] 检查 stringToDate() 使用 yyyy 和 yyyy-[m]m 格式的全部输入
  * [[SPARK-28308]](https://issues.apache.org/jira/browse/SPARK-28308)[CORE] 解析 CalendarInterval 之前应填充其子部分
  * [[SPARK-27485]](https://issues.apache.org/jira/browse/SPARK-27485)EnsureRequirements.reorder 应妥善处理重复表达式
* 2019 年 6 月 18 日
  * 改进了与使用 Delta 缓存相关的 Databricks 顾问消息
  * 修复了影响使用高阶函数的 bug
  * 修复了影响增量元数据查询的 bug
* 2019 年 5 月 28 日
  * 提高了 Delta 的稳定性
  * 读取 Delta LAST_CHECKPOINT 文件时容许 IOException
    * 已将恢复添加到失败的库安装
* 2019 年 5 月 7 日
  * 端口 HADOOP-15778（ABFS：修复客户端读取限制）到 Azure Data Lake Storage Gen2 连接器
  * 端口 HADOOP-16040（ABFS：对 TolerateObappens 配置 Bug 修复）到 Azure Data Lake Storage Gen2 连接器
  * 修复了影响表 Acl 的 bug
  * 修复了加载 Delta 日志校验和文件时的争用情况
  * 修复了 Delta 冲突检测逻辑，不将“插入 + 重写”标识为纯“追加”操作
  * 确保在启用表 ACL 时未禁用 DBIO 缓存
  * [SPARK-27494][SS] 空键/值在 Kafka source v2 中不起作用
  * [SPARK-27446][R] 如果可用，请使用现有 Spark 配置文件。
  * [SPARK-27454][SPARK-27454][ML][SQL] Spark 图像数据源遇到一些非法图像时失败
  * [SPARK-27160][SQL] 修复构建 orc 筛选器时出现的 DecimalType
  * [SPARK-27338][CORE] 修复 UnsafeeExternalSorter 和 TaskMemoryManager 之间的死锁

## <a name="databricks-runtime-52-unsupported"></a><a id="52"> </a><a id="databricks-runtime-52-unsupported"> </a>Databricks Runtime 5.2（不受支持）

请参阅 [Databricks Runtime 5.2（不支持）](5.2.md)。

* 2019 年 9 月 10 日
  * 将线程安全迭代器添加到 BytesToBytesMap
  * 修复了影响某些全局聚合查询的 bug。
  * [[SPARK-27330]](https://issues.apache.org/jira/browse/SPARK-27330)[SS] 在 foreach 编写器中支持任务中止
  * [[SPARK-28642]](https://issues.apache.org/jira/browse/SPARK-28642)在 SHOW CREATE TABLE 中隐藏凭据
  * [[SPARK-28699]](https://issues.apache.org/jira/browse/SPARK-28699)[SQL] 禁止在重新分区情况下对 ShuffleExchangeExec 使用基数排序
  * [[SPARK-28699]](https://issues.apache.org/jira/browse/SPARK-28699)[CORE] 修复了中止不确定阶段的个别案例
* 2019 年 8 月 27 日
  * 修复了影响某个 `transform` 表达式的问题
* 2019 年 8 月 13 日
  * 增量流式处理源应检查表的最新协议
  * [[SPARK-28489]](https://issues.apache.org/jira/browse/SPARK-28489)[SS] 修复了 KafkaOffsetRangeCalculator.getRanges 可能会删除偏移量的 bug
* 2019 年 7 月 30 日
  * [[SPARK-28015]](https://issues.apache.org/jira/browse/SPARK-28015)[SQL] 检查 stringToDate() 使用 yyyy 和 yyyy-[m]m 格式的全部输入
  * [[SPARK-28308]](https://issues.apache.org/jira/browse/SPARK-28308)[CORE] 解析 CalendarInterval 之前应填充其子部分
  * [[SPARK-27485]](https://issues.apache.org/jira/browse/SPARK-27485)EnsureRequirements.reorder 应妥善处理重复表达式
* 2019 年 7 月 2 日
  * 读取 Delta LAST_CHECKPOINT 文件时容许 IOException
* 2019 年 6 月 18 日
  * 改进了与使用 Delta 缓存相关的 Databricks 顾问消息
  * 修复了影响使用高阶函数的 bug
  * 修复了影响增量元数据查询的 bug
* 2019 年 5 月 28 日
  * 已将恢复添加到失败的库安装
* 2019 年 5 月 7 日
  * 端口 HADOOP-15778（ABFS：修复客户端读取限制）到 Azure Data Lake Storage Gen2 连接器
  * 端口 HADOOP-16040（ABFS：对 TolerateObappens 配置 Bug 修复）到 Azure Data Lake Storage Gen2 连接器
  * 修复了加载 Delta 日志校验和文件时的争用情况
  * 修复了 Delta 冲突检测逻辑，不将“插入 + 重写”标识为纯“追加”操作
  * 确保在启用表 ACL 时未禁用 DBIO 缓存
  * [SPARK-27494][SS] 空键/值在 Kafka source v2 中不起作用
  * [SPARK-27454][SPARK-27454][ML][SQL] Spark 图像数据源遇到一些非法图像时失败
  * [SPARK-27160][SQL] 修复构建 orc 筛选器时出现的 DecimalType
  * [SPARK-27338][CORE] 修复 UnsafeeExternalSorter 和 TaskMemoryManager 之间的死锁
* 2019 年 3 月 26 日
  * 避免在整个阶段生成的代码中直接嵌入依赖于平台的偏移量
  * [[SPARK-26665]](https://issues.apache.org/jira/browse/SPARK-26665)[CORE] 修复 BlockTransferService.fetchBlockSync 可能永远挂起的 bug。
  * [[SPARK-27134]](https://issues.apache.org/jira/browse/SPARK-27134)[SQL] array_distinct 函数不能正确处理包含数组数组的列。
  * [[SPARK-24669]](https://issues.apache.org/jira/browse/SPARK-24669)[SQL] 在 DROP DATABASE CASCADE 时使表无效。
  * [[SPARK-26572]](https://issues.apache.org/jira/browse/SPARK-26572)[SQL] 修复聚合 codegen 结果计算。
  * 修复了影响某些 PythonUDF 的 bug。
* 2019 年 2 月 26 日
  * [[SPARK-26864]](https://issues.apache.org/jira/browse/SPARK-26864)[SQL] 如果 python udf 用作左半联接条件，则查询可能会返回不正确的结果。
  * [[SPARK-26887]](https://issues.apache.org/jira/browse/SPARK-26887)[PYTHON] 直接创建 datetime.date，而不是创建 datetime64 作为中间数据。
  * 修复了影响 JDBC/ODBC 服务器的 bug。
  * 修复了影响 PySpark 的 bug。
  * 生成 HadoopRDD 时排除隐藏文件。
  * 修复了 Delta 中导致序列化问题的 bug。
* 2019 年 2 月 12 日
  * 修复了影响使用 Delta 与 Azure ADLS Gen2 装入点的问题。
  * 修复了 Spark 低级网络协议在发送启用加密的大型 RPC 错误消息时可能被破坏的问题（当 `spark.network.crypto.enabled` 设置为 true 时）。
* 2019 年 1 月 30 日
  * 修复了在缓存关系上放置倾斜联接提示时的 StackOverflowError。
  * 修复了 SQL 缓存的缓存 RDD 与其物理计划之间的不一致，这会导致不正确的结果。
  * [[SPARK-26706]](https://issues.apache.org/jira/browse/SPARK-26706)[SQL] 修复 ByteType 的 `illegalNumericPrecedence`。
  * [[SPARK-26709]](https://issues.apache.org/jira/browse/SPARK-26709)[SQL] OptimizeMetadataOnlyQuery 无法正确处理空记录。
  * 在推断架构时，CSV/JSON 数据源应避免使用文件名代换路径。
  * 修复了窗口运算符的约束推理。
  * 修复了影响安装 egg 库并启用了表 ACL 的群集的问题。

## <a name="databricks-runtime-51-unsupported"></a><a id="51"> </a><a id="databricks-runtime-51-unsupported"> </a>Databricks Runtime 5.1（不受支持）

请参阅 [Databricks Runtime 5.1（不支持）](5.1.md)。

* 2019 年 8 月 13 日
  * 增量流式处理源应检查表的最新协议
  * [[SPARK-28489]](https://issues.apache.org/jira/browse/SPARK-28489)[SS] 修复了 KafkaOffsetRangeCalculator.getRanges 可能会删除偏移量的 bug
* 2019 年 7 月 30 日
  * [[SPARK-28015]](https://issues.apache.org/jira/browse/SPARK-28015)[SQL] 检查 stringToDate() 使用 yyyy 和 yyyy-[m]m 格式的全部输入
  * [[SPARK-28308]](https://issues.apache.org/jira/browse/SPARK-28308)[CORE] 解析 CalendarInterval 之前应填充其子部分
  * [[SPARK-27485]](https://issues.apache.org/jira/browse/SPARK-27485)EnsureRequirements.reorder 应妥善处理重复表达式
* 2019 年 7 月 2 日
  * 读取 Delta LAST_CHECKPOINT 文件时容许 IOException
* 2019 年 6 月 18 日
  * 修复了影响使用高阶函数的 bug
  * 修复了影响增量元数据查询的 bug
* 2019 年 5 月 28 日
  * 已将恢复添加到失败的库安装
* 2019 年 5 月 7 日
  * 端口 HADOOP-15778（ABFS：修复客户端读取限制）到 Azure Data Lake Storage Gen2 连接器
  * 端口 HADOOP-16040（ABFS：对 TolerateObappens 配置 Bug 修复）到 Azure Data Lake Storage Gen2 连接器
  * 修复了加载 Delta 日志校验和文件时的争用情况
  * 修复了 Delta 冲突检测逻辑，不将“插入 + 重写”标识为纯“追加”操作
  * [SPARK-27494][SS] 空键/值在 Kafka source v2 中不起作用
  * [SPARK-27454][SPARK-27454][ML][SQL] Spark 图像数据源遇到一些非法图像时失败
  * [SPARK-27160][SQL] 修复构建 orc 筛选器时出现的 DecimalType
  * [SPARK-27338][CORE] 修复 UnsafeeExternalSorter 和 TaskMemoryManager 之间的死锁
* 2019 年 3 月 26 日
  * 避免在整个阶段生成的代码中直接嵌入依赖于平台的偏移量
  * 修复了影响某些 PythonUDF 的 bug。
* 2019 年 2 月 26 日
  * [[SPARK-26864]](https://issues.apache.org/jira/browse/SPARK-26864)[SQL] 如果 python udf 用作左半联接条件，则查询可能会返回不正确的结果。
  * 修复了影响 JDBC/ODBC 服务器的 bug。
  * 生成 HadoopRDD 时排除隐藏文件。
* 2019 年 2 月 12 日
  * 修复了影响安装 egg 库并启用了表 ACL 的群集的问题。
  * 修复了 SQL 缓存的缓存 RDD 与其物理计划之间的不一致，这会导致不正确的结果。
  * [[SPARK-26706]](https://issues.apache.org/jira/browse/SPARK-26706)[SQL] 修复 ByteType 的 `illegalNumericPrecedence`。
  * [[SPARK-26709]](https://issues.apache.org/jira/browse/SPARK-26709)[SQL] OptimizeMetadataOnlyQuery 无法正确处理空记录。
  * 修复了窗口运算符的约束推理。
  * 修复了 Spark 低级网络协议在发送启用加密的大型 RPC 错误消息时可能被破坏的问题（当 `spark.network.crypto.enabled` 设置为 true 时）。
* 2019 年 1 月 30 日
  * 修复了一个问题，该问题可能导致使用 UDT 的 `df.rdd.count()` 在某些情况下返回不正确的答案。
  * 修复了影响安装 wheelhouses 的问题。
  * [[SPARK-26267]](https://issues.apache.org/jira/browse/SPARK-26267)检测到来自 Kafka 的错误偏移时重试。
  * 修复了影响流式查询中多个文件流源的 bug。
  * 修复了在缓存关系上放置倾斜联接提示时的 StackOverflowError。
  * 修复了 SQL 缓存的缓存 RDD 与其物理计划之间的不一致，这会导致不正确的结果。
* 2019 年 1 月 8 日
  * 修复了导致错误 `org.apache.spark.sql.expressions.Window.rangeBetween(long,long) is not whitelisted` 的问题。
  * [[SPARK-26352]](https://issues.apache.org/jira/browse/SPARK-26352)联接重新排序不应更改输出属性的顺序。
  * [[SPARK-26366]](https://issues.apache.org/jira/browse/SPARK-26366)ReplaceExceptWithFilter 应将 NULL 视为 False。
  * Delta Lake 的稳定性改进。
  * 已启用 Delta Lake。
  * 修复了在为 Azure Data Lake Storage Gen1 启用 Azure AD 凭据传递时导致 Azure Data Lake Storage Gen2 访问失败的问题。
  * Databricks IO Cache 现在为所有定价层的 Ls 系列辅助角色实例类型启用。

## <a name="databricks-runtime-50-unsupported"></a><a id="50"> </a><a id="databricks-runtime-50-unsupported"> </a>Databricks Runtime 5.0（不受支持）

请参阅 [Databricks Runtime 5.0（不支持）](5.0.md)。

* 2019 年 6 月 18 日
  * 修复了影响使用高阶函数的 bug
* 2019 年 5 月 7 日
  * 修复了加载 Delta 日志校验和文件时的争用情况
  * 修复了 Delta 冲突检测逻辑，不将“插入 + 重写”标识为纯“追加”操作
  * [SPARK-27494][SS] 空键/值在 Kafka source v2 中不起作用
  * [SPARK-27454][SPARK-27454][ML][SQL] Spark 图像数据源遇到一些非法图像时失败
  * [SPARK-27160][SQL] 修复构建 orc 筛选器时出现的 DecimalType
    * [SPARK-27338][CORE] 修复 UnsafeeExternalSorter 和 TaskMemoryManager 之间的死锁
* 2019 年 3 月 26 日
  * 避免在整个阶段生成的代码中直接嵌入依赖于平台的偏移量
  * 修复了影响某些 PythonUDF 的 bug。
* 2019 年 3 月 12 日
  * [[SPARK-26864]](https://issues.apache.org/jira/browse/SPARK-26864)[SQL] 如果 python udf 用作左半联接条件，则查询可能会返回不正确的结果。
* 2019 年 2 月 26 日
  * 修复了影响 JDBC/ODBC 服务器的 bug。
  * 生成 HadoopRDD 时排除隐藏文件。
* 2019 年 2 月 12 日
  * 修复了 SQL 缓存的缓存 RDD 与其物理计划之间的不一致，这会导致不正确的结果。
  * [[SPARK-26706]](https://issues.apache.org/jira/browse/SPARK-26706)[SQL] 修复 ByteType 的 `illegalNumericPrecedence`。
  * [[SPARK-26709]](https://issues.apache.org/jira/browse/SPARK-26709)[SQL] OptimizeMetadataOnlyQuery 无法正确处理空记录。
  * 修复了窗口运算符的约束推理。
  * 修复了 Spark 低级网络协议在发送启用加密的大型 RPC 错误消息时可能被破坏的问题（当 `spark.network.crypto.enabled` 设置为 true 时）。
* 2019 年 1 月 30 日
  * 修复了一个问题，该问题可能导致使用 UDT 的 `df.rdd.count()` 在某些情况下返回不正确的答案。
  * [[SPARK-26267]](https://issues.apache.org/jira/browse/SPARK-26267)检测到来自 Kafka 的错误偏移时重试。
  * 修复了影响流式查询中多个文件流源的 bug。
  * 修复了在缓存关系上放置倾斜联接提示时的 StackOverflowError。
  * 修复了 SQL 缓存的缓存 RDD 与其物理计划之间的不一致，这会导致不正确的结果。
* 2019 年 1 月 8 日
  * 修复了导致错误 `org.apache.spark.sql.expressions.Window.rangeBetween(long,long) is not whitelisted` 的问题。
  * [[SPARK-26352]](https://issues.apache.org/jira/browse/SPARK-26352)联接重新排序不应更改输出属性的顺序。
  * [[SPARK-26366]](https://issues.apache.org/jira/browse/SPARK-26366)ReplaceExceptWithFilter 应将 NULL 视为 False。
  * Delta Lake 的稳定性改进。
  * 已启用 Delta Lake。
  * Databricks IO Cache 现在为所有定价层的 Ls 系列辅助角色实例类型启用。
* 2018 年 12 月 18 日
  * [[SPARK-26293]](https://issues.apache.org/jira/browse/SPARK-26293)子查询中有 Python UDF 时发生强制转换异常
  * 修复了影响使用联接和限制查询的问题。
  * 根据 Spark UI 中的 RDD 名称修订了凭据
* 2018 年 12 月 6 日
  * 修复了使用 orderBy 后接 groupBy 并将 group by key 作为 sort-by key 的前导部分时导致错误查询结果的问题。
  * 已将 Spark 的 Snowflake 连接器从 2.4.9.2-spark_2.4_pre_release 升级到 2.4.10。
  * 启用 `spark.sql.files.ignoreCorruptFiles` 或 `spark.sql.files.ignoreMissingFiles` 标志时，仅在一次或多次重试后忽略损坏的文件。
  * 修复了影响某些自联合查询的问题。
  * 修复了 thrift 服务器的一个 bug，即取消会话时，会话有时会泄漏。
  * [[SPARK-26307]](https://issues.apache.org/jira/browse/SPARK-26307)修复了使用 Hive SerDe 插入分区表时的 CTA。
  * [[SPARK-26147]](https://issues.apache.org/jira/browse/SPARK-26147)即使只使用联接的一侧的列，处于联接条件下的 Python udf 也会失败
  * [[SPARK-26211]](https://issues.apache.org/jira/browse/SPARK-26211)修复二进制的 InSet，以及带有 null 的结构和数组。
  * [[SPARK-26181]](https://issues.apache.org/jira/browse/SPARK-26181)`ColumnStatsMap` 的 `hasMinMaxStats` 方法不正确。
  * 修复了在没有 Internet 访问的环境中影响安装 Python Wheel 的问题。
* 2018 年 11 月 20 日
  * 修复了取消流式处理查询后导致笔记本无法使用的问题。
  * 修复了影响使用窗口函数的某些查询的问题。
  * 修复了影响来自 Delta 的流的多个架构更改的问题。
  * 修复了影响使用左半/反联接的某些聚合查询的问题。

## <a name="databricks-runtime-43-unsupported"></a><a id="43"> </a><a id="databricks-runtime-43-unsupported"> </a>Databricks Runtime 4.3（不受支持）

请参阅 [Databricks Runtime 4.3](4.3.md)

* 2019 年 4 月 9 日
  * [[SPARK-26665]](https://issues.apache.org/jira/browse/SPARK-26665)[CORE] 修复了可能导致 BlockTransferService.fetchBlockSync 永久挂起的 bug。
  * [[SPARK-24669]](https://issues.apache.org/jira/browse/SPARK-24669)[SQL] 在 DROP DATABASE CASCADE 时使表无效。
* 2019 年 3 月 12 日
  * 修复了影响代码生成的 bug。
  * 修复了影响 Delta 的 bug。
* 2019 年 2 月 26 日
  * 修复了影响 JDBC/ODBC 服务器的 bug。
* 2019 年 2 月 12 日
  * [[SPARK-26709]](https://issues.apache.org/jira/browse/SPARK-26709)[SQL] OptimizeMetadataOnlyQuery 无法正确处理空记录。
  * 生成 HadoopRDD 时排除隐藏文件。
  * 修复了 IN 谓词的值为空时的 Parquet 筛选器转换。
  * 修复了 Spark 低级网络协议在发送启用加密的大型 RPC 错误消息时可能被破坏的问题（当 `spark.network.crypto.enabled` 设置为 true 时）。
* 2019 年 1 月 30 日
  * 修复了一个问题，该问题可能导致使用 UDT 的 `df.rdd.count()` 在某些情况下返回不正确的答案。
  * 修复了 SQL 缓存的缓存 RDD 与其物理计划之间的不一致，这会导致不正确的结果。
* 2019 年 1 月 8 日
  * 修复了导致错误 `org.apache.spark.sql.expressions.Window.rangeBetween(long,long) is not whitelisted` 的问题。
  * 根据 Spark UI 中的 RDD 名称修订了凭据
  * [[SPARK-26352]](https://issues.apache.org/jira/browse/SPARK-26352)联接重新排序不应更改输出属性的顺序。
  * [[SPARK-26366]](https://issues.apache.org/jira/browse/SPARK-26366)ReplaceExceptWithFilter 应将 NULL 视为 False。
  * 已启用 Delta Lake。
  * Databricks IO Cache 现在为所有定价层的 Ls 系列辅助角色实例类型启用。
* 2018 年 12 月 18 日
  * [[SPARK-25002]](https://issues.apache.org/jira/browse/SPARK-25002)Avro：修改输出记录命名空间。
  * 修复了影响使用联接和限制查询的问题。
  * [[SPARK-26307]](https://issues.apache.org/jira/browse/SPARK-26307)修复了使用 Hive SerDe 插入分区表时的 CTA。
  * 启用 `spark.sql.files.ignoreCorruptFiles` 或 `spark.sql.files.ignoreMissingFiles` 标志时，仅在一次或多次重试后忽略损坏的文件。
  * [[SPARK-26181]](https://issues.apache.org/jira/browse/SPARK-26181)`ColumnStatsMap` 的 `hasMinMaxStats` 方法不正确。
  * 修复了在没有 Internet 访问的环境中影响安装 Python Wheel 的问题。
  * 修复了查询分析器中的性能问题。
  * 修复了 PySpark 中导致 DataFrame 操作失败并出现“连接被拒绝”错误的问题。
  * 修复了影响某些自联合查询的问题。
* 2018 年 11 月 20 日
  * [[SPARK-17916]](https://issues.apache.org/jira/browse/SPARK-17916)[[SPARK-25241]](https://issues.apache.org/jira/browse/SPARK-25241)修复当设置 nullValue 了时空字符串被分析为 null 的问题。
  * [[SPARK-25387]](https://issues.apache.org/jira/browse/SPARK-25387)修复了由错误的 CSV 输入导致的 NPE。
  * 修复了影响使用左半/反联接的某些聚合查询的问题。
* 2018 年 11 月 6 日
  * [[SPARK-25741]](https://issues.apache.org/jira/browse/SPARK-25741)长 URL 在 Web UI 中未正确呈现。
  * [[SPARK-25714]](https://issues.apache.org/jira/browse/SPARK-25714)修复优化器规则 BooleanSimplification 中的 NULL 处理。
  * 修复了在 Synapse Analytics 连接器中影响临时对象清除的问题。
  * [[SPARK-25816]](https://issues.apache.org/jira/browse/SPARK-25816)修复嵌套提取器中的属性解析。

* 2018 年 10 月 16 日
  * 修复了影响在 Delta 表上运行 `SHOW CREATE TABLE` 的输出的 bug。
  * 修复了影响 `Union` 操作的 bug。

* 2018 年 9 月 25 日
  * [[SPARK-25368]](https://issues.apache.org/jira/browse/SPARK-25368)[SQL] 不正确的约束推理返回错误的结果。
  * [[SPARK-25402]](https://issues.apache.org/jira/browse/SPARK-25402)[SQL] BooleanSimplification 中的 NULL 处理。
  * 修复了 Avro 数据源中的 `NotSerializableException`。
* 2018 年 9 月 11 日
  * [[SPARK-25214]](https://issues.apache.org/jira/browse/SPARK-25214)[SS] 修复 Kafka v2 源在 `failOnDataLoss=false` 时可能返回重复记录的问题。
  * [[SPARK-24987]](https://issues.apache.org/jira/browse/SPARK-24987)[SS] 修复了当 articlePartition 没有新的偏移量时 Kafka 使用者泄漏问题。
  * 筛选器精简版应正确处理 Null 值。
  * 提高了执行引擎的稳定性。
* 2018 年 8 月 28 日
  * 修复了 Delta Lake Delete 命令中的 bug，该 bug 会错误地删除条件评估为 null 的行。
  * [[SPARK-25142]](https://issues.apache.org/jira/browse/SPARK-25142)添加了 Python 辅助角色无法打开 `_load_from_socket` 中的套接字的错误消息。
* 2018 年 8 月 23 日
  * [[SPARK-23935]](https://issues.apache.org/jira/browse/SPARK-23935)mapEntry 引发 `org.codehaus.commons.compiler.CompileException`。
  * 修复了 Parquet 读取器中的可为空映射问题。
  * [[SPARK-25051]](https://issues.apache.org/jira/browse/SPARK-25051)[SQL] FixNullability 不应在 AnalysisBarrier 上停止。
  * [[SPARK-25081]](https://issues.apache.org/jira/browse/SPARK-25081)修复了当溢出无法分配内存时 ShuffleExternalSorter 可能访问已释放内存页的 bug。
  * 修复了 Databricks Delta 和 Pyspark 之间可能导致瞬时读取失败的交互。
  * [[SPARK-25084]](https://issues.apache.org/jira/browse/SPARK-25084)多个列上的“distribute by”（在括号中换行）可能会导致 codegen 问题。
  * [[SPARK-25096]](https://issues.apache.org/jira/browse/SPARK-25096)如果强制转换为可强制为空，则放宽可空性。
  * 减少了 Delta Lake Optimize 命令使用的默认线程数，从而减少了内存开销并提升了数据提交的速度。
  * [[SPARK-25114]](https://issues.apache.org/jira/browse/SPARK-25114)修复了两个数之间的减法可被 Integer.MAX_VALUE 整除时的 RecordBinaryComparator。
  * 修复了命令部分成功时的机密管理器修订。

## <a name="databricks-runtime-42-unsupported"></a><a id="42"> </a><a id="databricks-runtime-42-unsupported"> </a>Databricks Runtime 4.2（不受支持）

请参阅 [Databricks Runtime 4.2](4.2.md)。

* 2019 年 2 月 26 日
  * 修复了影响 JDBC/ODBC 服务器的 bug。
* 2019 年 2 月 12 日
  * [[SPARK-26709]](https://issues.apache.org/jira/browse/SPARK-26709)[SQL] OptimizeMetadataOnlyQuery 无法正确处理空记录。
  * 生成 HadoopRDD 时排除隐藏文件。
  * 修复了 IN 谓词的值为空时的 Parquet 筛选器转换。
  * 修复了 Spark 低级网络协议在发送启用加密的大型 RPC 错误消息时可能被破坏的问题（当 `spark.network.crypto.enabled` 设置为 true 时）。
* 2019 年 1 月 30 日
  * 修复了一个问题，该问题可能导致使用 UDT 的 `df.rdd.count()` 在某些情况下返回不正确的答案。
* 2019 年 1 月 8 日
  * 修复了导致错误 `org.apache.spark.sql.expressions.Window.rangeBetween(long,long) is not whitelisted` 的问题。
  * 根据 Spark UI 中的 RDD 名称修订了凭据
  * [[SPARK-26352]](https://issues.apache.org/jira/browse/SPARK-26352)联接重新排序不应更改输出属性的顺序。
  * [[SPARK-26366]](https://issues.apache.org/jira/browse/SPARK-26366)ReplaceExceptWithFilter 应将 NULL 视为 False。
  * 已启用 Delta Lake。
  * Databricks IO Cache 现在为所有定价层的 Ls 系列辅助角色实例类型启用。
* 2018 年 12 月 18 日
  * [[SPARK-25002]](https://issues.apache.org/jira/browse/SPARK-25002)Avro：修改输出记录命名空间。
  * 修复了影响使用联接和限制查询的问题。
  * [[SPARK-26307]](https://issues.apache.org/jira/browse/SPARK-26307)修复了使用 Hive SerDe 插入分区表时的 CTA。
  * 启用 `spark.sql.files.ignoreCorruptFiles` 或 `spark.sql.files.ignoreMissingFiles` 标志时，仅在一次或多次重试后忽略损坏的文件。
  * [[SPARK-26181]](https://issues.apache.org/jira/browse/SPARK-26181)`ColumnStatsMap` 的 `hasMinMaxStats` 方法不正确。
  * 修复了在没有 Internet 访问的环境中影响安装 Python Wheel 的问题。
  * 修复了查询分析器中的性能问题。
  * 修复了 PySpark 中导致 DataFrame 操作失败并出现“连接被拒绝”错误的问题。
  * 修复了影响某些自联合查询的问题。
* 2018 年 11 月 20 日
  * [[SPARK-17916]](https://issues.apache.org/jira/browse/SPARK-17916)[[SPARK-25241]](https://issues.apache.org/jira/browse/SPARK-25241)修复当设置 nullValue 了时空字符串被分析为 null 的问题。
  * 修复了影响使用左半/反联接的某些聚合查询的问题。
* 2018 年 11 月 6 日
  * [[SPARK-25741]](https://issues.apache.org/jira/browse/SPARK-25741)长 URL 在 Web UI 中未正确呈现。
  * [[SPARK-25714]](https://issues.apache.org/jira/browse/SPARK-25714)修复优化器规则 BooleanSimplification 中的 NULL 处理。

* 2018 年 10 月 16 日
  * 修复了影响在 Delta 表上运行 `SHOW CREATE TABLE` 的输出的 bug。
  * 修复了影响 `Union` 操作的 bug。

* 2018 年 9 月 25 日
  * [[SPARK-25368]](https://issues.apache.org/jira/browse/SPARK-25368)[SQL] 不正确的约束推理返回错误的结果。
  * [[SPARK-25402]](https://issues.apache.org/jira/browse/SPARK-25402)[SQL] BooleanSimplification 中的 NULL 处理。
  * 修复了 Avro 数据源中的 `NotSerializableException`。
* 2018 年 9 月 11 日
  * [[SPARK-25214]](https://issues.apache.org/jira/browse/SPARK-25214)[SS] 修复 Kafka v2 源在 `failOnDataLoss=false` 时可能返回重复记录的问题。
  * [[SPARK-24987]](https://issues.apache.org/jira/browse/SPARK-24987)[SS] 修复了当 articlePartition 没有新的偏移量时 Kafka 使用者泄漏问题。
  * 筛选器精简版应正确处理 Null 值。
* 2018 年 8 月 28 日
  * 修复了 Delta Lake Delete 命令中的 bug，该 bug 会错误地删除条件评估为 null 的行。
* 2018 年 8 月 23 日
  * 修复了 Delta 快照的 NoClassDefError
  * [[SPARK-23935]](https://issues.apache.org/jira/browse/SPARK-23935)mapEntry 引发 `org.codehaus.commons.compiler.CompileException`。
  * [[SPARK-24957]](https://issues.apache.org/jira/browse/SPARK-24957)[SQL] 后跟聚合的带小数的平均值返回了错误结果。 可能会返回错误的 AVERAGE 结果。 如果 Divide 的结果类型与强制转换的类型相同，则会跳过在 Average 运算符中添加的 CAST。
  * [[SPARK-25081]](https://issues.apache.org/jira/browse/SPARK-25081)修复了当溢出无法分配内存时 ShuffleExternalSorter 可能访问已释放内存页的 bug。
  * 修复了 Databricks Delta 和 Pyspark 之间可能导致瞬时读取失败的交互。
  * [[SPARK-25114]](https://issues.apache.org/jira/browse/SPARK-25114)修复了两个数之间的减法可被 Integer.MAX_VALUE 整除时的 RecordBinaryComparator。
  * [[SPARK-25084]](https://issues.apache.org/jira/browse/SPARK-25084)多个列上的“distribute by”（在括号中换行）可能会导致 codegen 问题。
  * [[SPARK-24934]](https://issues.apache.org/jira/browse/SPARK-24934)[SQL] 在内存中分区修剪的上限/下限中将支持的类型显式加入允许列表。 当在针对缓存数据的查询筛选器中使用复杂数据类型时，Spark 始终返回一个空结果集。 基于内存中统计信息的修剪生成了错误结果，原因是复杂类型的上限/下限设置为 null。 解决方法是不对复杂类型使用基于内存中统计信息的修剪。
  * 修复了命令部分成功时的机密管理器修订。
  * 修复了 Parquet 读取器中的可为空映射问题。
* 2018 年 8 月 2 日
  * 在 Python 中添加了 writeStream.table API。
  * 修复了影响 Delta 检查点的问题。
  * [[SPARK-24867]](https://issues.apache.org/jira/browse/SPARK-24867)[SQL] 将 AnalysisBarrier 添加到 DataFrameWriter。 使用 DataFrameWriter 编写具有 UDF 的数据帧时，不使用 SQL 缓存。 这是我们在 AnalysisBarrier 中所做的更改导致的回归，因为并非所有分析器规则都是幂等的。
  * 修复了可能导致 `mergeInto` 命令产生错误结果的问题。
  * 提高了访问 Azure Data Lake Storage Gen1 时的稳定性。
  * [[SPARK-24809]](https://issues.apache.org/jira/browse/SPARK-24809)在执行程序中序列化 LongHashedRelation 可能会导致数据错误。
  * [[SPARK-24878]](https://issues.apache.org/jira/browse/SPARK-24878)[SQL] 针对包含 null 的基元类型的数组类型修复 reverse 函数。
* 2018 年 7 月 11 日
  * 修复了查询执行中的 bug，该 bug 会导致具有不同精度的十进制列上的聚合在某些情况下返回不正确的结果。
  * 修复了在高级聚合操作期间引发的 `NullPointerException` bug，如分组集。

## <a name="databricks-runtime-41-ml-unsupported"></a><a id="41ml"> </a><a id="databricks-runtime-41-ml-unsupported"> </a>Databricks Runtime 4.1 ML（不受支持）

请参阅 [Databricks Runtime 4.1 ML（Beta 版本）](4.1ml.md)。

* 2018 年 7 月 31 日
  * 将 Azure Synapse Analytics 添加到了 ML 运行时 4.1
  * 修复了当谓词中使用的分区列的名称与表架构中该列的大小写不同时可能导致错误查询结果的 bug。
  * 修复了影响 Spark SQL 执行引擎的 bug。
  * 修复了影响代码生成的 bug。
  * 修复了影响 Delta Lake 的 bug (`java.lang.NoClassDefFoundError`)。
  * 改进了 Delta Lake 中的错误处理。
  * 修复了一个 bug，该 bug 导致为字符串列 32 个或更长的字符收集不正确的数据跳过统计信息。

## <a name="databricks-runtime-41-unsupported"></a><a id="41"> </a><a id="databricks-runtime-41-unsupported"> </a>Databricks Runtime 4.1（不受支持）

请参阅 [Databricks Runtime 4.1](4.1.md)。

* 2019 年 1 月 8 日
  * [[SPARK-26366]](https://issues.apache.org/jira/browse/SPARK-26366)ReplaceExceptWithFilter 应将 NULL 视为 False。
  * 已启用 Delta Lake。
* 2018 年 12 月 18 日
  * [[SPARK-25002]](https://issues.apache.org/jira/browse/SPARK-25002)Avro：修改输出记录命名空间。
  * 修复了影响使用联接和限制查询的问题。
  * [[SPARK-26307]](https://issues.apache.org/jira/browse/SPARK-26307)修复了使用 Hive SerDe 插入分区表时的 CTA。
  * 启用 `spark.sql.files.ignoreCorruptFiles` 或 `spark.sql.files.ignoreMissingFiles` 标志时，仅在一次或多次重试后忽略损坏的文件。
  * 修复了在没有 Internet 访问的环境中影响安装 Python Wheel 的问题。
  * 修复了 PySpark 中导致 DataFrame 操作失败并出现“连接被拒绝”错误的问题。
  * 修复了影响某些自联合查询的问题。
* 2018 年 11 月 20 日
  * [[SPARK-17916]](https://issues.apache.org/jira/browse/SPARK-17916)[[SPARK-25241]](https://issues.apache.org/jira/browse/SPARK-25241)修复当设置 nullValue 了时空字符串被分析为 null 的问题。
  * 修复了影响使用左半/反联接的某些聚合查询的问题。
* 2018 年 11 月 6 日
  * [[SPARK-25741]](https://issues.apache.org/jira/browse/SPARK-25741)长 URL 在 Web UI 中未正确呈现。
  * [[SPARK-25714]](https://issues.apache.org/jira/browse/SPARK-25714)修复优化器规则 BooleanSimplification 中的 NULL 处理。

* 2018 年 10 月 16 日
  * 修复了影响在 Delta 表上运行 `SHOW CREATE TABLE` 的输出的 bug。
  * 修复了影响 `Union` 操作的 bug。

* 2018 年 9 月 25 日
  * [[SPARK-25368]](https://issues.apache.org/jira/browse/SPARK-25368)[SQL] 不正确的约束推理返回错误的结果。
  * [[SPARK-25402]](https://issues.apache.org/jira/browse/SPARK-25402)[SQL] BooleanSimplification 中的 NULL 处理。
  * 修复了 Avro 数据源中的 `NotSerializableException`。
* 2018 年 9 月 11 日
  * [[SPARK-25214]](https://issues.apache.org/jira/browse/SPARK-25214)[SS] 修复 Kafka v2 源在 `failOnDataLoss=false` 时可能返回重复记录的问题。
  * [[SPARK-24987]](https://issues.apache.org/jira/browse/SPARK-24987)[SS] 修复了当 articlePartition 没有新的偏移量时 Kafka 使用者泄漏问题。
  * 筛选器精简版应正确处理 Null 值。
* 2018 年 8 月 28 日
  * 修复了 Delta Lake Delete 命令中的 bug，该 bug 会错误地删除条件评估为 null 的行。
  * [[SPARK-25084]](https://issues.apache.org/jira/browse/SPARK-25084)多个列上的“distribute by”（在括号中换行）可能会导致 codegen 问题。
  * [[SPARK-25114]](https://issues.apache.org/jira/browse/SPARK-25114)修复了两个数之间的减法可被 Integer.MAX_VALUE 整除时的 RecordBinaryComparator。
* 2018 年 8 月 23 日
  * 修复了 Delta 快照的 NoClassDefError。
  * [[SPARK-24957]](https://issues.apache.org/jira/browse/SPARK-24957)[SQL] 后跟聚合的带小数的平均值返回了错误结果。 可能会返回错误的 AVERAGE 结果。 如果 Divide 的结果类型与强制转换的类型相同，则会跳过在 Average 运算符中添加的 CAST。
  * 修复了 Parquet 读取器中的可为空映射问题。
  * [[SPARK-24934]](https://issues.apache.org/jira/browse/SPARK-24934)[SQL] 在内存中分区修剪的上限/下限中将支持的类型显式加入允许列表。 当在针对缓存数据的查询筛选器中使用复杂数据类型时，Spark 始终返回一个空结果集。 基于内存中统计信息的修剪生成了错误结果，原因是复杂类型的上限/下限设置为 null。 解决方法是不对复杂类型使用基于内存中统计信息的修剪。
  * [[SPARK-25081]](https://issues.apache.org/jira/browse/SPARK-25081)修复了当溢出无法分配内存时 ShuffleExternalSorter 可能访问已释放内存页的 bug。
  * 修复了 Databricks Delta 和 Pyspark 之间可能导致瞬时读取失败的交互。
  * 修复了命令部分成功时的机密管理器修订
* 2018 年 8 月 2 日
  * [[SPARK-24613]](https://issues.apache.org/jira/browse/SPARK-24613)[SQL] 采用 UDF 的缓存无法与后续的独立缓存匹配。 使用 AnalysisBarrier 包装逻辑计划以在 CacheManager 中编译执行计划，目的是避免再次分析计划。 这也是 Spark 2.3 的回归。
  * 修复了 Synapse Analytics 连接器影响写入 DateType 数据的时区转换的问题。
  * 修复了影响 Delta 检查点的问题。
  * 修复了可能导致 `mergeInto` 命令产生错误结果的问题。
  * [[SPARK-24867]](https://issues.apache.org/jira/browse/SPARK-24867)[SQL] 将 AnalysisBarrier 添加到 DataFrameWriter。 使用 DataFrameWriter 编写具有 UDF 的数据帧时，不使用 SQL 缓存。 这是我们在 AnalysisBarrier 中所做的更改导致的回归，因为并非所有分析器规则都是幂等的。
  * [[SPARK-24809]](https://issues.apache.org/jira/browse/SPARK-24809)在执行程序中序列化 LongHashedRelation 可能会导致数据错误。
* 2018 年 7 月 11 日
  * 修复了在查询执行过程中会导致聚合不同精度的十进制列在某些情况下返回不正确的结果的 bug。
  * 修复了在高级聚合操作期间引发的 `NullPointerException` bug，如分组集。
* 2018 年 6 月 28 日
  * 修复了当谓词中使用的分区列的名称与表架构中该列的大小写不同时可能导致错误查询结果的 bug。

* 2018 年 6 月 7 日
  * 修复了影响 Spark SQL 执行引擎的 bug。
  * 修复了影响代码生成的 bug。
  * 修复了影响 Delta Lake 的 bug (`java.lang.NoClassDefFoundError`)。
  * 改进了 Delta Lake 中的错误处理。
* 2018 年 5 月 17 日
  * 修复了一个 bug，该 bug 导致为字符串列 32 个或更长的字符收集不正确的数据跳过统计信息。

## <a name="databricks-runtime-40-unsupported"></a><a id="40"> </a><a id="databricks-runtime-40-unsupported"> </a>Databricks Runtime 4.0（不受支持）

请参阅 [Databricks Runtime 4.0](4.0.md)。

* 2018 年 11 月 6 日
  * [[SPARK-25714]](https://issues.apache.org/jira/browse/SPARK-25714)修复优化器规则 BooleanSimplification 中的 NULL 处理。

* 2018 年 10 月 16 日
  * 修复了影响 `Union` 操作的 bug。

* 2018 年 9 月 25 日
  * [[SPARK-25368]](https://issues.apache.org/jira/browse/SPARK-25368)[SQL] 不正确的约束推理返回错误的结果。
  * [[SPARK-25402]](https://issues.apache.org/jira/browse/SPARK-25402)[SQL] BooleanSimplification 中的 NULL 处理。
  * 修复了 Avro 数据源中的 `NotSerializableException`。
* 2018 年 9 月 11 日
  * 筛选器精简版应正确处理 Null 值。
* 2018 年 8 月 28 日
  * 修复了 Delta Lake Delete 命令中的 bug，该 bug 会错误地删除条件评估为 null 的行。
* 2018 年 8 月 23 日
  * 修复了 Parquet 读取器中的可为空映射问题。
  * 修复了命令部分成功时的机密管理器修订
  * 修复了 Databricks Delta 和 Pyspark 之间可能导致瞬时读取失败的交互。
  * [[SPARK-25081]](https://issues.apache.org/jira/browse/SPARK-25081)修复了当溢出无法分配内存时 ShuffleExternalSorter 可能访问已释放内存页的 bug。
  * [[SPARK-25114]](https://issues.apache.org/jira/browse/SPARK-25114)修复了两个数之间的减法可被 Integer.MAX_VALUE 整除时的 RecordBinaryComparator。
* 2018 年 8 月 2 日
  * [[SPARK-24452]](https://issues.apache.org/jira/browse/SPARK-24452)在整数加法或乘法中避免可能的溢出。
  * [[SPARK-24588]](https://issues.apache.org/jira/browse/SPARK-24588)流式处理联接应需要来自子代的 HashClusteredPartitioning。
  * 修复了可能导致 `mergeInto` 命令产生错误结果的问题。
  * [[SPARK-24867]](https://issues.apache.org/jira/browse/SPARK-24867)[SQL] 将 AnalysisBarrier 添加到 DataFrameWriter。 使用 DataFrameWriter 编写具有 UDF 的数据帧时，不使用 SQL 缓存。 这是我们在 AnalysisBarrier 中所做的更改导致的回归，因为并非所有分析器规则都是幂等的。
  * [[SPARK-24809]](https://issues.apache.org/jira/browse/SPARK-24809)在执行程序中序列化 LongHashedRelation 可能会导致数据错误。
* 2018 年 6 月 28 日
  * 修复了当谓词中使用的分区列的名称与表架构中该列的大小写不同时可能导致错误查询结果的 bug。

* 2018 年 6 月 7 日
  * 修复了影响 Spark SQL 执行引擎的 bug。
  * 改进了 Delta Lake 中的错误处理。

* 2018 年 5 月 17 日
  * 修复了 Databricks 机密管理的错误。
  * 提高了读取存储在 Azure data Lake Store 中的数据的稳定性。
  * 修复了影响 RDD 缓存的 bug。
  * 修复了 Spark SQL 中影响 NULL 安全相等的错误。
* 2018 年 4 月 24 日
  * 将 Azure Data Lake Store SDK 从 2.0.11 升级到 2.2.8，以提高访问 Azure Data Lake Store 的稳定性。
  * 修复了当 `spark.databricks.io.hive.fastwriter.enabled` 为 `false` 时影响对分区 Hive 表插入重写的 bug。
  * 修复了任务序列化失败的问题。
  * 提升了 Delta Lake 的稳定性。
* 2018 年 3 月 14 日
  * 在写入 Delta Lake 时防止不必要的元数据更新。
  * 修复了由争用条件引起的问题，这种情况在极少数情况下可能导致某些输出文件丢失。

## <a name="databricks-runtime-35-lts-unsupported"></a><a id="35"> </a><a id="databricks-runtime-35-lts-unsupported"> </a>Databricks Runtime 3.5 LTS（不受支持）

请参阅 [Databricks Runtime 3.5 LTS（不支持）](3.5.md)。

* 2019 年 11 月 7 日
  * [[SPARK-29743]](https://issues.apache.org/jira/browse/SPARK-29743)[SQL] 示例应将 needCopyResult 设置为 true（如果其子级的 needCopyResult 为 true）
* 2019 年 10 月 8 日
  * 服务器端更改为允许 Simba Apache Spark ODBC 驱动程序在获取结果时连接失败后重新连接并继续操作（需要 Simba Apache Spark ODBC 驱动程序更新到版本 2.6.10）。
* 2019 年 9 月 10 日
  * [[SPARK-28699]](https://issues.apache.org/jira/browse/SPARK-28699)[SQL] 禁止在重新分区情况下对 ShuffleExchangeExec 使用基数排序
* 2019 年 4 月 9 日
  * [[SPARK-26665]](https://issues.apache.org/jira/browse/SPARK-26665)[CORE] 修复了可能导致 BlockTransferService.fetchBlockSync 永久挂起的 bug。
* 2019 年 2 月 12 日
  * 修复了 Spark 低级网络协议在发送启用加密的大型 RPC 错误消息时可能被破坏的问题（当 `spark.network.crypto.enabled` 设置为 true 时）。
* 2019 年 1 月 30 日
  * 修复了一个问题，该问题可能导致使用 UDT 的 `df.rdd.count()` 在某些情况下返回不正确的答案。
* 2018 年 12 月 18 日
  * 启用 `spark.sql.files.ignoreCorruptFiles` 或 `spark.sql.files.ignoreMissingFiles` 标志时，仅在一次或多次重试后忽略损坏的文件。
  * 修复了影响某些自联合查询的问题。
* 2018 年 11 月 20 日
  * [[SPARK-25816]](https://issues.apache.org/jira/browse/SPARK-25816)修复了嵌套提取器中的属性解析。
* 2018 年 11 月 6 日
  * [[SPARK-25714]](https://issues.apache.org/jira/browse/SPARK-25714)修复优化器规则 BooleanSimplification 中的 NULL 处理。

* 2018 年 10 月 16 日
  * 修复了影响 `Union` 操作的 bug。

* 2018 年 9 月 25 日
  * [[SPARK-25402]](https://issues.apache.org/jira/browse/SPARK-25402)[SQL] BooleanSimplification 中的 NULL 处理。
  * 修复了 Avro 数据源中的 `NotSerializableException`。
* 2018 年 9 月 11 日
  * 筛选器精简版应正确处理 Null 值。
* 2018 年 8 月 28 日
  * 修复了 Delta Lake Delete 命令中的 bug，该 bug 会错误地删除条件评估为 null 的行。
  * [[SPARK-25114]](https://issues.apache.org/jira/browse/SPARK-25114)修复了两个数之间的减法可被 Integer.MAX_VALUE 整除时的 RecordBinaryComparator。
* 2018 年 8 月 23 日
  * [[SPARK-24809]](https://issues.apache.org/jira/browse/SPARK-24809)在执行程序中序列化 LongHashedRelation 可能会导致数据错误。
  * 修复了 Parquet 读取器中的可为空映射问题。
  * [[SPARK-25081]](https://issues.apache.org/jira/browse/SPARK-25081)修复了当溢出无法分配内存时 ShuffleExternalSorter 可能访问已释放内存页的 bug。
  * 修复了 Databricks Delta 和 Pyspark 之间可能导致瞬时读取失败的交互。
* 2018 年 6 月 28 日
  * 修复了当谓词中使用的分区列的名称与表架构中该列的大小写不同时可能导致错误查询结果的 bug。
* 2018 年 6 月 28 日
  * 修复了当谓词中使用的分区列的名称与表架构中该列的大小写不同时可能导致错误查询结果的 bug。

* 2018 年 6 月 7 日
  * 修复了影响 Spark SQL 执行引擎的 bug。
  * 改进了 Delta Lake 中的错误处理。

* 2018 年 5 月 17 日
  * 提高了读取存储在 Azure data Lake Store 中的数据的稳定性。
  * 修复了影响 RDD 缓存的 bug。
  * 修复了 Spark SQL 中影响 NULL 安全相等的错误。
  * 修复了影响流式处理查询中的某些聚合的 bug。
* 2018 年 4 月 24 日
  * 将 Azure Data Lake Store SDK 从 2.0.11 升级到 2.2.8，以提高访问 Azure Data Lake Store 的稳定性。
  * 修复了当 `spark.databricks.io.hive.fastwriter.enabled` 为 `false` 时影响对分区 Hive 表插入重写的 bug。
  * 修复了任务序列化失败的问题。
* 2018 年 3 月 9 日
  * 修复了由争用条件引起的问题，这种情况在极少数情况下可能导致某些输出文件丢失。
* 2018 年 3 月 1 日
  * 提高了处理需要很长时间才能停止的流的效率。
  * 修复了影响 Python 自动补全的问题。
  * 应用了 Ubuntu 安全补丁。
  * 修复了影响使用 Python UDF 和窗口函数的某些查询的问题。
  * 修复了在启用表访问控制的群集上影响 UDF 使用的问题。
* 2018年 1 月 29 日
  * 修复了影响对存储在 Azure Blob 存储中的表的操作的问题。
  * 修正了在空 DataFrame 上 dropDuplicate 后的聚合问题。

## <a name="databricks-runtime-34-unsupported"></a><a id="34"> </a><a id="databricks-runtime-34-unsupported"> </a>Databricks Runtime 3.4（不受支持）

请参阅 [Databricks Runtime 3.4](3.4.md)。

* 2018 年 6 月 7 日
  * 修复了影响 Spark SQL 执行引擎的 bug。
  * 改进了 Delta Lake 中的错误处理。

* 2018 年 5 月 17 日
  * 提高了读取存储在 Azure data Lake Store 中的数据的稳定性。
  * 修复了影响 RDD 缓存的 bug。
  * 修复了 Spark SQL 中影响 NULL 安全相等的错误。
* 2018 年 4 月 24 日
  * 修复了当 `spark.databricks.io.hive.fastwriter.enabled` 为 `false` 时影响对分区 Hive 表插入重写的 bug。
* 2018 年 3 月 9 日
  * 修复了由争用条件引起的问题，这种情况在极少数情况下可能导致某些输出文件丢失。
* 2017 年 12 月 13 日
  * 修复了影响 Scala 中 UDF 的问题。
  * 修复了影响在非 DBFS 路径中存储的数据源表上使用数据跳过索引的问题。
* 2017 年 12 月 7 日
  * 改进了随机稳定性。