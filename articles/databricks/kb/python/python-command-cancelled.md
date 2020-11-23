---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/26/2020
title: 群集因库冲突而取消 Python 命令执行 - Azure Databricks
description: 了解如果 Azure Databricks 群集因库冲突而取消 Python 命令执行，该执行哪些操作。
ms.openlocfilehash: 874115947b6a1477c2a0b51d6059e447dd890b4a
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589536"
---
# <a name="cluster-cancels-python-command-execution-due-to-library-conflict"></a><a id="cluster-cancels-python-command-execution-due-to-library-conflict"> </a><a id="python-command-cancelled"> </a>群集因库冲突而取消 Python 命令执行

## <a name="problem"></a>问题

群集在 Python 笔记本中返回 `Cancelled`。 其他所有语言的笔记本在该群集上成功执行。

## <a name="cause"></a>原因

将版本冲突的库（例如 `ipython`、`ipywidgets`、`numpy`、`scipy` 或 `pandas`）安装到 `PYTHONPATH` 时，Python REPL 可能会中断，导致所有命令在 30 秒后返回 `Cancelled`。 这也会中断 %sh（允许在 Python 笔记本单元格中输入 Shell 脚本的笔记本宏）。

> [!NOTE]
>
> * 如果最近在群集上安装了 `bokeh` 库，则该安装可能包含不兼容的 `tornado` 库。 请参阅[群集在安装 Bokeh 后取消 Python 命令执行](python-cmd-fails-tornado-version.md)。
> * 如果安装了 `numpy` 库，则它可能不兼容。 请参阅 [Python 命令执行失败，出现 AttributeError](python-exec-display-cancelled.md)。

## <a name="solution"></a>解决方案

若要解决此问题，请执行以下操作：

1. 确定发生冲突的库并将其卸载。
2. 在笔记本中或使用群集范围内的 init 脚本安装库的正确版本。

### <a name="identify-the-conflicting-library"></a>确定发生冲突的库

1. 一次卸载一个库，检查 Python REPL 是否仍然中断。
2. 如果 REPL 仍然中断，请重新安装已删除的库，然后删除下一个库。
3. 找到导致 REPL 中断的库后，使用下面两种方法之一安装该库的正确版本。

还可检查群集（在“群集配置”页面上）的驱动程序日志 (`std.err`)，获取堆栈跟踪和错误消息来帮助确定库冲突。

### <a name="install-the-correct-library"></a>安装正确的库

执行下列操作之一：

#### <a name="option-1-install-in-a-notebook-using-pip3"></a>选项 1：使用 pip3 在笔记本中进行安装

```python
%sh sudo apt-get -y install python3-pip
  pip3 install <library-name>
```

#### <a name="option-2-install-using-a-cluster-scoped-init-script"></a>选项 2：使用群集范围内的 init 脚本进行安装

按照以下步骤创建[群集范围内的 init 脚本](/databricks/clusters/init-scripts#cluster-scoped-init-script)，该脚本将安装库的正确版本。 将示例中的 `<library-name>` 替换为要安装的库的文件名。

1. 如果 init 脚本尚不存在，请创建一个基目录来存储它：

   ```bash
   dbutils.fs.mkdirs("dbfs:/databricks/<directory>/")
   ```

2. 创建以下脚本：

   ```bash
   dbutils.fs.put("/databricks/init/cluster-name/<library-name>.sh","""
    #!/bin/bash
    sudo apt-get -y install python3-pip
    sudo pip3 install <library-name>
    """, True)
   ```

3. 确认该脚本存在：

   ```bash
   display(dbutils.fs.ls("dbfs:/databricks/<directory>/<library-name>.sh"))
   ```

4. 转到[群集配置](/databricks//clusters/clusters-manage#edit-a-cluster)页，然后单击“高级选项”切换开关。
5. 在页面底部，单击“Init 脚本”选项卡：

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/clusters/init-script-tab.png)

6. 在“目标”下拉列表中，选择“DBFS”，提供脚本的文件路径，然后单击“添加”  。
7. 重启群集。