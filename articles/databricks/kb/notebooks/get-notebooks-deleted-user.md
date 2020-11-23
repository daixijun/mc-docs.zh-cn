---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 10/01/2020
title: 访问已删除的用户拥有的笔记本 - Azure Databricks
description: 如何访问已删除的用户拥有的 Azure Databricks 笔记本。
category: Notebooks
author: john-lourdu
db-author: john.lourdu@databricks.com
ms.openlocfilehash: e19748a09188b3c089c9071124ccd497a8f680ff
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589452"
---
# <a name="access-notebooks-owned-by-a-deleted-user"></a>访问已删除的用户拥有的笔记本

从 Azure Databricks 中[删除用户](/databricks/administration-guide/users-groups/users#remove-a-user)时，将在工作区中创建一个特殊的备份文件夹。 此备份文件夹包含已删除的用户的所有内容。

  ![显示备份用户示例的图像](../_static/images/notebooks/get-notebooks-deleted-user.jpg)

备份文件夹将在工作区中显示为“<deleted username>-backup-#”。

> [!NOTE]
>
> 只有管理员用户可以访问备份文件夹。

访问备份文件夹：

1. 以管理员用户身份登录 Azure Databricks。
2. 从边栏中选择“工作区”。
3. 选择“用户”。
4. 选择备份文件夹。

如果不再需要备份文件夹，可以将其删除。