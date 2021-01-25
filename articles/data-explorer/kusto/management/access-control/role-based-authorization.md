---
title: Kusto 中基于角色的授权 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器的 Kusto 中基于角色的授权。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/14/2020
ms.date: 01/22/2021
ms.openlocfilehash: f8342183daf6af927e8f7ad4854a72767dfd3e79
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611424"
---
# <a name="role-based-authorization-in-kusto"></a>Kusto 中基于角色的授权

授权是允许或禁止执行操作所需的安全主体权限的过程。
Kusto 使用“基于角色的访问控制”模型。在该模型中，经过身份验证的主体会被映射到角色，并根据分配的角色获得访问权限。

Kusto 引擎服务具有以下角色：

|角色                       |权限                                                                                                                                                  |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
|所有数据库管理员        |可以在任何数据库的范围内执行任何操作。 可以显示和更改某些群集级别策略                                                               |
|数据库管理员             |可以在特定数据库的范围内执行任何操作                                                                                                         |
|数据库用户              |可以读取数据库的所有数据和元数据。 此外，可以创建表并成为这些表的表管理员，并在数据库中创建函数。|
|所有数据库查看者       |可以读取任何数据库的所有数据和元数据                                                                                                               |
|数据库查看器            |可以读取特定数据库的所有数据和元数据                                                                                                       |
|数据库引入器          |可以将数据引入到数据库中的所有现有表，但不能查询数据                                                                             |
|数据库无限制查看器|可以查询数据库中已启用 [RestrictedViewAccess 策略](../restrictedviewaccess-policy.md)的所有表                                |
|数据库监视器           |可以在数据库及其子实体的上下文中执行 `.show` 命令                                                                           |
|函数管理员             |可以更改函数、删除函数或向其他主体授予管理员权限                                                                         |
|表管理员                |可以在特定表的范围内执行任何操作                                                                                                           |
|表引入器             |可以在特定表的范围内引入数据，但不能查询数据                                                                                 |

> [!NOTE]
> 若要授予来自其他租户的主体对群集的访问权限，请参阅[允许跨租户查询和命令](../../../cross-tenant-query-and-commands.md)。