---
title: 使用 Databricks Jar 转换数据
description: 了解如何通过运行 Databricks Jar 处理或转换数据。
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: WenJason
manager: digimobile
origin.date: 03/15/2018
ms.date: 11/23/2020
ms.openlocfilehash: 1dabaa3ff05741551dbc5e1ed8dd7309b31b07f4
ms.sourcegitcommit: c89f1adcf403f5845e785064350136698eed15b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680656"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>通过运行 Azure Databricks 中的 Jar 活动转换数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[数据工厂管道](concepts-pipelines-activities.md)中的 Azure Databricks Jar 活动在 Azure Databricks 群集中运行 Spark Jar。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。

## <a name="databricks-jar-activity-definition"></a>Databricks Jar 活动定义

下面是 Databricks Jar 活动的示例 JSON 定义：

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Databricks Jar 活动属性

下表描述了 JSON 定义中使用的 JSON 属性：

|properties|描述|必需|
|:--|---|:-:|
|name|管道中活动的名称。|是|
|description|描述活动用途的文本。|否|
|type|对于 Databricks Jar 活动，活动类型是 DatabricksSparkJar。|是|
|linkedServiceName|Databricks 链接服务的名称，Jar 活动在其上运行。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。|是|
|mainClassName|类的全名，包含要执行的主要方法。 此类必须包含在作为库提供的 JAR 中。|是|
|parameters|将传递到主要方法的参数。  这是一个字符串数组。|否|
|库|要安装在将执行作业的群集上的库列表。 它可以是 <string, object> 数组|是（至少有一个包含 mainClassName 方法）|

> [!NOTE]
> **已知问题** - 当使用同一[交互式群集](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks)来运行并发的 Databricks Jar 活动（没有重启群集）时，在 Databricks 中存在一个已知问题，即第一个活动的输入参数也会被后续活动使用。 这会导致将不正确的参数传递给后续作业。 若要缓解此问题，请改用[作业群集](compute-linked-services.md#example---using-new-job-cluster-in-databricks)。 

## <a name="supported-libraries-for-databricks-activities"></a>databricks 活动支持的库

在以上 Databricks 活动定义中，指定这些库类型：jar、egg、maven、pypi、cran。

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

