---
title: 数据库 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的数据库。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 10/30/2019
ms.date: 01/22/2021
ms.openlocfilehash: dd7667dfa403272a4da5c86f59c5d8abe76293b5
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611636"
---
# <a name="databases"></a>数据库

Kusto 遵循用于存储数据的关系模型，其中较高级别的实体是 `database`。 

Kusto 群集可以托管多个数据库，其中每个数据库将托管其自己的[表](tables.md)集合、[存储的函数](stored-functions.md)和[外部表](externaltables.md)。
每个数据库都有自己的权限集，基于[基于角色的访问控制 (RBAC) 模型](../../management/access-control/index.md)

**说明**  

* 数据库名称必须遵循[实体名称](./entity-names.md)的规则。
* 每个群集的数据库数量最大限制为 10,000。