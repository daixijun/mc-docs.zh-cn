---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/26/2020
title: 分布式图像模型推理的参考解决方案 - Azure Databricks
description: 分布式图像模型推理的参考解决方案。
ms.openlocfilehash: 0f7d00d3b184ffd861e82f4a2d2884780b52d365
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589955"
---
# <a name="reference-solution-for-distributed-image-model-inference"></a>分布式图像模型推理的参考解决方案

本文及其随附的笔记本介绍了一个分布式图像模型推理的参考解决方案，该方案基于许多真实图像应用程序共享的通用设置。 此设置假定在对象存储区中存储多个图像。 假设有多个经过训练的深度学习 (DL) 模型用于图像分类和对象检测（例如，MobileNetV2 用于检测用户上传的照片中的人类对象，以帮助保护隐私），并且你希望将这些 DL 模型应用于存储的图像。

你可以重新训练模型并更新以前计算得出的预测。
然而，加载许多图像和应用 DL 模型需要大量的 I/O 和大量的计算。
幸运的是，推理工作负载是易并行的，理论上可以轻松地进行分布。
本指南将指导你完成一个包含两个主要阶段的实用解决方案：

1. ETL 图像到 Delta 表中。 专用 ETL 作业可帮助管理数据并简化推理任务。
2. 使用 pandas UDF 执行分布式推理。

## <a name="notebooks"></a>笔记本

以下笔记本使用已安装的 PyTorch 和 TensorFlow tf.Keras 演示参考解决方案。

### <a name="etl-image-dataset-into-a-delta-table-notebook"></a>ETL 图像数据集到 Delta 表笔记本中

[获取笔记本](../../../_static/notebooks/deep-learning/dist-img-infer-1-etl.html)

### <a name="distributed-inference-via-pytorch-and-pandas-udf-notebook"></a>通过 Pytorch 和 pandas UDF 笔记本进行的分布式推理

[获取笔记本](../../../_static/notebooks/deep-learning/dist-img-infer-2-pandas-udf.html)

### <a name="distributed-inference-via-keras-and-pandas-udf-notebook"></a>通过 Keras 和 pandas UDF 笔记本进行的分布式推理

[获取笔记本](../../../_static/notebooks/deep-learning/dist-img-infer-3-keras-udf.html)