---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 将 Apache Spark 数据帧保存到 TFRecord 文件并使用 TensorFlow 进行加载 - Azure Databricks
description: 了解如何使用 spark-tensorflow-connector 将 Apache Spark 数据帧保存到 TFRecord 文件并使用 TensorFlow 加载 TFRecord。
ms.openlocfilehash: 328ec0fa4200876ac5b57046fcecba7cf5e1e174
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589935"
---
# <a name="save-apache-spark-dataframes-to-tfrecord-files-and-load-with-tensorflow"></a>将 Apache Spark 数据帧保存到 TFRecord 文件并使用 TensorFlow 进行加载

TFRecord 文件格式是简单的面向记录的二进制格式，适用于 ML 训练数据。 使用 [tf.data.TFRecordDataset](https://www.tensorflow.org/api_docs/python/tf/data/TFRecordDataset) 类可以将一个或多个 TFRecord 文件的内容作为输入管道的一部分进行流式传输。

> [!NOTE]
>
> 本指南不是关于使用 TensorFlow 导入数据的综合指南。  请参阅 [TensorFlow API 指南](https://www.tensorflow.org/tutorials/load_data/tfrecord#tfrecord_files_in_python)。

## <a name="save-apache-spark-dataframes-to-tfrecord-files"></a><a id="df-to-tfrecord"> </a><a id="save-apache-spark-dataframes-to-tfrecord-files"> </a>将 Apache Spark 数据帧保存到 TFRecord 文件

可以使用 [spark-tensorflow-connector](https://github.com/tensorflow/ecosystem/tree/master/spark/spark-tensorflow-connector) 将 Apache Spark 数据帧保存到 TFRecord 文件。

[spark-tensorflow-connector](https://github.com/tensorflow/ecosystem/tree/master/spark/spark-tensorflow-connector) 是 [TensorFlow 生态系统](https://github.com/tensorflow/ecosystem)中的一个库，可实现 Spark 数据帧和 [TFRecords](https://www.tensorflow.org/tutorials/load_data/tfrecord#tfrecord_files_in_python)（用于存储 TensorFlow 数据的常用格式）之间的转换。 通过 spark-tensorflow-connector，可以使用 Spark 数据帧 API 将 TFRecords 文件读入数据帧，并以 TFRecords 形式写入数据帧。

> [!NOTE]
>
> spark-tensorflow-connector 库包含在[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md)（它是一个机器学习运行时，提供用于机器学习和数据科学的现成环境）中。 不必使用以下说明来安装该库，可以直接使用[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md) 来创建群集。
> 若要在 [Databricks Runtime](../../../runtime/dbr.md) 上使用 spark-tensorflow-connector，需从 Maven 安装该库。 有关详细信息，请参阅 [Maven 或 Spark 包](../../../libraries/workspace-libraries.md#maven-libraries)。

## <a name="load-data-from-tfrecord-files-with-tensorflow"></a>使用 TensorFlow 从 TFRecord 文件加载数据

可以使用 `tf.data.TFRecordDataset` 类加载 TFRecord 文件。 有关详细信息，请参阅从 TensorFlow [读取 TFRecord 文件](https://www.tensorflow.org/tutorials/load_data/tfrecord#reading_a_tfrecord_file)。

以下示例笔记本演示了如何将数据从 Apache Spark 数据帧保存到 TFRecord 文件并加载 TFRecord 文件以进行 ML 训练。

### <a name="prepare-image-data-for-distributed-dl"></a>准备图像数据以进行分布式 DL

[获取笔记本](../../../_static/notebooks/deep-learning/tfrecords-save-load.html)