---
title: Azure API 管理策略示例 - 使用 OAuth2 在网关和后端之间进行授权
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何使用 OAuth2 在网关和后端之间进行授权。 该示例演示如何从 AAD 获取访问令牌并将其转发到后端。
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
ms.openlocfilehash: 9ade73c668781d0c26167a894ec6795545134184
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306389"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>使用 OAuth2 在网关和后端之间进行授权

本文介绍 Azure API 管理策略示例，该示例演示如何使用 OAuth2 在网关和后端之间进行授权。 该示例演示如何从 AAD 获取访问令牌并将其转发到后端。 

若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

以下脚本使用了 {{property}} 中出现的属性。 若要了解各个属性以及如何在 API 管理策略中使用它们，请参阅[此](../api-management-howto-properties.md)主题。
 
## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```xml
<!-- The policy defined in this file provides an example of using OAuth2 for authorization between the gateway and a backend. -->
<!-- It shows how to obtain an access token from AAD and forward it to the backend. -->

<!-- Send request to AAD to obtain a bearer token -->
<!-- Parameters: authorizationServer - format https://login.windows.net/TENANT-GUID/oauth2/token -->
<!-- Parameters: scope - a URI encoded scope value -->
<!-- Parameters: clientId - an id obtained during app registration -->
<!-- Parameters: clientSecret - a URL encoded secret, obtained during app registration -->

<!-- Copy the following snippet into the inbound section. -->

<policies>
  <inbound>
    <base />
      <send-request ignore-error="true" timeout="20" response-variable-name="bearerToken" mode="new">
        <set-url>{{authorizationServer}}</set-url>
        <set-method>POST</set-method>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>
          @{
          return "client_id={{clientId}}&resource={{scope}}&client_secret={{clientSecret}}&grant_type=client_credentials";
          }
        </set-body>
      </send-request>

      <set-header name="Authorization" exists-action="override">
        <value>
          @("Bearer " + (String)((IResponse)context.Variables["bearerToken"]).Body.As<JObject>()["access_token"])
      </value>
      </set-header>

      <!--  Don't expose APIM subscription key to the backend. -->
      <set-header exists-action="delete" name="Ocp-Apim-Subscription-Key"/>
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

