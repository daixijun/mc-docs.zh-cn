---
title: 查询限制策略 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的查询限制策略
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
origin.date: 10/05/2020
ms.date: 01/22/2021
ms.openlocfilehash: e14cf2760a693d8a0e938f4dc5f895f4a382957f
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611618"
---
# <a name="query-throttling-policy"></a>查询限制策略

定义查询限制策略以限制群集同时可执行的并发查询数。 此策略可保护群集不因并发查询过多而过载。 此策略可以在运行时更改，在 alter policy 命令完成后立即进行。

* 使用 [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling) 显示群集的当前查询限制策略。
* 使用 [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling) 设置群集的当前查询限制策略。
* 使用 [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling) 删除群集的当前查询限制策略。

> [!NOTE]
> 未经全面测试，请勿更改查询限制策略。 如果群集资源没有按照负载进行缩放，那么更改可能会导致性能下降，因此查询可能会开始失败。 
