---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 07/09/2020
title: 替换默认库 jar - Azure Databricks
description: 了解如何将默认的 Java 或 Scala 库 jar 替换为其他版本。
category: Libraries
author: ram-sankarasubramanian
db-author: ram.sankarasubramanian@databricks.com
ms.openlocfilehash: 7ffe6a13f31ff7ca49a30840f108bce51fa81fed
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589737"
---
# <a name="replace-a-default-library-jar"></a>替换默认库 jar

Azure Databricks 包含许多默认的 Java 和 Scala 库。 可以通过以下方法将这些库中的任意一个替换为其他版本：使用[群集范围内的 init 脚本](/databricks/clusters/init-scripts#example-cluster-scoped-init-script)删除默认库 jar，然后安装所需的版本。

> [!IMPORTANT]
>
> 删除默认库并安装新版本可能会导致性能不稳定或完全损坏 Azure Databricks 群集。 在运行生产作业之前，应在环境中对任何新库版本进行全面测试。

## <a name="identify-the-artifact-id"></a>标识项目 ID

若要标识想要删除的 jar 文件名，请执行以下操作：

1. 从[受支持的版本](/databricks/release-notes/runtime/releases#supported-databricks-runtime-releases-and-support-schedule)列表中，单击正在使用的 Databricks Runtime 版本。
2. 导航至“Java 和 Scala 库”部分。
3. 标识要删除的库的项目 ID。

## <a name="use-the-artifact-id-to-find-the-jar-filename"></a>使用项目 ID 查找 jar 文件名

在笔记本中使用 `ls -l` 命令查找包含项目 ID 的 jar。例如，要在 Databricks Runtime 7.0 中找到 `spark-snowflake_2.12` 项目 ID 的 jar 文件名，可以使用以下代码：

```scala
%sh
ls -l /databricks/jars/*spark-snowflake_2.12*
```

这将返回 jar 文件名

```console
`----workspace_spark_3_0--maven-trees--hive-2.3__hadoop-2.7--net.snowflake--spark-snowflake_2.12--net.snowflake__spark-snowflake_2.12__2.5.9-spark_2.4.jar`.
```

## <a name="upload-the-replacement-jar-file"></a>上传替换 jar 文件

将替换 jar 文件上传到 DBFS 路径。

## <a name="create-the-init-script"></a>创建 init 脚本

使用以下模板创建[群集范围的 init 脚本](/databricks/clusters/init-scripts#example-cluster-scoped-init-script)。

```scala
#!/bin/bash
rm -rf /databricks/jars/<jar_filename_to_remove>.jar
cp /dbfs/<path_to_replacement_jar>/<replacement_jar_filename>.jar /databricks/jars/
```

使用上一步中的 `spark-snowflake_2.12` 示例将生成类似如下所示的 init 脚本：

```scala
#!/bin/bash
rm -rf /databricks/jars/----workspace_spark_3_0--maven-trees--hive-2.3__hadoop-2.7--net.snowflake--spark-snowflake_2.12--net.snowflake__spark-snowflake_2.12__2.5.9-spark_2.4.jar
cp /dbfs/FileStore/jars/e43fe9db_c48d_412b_b142_cdde10250800-spark_snowflake_2_11_2_7_1_spark_2_4-b2adc.jar /databricks/jars/
```

## <a name="install-the-init-script-and-restart"></a>安装 init 脚本并重启

1. 按照[配置群集范围的 init 脚本](/databricks/clusters/init-scripts#configure-a-cluster-scoped-init-script)中的说明操作，在群集上安装群集范围的 init 脚本。
2. 重启群集。