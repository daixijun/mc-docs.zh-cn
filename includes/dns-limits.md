---
author: WenJason
ms.service: azure-resource-manager
ms.topic: include
origin.date: 11/05/2020
ms.date: 12/07/2020
ms.author: v-jay
ms.openlocfilehash: d26b175033e8147acc3d1e53c6614cae2ac46dc9
ms.sourcegitcommit: ac1cb9a6531f2c843002914023757ab3f306dc3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747267"
---
**公共 DNS 区域**

| 资源 | 限制 |
| --- | --- |
| 每个订阅的公共 DNS 区域数 |250 <sup>1</sup> |
| 每个公共 DNS 区域的记录集数 |10,000 <sup>1</sup> |
| 公共 DNS 区域中每个记录集的记录数 |20 |
| 单个 Azure 资源的别名记录数 |20|

<sup>1</sup>如果需要增加这些限制，请与 Azure 支持部门联系。

**专用 DNS 区域**

| 资源 | 限制 |
| --- | --- |
| 每个订阅的专用 DNS 区域数 |1000|
| 每个专用 DNS 区域的记录集数 |25000|
| 专用 DNS 区域的每个记录集的记录数 |20|
| 每个专用 DNS 区域的虚拟网络链接数 |1000|
| 在启用了自动注册的情况下，每个专用 DNS 区域的虚拟网络链接数 |100|
| 在启用了自动注册的情况下，虚拟网络可以链接到的专用 DNS 区域数 |1|
| 虚拟网络可以链接的专用 DNS 区域数 |1000|
| 虚拟机每秒可发送到 Azure DNS 解析程序的 DNS 查询数 |1000 <sup>1</sup> |
| 每个虚拟机排队（等待响应）的最大 DNS 查询数 |200 <sup>1</sup> |

<sup>1</sup>这些限制适用于每个单独的虚拟机，而不适用于虚拟网络级别。 将删除超出这些限制的 DNS 查询。
