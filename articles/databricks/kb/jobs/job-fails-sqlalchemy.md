---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 03/12/2020
title: Databricks Runtime 5.5 LTS 上的作业失败，出现 SQLAlchemy 包错误 - Azure Databricks
description: 了解如何解决在 Databricks Runtime 5.5 LTS 群集上将 SQLAlchemy 从 1.3.13 升级到 1.3.15 时发生的作业失败。
category: Job
author: tanvirkcs
db-author: tanvir.kherada@databricks.com
ms.openlocfilehash: efe87d2cd5251ea620e1fbff8e108a406d185735
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589978"
---
# <a name="jobs-failing-on-databricks-runtime-55-lts-with-an-sqlalchemy-package-error"></a>Databricks Runtime 5.5 LTS 上的作业失败，出现 SQLAlchemy 包错误

## <a name="problem"></a>问题

需要第三方库 [SQLAlchemy](https://www.sqlalchemy.org/) 的 Azure Databricks 作业失败。 此问题大约于 2020 年 3 月 10 日开始发生。

作业群集和通用群集的错误消息位置有所不同，但它类似于下面的示例错误消息：

```console
Library installation failed for library pypi {
 package: "sqlalchemy"
}
. Error messages:
java.lang.RuntimeException: ManagedLibraryInstallFailed: org.apache.spark.SparkException: Process List(/databricks/python/bin/pip, install, sqlalchemy, --disable-pip-version-check) exited with code 2. ERROR: Exception:
```

### <a name="failure-on-job-clusters"></a>作业群集上的失败

在作业群集上，该错误会表现为无法启动。 可以通过查看作业运行结果并查找类似于示例错误消息的文本来确认问题。

### <a name="failure-on-all-purpose-clusters"></a>通用群集上的失败

如果通用群集使用 Databricks Runtime 5.5 LTS，则可以在工作区 UI 中查看错误消息。

1. 单击“群集”。
2. 单击群集的名称。
3. 单击“库”。
4. 单击“sqlalchemy”。
5. 阅读“消息”标题下的错误消息。

查找类似于示例错误消息的文本。

## <a name="version"></a>Version

此问题影响使用 SQLAlchemy 1.3.15 的 Databricks Runtime 5.5 LTS 群集。

## <a name="cause"></a>原因

在 2020 年 3 月 10 日，[PyPI](https://pypi.org/) 已将 SQLAlchemy 的发行版更新到 1.3.15。 如果使用 PyPI 自动下载最新版本的 SQLAlchemy，并使用 Databricks Runtime 5.5 LTS 群集，则对 SQLAlchemy 的更新可能会导致作业失败。

## <a name="solution"></a>解决方案

有两种解决方法可用。

* 将 SQLAlchemy 的版本限制为 1.3.13。
* 阻止 Python 使用 [pep517 生成系统](https://www.python.org/dev/peps/pep-0517/)。

### <a name="restrict-sqlalchemy-to-version-1313"></a>将 SQLAlchemy 限制为版本 1.3.13

使用 [PyPI 包安装](/databricks/libraries#pypi-libraries)说明安装 SQLAlchemy，并将版本值设置为 1.3.13。

### <a name="prevent-python-from-using-the-pep517-build-system"></a>阻止 Python 使用 pep517 生成系统

可以使用 init 脚本阻止 Python 使用 pep517 生成系统。

使用以下代码块在群集上生成 init 脚本 `install-sqlalchemy.sh`：

```python
dbutils.fs.put("/databricks/init-scripts/install-sqlalchemy.sh", """
#!/bin/bash
/databricks/python/bin/pip install sqlalchemy --disable-pip-version-check --no-use-pep517
""", True)
```

按照现有文档，将脚本作为[群集范围内的 init 脚本](/databricks/clusters/configure#init-scripts)进行安装。 安装此脚本后，重新启动群集。

### <a name="best-practice-recommendation"></a>最佳做法建议

无论何时使用第三方库，都应始终将群集配置为使用已知要工作的每个库的特定版本。 新版本的库可以提供新功能，但如果在不进行测试和验证的情况下部署这些新版本的库，它们也可能会带来问题。