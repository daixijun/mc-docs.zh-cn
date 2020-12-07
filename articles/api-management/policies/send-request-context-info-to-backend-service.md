---
title: 示例 API 管理策略 - 将请求上下文信息发送到后端服务
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何将请求上下文信息发送到后端服务。
services: api-management
documentationcenter: ''
author: Johnnytechn
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
origin.date: 10/13/2017
ms.date: 11/18/2020
ms.author: v-johya
ms.openlocfilehash: cfc3d5bfcbf06eebf7b0615243093545543b8672
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306424"
---
# <a name="send-request-context-information-to-the-backend-service"></a>将请求上下文信息转发到后端服务

本文介绍 Azure API 管理策略示例，该示例演示如何将请求上下文信息发送到后端服务。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```xml
<!-- The policies described in this file show how to send some context information to the backend service for logging or processing. -->

<!-- Copy these snippets into the inbound element -->

<policies>
  <inbound>
    <base />
      <!-- Forward the name of the product associated with the subscription key in the request to the backend service. -->
      <set-query-parameter name="x-product-name" exists-action="override">
        <value>@(context.Product.Name)</value>
      </set-query-parameter>

      <!-- Forward the user id associated with the subscription key in the request as well as the region where the proxy processing the request is hosted. -->
      <set-header name="x-request-context-data" exists-action="override">
        <value>@(context.User.Id)</value>
        <value>@(context.Deployment.Region)</value>
      </set-header>    
  </inbound>
  <backend>
    <base />
  </backend>
  <outbound>
    <base />
  </outbound>
  <on-error>
    <base />
  </on-error>
</policies>
```

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)

