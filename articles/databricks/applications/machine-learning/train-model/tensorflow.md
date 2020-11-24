---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 09/14/2020
title: TensorFlow - Azure Databricks
description: 了解如何使用 TensorFlow 在单个节点上训练机器学习模型，以及如何使用 TensorBoard 调试机器学习程序。
ms.openlocfilehash: fb6f7860bf7060889d2b1abe74a59178cbb16361
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589486"
---
# <a name="tensorflow"></a><a id="tensorflow"> </a><a id="tensorflow1"> </a>TensorFlow

TensorFlow 是 Google 创建的机器学习开源框架。 它支持在 CPU、GPU 以及 GPU 群集上进行深度学习和常规数字计算。 它遵守 [Apache 2.0 许可证](https://github.com/tensorflow/tensorflow/blob/master/LICENSE)的条款和条件。

以下部分提供了有关在 Azure Databricks 上安装 TensorFlow 的指导，并提供了运行 TensorFlow 程序的示例。

> [!NOTE]
>
> 本指南不是关于 TensorFlow 的综合指南。  请参阅 [TensorFlow 网站](https://www.tensorflow.org/)。

## <a name="tensorflow-versions-included-in-databricks-runtime-ml"></a>Databricks Runtime ML 中包含的 TensorFlow 版本

[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md) 包括 TensorFlow 和 TensorBoard，因此你可以在不安装任何程序包的情况下使用这些库。 下面是包括的 TensorFlow 版本：

| Databricks Runtime ML 版                      | TensorFlow 版本                                 |
|----------------------------------------------------|----------------------------------------------------|
| 7.3                                                | 2.3.0                                              |
| 7.0 - 7.2                                          | 2.2.0                                              |
| 6.3 - 6.6                                          | 1.15.0                                             |

## <a name="install-tensorflow"></a><a id="install-tensorflow"> </a><a id="tensorflow-install"> </a>安装 TensorFlow

本部分提供的说明用于在[用于机器学习的 Databricks Runtime](../../../runtime/mlruntime.md) 和 [Databricks Runtime](../../../runtime/dbr.md) 上安装或降级 TensorFlow，以便你可以试用 TensorFlow 中的最新功能。
由于程序包依赖关系，可能存在与其他预安装程序包的兼容性问题。 安装后，可以通过在 Python 笔记本中执行以下命令来验证已安装的版本：

```python
import tensorflow as tf
print([tf.__version__, tf.test.is_gpu_available()])
```

### <a name="install-tensorflow-23-on-databricks-runtime-72"></a>在 Databricks Runtime 7.2 上安装 TensorFlow 2.3

Azure Databricks 建议使用 [%pip 和 %conda magic 命令](../../../libraries/notebooks-python-libraries.md)安装 TensorFlow。
在笔记本中，运行：

```bash
%pip install tensorflow-cpu==2.3.*
```

### <a name="install-tensorflow-115-on-databricks-runtime-72"></a>在 Databricks Runtime 7.2 上安装 TensorFlow 1.15

在笔记本中，运行：

```bash
%pip install tensorflow-cpu==1.15.*
```

### <a name="install-tensorflow-23-on-databricks-runtime-72-ml"></a>在 Databricks Runtime 7.2 ML 上安装 TensorFlow 2.3

在笔记本中，运行：

#### <a name="cpu"></a>Cpu

```bash
%pip install tensorflow-cpu==2.3.*
```

#### <a name="gpu"></a>Gpu

```bash
%pip install tensorflow-gpu==2.3.*
```

### <a name="install-tensorflow-115-on-databricks-runtime-72-ml"></a>在 Databricks Runtime 7.2 ML 上安装 TensorFlow 1.15

在笔记本中，运行：

#### <a name="cpu"></a>Cpu

```bash
%pip install tensorflow-cpu==1.15.*
```

#### <a name="gpu"></a>Gpu

正式的 TensorFlow 1.15 版本是针对 CUDA 10.0 构建的，它与 Databricks Runtime 7.0 ML 及更高版本中安装的 CUDA 10.1 不兼容。
Azure Databricks 提供了与 CUDA 10.1 兼容的 TensorFlow 1.15.3 的自定义版本。 可以使用以下命令安装它。

```bash
%pip install https://databricks-prod-cloudfront.cloud.databricks.com/artifacts/tensorflow/runtime-7.x/tensorflow-1.15.3-cp37-cp37m-linux_x86_64.whl
```

### <a name="install-tensorflow-23-on-databricks-runtime-55-lts-ml"></a>在 Databricks Runtime 5.5 LTS ML 上安装 TensorFlow 2.3

以下位置的用于群集的初始化脚本：

#### <a name="cpu"></a>Cpu

```bash
#!/bin/bash

set -e

/databricks/python/bin/python -V
. /databricks/conda/etc/profile.d/conda.sh
conda activate /databricks/python

pip install --upgrade pip
pip install tensorflow-cpu==2.3.* setuptools==41.* grpcio==1.24.*
```

#### <a name="gpu"></a>Gpu

```bash
#!/bin/bash

set -e

apt-get remove -y --auto-remove cuda-toolkit-10-0
apt-get update
apt-get install -y --no-install-recommends --allow-downgrades \
  libnccl2=2.4.8-1+cuda10.1 \
  libnccl-dev=2.4.8-1+cuda10.1 \
  cuda-libraries-10-1 \
  libcudnn7=7.6.4.38-1+cuda10.1 \
  libcudnn7-dev=7.6.4.38-1+cuda10.1 \
  libcublas10=10.2.1.243-1 \
  libcublas-dev=10.2.1.243-1 \
  cuda-libraries-dev-10-1 \
  cuda-compiler-10-1
ln -sfn cuda-10.1 /usr/local/cuda

/databricks/python/bin/python -V
. /databricks/conda/etc/profile.d/conda.sh
conda activate /databricks/python

pip install --upgrade pip
pip install tensorflow==2.3.* setuptools==41.* grpcio==1.24.*
```

### <a name="install-tensorflow-23-on-databricks-runtime-55-lts"></a>在 Databricks Runtime 5.5 LTS 上安装 TensorFlow 2.3

以下位置的用于群集的初始化脚本：

#### <a name="cpu"></a>Cpu

```bash
#!/bin/bash

set -e

/databricks/python/bin/python -V
/databricks/python/bin/pip install tensorflow-cpu==2.3.* setuptools==41.* pyasn1==0.4.6
/databricks/python/bin/pip uninstall -y numpy
rm -rf /databricks/python/lib/python3.5/site-packages/numpy
/databricks/python/bin/pip install numpy==1.18.4
```

#### <a name="gpu"></a>Gpu

```bash
#!/bin/bash

set -e

apt-get update
apt-get install -y gnupg-curl

wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_10.1.243-1_amd64.deb
dpkg -i cuda-repo-ubuntu1604_10.1.243-1_amd64.deb
apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub

wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
dpkg -i nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb

apt-get update
apt-get install -y --no-install-recommends --allow-downgrades \
  libnccl2=2.4.8-1+cuda10.1 \
  libnccl-dev=2.4.8-1+cuda10.1 \
  cuda-libraries-10-1 \
  libcudnn7=7.6.4.38-1+cuda10.1 \
  libcudnn7-dev=7.6.4.38-1+cuda10.1 \
  libcublas10=10.2.1.243-1 \
  libcublas-dev=10.2.1.243-1 \
  cuda-libraries-dev-10-1 \
  cuda-compiler-10-1
ln -sfn cuda-10.1 /usr/local/cuda

/databricks/python/bin/python -V
/databricks/python/bin/pip install tensorflow==2.3.* setuptools==41.*
/databricks/python/bin/pip uninstall -y numpy
rm -rf /databricks/python/lib/python3.5/site-packages/numpy
/databricks/python/bin/pip install numpy==1.18.4
```

### <a name="tensorflow-2-known-issues"></a><a id="tensorflow-2-known-issues"> </a><a id="tf-20-ki"> </a>TensorFlow 2 已知问题

TensorFlow 2 与 Python pickling 之间有[一个已知的兼容性问题](https://github.com/tensorflow/tensorflow/issues/32159)。 如果你使用依赖于 pickling 的 PySpark、[HorovodRunner](distributed-training/horovod-runner.md#horovodrunner)、[Hyperopt](../automl-hyperparam-tuning/index.md#hyperopt-overview) 或任何其他程序包，则可能会遇到此问题。 解决方法是在函数中显式导入 TensorFlow 模块。 以下是示例：

```python
import tensorflow as tf

def bad_func(_):
  tf.keras.Sequential()

# You might see an error.
sc.parallelize(range(0)).foreach(bad_func)

def good_func(_):
  import tensorflow as tf
  tf.keras.some_func

# No error.
sc.parallelize(range(0)).foreach(good_func)
```

### <a name="install-tensorflow-115-on-databricks-runtime-55-lts-ml"></a>在 Databricks Runtime 5.5 LTS ML 上安装 TensorFlow 1.15

Azure Databricks 建议使用[初始化脚本](../../../clusters/init-scripts.md#cluster-scoped-init-script)在 Databricks Runtime 5.5 LTS ML 上安装 TensorFlow 1.15。

以下位置的用于群集的初始化脚本：

#### <a name="cpu"></a>Cpu

```bash
#!/bin/bash

set -e

/databricks/python/bin/python -V
. /databricks/conda/etc/profile.d/conda.sh
conda install -y conda=4.6
conda activate /databricks/python

conda install -y tensorflow-mkl=1.15 setuptools=41
```

#### <a name="gpu"></a>Gpu

```bash
#!/bin/bash

set -e

/databricks/python/bin/python -V
. /databricks/conda/etc/profile.d/conda.sh
conda install -y conda=4.6
conda activate /databricks/python

conda install -y tensorflow-gpu=1.15 setuptools=41
```

### <a name="install-tensorflow-115-on-databricks-runtime-55-lts"></a>在 Databricks Runtime 5.5 LTS 上安装 TensorFlow 1.15

Azure Databricks 建议使用[初始化脚本](../../../clusters/init-scripts.md#cluster-scoped-init-script)在 Databricks Runtime 5.5 LTS 上安装 TensorFlow 1.15。

以下位置的用于群集的初始化脚本：

#### <a name="cpu"></a>Cpu

```bash
#!/bin/bash

set -e

/databricks/python/bin/python -V
/databricks/python/bin/pip install tensorflow-cpu==1.15.* setuptools==41.*
```

#### <a name="gpu"></a>Gpu

```bash
#!/bin/bash

set -e

apt-get update
apt-get install -y gnupg-curl

wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
dpkg -i cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub

wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
dpkg -i nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb

apt-get update
apt-get install -y --no-install-recommends cuda-libraries-10-0 libcudnn7=7.4.2.24-1+cuda10.0

/databricks/python/bin/python -V
/databricks/python/bin/pip install tensorflow-gpu==1.15.* setuptools==41.*
```

## <a name="tensorboard"></a><a id="tensorboard"> </a><a id="using-tensorboard"> </a>TensorBoard

[TensorBoard](https://www.tensorflow.org/tensorboard) 是一套可视化效果工具，用于调试、优化和了解 TensorFlow、PyTorch 和其他机器学习程序。

### <a name="use-tensorboard"></a>使用 TensorBoard

#### <a name="use-tensorboard-on-databricks-runtime-72-and-above"></a>在 Databricks Runtime 7.2 及更高版本中使用 TensorBoard

在 Azure Databricks 中启动 TensorBoard 与在本地计算机上的 Jupyter 笔记本中启动它没有什么不同。

1. 加载 `%tensorboard` magic 命令并定义日志目录。

   ```
   %load_ext tensorboard
   experiment_log_dir = <log-directory>
   ```

2. 调用 `%tensorboard` magic 命令。

   ```
   %tensorboard --logdir $experiment_log_dir
   ```

   TensorBoard 服务器将启动并在笔记本中显示内联用户界面。 它还提供了用于在新标签页中打开 TensorBoard 的链接。

   以下屏幕截图显示了在填充的日志目录中启动的 TensorBoard UI。

   > [!div class="mx-imgBorder"]
   > ![TensorBoard](../../../_static/images/third-party-integrations/tensorflow/tensorboard.png)

还可以直接使用 TensorBoard 的笔记本模块启动 TensorBoard。

```python
from tensorboard import notebook
notebook.start("--logdir {}".format(experiment_log_dir))
```

#### <a name="use-tensorboard-on-databricks-runtime-71-and-below"></a>在 Databricks Runtime 7.1 及更低版本中使用 TensorBoard

若要从笔记本启动 TensorBoard，请使用 `dbutils.tensorboard` 实用工具。

```python
dbutils.tensorboard.start("/tmp/tensorflow_log_dir")
```

此命令会显示一个链接，单击该链接会在新标签页中打开 TensorBoard。

当使用此 API 启动时，TensorBoard 会一直运行，直到你使用 `dbutils.tensorboard.stop()` 停止它或关闭群集。

> [!NOTE]
>
> 如果将 TensorFlow 作为 Azure Databricks 库附加到群集，则在启动 TensorBoard 之前可能需要重新附加笔记本。

### <a name="tensorboard-logs-and-directories"></a>TensorBoard 日志和目录

TensorBoard 通过读取 [TensorBoard](https://www.tensorflow.org/tensorboard/get_started) 或 [PyTorch](https://pytorch.org/docs/stable/tensorboard.html) 中的 TensorBoard 回调和函数生成的日志来可视化你的机器学习程序。 若要为其他机器学习库生成日志，可以使用 TensorFlow 文件编写器直接编写日志（对于 TensorFlow 2.x，请参阅[模块：tf.summary](https://www.tensorflow.org/api_docs/python/tf/summary)，而对于 TensorFlow 1.x 中的较旧 API，请参阅[模块：tf.compat.v1.summary](https://www.tensorflow.org/api_docs/python/tf/compat/v1/summary)）。

若要确保可靠地存储试验日志，Azure Databricks 建议将日志写入到 DBFS（即 `/dbfs/` 下的一个日志目录），而不是写入到临时的群集文件系统。 对于每个试验，请在唯一目录中启动 TensorBoard。 对于生成日志的试验中的机器学习代码的每次运行，请将 TensorBoard 回调或文件编写器设置为写入到试验目录的子目录。 这样，TensorBoard UI 中的数据就会被分隔到运行中。

阅读官方 [TensorBoard 文档](https://www.tensorflow.org/tensorboard/get_started)，开始使用 TensorBoard 来记录你的机器学习程序的信息。

### <a name="manage-tensorboard-processes"></a>管理 TensorBoard 进程

当 Azure Databricks 笔记本拆离或 REPL 重启时（例如，当你清除笔记本的状态时），笔记本中启动的 TensorBoard 进程不会终止。 若要手动终止 TensorBoard 进程，请使用 `%sh kill -15 pid` 向其发送终止信号。 不正确地终止 TensorBoard 进程可能会损坏 `notebook.list()`。

若要列出群集上当前运行的 TensorBoard 服务器及其相应的日志目录和进程 ID，请从 TensorBoard 笔记本模块运行 `notebook.list()`。

### <a name="known-issues"></a>已知问题

* 内联 TensorBoard UI 位于一个 iframe 中。 浏览器安全功能会妨碍 UI 内的外部链接生效，除非你在新标签页中打开此类链接。
* TensorBoard 的 `--window_title` 选项在 Azure Databricks 上被替代。
* 默认情况下，TensorBoard 会扫描一个端口范围来选择要侦听的端口。 如果在群集上运行的 TensorBoard 进程太多，则并非端口范围内的所有端口都可用。 可以通过使用 `--port` 参数指定端口号来解决此限制。 指定的端口应介于 6006 到 6106 之间。
* 为了使下载链接生效，应在标签页中打开 TensorBoard。
* 当使用 TensorBoard 1.15.0 时，“投影仪”选项卡为空。 可以将 URL 中的 `#projector` 替换为 `data/plugin/projector/projector_binary.html`，以便直接访问投影仪页，这是一种解决方法。

## <a name="use-tensorflow-on-a-single-node"></a>在单节点上使用 TensorFlow

若要测试和迁移单机 TensorFlow 工作流，可以通过将工作器数设置为零，在 Azure Databricks 上从仅限驱动程序的群集着手。
尽管 Apache Spark 在此设置下不起作用，但这是运行单机 TensorFlow 工作流的一种经济高效的方法。 以下笔记本显示了如何在仅限驱动程序的群集上运行 TensorFlow（1.x 和 2.x）和 TensorBoard 监视。

### <a name="tensorflow-1152x-notebook"></a>TensorFlow 1.15/2.x 笔记本

[获取笔记本](../../../_static/notebooks/deep-learning/tensorflow-single-node.html)