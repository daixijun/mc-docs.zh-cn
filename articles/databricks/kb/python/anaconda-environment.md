---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/26/2020
title: 通过 Conda 创建群集 - Azure Databricks
description: 了解如何通过 Conda 创建 Azure Databricks 群集。
ms.openlocfilehash: 321708ac74ccb12cd237e56b7f68ce3ee873078f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589543"
---
# <a name="create-a-cluster-with-conda"></a>使用 Conda 创建群集

[Conda](https://conda.io/en/latest/) 是 [Anaconda](https://www.anaconda.com/) 存储库的常用开源程序包管理系统。

用于机器学习的 Databricks Runtime (Databricks Runtime ML) 使用 Conda 管理 Python 库依赖项。 如果要使用 Conda，则应使用 Databricks Runtime ML。 不支持安装 Anaconda 或 Conda 并与 Databricks Runtime 一起使用。

按照[使用 Databricks Runtime ML 创建群集](/databricks/runtime/mlruntime#create-a-cluster-using-databricks-runtime-ml)中的说明，通过 Conda 创建群集。 创建群集后，可以使用 Conda 管理群集上的 Python 包。