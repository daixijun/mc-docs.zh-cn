---
title: API 管理策略示例 - 基于 JWT 声明授予访问权限
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何基于 JWT 声明授予对 API 中特定 HTTP 方法的访问权限。
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
ms.openlocfilehash: 1871eb0b1bfd04b11829fea3d198a85048d17cca
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306466"
---
# <a name="authorize-access-based-on-jwt-claims"></a>基于 JWT 声明授权访问权限

本文介绍 Azure API 管理策略示例，该示例演示如何基于 JWT 声明授予对 API 中特定 HTTP 方法的访问权限。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```xml
<!-- The policy defined in this file shows how to authorize access to specific HTTP methods on an API based on JWT claims. -->
<!-- To test the policy you can use https://jwt.io to generate tokens. -->

<!-- Copy the following snippet into the inbound section. -->

<policies>
  <inbound>
    <base />
      <choose>
        <when condition="@(context.Request.Method.Equals("patch=""",StringComparison.OrdinalIgnoreCase))">
          <validate-jwt header-name="Authorization">
            <issuer-signing-keys>
              <key>{{signing-key}}</key>
            </issuer-signing-keys>
            <required-claims>
              <claim name="edit">
                <value>true</value>
              </claim>
            </required-claims>
          </validate-jwt>
        </when>
        <when condition="@(new [] {"post=""", "put="""}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">
          <validate-jwt header-name="Authorization">
            <issuer-signing-keys>
              <key>{{signing-key}}</key>
            </issuer-signing-keys>
            <required-claims>
              <claim name="create">
                <value>true</value>
              </claim>
            </required-claims>
          </validate-jwt>
        </when>
        <otherwise>
          <validate-jwt header-name="Authorization">
            <issuer-signing-keys>
              <key>{{signing-key}}</key>
            </issuer-signing-keys>
          </validate-jwt>
        </otherwise>
      </choose>    
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
+ [策略示例](../policy-reference.md)

