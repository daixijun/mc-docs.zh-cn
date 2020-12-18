---
services: storage
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 09/29/2020
ms.date: 12/14/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: ff925e9ca5ea52448664d1750607fc99689d5552
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850818"
---
| 机制 | 范围 |限制 | 支持的权限级别 |
|---|---|---|---|
| Azure RBAC | 存储帐户、容器。 <br>在订阅或资源组级别进行跨资源 Azure 角色分配。 | 一个订阅中 2000 个 Azure 角色分配 | Azure 角色（内置或自定义） |
| ACL| 目录、文件 |每个文件和每个目录有 32 个 ACL 条目（实际上是 28 个 ACL 条目）。 访问 ACL 和默认 ACL 都有自己的 32 个 ACL 条目的限制。 |ACL 权限|
