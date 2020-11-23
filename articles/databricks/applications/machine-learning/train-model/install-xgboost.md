---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 在 Azure Databricks 上安装 XGBoost - Azure Databricks
description: 了解如何在 Azure Databricks 上安装 XGBoost。
ms.openlocfilehash: 70a286acf674faa1e0926e28d51a265c669653ec
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589601"
---
# <a name="install-xgboost-on-azure-databricks"></a>在 Azure Databricks 上安装 XGBoost

如果需要在 Databricks Runtime 上安装 XGBoost 或使用与预装在 Databricks Runtime ML 中的版本不同的版本，请按照以下说明操作。

## <a name="install-xgboost-on-databricks-runtime-ml"></a>在 Databricks Runtime ML 中安装 XGBoost

XGBoost 已包含在 Databricks Runtime ML 中。 可在 Databricks Runtime ML 中使用这些库，而无需安装任何包。 请参阅[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md#mlruntime)。

有关所使用 Databricks Runtime ML 版本中安装的 XGBoost 版本，请参阅[发行说明](../../../release-notes/runtime/releases.md)。 若要在 Databricks Runtime ML 中安装其他 Python 版本，请将 XGBoost 安装为 [Databricks PyPI 库](../../../libraries/index.md)。  按如下所示指定版本（请将 `<xgboost version>` 替换为所需版本）。

```python
xgboost==<xgboost version>
```

## <a name="install-xgboost-on-databricks-runtime"></a>在 Databricks Runtime 中安装 XGBoost

* **Python 包**：通过在笔记本单元格中执行以下命令并将 `<xgboost version>` 替换为所需版本来使用 [Databricks 库实用工具](../../../dev-tools/databricks-utils.md#dbutils-library)：

  ```python
  dbutils.library.installPyPI("xgboost", version="<xgboost version>" )
  ```

* **Scala/Java 包**：使用 Spark 包名称 `xgboost-linux64` 安装 [Databricks 库](../../../libraries/index.md)。