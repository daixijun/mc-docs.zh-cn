---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 在群集上安装 pyodbc 时出错 - Azure Databricks
description: 了解如何排查在 Azure Databricks 群集上安装 pyodbc 时出现的错误。
ms.openlocfilehash: b18d92b785a0c500a36474030b251cc4ca87c0dc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589961"
---
# <a name="error-when-installing-pyodbc-on-a-cluster"></a>在群集上安装 `pyodbc` 时出错

## <a name="problem"></a>问题

使用 `pip` 安装 `pyodbc` 库时，会出现以下错误之一。

```console
java.lang.RuntimeException: Installation failed with message: Collecting pyodbc
```

```console
"Library installation is failing due to missing dependencies. sasl and thrift_sasl are optional dependencies for SASL or Kerberos support"
```

## <a name="cause"></a>原因

虽然 `sasl` 和 `thrift_sasl` 是面向 SASL 或 Kerberos 支持的可选依赖项，但是必须有它们，才能成功执行 `pyodbc` 安装。

## <a name="solution"></a>解决方案

### <a name="set-up-solution-in-a-single-notebook"></a>在单个笔记本中设置解决方案

1. 在笔记本中，检查 `thrift` 版本并升级到最新版本。

   ```bash
   %sh
   pip list | egrep 'thrift-sasl|sasl'
   pip install --upgrade thrift
   ```

2. 确保已安装依赖包。

   ```bash
   %sh dpkg -l | egrep 'thrift_sasl|libsasl2-dev|gcc|python-dev'
   ```

3. 安装 `pyodbc` 之前，请安装 `nnixodbc`。

   ```bash
   %sh sudo apt-get -y install unixodbc-dev libsasl2-dev gcc python-dev
   ```

### <a name="set-up-solution-as-a-cluster-scoped-init-script"></a>将解决方案设置为群集范围的 init 脚本

可将这些命令放入单个 init 脚本并将其附加到群集。 这可确保在启动群集之前安装 `pyodbc` 的依赖库。

1. 如果基目录不存在，请创建基目录用于存储 init 脚本。 此处，以 `dbfs:/databricks/<directory>` 为例。

   ```bash
   dbutils.fs.mkdirs("dbfs:/databricks/<directory>/")
   ```

2. 创建脚本并将其保存到文件中。

   ```bash
   dbutils.fs.put("dbfs:/databricks/<directory>/tornado.sh","""
   #!/bin/bash
   pip list | egrep 'thrift-sasl|sasl'
   pip install --upgrade thrift
   dpkg -l | egrep 'thrift_sasl|libsasl2-dev|gcc|python-dev'
   sudo apt-get -y install unixodbc-dev libsasl2-dev gcc python-dev
   """,True)
   ```

3. 检查该脚本是否存在。

   ```python
   display(dbutils.fs.ls("dbfs:/databricks/<directory>/tornado.sh"))
   ```

4. 在群集配置页面上，单击“高级选项”切换开关。
5. 在页面底部，单击“Init 脚本”选项卡。

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/libraries/db-init-script-ui.png)

6. 在“目标”下拉列表中，选择“DBFS”，提供脚本的文件路径，然后单击“添加” 。
7. 重新启动群集

有关群集范围的 init 脚本的更多详细信息，请参阅[群集范围的 init 脚本](/databricks/clusters/init-scripts#cluster-scoped-init-scripts)。