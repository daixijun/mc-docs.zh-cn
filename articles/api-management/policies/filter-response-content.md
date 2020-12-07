---
title: Azure API 管理策略示例 - 筛选响应内容
description: Azure API 管理策略示例 - 演示如何基于与请求关联的产品从响应有效负载中筛选数据元素。
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
ms.openlocfilehash: 6e2f4f2d72e10e1a9e29d7cecb5d2c50b2b69470
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306193"
---
# <a name="filter-response-content"></a>筛选响应内容

本文介绍 Azure API 管理策略示例，该示例演示如何基于与请求关联的产品从响应有效负载中筛选数据元素。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“出站”块中  。

```xml
<!-- The policy defined in this file demonstrates how to filter data elements from the response payload based on the product associated with the request.
<!-- The snippet assumes that response content is formatted as JSON and contains root-level properties named "minutely", "hourly", "daily", "flags". -->

<!-- Use https://darksky.net/dev/ API to test this policy. -->

<!-- Copy this snippet into the outbound section. -->

<policies>
  <inbound>
    <base />
  </inbound>
  <backend>
    <base />
  </backend>
  <outbound>
    <base />
    <choose>
      <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter="""))">
        <set-body>
          @{
            <!-- NOTE that we are not using preserveContent=true when deserializing response body stream into a JSON object since we don't intend to access it again. See details on https://docs.azure.cn/zh-cn/api-management/api-management-transformation-policies#SetBody -->
            var response = context.Response.Body.As<JObject>();
            foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
            response.Property (key).Remove ();
           }
          return response.ToString();
          }
        </set-body>
      </when>
    </choose>    
  </outbound>
  <on-error>
    <base />
  </on-error>
</policies>
```

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-reference.md)

