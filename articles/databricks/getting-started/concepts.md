---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/14/2020
title: Azure Databricks 概念 - Azure Databricks
description: 了解工作区、数据对象、群集、机器学习模型和访问权限等基本 Azure Databricks 概念。
ms.openlocfilehash: abc02118c6287f15c014469507dfda542e02d464
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589643"
---
# <a name="azure-databricks-concepts"></a><a id="azure-databricks-concepts"> </a><a id="concepts"> </a>Azure Databricks 概念

本文介绍为有效地使用 Azure Databricks，你需要了解的一组基本概念。

## <a name="workspace"></a>工作区

[工作区](../workspace/index.md)是用于访问所有 Azure Databricks 资产的环境。 工作区将对象（笔记本、库、仪表板和试验）组织成[文件夹](../workspace/workspace-objects.md#folders)，并提供对数据对象和计算资源的访问。

本部分介绍 Azure Databricks 工作区文件夹中包含的对象。

**[笔记本](../notebooks/index.md)**

文档的基于 Web 的接口，其中包含可运行的代码、可视化效果和叙述性文本。

**[仪表板](../notebooks/dashboards.md)**

以有组织的方式访问可视化效果的的接口。

**[库](../libraries/index.md)**

可对群集上运行的笔记本或作业使用的代码包。 Databricks 运行时包括许多库，也可以添加自己的库。

**[试验](../applications/mlflow/tracking.md#mlflow-experiments)**

[MLflow 运行](../applications/mlflow/tracking.md#mlflow-tracking)的集合，用于训练机器学习模型。

## <a name="interface"></a>接口

本部分介绍 Azure Databricks 支持的用于访问资产的接口：UI、API 和命令行 (CLI)。

**UI**

Azure Databricks UI 为工作区文件夹及其包含的对象、数据对象和计算资源提供了一个易于使用的图形界面。

> [!div class="mx-imgBorder"]
> ![登陆页面](../_static/images/getting-started/landing-azure.png)

**[REST API](../dev-tools/api/index.md)**

有两种 REST API 版本：[REST API 2.0](../dev-tools/api/latest/index.md#rest-api-v2) 和 [REST API 1.2](../dev-tools/api/1.2/index.md#rest-api-v1)。 首选 REST API 2.0，其支持 REST API 1.2 的大多数功能，并支持其他功能。

**[CLI](../dev-tools/cli/index.md)**

此开放源代码项目承载在 [GitHub](https://github.com/databricks/databricks-cli) 上。 CLI 在 [REST API 2.0](../dev-tools/api/latest/index.md#rest-api-v2) 的基础上构建。

## <a name="data-management"></a>数据管理

本部分介绍用于保存数据的对象，将对这些数据执行分析并将其馈送到机器学习算法中。

**[Databricks 文件系统 (DBFS)](../data/databricks-file-system.md)**

blob 存储区之上的文件系统抽象层。 它包含目录，其中可以包含文件（数据文件、库和图像）和其他目录。 系统会自动使用某些可用来了解 Azure Databricks 的[数据集](../data/databricks-datasets.md)来填充 DBFS。

[数据库](../data/tables.md)

信息集合，该信息经过整理以便轻松进行访问、管理和更新。

**Table**

结构化数据的表示形式。 利用 Apache Spark SQL 和 Apache Spark API 查询表。

**[元存储](../data/metastores/index.md)**

用于存储数据仓库中各种表和分区的所有结构信息的组件，包括列和列类型信息、读取和写入数据所需的序列化器和去序列化器，以及用于存储数据的相应文件。 每个 Azure Databricks 部署都有一个中心 Hive 元存储，供所有需要保存表元数据的群集访问。 也可以选择使用现有的[外部 Hive 元存储](../data/metastores/external-hive-metastore.md)。

## <a name="computation-management"></a>计算管理

本部分介绍在 Azure Databricks 中运行计算时需要了解的概念。

**[群集](../clusters/index.md)**

用于运行笔记本和作业的一组计算资源和配置。 有两种类型的群集：通用和作业。

* 使用 UI、CLI 或 REST API 创建通用群集。 可手动终止和重启通用群集。 多个用户可以共享此类群集，以协作的方式执行交互式分析。
* 当你在新的作业群集上运行[作业](../jobs.md)时，Azure Databricks 作业计划程序将创建一个作业群集，并在作业完成时终止该群集 。 无法重启作业群集。

**[池](../clusters/instance-pools/index.md)**

一组空闲的随时可用的实例，可减少群集启动和自动缩放时间。 附加到池时，群集会从池中分配其驱动程序节点和工作器节点。
如果池中没有足够的空闲资源来满足群集的请求，则池会通过从实例提供程序分配新的实例进行扩展。 终止附加的群集后，它使用的实例会返回到池中，可供其他群集重复使用。

[Databricks 运行时](../runtime/index.md#dbr-overview)

核心组件集，可在 Azure Databricks 管理的群集上运行。 Azure Databricks 提供多种类型的运行时：

* [Databricks Runtime](../release-notes/runtime/index.md) 包括 Apache Spark，但还添加了许多可以显著提高大数据分析可用性、性能和安全性的组件与更新。
* [用于机器学习的 Databricks Runtime](../runtime/mlruntime.md#mlruntime) 基于 Databricks Runtime 构建的，为机器学习和数据科学提供随时可用的环境。 它包含多个流行库，其中包括 TensorFlow、Keras、PyTorch 和 XGBoost。
* [用于基因组学的 Databricks Runtime](../runtime/genomicsruntime.md#dbr-genomics) 是 Databricks Runtime 的一个版本，已针对处理基因组和生物医学数据而进行了优化。
* [Databricks Light](../runtime/light.md#light) 是开放源代码 Apache Spark 运行时的 Azure Databricks 包。 它为不需要 Databricks Runtime 所提供的高级性能、可靠性或自动缩放优势的作业提供运行时选项。 仅当创建运行 [JAR、Python 或 spark-submit](../dev-tools/api/latest/jobs.md#jobscreatejob) 作业的群集时，才可以选择 Databricks Light；对于要在其上运行交互式或笔记本作业工作负荷的群集，不能选择此运行时。

**[作业](../jobs.md)**

可立即或按计划运行笔记本或库的非交互式机制。

**工作负载**

Azure Databricks 标识了[定价](https://databricks.com/product/pricing)方案不同的两种类型的工作负载：数据工程（作业）和数据分析（通用）。

* **数据工程**（自动）工作负载在 Azure Databricks 作业计划程序为每个工作负载创建的 _工作群集_ 上运行。
* **数据分析**（交互式）工作负载在 _通用群集_ 上运行。 交互式工作负载通常在 Azure Databricks [笔记本](../notebooks/index.md)内运行命令。 但是，在现有通用群集上运行作业也被视为交互式工作负载 。

**执行上下文**

每种受支持编程语言的 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) 环境的状态。 支持的语言包括 Python、R、Scala 和 SQL。

## <a name="model-management"></a>模型管理

本部分介绍训练机器学习模型时需要了解的概念。

[模型](../applications/mlflow/models.md)

表示一组预测变量和结果之间的关系的数学函数。 机器学习包括“训练”和“推理”步骤 。 你使用现有数据集训练模型，然后使用该模型预测新数据的结果（推理） 。

**[运行](../applications/mlflow/tracking.md#mlflow-tracking)**

与训练机器学习模型相关的参数、指标和标记的集合。

**[试验](../applications/mlflow/tracking.md#mlflow-experiments)**

对运行进行组织和访问控制时使用的主要单位；所有 MLflow 运行都属于实验。 通过试验，你能够可视化、搜索和比较运行，以及下载运行项目或元数据以便在其他工具中进行分析。

## <a name="authentication-and-authorization"></a>身份验证和授权

本部分介绍管理 Azure Databricks 用户及其对 Azure Databricks 资产的访问时需要了解的概念。

[用户](../administration-guide/users-groups/users.md)

有权访问系统的唯一个人。

[组](../administration-guide/users-groups/groups.md)

用户集合。

[访问控制列表 (ACL)](../administration-guide/access-control/index.md)

附加到工作区、群集、作业、表或实验的权限的列表。 ACL 指定向哪些用户或系统进程授予对对象的访问权限，以及允许对资产执行哪些操作。 典型 ACL 中的每个条目都指定主题和操作。