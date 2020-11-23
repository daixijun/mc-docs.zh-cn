---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 安装 rJava 和 RJDBC 库 - Azure Databricks
description: 了解如何在 Azure Databricks 群集上安装 rJava 和 RJDBC 库。
ms.openlocfilehash: d8e6598aea3f43b74353a35d9e12e853f242e1d2
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589523"
---
# <a name="install-rjava-and-rjdbc-libraries"></a>安装 rJava 和 RJDBC 库

本文介绍如何安装 rJava 和 RJBDC 库。

## <a name="problem"></a>问题

在笔记本单元格中使用以下命令安装 rJava 和 RJDBC 库时：

```r
install.packages(c("rJava", "RJDBC"))
```

你看到以下错误：

```console
ERROR: configuration failed for package 'rJava'
```

## <a name="cause"></a>原因

RJava 和 RJDBC 包检查 Azure Databricks R 目录中不存在的 Java 依赖项和文件路径。

## <a name="solution"></a>解决方案

按照以下步骤在运行的群集上安装这些库。

1. 在 `%sh` 单元格中运行以下命令。

   ```bash
   %sh
   ls -l /usr/bin/java
   ls -l /etc/alternatives/java
   ln -s /usr/lib/jvm/java-8-openjdk-amd64 /usr/lib/jvm/default-java
   R CMD javareconf
   ```

2. 安装 rJava 和 RJDBC 包。

   ```r
   install.packages(c("rJava", "RJDBC"))
   ```

3. 验证是否已安装 rJava 包。

   ```r
   dyn.load('/usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/server/libjvm.so')
   library(rJava)
   ```