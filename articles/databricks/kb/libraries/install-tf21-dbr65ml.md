---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 07/08/2020
title: 在 Databricks Runtime 6.5 ML GPU 群集上安装 TensorFlow 2.1 - Azure Databricks
description: 了解如何在 Databricks Runtime 6.5 ML GPU 群集上安装 TensorFlow 2.1。
category: Libraries
author: ram-sankarasubramanian
db-author: ram.sankarasubramanian@databricks.com
ms.openlocfilehash: b9d706b6c407abe57b219796d857b0af5f4078dc
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589790"
---
# <a name="install-tensorflow-21-on-databricks-runtime-65-ml-gpu-clusters"></a>在 Databricks Runtime 6.5 ML GPU 群集上安装 TensorFlow 2.1

Databricks Runtime ML [包含 TensorFlow 的版本](/databricks/applications/deep-learning/single-node-training/tensorflow#tensorflow-versions-included-in-databricks-runtime-ml)，因此无需安装任何包即可使用它。

| Databricks Runtime ML 版                      | TensorFlow 版本                                 |
|----------------------------------------------------|----------------------------------------------------|
| 7.0                                                | 2.2.0                                              |
| 6.3 - 6.6                                          | 1.15.0                                             |

可以通过使用[群集范围内的初始化脚本](/databricks/clusters/init-scripts#example-cluster-scoped-init-script)来安装其他版本的 TensorFlow。

本文介绍如何在 Databricks Runtime 6.5 ML GPU 群集上安装 TensorFlow 2.1。

> [!IMPORTANT]
>
> 删除默认库并安装新版本可能会导致性能不稳定或完全损坏 Azure Databricks 群集。 在运行生产作业之前，应在环境中对任何新库版本进行全面测试。

## <a name="install-the-init-script"></a>安装初始化脚本

1. 在 Databricks Runtime 6.5 ML GPU 群集上安装以下[群集范围内的初始化脚本](/databricks/clusters/init-scripts#example-cluster-scoped-init-script)。

   ```scala
   #!/bin/bash
   set -e

   apt-get update
   apt-get install -y --no-install-recommends --allow-downgrades \
     libnccl2=2.4.8-1+cuda10.1 \
     libnccl-dev=2.4.8-1+cuda10.1 \
     cuda-libraries-10-1 \
     libcudnn7=7.6.4.38-1+cuda10.1 \
     libcudnn7-dev=7.6.4.38-1+cuda10.1 \
     libnvinfer6=6.0.1-1+cuda10.1 \
     libnvinfer-dev=6.0.1-1+cuda10.1 \
     libnvinfer-plugin6=6.0.1-1+cuda10.1
   apt-get clean
   ln -sfn cuda-10.1 /usr/local/cuda

   pip install tensorflow==2.1.* setuptools==41.* grpcio==1.24.*

   # This `conda list` is necessary to recognize the pip-installed packages.
   conda list
   conda install cudatoolkit=10.1
   ```

2. 重启群集。