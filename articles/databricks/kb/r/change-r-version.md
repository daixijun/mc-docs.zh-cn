---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 更改 R (r-base) 的版本 - Azure Databricks
description: 了解如何在 Azure Databricks 群集上更改 R 的版本。
ms.openlocfilehash: e432bb0ca6403f4f1338a38cab0ac13e7008139b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589525"
---
# <a name="change-version-of-r-r-base"></a>更改 R (r-base) 的版本

这些说明介绍了如何在群集上安装 R (r-base) 的其他版本。 可以在每个 [Databricks Runtime 发行说明](/databricks/release-notes/runtime/index)的“系统环境”部分中检查需要与每个 Databricks Runtime 版本一起安装的默认 r-base 版本。

## <a name="list-available-r-base-core-versions"></a>列出可用的 r-base-core 版本

若要列出可以安装的 r-base-core 版本以及版本格式，请执行以下操作：

1. 在笔记本单元格中粘贴以下 shell 命令：

   ```bash
   %sh
   add-apt-repository 'deb [arch=amd64,i386] https://cran.rstudio.com/bin/linux/ubuntu xenial//'
   apt-get -y update
   apt-cache madison r-base-core
   ```

2. 运行单元。

例如，可以通过指定 `3.3.3-1xenial0` 来安装版本 3.3.3。

## <a name="install-a-specific-r-version"></a>安装特定的 R 版本

1. 将以下 shell 命令粘贴到笔记本单元格中。 将 `<r-version>` 设置为要安装的 R 版本。 将 `<init-script-path>` 设置为 `/dbfs` 下的文件路径，该位置将用于保存此 init 脚本。

   ```bash
   %sh

   R_VERSION='<r-version>'
   INIT_SCRIPT_PATH='<init-script-path>'

   mkdir -p $(dirname $INIT_SCRIPT_PATH)

   echo "set -e

   # Add the repository containing another version of R
   add-apt-repository 'deb [arch=amd64,i386] https://cran.rstudio.com/bin/linux/ubuntu xenial//'
   apt-get -y update

   # Uninstall current R version
   apt-get remove -y r-base-core

   # Install another version of R
   apt-get install -y r-base-core=$R_VERSION

   # Must install Rserve to use Databricks notebook
   R -e \"install.packages('Rserve', repos='https://rforge.net/', type = 'source')\"
   R -e \"install.packages('hwriterPlus', repos='https://mran.revolutionanalytics.com/snapshot/2017-02-26')\"" > $INIT_SCRIPT_PATH
   ```

2. 运行该笔记本单元格，以将 init 脚本保存到 DBFS 上的文件。
3. 使用[群集范围内的 init 脚本](/databricks/clusters/init-scripts#cluster-scoped-init-script)配置群集。  在群集创建 UI 中指定 init 脚本路径时，请修改 init 脚本路径的格式，以将 `/dbfs` 更改为 `dbfs:/`。  例如，如果已将 `<init-script-path>` 设置为 `/dbfs/examplepath/change-r-base.sh`，那么在群集创建 UI 中，请将 init 脚本路径指定为 `dbfs:/examplepath/change-r-base.sh`。
4. 在该群集启动后，请通过在笔记本单元格中运行 `%r R.version` 来验证是否安装了所需的 R 版本。