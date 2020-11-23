---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 03/10/2020
title: 长时间运行的作业在启用了凭据传递的群集上出现身份验证失败 - Azure Databricks
description: 问题 - 长时间运行的作业失败，并在启用了凭据传递的群集上出现 401 未授权身份验证错误。
category: Job
author: xwang30
db-author: xin.wang@databricks.com
ms.openlocfilehash: 0a98316ea4de54dc5d39ccf6aecfa09e56f4c6e3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589867"
---
# <a name="long-running-jobs-have-authentication-failures-on-clusters-with-credential-passthrough"></a>长时间运行的作业在启用了凭据直通身份验证的群集上出现身份验证失败

## <a name="problem"></a>问题

连接到其他 Azure 服务（如 ADLS）时，Azure Databricks 作业失败，并出现 `401 - Unauthorized` 错误消息。 作业运行一小时后失败。 作业在启用了凭据传递的群集上运行。

## <a name="cause"></a>原因

访问令牌的默认生存期为一小时。 因此，当基础访问令牌在一小时后过期时，作业将失败。 在交互式会话过程中，启用了凭据传递的群集使用刷新令牌来自动扩展访问令牌的生存期。 在作业期间，不会使用刷新令牌，因此当访问令牌过期时，访问将终止。

## <a name="solution"></a>解决方案

如果在启用了凭据传递的群集上运行作业，则应使用令牌生存期策略扩展访问令牌生存期。 访问令牌的生存期应比作业的运行时长。 必须具有全局管理员或应用程序 Azure Active Directory 角色才能创建令牌生存期策略。

请阅读[在 Azure 中配置令牌生存期](/active-directory/develop/active-directory-configurable-token-lifetimes)一文，了解如何创建令牌生存期策略。