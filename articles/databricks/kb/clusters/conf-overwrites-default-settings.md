---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 添加配置设置会覆盖所有默认的 spark.executor.extraJavaOptions 设置 - Azure Databricks
description: 了解如何解决 Azure Databricks 中覆盖的配置设置。
ms.openlocfilehash: 12d9ba06c17c9fe7f0be6a989e185f84d6e6f3fb
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589890"
---
# <a name="adding-a-configuration-setting-overwrites-all-default-sparkexecutorextrajavaoptions-settings"></a>添加配置设置会覆盖所有默认的 `spark.executor.extraJavaOptions` 设置

## <a name="problem"></a>问题

当通过在 Apache“Spark 配置”文本区域中输入配置设置来添加配置设置时，新设置将替换现有设置，而不是进行追加。

## <a name="version"></a>Version

Databricks Runtime 5.1 及更低版本。

## <a name="cause"></a>原因

当群集重启时，群集将从“群集”UI 中创建的配置文件中读取设置，并覆盖默认设置。

例如，将以下 `extraJavaOptions` 添加到“Spark 配置”文本区域：

```java
spark.executor.extraJavaOptions -
javaagent:/opt/prometheus_jmx_exporter/jmx_prometheus_javaagent.jar=9404:/opt/prometheus
_jmx_exporter/jmx_prometheus_javaagent.yml
```

然后，在 `spark.executor.extraJavaOptions` 下的“Spark UI”>“环境”>“Spark 属性”中，则仅显示新添加的配置设置：

```java
-javaagent:/opt/prometheus_jmx_exporter/jmx_prometheus_javaagent.jar=9404:/opt/prometheus
_jmx_exporter/jmx_prometheus_javaagent.yml
```

删除任何现有设置。

作为参考，默认设置为：

```java
-Djava.io.tmpdir=/local_disk0/tmp -XX:ReservedCodeCacheSize=256m -
XX:+UseCodeCacheFlushing -Ddatabricks.serviceName=spark-executor-1 -
Djava.security.properties=/databricks/spark/dbconf/java/extra.security -XX:+PrintFlagsFinal -
XX:+PrintGCDateStamps -verbose:gc -XX:+PrintGCDetails -Xss4m -
Djavax.xml.datatype.DatatypeFactory=com.sun.org.apache.xerces.internal.jaxp.datatype.Dataty
peFactoryImpl -
Djavax.xml.parsers.DocumentBuilderFactory=com.sun.org.apache.xerces.internal.jaxp.Documen
tBuilderFactoryImpl -
Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFact
oryImpl -
Djavax.xml.validation.SchemaFactory=https://www.w3.org/2001/XMLSchema=com.sun.org.apache.xerces.internal.jaxp.validation.XMLSchemaFactory -
Dorg.xml.sax.driver=com.sun.org.apache.xerces.internal.parsers.SAXParser -
Dorg.w3c.dom.DOMImplementationSourceList=com.sun.org.apache.xerces.internal.dom.DOMX
SImplementationSourceImpl
```

## <a name="solution"></a>解决方案

将新的配置设置添加到 `spark.executor.extraJavaOptions`，但不丢失默认设置：

1. 在“Spark UI”>“环境”>“Spark 属性”中，选择并复制默认为 `spark.executor.extraJavaOptions` 设置的所有属性。
2. 单击 **“编辑”** 。
3. 在“Spark 配置”文本区域（“群集”>“群集名称”>“高级选项”>“Spark”）中，粘贴默认设置 。
4. 在默认设置下追加新的配置设置。
5. 单击文本区域外部，然后单击“确认”。
6. 重启群集。

例如，假设你将以下设置粘贴到“Spark 配置”文本区域中。 新配置设置将追加到默认设置。

```java
spark.executor.extraJavaOptions = -Djava.io.tmpdir=/local_disk0/tmp -
XX:ReservedCodeCacheSize=256m -XX:+UseCodeCacheFlushing -Ddatabricks.serviceName=spark-
executor-1 -Djava.security.properties=/databricks/spark/dbconf/java/extra.security -
XX:+PrintFlagsFinal -XX:+PrintGCDateStamps -verbose:gc -XX:+PrintGCDetails -Xss4m -
Djavax.xml.datatype.DatatypeFactory=com.sun.org.apache.xerces.internal.jaxp.datatype.Dataty
peFactoryImpl -
Djavax.xml.parsers.DocumentBuilderFactory=com.sun.org.apache.xerces.internal.jaxp.DocumentB
uilderFactoryImpl -
Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactor
yImpl -
Djavax.xml.validation.SchemaFactory:https://www.w3.org/2001/XMLSchema=com.sun.org.apache.xer
ces.internal.jaxp.validation.XMLSchemaFactory -
Dorg.xml.sax.driver=com.sun.org.apache.xerces.internal.parsers.SAXParser -
Dorg.w3c.dom.DOMImplementationSourceList=com.sun.org.apache.xerces.internal.dom.DOMXSImplem
entationSourceImpl -
javaagent:/opt/prometheus_jmx_exporter/jmx_prometheus_javaagent.jar=9404:/opt/prometheus_jm
x_exporter/jmx_prometheus_javaagent.yml
```

重启群集后，默认设置和新添加的配置设置会显示在“Spark UI”>“环境”>“Spark 属性”中。