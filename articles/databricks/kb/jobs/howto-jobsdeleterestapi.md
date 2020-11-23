---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 如何使用 REST API 删除所有作业 - Azure Databricks
description: 了解如何使用 REST API 删除所有 Azure Databricks 作业。
ms.openlocfilehash: e4300e69e845bd59104ca951f186ec685c7edbd3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589450"
---
# <a name="how-to-delete-all-jobs-using-the-rest-api"></a>如何使用 REST API 删除所有作业

运行以下命令以删除 Azure Databricks 工作区中的所有作业。

1. 标识要删除的作业，并在文本文件中列出它们：

   ```bash
   curl -X GET -u "Bearer: <token>" https://<databricks-instance>/api/2.0/jobs/list | grep -o -P 'job_id.{0,6}' | awk -F':' '{print $2}' >> job_id.txt
   ```

2. 在循环中运行 `curl` 命令，以删除标识的作业：

   ```bash
   while read line
   do
   job_id=$line
   curl -X POST -u "Bearer: <token>" https://<databricks-instance>/api/2.0/jobs/delete -d '{"job_id": '"$job_id"'}'
   done < job_id.txt
   ```