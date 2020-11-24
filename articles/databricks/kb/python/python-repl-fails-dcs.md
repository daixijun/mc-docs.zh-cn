---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 03/31/2020
title: Python REPL 无法在 Docker 中启动 - Azure Databricks
description: 了解如何修复 Python virtualenv 错误，该错误会阻止 REPL 在 Docker 容器中启动
category: Python
author: kr-arjun
db-author: arjun.kaimaparambilrajan@databricks.com
ms.openlocfilehash: 22385cdfc8951494cfa948e3ee698c11f88ba0b8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589532"
---
# <a name="python-repl-fails-to-start-in-docker"></a>Python REPL 无法在 Docker 中启动

## <a name="problem"></a>问题

当使用包含预生成 Python 库的 Docker 容器时，Python 命令会失败，并且不会创建虚拟环境。 以下错误消息在驱动程序日志中可见。

```console
20/02/29 16:38:35 WARN PythonDriverWrapper: Failed to start repl ReplId-5b591-0ce42-78ef3-7
java.io.IOException: Cannot run program "/local_disk0/pythonVirtualEnvDirs/virtualEnv-56a5be60-3e71-486f-ac04-08e8f2491032/bin/python" (in directory "."): error=2, No such file or directory
        at java.lang.ProcessBuilder.start(ProcessBuilder.java:1048)
        at org.apache.spark.util.Utils$.executeCommand(Utils.scala:1367)
        at org.apache.spark.util.Utils$.executeAndGetOutput(Utils.scala:1393)
        at org.apache.spark.util.Utils$.executePythonAndGetOutput(Utils.scala:
…
        at java.lang.Thread.run(Thread.java:748)
Caused by: java.io.IOException: error=2, No such file or directory
        at java.lang.UNIXProcess.forkAndExec(Native Method)
        at java.lang.UNIXProcess.<init>(UNIXProcess.java:247)
        at java.lang.ProcessImpl.start(ProcessImpl.java:134)
        at java.lang.ProcessBuilder.start(ProcessBuilder.java:1029)
        ... 17 more
```

可以通过在笔记本中运行以下命令来确认问题：

```bash
%sh virtualenv --no-site-packages
```

结果是一条错误消息，类似于以下内容：

```console
usage: virtualenv [--version] [--with-traceback] [-v | -q] [--discovery {builtin}] [-p py] [--creator {builtin,cpython3-posix,venv}] [--seeder {app-data,pip}] [--no-seed] [--activators comma_separated_list] [--clear]
                  [--system-site-packages] [--symlinks | --copies] [--download | --no-download] [--extra-search-dir d [d ...]] [--pip version] [--setuptools version] [--wheel version] [--no-pip] [--no-setuptools] [--no-wheel]
                  [--clear-app-data] [--symlink-app-data] [--prompt prompt] [-h]
                  dest
virtualenv: error: the following arguments are required: dest
```

`virtualenv` 命令无法识别 `--no-site-packages` 选项。

## <a name="version"></a>Version

此问题会影响所有当前的 Databricks Runtime 版本，但包含 Conda 的 Databricks Runtime 版本除外。 它会影响 `virtualenv` 库 20.0.0 及更高版本。

## <a name="cause"></a>原因

此问题是由于在 Docker 容器中使用不支持 `--no-site-packages` 选项的 Python `virtualenv` 库版本导致的。

Databricks Runtime 需要支持 `--no-site-packages` 选项的 `virtualenv` 库。 `virtualenv` 库 20.0.0 及更高版本中已删除此选项。

可以通过在笔记本中运行以下命令来验证 `virtualenv` 库版本：

```bash
%sh virtualenv --version
```

## <a name="solution"></a>解决方案

可以通过在安装 `virtualenv` 库时指定兼容的版本来解决此问题。

例如，在 Dockerfile 中设置 `virtualenv==16.0.0` 会安装 `virtualenv` 库版本 16.0.0。 此版本的库支持所需的选项。