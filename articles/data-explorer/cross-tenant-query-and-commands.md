---
title: 在 Azure 数据资源管理器中允许跨租户的查询和命令
description: 了解在 Azure 数据资源管理器上如何允许来自多个租户的查询或命令。
author: orspod
ms.author: v-tawe
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: reference
origin.date: 01/06/2021
ms.date: 01/20/2021
ms.openlocfilehash: 18ac0f74938f502689102de9cec302ebd796e4a6
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614901"
---
# <a name="allow-cross-tenant-queries-and-commands"></a>允许跨租户查询和命令 

多个租户可以在一个 Azure 数据资源管理器群集中运行查询和命令。 本文介绍如何向来自另一租户的主体授予群集访问权限。

群集所有者可以阻止其群集接受来自其他租户的查询和命令。 这是通过管理 `TrustedExternalTenants` 群集属性实现的。 `trustedExternalTenants` 属性显式定义允许哪些租户在该群集上运行查询和命令。 有关详细信息，请参阅 [Azure 数据资源管理器群集请求正文](https://docs.microsoft.com/rest/api/azurerekusto/clusters/createorupdate#request-body)。

> [!NOTE]
> 将要运行查询或命令的主体还必须具有相关的数据库角色。 有关详细信息，请参阅[基于角色的授权](./kusto/management/access-control/role-based-authorization.md)。 先验证外部租户是否受信任，然后再验证角色是否正确。

## <a name="syntax"></a>语法

定义特定租户

`trustedExternalTenants``: [ {"`*value*`": "`*tenantId1*`" }, { "`*value*`": "`*tenantId2*`" }, ... ]`

允许所有租户

trustedExternalTenants 数组也支持所有租户星号（“*”）表示法，从而能够允许来自所有租户的查询和命令。 

`trustedExternalTenants``: [ { "`*value*`": "`*`" }]`

> [!NOTE]
> `trustedExternalTenants` 的默认值为所有租户：`[ { "value": "*" }]`。 如果在群集创建时未定义外部租户数组，可以通过群集更新操作来重写该数组。 不接受空数组。

## <a name="example"></a>示例

使用以下操作更新群集：

```http
PATCH https://management.chinacloudapi.cn/subscriptions/12345678-1234-1234-1234-123456789098/resourceGroups/kustorgtest/providers/Microsoft.Kusto/clusters/kustoclustertest?api-version=2020-09-18
```

### <a name="request-body-specific-tenants"></a>针对特定租户的请求正文

```json
{
              "properties": { 
                           "trustedExternalTenants": [
                                         { "value": "tenantId1" }, 
                                         { "value": "tenantId2" }, 
                                         ...
                           ]
              }
}
```

### <a name="request-body-all-tenants"></a>针对所有租户的请求正文

```json
{
              "properties": { 
                           "trustedExternalTenants": [  { "value": "*" }  ]
              }
}

```
