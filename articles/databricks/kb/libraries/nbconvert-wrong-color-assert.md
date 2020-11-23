---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 10/01/2020
title: Python 命令失败，提示断言错误“颜色格式错误” - Azure Databricks
description: 解决当 Python 命令失败时由 nbconvert 引起的错误颜色格式 AssertionError。
category: Libraries
author: adampavDB
db-author: john.lourdu@databricks.com
ms.openlocfilehash: ba3629b76f9d5cf6234c608a5ef63c2cf3ea95a1
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589738"
---
# <a name="python-command-fails-with-assertionerror-wrong-color-format"></a>Python 命令失败，提示 `AssertionError: wrong color format`

## <a name="problem"></a>问题

运行 Python 笔记本，它会失败并显示 `AssertionError: wrong color format` 消息。

示例堆栈跟踪：

```console
  File "/local_disk0/tmp/1599775649524-0/PythonShell.py", line 39, in <module>
    from IPython.nbconvert.filters.ansi import ansi2html
  File "<frozen importlib._bootstrap>", line 983, in _find_and_load
  File "<frozen importlib._bootstrap>", line 963, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 902, in _find_spec
  File "<frozen importlib._bootstrap>", line 876, in _find_spec_legacy
  File "/databricks/python/lib/python3.7/site-packages/IPython/utils/shimmodule.py", line 36, in find_module
    mod = import_item(mirror_name)
  File "/databricks/python/lib/python3.7/site-packages/IPython/utils/importstring.py", line 31, in import_item
    module = __import__(package, fromlist=[obj])
  File "/databricks/python/lib/python3.7/site-packages/nbconvert/__init__.py", line 4, in <module>
    from .exporters import *
  File "/databricks/python/lib/python3.7/site-packages/nbconvert/exporters/__init__.py", line 4, in <module>
    from .slides import SlidesExporter
  File "/databricks/python/lib/python3.7/site-packages/nbconvert/exporters/slides.py", line 12, in <module>
    from ..preprocessors.base import Preprocessor
  File "/databricks/python/lib/python3.7/site-packages/nbconvert/preprocessors/__init__.py", line 7, in <module>
    from .csshtmlheader import CSSHTMLHeaderPreprocessor
  File "/databricks/python/lib/python3.7/site-packages/nbconvert/preprocessors/csshtmlheader.py", line 14, in <module>
    from jupyterlab_pygments import JupyterStyle
  File "/databricks/python/lib/python3.7/site-packages/jupyterlab_pygments/__init__.py", line 4, in <module>
    from .style import JupyterStyle
  File "/databricks/python/lib/python3.7/site-packages/jupyterlab_pygments/style.py", line 10, in <module>
    class JupyterStyle(Style):
  File "/databricks/python/lib/python3.7/site-packages/pygments/style.py", line 101, in __new__
    ndef[0] = colorformat(styledef)
  File "/databricks/python/lib/python3.7/site-packages/pygments/style.py", line 58, in colorformat
    assert False, "wrong color format %r" % text
AssertionError: wrong color format 'var(--jp-mirror-editor-variable-color)'
```

## <a name="cause"></a>原因

这是由 `nbconvert` 库的不兼容版本引起的。 如果没有将 `nbconvert` 固定到正确的版本，则可能会意外通过 PyPI 安装不兼容的版本。

## <a name="solution"></a>解决方案

在群集上手动安装 `nbconvert` 版本 6.0.0rc0。 这将重写可能已安装的库的任何不正确版本。

1. 单击“群集”图标 ![“群集”图标](../_static/images/clusters/clusters-icon.png) （在边栏中）。
2. 单击群集名称。
3. 单击 **“库”** 选项卡。
4. 单击“新安装”。
5. 在“库源”按钮列表中，选择“PyPi”。
6. 在“包”字段中输入 `nbconvert==6.0.0rc0`。
7. 单击“安装”。