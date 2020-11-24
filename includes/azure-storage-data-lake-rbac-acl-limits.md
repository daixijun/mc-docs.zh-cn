---
services: storage
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 09/29/2020
ms.date: 11/16/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: ce9a3868991935c94dde4068c531b43d14fee14e
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552150"
---
| 机制 | 范围 |限制 | 支持的权限级别 |
|---|---|---|---|
| RBAC | 存储帐户、容器。 <br>在订阅或资源组级别进行跨资源 RBAC 角色分配。 | 一个订阅中的 2000 个 RBAC 角色分配 | RBAC 角色（内置或自定义） |
| ACL| 目录、文件 |每个文件和每个目录有 32 个 ACL 条目（实际上是 28 个 ACL 条目）。 访问 ACL 和默认 ACL 都有自己的 32 个 ACL 条目的限制。 |ACL 权限|
