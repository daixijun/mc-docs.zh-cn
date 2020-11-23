---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何为基于树的 Apache SparkML 管道模型提取特征信息 - Azure Databricks
description: 了解如何在 Azure Databricks 中为基于树的 Apache SparkML 管道模型提取特征信息。
ms.openlocfilehash: ab1c1d34d8c729cbcad1c27cb5fa54642c49147f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589735"
---
# <a name="how-to-extract-feature-information-for-tree-based-apache-sparkml-pipeline-models"></a>如何为基于树的 Apache SparkML 管道模型提取特征信息

拟合基于树的模型（例如决策树、随机林或梯度增强树）时，能够查看特征重要性级别以及特征名称将非常有用。 通常，SparkML 中的模型适合充当管道的最后一个阶段。 若要使用树模型从管道中提取相关特征信息，必须提取恰当的管道阶段。 可以从 `VectorAssembler` 对象提取特征名称：

```python
from pyspark.ml.feature import StringIndexer, VectorAssembler
from pyspark.ml.classification import DecisionTreeClassifier
from pyspark.ml import Pipeline

pipeline = Pipeline(stages=[indexer, assembler, decision_tree)
DTmodel = pipeline.fit(train)
va = dtModel.stages[-2]
tree = DTmodel.stages[-1]

display(tree) #visualize the decision tree model
print(tree.toDebugString) #print the nodes of the decision tree model

list(zip(va.getInputCols(), tree.featureImportances))
```

还可以在管道的最后一个阶段使用交叉验证程序来调整基于树的模型。 若要可视化决策树并打印特征重要性级别，请从 `CrossValidator` 对象中提取 `bestModel`：

```python
from pyspark.ml.tuning import ParamGridBuilder, CrossValidator

cv = CrossValidator(estimator=decision_tree, estimatorParamMaps=paramGrid, evaluator=evaluator, numFolds=3)
pipelineCV = Pipeline(stages=[indexer, assembler, cv)
DTmodelCV = pipelineCV.fit(train)
va = DTmodelCV.stages[-2]
treeCV = DTmodelCV.stages[-1].bestModel

display(treeCV) #visualize the best decision tree model
print(treeCV.toDebugString) #print the nodes of the decision tree model

list(zip(va.getInputCols(), treeCV.featureImportances))
```

`display` 函数仅可视化决策树模型。 请参阅[机器学习可视化效果](/databricks/notebooks/visualizations/index#display-ml-models)。