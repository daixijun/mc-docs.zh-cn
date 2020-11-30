---
title: 使用 Databricks Python 转换数据
description: 了解如何通过运行 Databricks Python 处理或转换数据。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
origin.date: 03/15/2018
ms.date: 11/23/2020
author: WenJason
ms.author: v-jay
ms.reviewer: maghan
manager: digimobile
ms.custom: devx-track-python
ms.openlocfilehash: 1d8fc681a69296a0f9dce55f8760fd07958cdfea
ms.sourcegitcommit: c89f1adcf403f5845e785064350136698eed15b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680655"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>通过运行 Azure Databricks 中的 Python 活动转换数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


[数据工厂管道](concepts-pipelines-activities.md)中的 Azure Databricks Python 活动在 Azure Databricks 群集中运行 Python 文件。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。

## <a name="databricks-python-activity-definition"></a>Databricks Python 活动定义

下面是 Databricks Python 活动的示例 JSON 定义：

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Databricks Python 活动属性

下表描述了 JSON 定义中使用的 JSON 属性：

|properties|描述|必需|
|---|---|---|
|name|管道中活动的名称。|是|
|description|描述活动用途的文本。|否|
|type|对于 Databricks Python 活动，活动类型是 DatabricksSparkPython。|是|
|linkedServiceName|Databricks 链接服务的名称，Python 活动在其上运行。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。|是|
|pythonFile|要执行的 Python 文件的 URI。 仅支持 DBFS 路径。|是|
|parameters|将传递到 Python 文件的命令行参数。 这是一个字符串数组。|否|
|库|要安装在将执行作业的群集上的库列表。 它可以是 <string, object> 数组|否|

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

