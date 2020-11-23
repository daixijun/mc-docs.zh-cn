---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: Python 命令执行失败，出现 AttributeError - Azure Databricks
description: 了解在 Azure Databricks 笔记本中的 Python 命令失败并出现 AttributeError 时要执行的操作。
ms.openlocfilehash: 6fb7f761fae79a9e1ced2ad4314b07ef36ab7d23
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589531"
---
# <a name="python-command-execution-fails-with-attributeerror"></a>Python 命令执行失败，出现错误 `AttributeError`

本文可帮助你解决 Python 命令执行失败并出现 `AttributeError` 的情况。

## <a name="problem-tuple-object-has-no-attribute-type"></a>问题：`'tuple' object has no attribute 'type'`

运行笔记本时，Python 命令执行失败，并出现以下错误和堆栈跟踪：

```console
AttributeError: 'tuple' object has no attribute 'type'
```

```console
Traceback (most recent call last):
File "/local_disk0/tmp/1547561952809-0/PythonShell.py", line 23, in <module>
  import matplotlib as mpl
File "/databricks/python/local/lib/python2.7/site-packages/matplotlib/__init__.py", line 122, in <module>
  from matplotlib.cbook import is_string_like, mplDeprecation, dedent, get_label
File "/databricks/python/local/lib/python2.7/site-packages/matplotlib/cbook.py", line 33, in <module>
  import numpy as np
File "/databricks/python/local/lib/python2.7/site-packages/numpy/__init__.py", line 142, in <module>
  from . import core
File "/databricks/python/local/lib/python2.7/site-packages/numpy/core/__init__.py", line 57, in <module>
  from . import numerictypes as nt
File "/databricks/python/local/lib/python2.7/site-packages/numpy/core/numerictypes.py", line 111, in <module>
  from ._type_aliases import (
File "/databricks/python/local/lib/python2.7/site-packages/numpy/core/_type_aliases.py", line 63, in <module>
  _concrete_types = {v.type for k, v in _concrete_typeinfo.items()}
File "/databricks/python/local/lib/python2.7/site-packages/numpy/core/_type_aliases.py", line 63, in <setcomp>
  _concrete_types = {v.type for k, v in _concrete_typeinfo.items()}
AttributeError: 'tuple' object has no attribute 'type'

19/01/15 11:29:26 WARN PythonDriverWrapper: setupRepl:ReplId-7d8d1-8cc01-2d329-9: at the end, the status is
Error(ReplId-7d8d1-8cc01-2d329-,com.databricks.backend.daemon.driver.PythonDriverLocal$PythonException: Python shell failed to start in 30 seconds)
```

### <a name="cause"></a>原因

`numpy` 的较新版本 1.16.1（由某些 PyPI 客户端默认安装）与其他库不兼容。

### <a name="solution"></a>解决方案

按照以下步骤创建[群集范围内的 init 脚本](/databricks/clusters/init-scripts#cluster-scoped-init-script)，该脚本将删除 `numpy` 的当前版本并安装其 1.15.0 版本。

1. 如果 init 脚本尚不存在，请创建一个基目录来存储它：

   ```python
   dbutils.fs.mkdirs("dbfs:/databricks/<directory>/")
   ```

2. 创建以下脚本：
   * 如果群集正在运行 Python 2，请使用此 init 脚本：

     ```python
     dbutils.fs.put("dbfs:/databricks/<directory>/numpy.sh","""
     #!/bin/bash
     pip uninstall --yes numpy
     rm -rf /home/ubuntu/databricks/python/lib/python2.7/site-packages/numpy*
     rm -rf /databricks/python/lib/python2.7/site-packages/numpy*
     /usr/bin/yes | /home/ubuntu/databricks/python/bin/pip install numpy==1.15.0
     """,True)
     ```

   * 如果群集正在运行 Python 3，请使用此 init 脚本：

     ```python
     dbutils.fs.put("dbfs:/databricks/<directory>/numpy.sh","""
     #!/bin/bash
     pip uninstall --yes numpy
     rm -rf /home/ubuntu/databricks/python/lib/python3.5/site-packages/numpy*
     rm -rf /databricks/python/lib/python3.5/site-packages/numpy*
     /usr/bin/yes | /home/ubuntu/databricks/python/bin/pip install numpy==1.15.0
     """,True)
     ```

3. 确认该脚本存在：

   ```python
   display(dbutils.fs.ls("dbfs:/databricks/<directory>/numpy.sh"))
   ```

4. 转到[群集配置](/databricks/clusters/clusters-manage#edit-a-cluster)页，然后单击“高级选项”切换开关。
5. 在页面底部，单击“Init 脚本”选项卡：

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/clusters/init-script-tab.png)

6. 在“目标”下拉列表中，选择“DBFS”，提供脚本的文件路径，然后单击“添加”  。
7. 重启群集。
8. 在 PyPI 客户端中，将 `numpy` 安装固定到版本 1.15.1，这是最新的工作版本。

## <a name="problem-module-lib-has-no-attribute-ssl_st_init"></a>问题：`module 'lib' has no attribute 'SSL_ST_INIT'`

当你运行笔记本时，库安装失败，笔记本上执行的所有 Python 命令均被取消，并出现以下错误和堆栈跟踪：

```console
AttributeError: module 'lib' has no attribute 'SSL_ST_INIT'
```

```console
Traceback (most recent call last): File "/databricks/python3/bin/pip", line 7, in <module>
 from pip._internal import main
File "/databricks/python3/lib/python3.5/site-packages/pip/_internal/__init__.py", line 40, in <module>
 from pip._internal.cli.autocompletion import autocomplete
File "/databricks/python3/lib/python3.5/site-packages/pip/_internal/cli/autocompletion.py", line 8, in <module>
 from pip._internal.cli.main_parser import create_main_parser
File "/databricks/python3/lib/python3.5/site-packages/pip/_internal/cli/main_parser.py", line 12, in <module>
 from pip._internal.commands import (
File "/databricks/python3/lib/python3.5/site-packages/pip/_internal/commands/__init__.py", line 6, in <module>
 from pip._internal.commands.completion import CompletionCommand
File "/databricks/python3/lib/python3.5/site-packages/pip/_internal/commands/completion.py", line 6, in <module>
 from pip._internal.cli.base_command import Command
File "/databricks/python3/lib/python3.5/site-packages/pip/_internal/cli/base_command.py", line 20, in <module>
 from pip._internal.download import PipSession
File "/databricks/python3/lib/python3.5/site-packages/pip/_internal/download.py", line 15, in <module>
 from pip._vendor import requests, six, urllib3
File "/databricks/python3/lib/python3.5/site-packages/pip/_vendor/requests/__init__.py", line 97, in <module>
 from pip._vendor.urllib3.contrib import pyopenssl
File "/databricks/python3/lib/python3.5/site-packages/pip/_vendor/urllib3/contrib/pyopenssl.py", line 46, in <module>
 import OpenSSL.SSL
File "/databricks/python3/lib/python3.5/site-packages/OpenSSL/__init__.py", line 8, in <module>
 from OpenSSL import rand, crypto, SSL
File "/databricks/python3/lib/python3.5/site-packages/OpenSSL/SSL.py", line 124, in <module>
 SSL_ST_INIT = _lib.SSL_ST_INIT AttributeError: module 'lib' has no attribute 'SSL_ST_INIT'
```

### <a name="cause"></a>原因

默认情况下已将 `cryptography` 包的较新版本（在本例中为 2.7）与其他 PyPI 库一起安装，并且此 `cryptography` 版本与 Databricks Runtimes 中包含的 `pyOpenSSL` 版本不兼容。

### <a name="solution"></a>解决方案

若要解决并避免此问题，请在安装任何库之前将 `pyOpenSSL` 升级到最新版本。
使用[群集范围内的 init 脚本](/databricks/clusters/init-scripts#cluster-scoped-init-script)来安装最新版本的 `pyOpenSSL`：

1. 创建基目录以存储 init 脚本：

   ```python
   dbutils.fs.mkdirs("dbfs:/databricks/<directory>/")
   ```

2. 创建以下脚本：

   ```python
   dbutils.fs.put("dbfs:/databricks/<directory>/openssl_fix.sh","""
   #!/bin/bash
   echo "Removing pyOpenSSL package"
   rm -rf /databricks/python2/lib/python2.7/site-packages/OpenSSL
   rm -rf /databricks/python2/lib/python2.7/site-packages/pyOpenSSL-16.0.0-*.egg-info
   rm -rf /databricks/python3/lib/python3.5/site-packages/OpenSSL
   rm -rf /databricks/python3/lib/python3.5/site-packages/pyOpenSSL-16.0.0*.egg-info
   /databricks/python2/bin/pip install pyOpenSSL==19.0.0
   /databricks/python3/bin/pip3 install pyOpenSSL==19.0.0
   """, True)
   ```

3. 确认该脚本存在：

   ```python
   display(dbutils.fs.ls("dbfs:/databricks/<directory>/openssl_fix.sh"))
   ```

4. 转到[群集配置](/databricks/clusters/clusters-manage#edit-a-cluster)页，然后单击“高级选项”切换开关。
5. 在页面底部，单击“Init 脚本”选项卡：

   > [!div class="mx-imgBorder"]
   > ![no-alternative-text](../_static/images/clusters/init-script-tab.png)

6. 在“目标”下拉列表中，选择“DBFS”，提供脚本的文件路径，然后单击“添加”  。
7. 重启群集。