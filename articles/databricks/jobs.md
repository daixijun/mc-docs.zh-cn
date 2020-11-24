---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/13/2020
title: 作业 - Azure Databricks
description: 了解如何在 Azure Databricks 中查看、创建和运行作业。
ms.openlocfilehash: 9693aaf10e0c3c8e88fdf12e052449e5b9e0d77a
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589825"
---
# <a name="jobs"></a>作业

作业是立即运行或按计划运行笔记本或 JAR 的一种方法。 运行笔记本的另一种方法是在[笔记本 UI](notebooks/index.md) 中以交互方式运行。

可以通过 UI、CLI 以及调用作业 API 来创建和运行作业。 可以通过 CLI、通过查询 API 以及通过电子邮件警报来监视 UI 中的作业运行结果。 本文重点介绍如何使用 UI 执行作业任务。 有关其他方法，请参阅[作业 CLI](dev-tools/cli/jobs-cli.md) 和[作业 API](dev-tools/api/latest/jobs.md)。

> [!IMPORTANT]
>
> * 作业数限制为 1000。
> * 工作区在一小时内可以创建的作业数限制为 5000（包括“立即运行”和“运行提交”）。 此限制还会影响 REST API 和笔记本工作流创建的作业。
> * 工作区限制为 150 个并发（正在运行的）作业运行。
> * 工作区限制为 1000 个活动（正在运行和挂起的）作业运行。

## <a name="view-jobs"></a>查看作业

单击“作业”图标 ![作业菜单图标](_static/images/jobs/jobs-icon.png) （在边栏中）。 此时会显示“作业”列表。 “作业”页会列出所有已定义的作业、群集定义、计划（如果有）以及上次运行的结果。

在“作业”列表中，可以筛选作业：

* 使用关键字。
* 仅选择你拥有的作业或你有权访问的作业。 针对此筛选器的访问权限取决于所启用的[作业访问控制](security/access-control/jobs-acl.md)。

你还可以单击任何列标题，以便按该列对作业列表排序（降序或升序）。 默认情况下，此页面按作业名称以升序排序。

> [!div class="mx-imgBorder"]
> ![作业列表](_static/images/jobs/job-list-azure.png)

## <a name="create-a-job"></a><a id="create-a-job"> </a><a id="job-create"> </a>创建作业

1. 单击“+ 创建作业”。 此时会显示作业详细信息页。

   > [!div class="mx-imgBorder"]
   > ![作业详细信息](_static/images/jobs/jobs-conf-azure.png)

2. 在包含占位符文本 `Untitled` 的文本字段中输入一个名称。
3. 指定任务类型：单击“选择笔记本”、“设置 JAR”或“配置 spark-submit”。
   * **笔记本**
     1. 选择一个笔记本，并单击“确定”。
     1. 在“参数”旁边，单击“编辑”。  指定键值对或表示键值对的 JSON 字符串。 此类参数设置[小组件](notebooks/widgets.md)的值。
   * **JAR**：上传 JAR，指定主类和参数，并单击“确定”。 若要详细了解 JAR 作业，请参阅 [JAR 作业提示](#jar-jobs)。
   * **spark-submit**：指定主类、库 JAR 的路径、参数，并单击“确认”。 若要详细了解 spark-submit，请参阅 [Apache Spark 文档](https://spark.apache.org/docs/latest/submitting-applications.html)。

     > [!NOTE]
     >
     > 以下 Azure Databricks 功能不可用于 spark-submit 作业：
     >
     > * 群集自动缩放。 若要详细了解自动缩放，请参阅[群集自动缩放](clusters/configure.md#autoscaling)。
     > * [Databricks 实用工具](dev-tools/databricks-utils.md)。 如果要使用 Databricks 实用工具，请改用 JAR 作业。

4. 在“依赖库”字段中，可以选择单击“添加”并指定依赖库。 依赖库会在启动时自动附加到群集。 请按照[库依赖项](#library-dependencies)中的建议来指定依赖项。

   > [!IMPORTANT]
   >
   > 如果已将库配置为[自动在所有群集上安装](libraries/cluster-libraries.md#install-workspace-libraries)，或在下一步中选择一个现有的已终止群集且该群集已安装了库，则作业执行操作不会等待库安装完成。 如果作业需要某个库，则应在“依赖库”字段中将该库附加到此作业。

5. 在“群集”字段中单击“编辑”，指定要在其上运行作业的群集。 在“群集类型”下拉列表中，选择“新建作业群集”或“现有通用群集”。 

   > [!NOTE]
   >
   > 选择群集类型时，请注意以下事项：
   >
   > * 对于生产级别作业或务必要完成的作业，建议选择“新建作业群集”。
   > * 只能在新群集上运行 spark-submit 作业。
   > * 在新群集上运行作业时，该作业会被视为按作业工作负荷定价的数据工程（作业）工作负荷。 在现有群集上运行作业时，该作业会被视为按通用工作负荷定价的数据分析（通用）工作负荷。
   > * 如果选择一个已终止的现有群集，并且作业所有者拥有“可重启”[权限](security/access-control/cluster-acl.md)，则 Azure Databricks 会在作业按计划运行时启动该群集。
   > * 现有群集最适用于按固定时间间隔更新[仪表板](notebooks/dashboards.md)之类的任务。

   * **新建作业群集** - 完成[群集配置](clusters/configure.md#cluster-configurations)。
     1. 在群集配置中，选择一个运行时版本。 有关如何选择运行时版本的帮助，请参阅 [Databricks Runtime](release-notes/runtime/index.md#runtime-release-notes) 和 [Databricks Light](runtime/light.md#light)。
     1. 若要缩短新群集启动时间，请在群集配置中选择一个[池](clusters/instance-pools/cluster-instance-pool.md#cluster-instance-pool)。
   * **现有的通用群集** - 在下拉列表中选择现有群集。
6. 在“计划”字段中，可以选择单击“编辑”并计划作业。 请参阅[运行作业](#run-a-job)。
7. 可以选择单击“高级”并指定高级作业选项。 请参阅[高级作业选项](#advanced-job-options)。

## <a name="view-job-details"></a><a id="job-details"> </a><a id="view-job-details"> </a>查看作业详细信息

在“作业”页上，单击“名称”列中的作业名称。 作业详细信息页会显示配置参数、活动的运行（正在运行的和挂起的）以及已完成的运行。

> [!div class="mx-imgBorder"]
> ![作业详细信息](_static/images/jobs/job-details.png)

Databricks 最多维护作业运行的历史记录 60 天。
如果需要保留作业运行，建议在作业运行结果过期之前将其导出。 有关详细信息，请参阅[导出作业运行结果](#export-job-runs)。

在作业运行页上，可以通过单击 Spark 列中的“日志”链接来查看作业运行的标准错误、标准输出、log4j 输出。

## <a name="run-a-job"></a>运行作业

可以按计划运行作业，或立即运行作业。

### <a name="schedule-a-job"></a>计划作业

若要定义作业的计划，请执行以下操作：

1. 单击“计划”旁边的“编辑”。

   > [!div class="mx-imgBorder"]
   > ![编辑计划](_static/images/jobs/edit-schedule.png)

   此时会显示“计划作业”对话框。

   > [!div class="mx-imgBorder"]
   > ![计划作业](_static/images/jobs/job-schedule.png)

2. 指定计划粒度、开始时间和时区。 （可选）选中“显示 Cron 语法”复选框以使用 [Quartz Cron 语法](http://www.quartz-scheduler.org/documentation/quartz-2.3.0/tutorials/crontrigger.html)显示和编辑计划。

   > [!NOTE]
   > * Azure Databricks 强制实施在作业计划触发的后续运行之间的最小间隔（10 秒），而不考虑 cron 表达式中的秒配置。
   > * 可以选择一个采用夏令时或 UTC 时间的时区。 如果选择一个采用夏令时的区域，则[在夏令时开始或结束](https://www.quartz-scheduler.org/documentation/2.3.1-SNAPSHOT/faq.html#questions-about-jobs)时，某个每小时作业可能会被系统跳过，或者会在一到两个小时内显示为未触发。 如果希望作业每小时（绝对时间）运行一次，请选择 UTC 时间。
   > * 作业计划程序与 Spark 批处理接口类似，不适用于低延迟作业。 由于网络或云问题，作业运行的延迟时间有时可能会长达数分钟。 在这些情况下，计划的作业会在服务可用后立即运行。

3. 单击“确认”  。

   > [!div class="mx-imgBorder"]
   > ![计划的作业](_static/images/jobs/job-scheduled.png)

#### <a name="pause-and-resume-a-job-schedule"></a>暂停和恢复作业计划

若要暂停作业，请单击作业计划旁边的“暂停”按钮：

> [!div class="mx-imgBorder"]
> ![计划的作业](_static/images/jobs/job-scheduled.png)

若要继续暂停的作业计划，请单击“继续”按钮：

> [!div class="mx-imgBorder"]
> ![继续作业](_static/images/jobs/resume-job.png)

### <a name="run-a-job-immediately"></a>立即运行作业

若要立即运行作业，请在“活动运行”表中单击“立即运行”。

> [!div class="mx-imgBorder"]
> ![立即运行](_static/images/jobs/run-option.png)

> [!TIP]
>
> 单击“立即运行”，在完成作业配置后进行笔记本或 JAR 的测试性运行。 如果笔记本出现故障，可以对其进行编辑，作业会自动运行新版本的笔记本。

## <a name="run-a-job-with-different-parameters"></a><a id="job-runs-with-different-params"> </a><a id="run-a-job-with-different-parameters"> </a>使用不同参数运行作业

可以使用“立即使用不同参数运行”选项，以便重新运行一个指定不同参数或为现有参数指定不同值的作业。

1. 在“活动运行”表中，单击“立即使用不同参数运行”。 此对话框因你运行的作业而异，具体取决于你运行的是笔记本作业还是 spark-submit 作业。
   * **笔记本** - 显示用于设置键值对或 JSON 对象的 UI。  可以使用此对话框设置[小组件](notebooks/widgets.md)的值：

     > [!div class="mx-imgBorder"]
     > ![使用参数运行笔记本](_static/images/jobs/run-now-with-notebook-params.png)

   * **spark-submit** - 显示包含参数列表的对话框。 例如，你可以使用 100 个分区而不是默认的 10 个分区来运行[创建作业](#job-create)中所述的 SparkPi 估算器：

     > [!div class="mx-imgBorder"]
     > ![设置 spark-submit 参数](_static/images/jobs/pi-parameters-diff.png)

2. 指定参数。 提供的参数会与已触发的运行的默认参数合并。 如果删除键，则会使用默认参数。
3. 单击 **“运行”** 。

## <a name="notebook-job-tips"></a><a id="notebook-job-tips"> </a><a id="notebook-jobs"> </a>笔记本作业提示

笔记本单元格输出总计（所有笔记本单元格的合并输出）存在 20MB 的大小限制。 此外，单个单元格输出存在 8MB 的大小限制。
如果单元格输出大小总计超出 20MB，或者单个单元格的输出大于 8MB，则会取消该运行并将其标记为失败。
如果不知道如何查找接近或超出限制的单元格，请针对通用群集运行该笔记本，并使用这个[笔记本自动保存方法](/databricks/kb/notebooks/notebook-autosave)。

## <a name="jar-job-tips"></a><a id="jar-job-tips"> </a><a id="jar-jobs"> </a>JAR 作业提示

在运行 JAR 作业时，需要了解一些注意事项。

### <a name="output-size-limits"></a><a id="jar-job-output-size-limits"> </a><a id="output-size-limits"> </a>输出大小限制

> [!NOTE]
>
> 适用于 Databricks Runtime 6.3 及更高版本。

作业输出（如发送到 stdout 的日志输出）的大小限制为 20MB。 如果总输出大于该限制，则会取消此运行并将其标记为失败。

若要避免遇到此限制，可以通过将 `spark.databricks.driver.disableScalaOutput` Spark 配置设置为 `true` 来阻止 stdout 从驱动程序返回到 Azure Databricks。 默认情况下，标志值为 `false`。  该标志控制 Scala JAR 作业和 Scala 笔记本的单元格输出。 如果启用该标志，Spark 不会将作业执行结果返回给客户端。
该标志不影响写入群集日志文件中的数据。 建议只对 JAR 作业的作业群集设置此标志，因为它会禁用笔记本结果。

### <a name="use-the-shared-sparkcontext"></a><a id="shared-spark-context"> </a><a id="use-the-shared-sparkcontext"> </a>使用共享的 `SparkContext`

因为 Databricks 是托管服务，所以可能需要进行一些代码更改，以确保 Apache Spark 作业正常运行。 JAR 作业程序必须使用共享 `SparkContext` API 来获取 `SparkContext`。 由于 Databricks 初始化 `SparkContext`，因此调用 `new SparkContext()` 的程序会失败。 若要获取 `SparkContext`，请只使用由 Databricks 创建的共享 `SparkContext`：

```scala
val goodSparkContext = SparkContext.getOrCreate()
val goodSparkSession = SparkSession.builder().getOrCreate()
```

此外，在使用共享 `SparkContext` 时，还应避免使用几种方法。

* 不要调用 `SparkContext.stop()`。
* 请勿在 `Main` 程序的末尾调用 `System.exit(0)` 或 `sc.stop()`。 这可能会导致未定义的行为。

### <a name="use-try-finally-blocks-for-job-clean-up"></a>使用 `try-finally` 块进行作业清理

假设有一个由两部分组成的 JAR：

* `jobBody()`，包含作业的主要部分
* `jobCleanup()`，必须在 `jobBody()` 后执行，不管该函数是成功还是返回了异常

例如，`jobBody()` 可以创建表，你可以使用 `jobCleanup()` 来删除这些表。

若要确保调用清理方法，安全的方法是在代码中放置一个 `try-finally` 块：

```scala
try {
  jobBody()
} finally {
  jobCleanup()
}
```

不应尝试使用 `sys.addShutdownHook(jobCleanup)` 或以下代码进行清理：

```scala
val cleanupThread = new Thread { override def run = jobCleanup() }
Runtime.getRuntime.addShutdownHook(cleanupThread)
```

考虑到 Spark 容器的生存期在 Azure Databricks 中的管理方式，ShutdownHook 的运行并不可靠。

### <a name="configure-jar-job-parameters"></a>配置 JAR 作业参数

JAR 作业使用字符串数组进行参数化。

* 在 UI 中，可以在“参数”文本框中输入参数，并通过应用 POSIX shell 分析规则将这些参数拆分为数组。 有关详细信息，请参阅 [shlex 文档](https://docs.python.org/2/library/shlex.html#shlex.split)。
* 在 API 中，将参数以标准 JSON 数组形式输入。 有关详细信息，请参阅 [SparkJarTask](dev-tools/api/latest/jobs.md#jobssparkjartask)。 若要访问这些参数，请检查传入到 `main` 函数中的 `String` 数组。

## <a name="view-job-run-details"></a><a id="job-run-details"> </a><a id="view-job-run-details"> </a>查看作业运行详细信息

作业运行详细信息页包含作业输出和日志链接：

> [!div class="mx-imgBorder"]
> ![作业运行详细信息](_static/images/jobs/job-run-output.png)

可以从“作业”页和“群集”页查看作业运行详细信息。

* 单击“作业”图标 ![作业菜单图标](_static/images/jobs/jobs-icon.png)。 在“在过去 60 天内完成”表的“运行”列中，单击运行编号链接。 

  > [!div class="mx-imgBorder"]
  > ![从“作业”运行的作业](_static/images/jobs/job-run.png)

* 单击“群集”图标 ![群集图标](_static/images/clusters/clusters-icon.png)。 在“作业群集”表的作业行中，单击“作业运行”链接。

  > [!div class="mx-imgBorder"]
  > ![从“群集”运行的作业](_static/images/clusters/job-run-link.png)

## <a name="export-job-run-results"></a><a id="export-job-run-results"> </a><a id="export-job-runs"> </a>导出作业运行结果

可以为所有作业类型导出笔记本运行结果和作业运行日志。

### <a name="export-notebook-run-results"></a>导出笔记本运行结果

可以通过导出作业运行的结果来持久保存作业运行。 对于笔记本作业运行，可以先[导出](notebooks/notebooks-manage.md#export-notebook)呈现的笔记本，稍后再将其[导入](notebooks/notebooks-manage.md#import-notebook)到 Databricks 工作区中。

1. 在作业详细信息页中，单击“运行”列中的作业运行名称。

   > [!div class="mx-imgBorder"]
   > ![作业运行](_static/images/jobs/job-run.png)

2. 单击“导出到 HTML”。

   > [!div class="mx-imgBorder"]
   > ![导出运行结果](_static/images/jobs/export-notebook-run.png)

### <a name="export-job-run-logs"></a>导出作业运行日志

你还可以导出作业运行的日志。 若要自动执行此过程，可以设置作业，使其通过作业 API 自动将日志传送到 DBFS。 有关详细信息，请查看作业[创建](dev-tools/api/latest/jobs.md#jobsjobsservicecreatejob) API 调用中的 [NewCluster](dev-tools/api/latest/jobs.md#jobsclusterspecnewcluster) 和 [ClusterLogConf](dev-tools/api/latest/clusters.md#clusterclusterlogconf) 字段。

## <a name="edit-a-job"></a>编辑作业

若要编辑作业，请单击“作业”列表中的作业名称链接。

## <a name="delete-a-job"></a>删除作业

若要删除作业，请单击“作业”列表的“操作”列中的“x”。

## <a name="library-dependencies"></a>库依赖项

Spark 驱动程序的某些库依赖项不能重写。 这些库优先于你自己的与之冲突的任何库。

若要获取驱动程序库依赖项的完整列表，请在附加到同一 Spark 版本的群集（或包含要检查的驱动程序的群集）的笔记本中运行以下命令。

```bash
%sh
ls /databricks/jars
```

### <a name="manage-library-dependencies"></a>管理库依赖项

如果在为作业创建 JAR 时处理库依赖项，则最好是将 Spark 和 Hadoop 作为 `provided` 依赖项列出。 在 Maven 上，将 Spark 和/或 Hadoop 添加为提供的依赖项，如以下示例所示。

```xml
<dependency>
  <groupId>org.apache.spark</groupId>
  <artifactId>spark-core_2.11</artifactId>
  <version>2.3.0</version>
  <scope>provided</scope>
</dependency>
<dependency>
  <groupId>org.apache.hadoop</groupId>
  <artifactId>hadoop-core</artifactId>
  <version>1.2.1</version>
  <scope>provided</scope>
</dependency>
```

在 `sbt` 中，将 Spark 和 Hadoop 添加为提供的依赖项，如以下示例所示。

```scala
libraryDependencies += "org.apache.spark" %% "spark-core" % "2.3.0" % "provided"
libraryDependencies += "org.apache.hadoop" %% "hadoop-core" % "1.2.1" % "provided"
```

> [!TIP]
>
> 为依赖项指定正确的 Scala 版本，具体取决于你所运行的版本。

## <a name="advanced-job-options"></a>高级作业选项

**最大并发运行数**

可并行运行的最大运行数。 启动新的运行时，如果作业已达到其最大活动运行数，Azure Databricks 会跳过该运行。 如果希望能够以并发方式执行同一作业的多个运行，请将此值设置为高于默认值 1。 设置此值适用于这样的情形：例如，如果你按计划频繁触发作业并希望允许连续的运行彼此重叠，或者，如果你希望触发多个在输入参数方面有区别的运行。

**警报**

作业失败、成功或超时情况下发送的电子邮件警报。 可以针对作业启动、作业成功和作业失败（包括已跳过的作业）设置警报，并为每种警报类型提供多个以逗号分隔的电子邮件地址。 还可以选择退出已跳过的作业运行的警报。

> [!div class="mx-imgBorder"]
> ![配置电子邮件警报](_static/images/jobs/job-alerts.png)

将这些电子邮件警报与你最喜欢的通知工具集成，这些工具包括：

* [PagerDuty](https://www.pagerduty.com/docs/guides/email-integration-guide)
* [Slack](https://get.slack.help/hc/articles/206819278-Send-emails-to-Slack)

**超时**

作业的最长完成时间。 如果作业未在此时间内完成，则 Databricks 会将其状态设置为“已超时”。

**重试**

一项策略，用于确定重试已失败运行的时间和次数。

> [!div class="mx-imgBorder"]
> ![重试策略](_static/images/jobs/retry-policy.png)

> [!NOTE]
>
> 如果同时配置“超时”和“重试”，则超时将应用于每次重试。 

## <a name="control-access-to-jobs"></a><a id="control-access-to-jobs"> </a><a id="jobs_acl_user_guide"> </a>控制对作业的访问

作业所有者和管理员可以通过作业访问控制授予对其作业的精细权限。 使用作业访问控制，作业所有者可以选择允许哪些其他用户或组查看作业的结果。 所有者还可以选择允许谁管理其作业的运行（即，调用“立即运行”，然后单击“取消”）。

有关详细信息，请参阅[作业访问控制](security/access-control/jobs-acl.md)。