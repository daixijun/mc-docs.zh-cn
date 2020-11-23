---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 使用 Azure 数据工厂时的常见错误 - Azure Databricks
description: 了解将 Azure 数据工厂与 Azure Databricks 配合使用时常见错误的解决方案和说明。
ms.openlocfilehash: 0a539630f401ef2761835ec6d4e9504a2a77c0ac
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589736"
---
# <a name="common-errors-using-azure-data-factory"></a>使用 Azure 数据工厂时的常见错误

Azure 数据工厂是一项托管服务，借助该服务可以使用 Azure Databricks 笔记本、JAR 和 Python 脚本创作数据管道。 本文介绍常见的问题和解决方案。

## <a name="cluster-could-not-be-created"></a>无法创建群集

在 Azure 数据工厂中创建使用与 Azure Databricks 相关的活动（如 Notebook 活动）的数据管道时，可以要求创建新的群集。 在 Azure 中，群集创建可能会因多种原因而失败：

* Azure 订阅的可预配虚拟机数量受到限制。
* `Failed to create cluster because of Azure quota` 表示你正在使用的订阅没有足够的配额来创建所需的资源。 例如，如果请求 500 个核心，但配额为 50 个核心，则请求将失败。 请联系 Azure 支持部门，请求增加配额。
* `Azure resource provider is currently under high load and requests are being throttled.` 此错误表示你的 Azure 订阅乃至区域可能正受到限制。 仅重试数据管道可能无济于事。 有关此问题的详细信息，请参阅[排查 API 限制错误](/virtual-machines/troubleshooting/troubleshooting-throttling-errors)。
* `Could not launch cluster due to cloud provider failures` 表示为群集预配一个或多个虚拟机时出现常规故障。 请等待并稍后重试。

## <a name="cluster-ran-into-issues-during-data-pipeline-execution"></a>群集在数据管道执行期间遇到问题

Azure Databricks 包括多种机制，可提高 Apache Spark 群集的复原能力。 也就是说，它无法从每次故障中恢复，导致出现如下错误：

* `Connection refused`
* `RPC timed out`
* `Exchange times out after X seconds`
* `Cluster became unreachable during run`
* `Too many execution contexts are open right now`
* `Driver was restarted during run`
* `Context ExecutionContextId is disconnected`
* `Could not reach driver of cluster for X seconds`

大多数情况下，这些错误并不表示 Azure 的底层基础结构出现问题。 相反，很可能是群集上运行的作业太多，使群集过载并导致超时。

一般来说，应该将更大的数据管道移动到它们自己的 Azure Databricks 群集上运行。 [与 Azure Monitor 集成](/data-factory/monitor-using-azure-monitor)并使用 [Grafana](https://grafana.com/) 观察执行指标可以深入了解即将过载的群集。

## <a name="azure-databricks-service-is-experiencing-high-load"></a>Azure Databricks 服务的负载过高

你可能会注意到某些数据管道失败，并出现如下错误：

* `The service at {API} is temporarily unavailable`
* `Jobs is not fully initialized yet. Please retry later`
* `Failed or timeout processing HTTP request`
* `No webapps are available to handle your request`

这些错误表明 Azure Databricks 服务负载过重。 如果发生这种情况，请尝试限制包含 Azure Databricks 活动的并发数据管道的数目。  例如，如果要对从源到目标的 1,000 个表执行 ETL，而不是为每个表启动数据管道，可以将多个表合并到一个数据管道中，或者使其错开执行，以免一次触发所有执行。

> [!IMPORTANT]
>
> Azure Databricks 不允许在 3,600 秒窗口中创建超过 1,000 个作业。 如果在 Azure 数据工厂中尝试执行此操作，则数据管道将会失败。

如果过于频繁地（例如每 5 秒一次）轮询 Databricks 作业 API 的作业运行状态，也会显示这些错误。 补救措施是降低轮询频率。

## <a name="library-installation-timeout"></a>库安装超时

Azure Databricks 包括对安装第三方库的可靠支持。 遗憾的是，你可能会看到如下所示的问题：

* `Failed or timed out installing libraries`

出现这种情况的原因是每次启动带有附加库的群集时，Azure Databricks 都会从相应的存储库（如 PyPI）下载库。 此操作可能会超时，导致群集无法启动。

除了限制附加到群集的库的数目之外，这个问题没有简单的解决方案。