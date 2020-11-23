---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 准备数据以进行分布式训练 - Azure Databricks
description: 了解如何准备数据以对机器学习模型执行分布式训练。
ms.openlocfilehash: 5bcda97bb4783c4ea357bcc5a4b3fb98d1446233
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589944"
---
# <a name="prepare-data-for-distributed-training"></a>准备数据以进行分布式训练

本文介绍了两种用于准备数据进行分布式训练的方法：Petastorm 和 TFRecords。

## <a name="petastorm-recommended"></a>Petastorm（推荐）

[Petastorm](https://github.com/uber/petastorm) 是开源数据访问库，支持直接加载以 [Apache Parquet](https://parquet.apache.org/) 格式存储的数据。 这对于 Azure Databricks 和 Apache Spark 用户来说很方便，因为 Parquet 是推荐的数据格式。 下文对此用例进行了描述和说明：

* [使用 Petastorm 加载数据](petastorm.md)

## <a name="tfrecord"></a>TFRecord

你也可以使用 TFRecord 格式作为数据源来进行分布式深度学习。
TFRecord 格式是简单的面向记录的二进制格式，许多 TensorFlow 应用程序将其用于训练数据。

[tf.data.TFRecordDataset](https://www.tensorflow.org/api_docs/python/tf/data/TFRecordDataset) 是 TensorFlow 数据集，由 TFRecords 文件中的记录构成。
若要详细了解如何使用 TFRecord 数据，请参阅 TensorFlow 指南[使用 TFRecord 数据](https://www.tensorflow.org/guide/data#consuming_tfrecord_data)。

以下文章描述并说明了关于将数据保存到 TFRecord 文件和加载 TFRecord 文件的推荐方法：

* [将 Apache Spark 数据帧保存到 TFRecord 文件并使用 TensorFlow 进行加载](tfrecords-save-load.md)