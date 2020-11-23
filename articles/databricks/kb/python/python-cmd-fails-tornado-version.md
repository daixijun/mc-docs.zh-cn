---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 群集在安装 Bokeh 后取消 Python 命令执行 - Azure Databricks
description: 了解安装 Bokeh 后，当 Azure Databricks 群集取消 Python 命令执行时要执行的操作。
ms.openlocfilehash: 8d4508e76dcad323674d283af9c43a02e0c07479
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589473"
---
# <a name="cluster-cancels-python-command-execution-after-installing-bokeh"></a>群集在安装 Bokeh 后取消 Python 命令执行

## <a name="problem"></a>问题

群集在 Python 笔记本中返回 `Cancelled`。 检查“群集配置”页中的驱动程序日志 (`std.err`)，查看堆栈跟踪和类似于以下内容的错误消息：

```console
log4j:WARN No appenders could be found for logger (com.databricks.conf.trusted.ProjectConf$).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See https://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
OpenJDK 64-Bit Server VM warning: ignoring option MaxPermSize=512m; support was removed in 8.0
Traceback (most recent call last):
  File "/local_disk0/tmp/1551693540856-0/PythonShell.py", line 30, in <module>
    from IPython.nbconvert.filters.ansi import ansi2html
  File "/databricks/python/lib/python3.5/site-packages/IPython/nbconvert/__init__.py", line 6, in <module>
    from . import postprocessors
  File "/databricks/python/lib/python3.5/site-packages/IPython/nbconvert/postprocessors/__init__.py", line 6, in <module>
    from .serve import ServePostProcessor
  File "/databricks/python/lib/python3.5/site-packages/IPython/nbconvert/postprocessors/serve.py", line 29, in <module>
    class ProxyHandler(web.RequestHandler):
  File "/databricks/python/lib/python3.5/site-packages/IPython/nbconvert/postprocessors/serve.py", line 31, in ProxyHandler
    @web.asynchronous
AttributeError: module 'tornado.web' has no attribute 'asynchronous'
```

## <a name="cause"></a>原因

安装 `bokeh` 库时，默认将安装 `tornado` 版本 6.0a1，这是一个 alpha 版本。
Alpha 版本会导致此错误，因此解决办法是恢复到 `tornado` 的稳定版本。

## <a name="solution"></a>解决方案

按照以下步骤创建群集范围的 init 脚本。 Init 脚本会删除 `tornado` 的较新版本，然后安装稳定版本。

1. 如果 init 脚本尚不存在，请创建一个基目录来存储它：

   ```bash
   dbutils.fs.mkdirs("dbfs:/databricks/<directory>/")
   ```

2. 创建以下脚本：

   ```bash
   dbutils.fs.put("dbfs:/databricks/<directory>/tornado.sh","""
   #!/bin/bash
   pip uninstall --yes tornado
   rm -rf /home/ubuntu/databricks/python/lib/python3.5/site-packages/tornado*
   rm -rf /databricks/python/lib/python3.5/site-packages/tornado*
   /usr/bin/yes | /home/ubuntu/databricks/python/bin/pip install tornado==5.1.1
   """,True)
   ```

3. 确认该脚本存在：

   ```bash
   display(dbutils.fs.ls("dbfs:/databricks/<directory>/tornado.sh"))
   ```

4. 转到[群集配置](/databricks/clusters/clusters-manage#edit-a-cluster)页，然后单击“高级选项”切换开关。
5. 在页面底部，单击“Init 脚本”选项卡：

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/clusters/init-script-tab.png)

6. 在“目标”下拉列表中，选择“DBFS”，提供脚本的文件路径，然后单击“添加”  。
7. 重启群集。

有关详细信息，请参阅：

* [tornado 6.0a1 的 CI 故障](https://github.com/jupyter/notebook/issues/4311)
* [将代理处理程序从回调转换为协同例程](https://github.com/jupyter/nbconvert/pull/937)
* [群集范围的初始化脚本](/databricks/clusters/init-scripts#cluster-scoped-init-script)