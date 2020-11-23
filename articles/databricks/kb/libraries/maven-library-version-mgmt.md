---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何在 Azure Databricks 中正确更新 Maven 库 - Azure Databricks
description: 了解如何在 Azure Databricks 中正确更新 Maven 库。
ms.openlocfilehash: b44d37efd16aeffbd1d82f15289e54d7e2939574
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589831"
---
# <a name="how-to-correctly-update-a-maven-library-in-azure-databricks"></a>如何在 Azure Databricks 中正确更新 Maven 库

## <a name="problem"></a>问题

假设你对存储库中的库进行了少许更新，并且不希望更改版本号，因为这只是一种用于测试目的的小更改。 但是，当你再次将库附加到群集时，你的代码更改不会包含在库中。

## <a name="cause"></a>原因

Azure Databricks 的一个优点是可以安装第三方或自定义库（例如，来自 Maven 存储库的库）。 但是，在存储库中更新了库时，无法通过自动方式更新群集中的相应库。

当你请求 Azure Databricks 下载库以便将其附加到群集时，将发生以下过程：

1. 在 Azure Databricks 中，你请求 Maven 存储库中的一个库。
2. Azure Databricks 检查本地缓存中是否存在该库，如果不存在，则将该库从 Maven 存储库下载到本地缓存。
3. 然后，Azure Databricks 将该库复制到 DBFS (`/FileStore/jars/maven/`)。
4. 发生对该库的后续请求时，Azure Databricks 会使用已复制到 DBFS 的文件，并且不会下载新副本。

## <a name="solution"></a>解决方案

若要确保将更新的库版本（或已自定义的库）下载到群集，请确保以某种方式递增项目的内部版本号或版本号。 例如，你可以将 `libA_v1.0.0-SNAPSHOT` 更改为 `libA_v1.0.1-SNAPSHOT`，然后新库将会进行下载。 然后，你可以将其附加到群集。