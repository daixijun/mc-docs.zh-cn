---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 管理员用户无法重启群集来运行作业 - Azure Databricks
description: 了解如何向 Azure Databricks 管理员用户重新授予特权。
ms.openlocfilehash: 8b786168d0feae1314753791a17965c81884019b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589775"
---
# <a name="admin-user-cannot-restart-cluster-to-run-job"></a>管理员用户无法重启群集来运行作业

## <a name="problem"></a>问题

当有权启动群集的用户（例如 Azure Databricks 管理员用户）提交其他用户所拥有的作业时，该作业会失败并显示以下消息：

```console
Message: Run executed on existing cluster ID <cluster id> failed because of insufficient permissions. The error received from the cluster manager was: 'You are not authorized to restart this cluster. Please contact your administrator or the cluster creator.'
```

## <a name="cause"></a>原因

当作业所有者启动群集的特权被撤销时，可能会发生此错误。 在这种情况下，即使作业由管理员用户提交，也会失败。

## <a name="solution"></a>解决方案

将启动群集的特权（称为“可以管理”）重新授予作业所有者。
将作业所有者更改为具有群集启动特权的用户或组。 可以通过导航到作业页中的“作业”，然后导航至“高级”>“权限”>“编辑”来对其进行更改。