---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/26/2020
title: HorovodRunner - 使用 Horovod 进行分布式深度学习 - Azure Databricks
description: 了解如何使用 HorovodRunner 对机器学习模型执行分布式训练。
ms.openlocfilehash: 3380590ea5ef042808e38d1b8a9e323b4e0fb4db
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589907"
---
# <a name="horovodrunner-distributed-deep-learning-with-horovod"></a><a id="horovodrunner"> </a><a id="horovodrunner-distributed-deep-learning-with-horovod"> </a>HorovodRunner：使用 Horovod 进行分布式深度学习

`HorovodRunner` 是一种通用 API，可使用 Uber 的 [Horovod](https://github.com/horovod/horovod) 框架在 Azure Databricks 上运行分布式深度学习工作负载。 通过将 Horovod 与 Spark 的[屏障模式](https://jira.apache.org/jira/browse/SPARK-24374)集成，Azure Databricks 可为 Spark 上长期运行的深度学习训练作业提供更高的稳定性。 `HorovodRunner` 采用 Python 方法，后者包含带 Horovod 挂钩的 DL 训练代码。 在驱动程序上对该方法执行 pickle 操作，然后将其发送到 Spark 辅助角色。 使用屏障执行模式将 Horovod MPI 作业以 Spark 作业的形式嵌入。 第一个执行程序使用 `BarrierTaskContext` 收集所有任务执行程序的 IP 地址，并使用 `mpirun` 触发 Horovod 作业。 每个 Python MPI 进程都会重新加载执行过 pickle 操作的程序，然后将其反序列化并运行。

> [!div class="mx-imgBorder"]
> ![HorovodRunner](../../../../_static/images/distributed-deep-learning/horovod-runner.png)

## <a name="distributed-training-with-horovodrunner"></a>使用 HorovodRunner 进行分布式训练

通过 HorovodRunner 可将 Horovod 训练作业以 Spark 作业的形式启动。 `HorovodRunner` API 支持以下方法：

**`init(self, np)`**

创建 HorovodRunner 的实例。

**`run(self, main, **kwargs)`**

运行调用 `main(**kwargs)` 的 Horovod 训练作业。 使用 cloudpickle 对主函数和关键字参数进行了序列化，并分发到了群集辅助角色。

有关详细信息，请参阅 [HorovodRunner API 文档](https://databricks.github.io/spark-deep-learning/docs/_site/api/python/index.html#sparkdl.HorovodRunner)。

使用 `HorovodRunner` 开发分布式训练计划的常规方法如下：

1. 创建 `HorovodRunner` 实例，并使用节点数将其初始化。
2. 根据 [Horovod 用法](https://github.com/horovod/horovod#usage)中所述的方法定义 Horovod 训练方法，确保可在该方法中添加任何 import 语句。
3. 将训练方法传递到 `HorovodRunner` 实例。

例如：

```python
hr = HorovodRunner(np=2)

def train():
  import tensorflow as tf
  hvd.init()

hr.run(train)
```

若要仅使用 `n` 子进程在驱动程序上运行 `HorovodRunner`，请使用 `hr = HorovodRunner(np=-n)`。 例如，如果驱动程序节点上有 4 个 GPU，则 `n` 最多可选为 `4`。 有关参数 `np` 的详细信息，可参阅 [HorovodRunner API 文档](https://databricks.github.io/spark-deep-learning/docs/_site/api/python/index.html#sparkdl.HorovodRunner)。 若要详细了解如何对每个子进程固定一个 GPU，可参阅 [Horovod 使用指南](https://github.com/horovod/horovod#usage)。

常见错误如下：找不到 TensorFlow 对象，或无法对该对象执行 pickle 操作。 未向其他执行程序分发 import 语句库时，会发生以上错误。 为避免此问题，请确保在 Horovod 训练方法的顶部和 Horovod 训练方法所引用的其他任何用户定义函数中添加所有 import 语句（例如 `import tensorflow as tf`）。

## <a name="record-horovod-training-with-horovod-timeline"></a>使用 Horovod Timeline 记录 Horovod 训练

Horovod 可记录其活动的时间线，称为 [Horovod 时间线](https://horovod.readthedocs.io/en/latest/timeline.html)。

> [!IMPORTANT]
>
> Horovod 时间线会对性能造成重大影响。 启用 Horovod 时间线后，Inception3 吞吐量可能会降低约 40%。 如果要加快 HorovodRunner 作业的速度，请禁用 Horovod 时间线。

若要记录 Horovod 时间线，请将 `HOROVOD_TIMELINE` 环境变量设置为要创建的时间线文件的位置。 该位置应位于共享存储（例如使用 [DBFS 本地文件 API](../../../../data/databricks-file-system.md#fuse)），以便可轻松检索到时间线文件。  例如：

```python
timeline_dir = "/dbfs/ml/horovod-timeline/%s" % uuid.uuid4()
os.makedirs(timeline_dir)
os.environ['HOROVOD_TIMELINE'] = timeline_dir + "/horovod_timeline.json"
hr = HorovodRunner(np=4)
hr.run(run_training_horovod, params=params)
```

可使用 [Databricks CLI](../../../../dev-tools/cli/index.md) 或 [FileStore](../../../../data/filestore.md#filestore) 下载时间线文件，然后使用 Chrome 浏览器的 `chrome://tracing` 工具查看该文件。 例如：

> [!div class="mx-imgBorder"]
> ![Horovod 时间线](../../../../_static/images/distributed-deep-learning/mnist-timeline.png)

## <a name="development-workflow"></a><a id="development-workflow"> </a><a id="horovod-timeline"> </a>开发工作流

若要将单节点 DL 迁移到分布式训练，请在工作流中执行下列主要步骤。

1. **准备单节点 DL 代码：** 使用 TensorFlow、Keras 或 PyTorch 准备和测试单节点 DL 代码。
2. **迁移到 Horovod：** 请按照 [Horovod 用法](https://github.com/horovod/horovod#usage)中的说明，使用 Horovod 迁移代码并在驱动程序上对其进行测试：
   1. 添加 `hvd.init()` 以初始化 Horovod。
   1. 使用 `config.gpu_options.visible_device_list` 固定服务器 GPU，以供该进程使用。 典型设置是每个进程一个 GPU；通过它，这可设置为“本地等级”。 这样的话，将向服务器上的第一个进程分配第一个 GPU，向第二个进程分配第二个 GPU，依此类推。
   1. 包含数据集的分片。 运行分布式训练时，该数据集运算符非常有用，因为它允许每个辅助角色读取唯一的子集。
   1. 按辅助角色数缩放学习速率。 按辅助角色数缩放同步分布式训练中的有效批大小。 学习速率的提高可平衡批大小的增加。
   1. 在 `hvd.DistributedOptimizer` 中包装优化器。 分布式优化器将梯度计算委派给初始优化器，使用 allreduce 或 allgather 对梯度求平均，然后应用这些平均后的梯度。
   1. 添加 `hvd.BroadcastGlobalVariablesHook(0)`，将初始变量状态从等级 0 广播到其他所有进程。 使用随机权重开始训练或从检查点恢复训练时，必须这样操作以确保所有辅助角色实现一致的初始化。 另外，如果你不使用 `MonitoredTrainingSession`，则可在初始化全局变量后执行 `hvd.broadcast_global_variables` 操作。
   1. 将代码修改为仅在辅助角色 0 上保存检查点，以防止其他辅助角色对检查点造成干扰。 可通过将 `checkpoint_dir=None` 传递到 `tf.train.MonitoredTrainingSession if hvd.rank() != 0` 来实现此目的。
3. **迁移到 HorovodRunner：** `HorovodRunner` 通过调用 Python 函数来运行 Horovod 训练作业。 你必须将主要训练过程包装到一个函数中。 然后，可在本地模式和分布式模式下测试 `HorovodRunner`。

## <a name="update-the-deep-learning-libraries"></a>更新深度学习库

如果要升级或降级 TensorFlow、Keras 或 PyTorch，必须重新安装 Horovod，以便根据新安装的库对其进行编译。 例如，如果要升级 TensorFlow，Databricks 建议使用 [TensorFlow 安装说明](../tensorflow.md)中的 init 脚本，并在该脚本末尾追加以下特定于 TensorFlow 的 Horovod 安装代码。  请参阅 [Horovod 安装说明](https://github.com/horovod/horovod#install)以使用不同的组合，例如升级或降级 PyTorch 和其他库。

```bash
add-apt-repository -y ppa:ubuntu-toolchain-r/test
apt update
# Using the same compiler that TensorFlow was built to compile Horovod
apt install g++-7 -y
update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 60 --slave /usr/bin/g++ g++ /usr/bin/g++-7

HOROVOD_GPU_ALLREDUCE=NCCL HOROVOD_CUDA_HOME=/usr/local/cuda pip install horovod==0.18.1 --force-reinstall --no-deps --no-cache-dir
```

## <a name="examples"></a>示例

以下示例基于 [MNIST](https://en.wikipedia.org/wiki/MNIST_database) 数据集，它们演示了如何使用 `HorovodRunner` 将单节点深度学习程序迁移到分布式深度学习。

* [结合使用 TensorFlow 和面向 MNIST 的 HorovodRunner 的分布式深度学习训练](mnist-tensorflow-keras.md)
* [单节点 PyTorch 到分布式深度学习](mnist-pytorch.md)