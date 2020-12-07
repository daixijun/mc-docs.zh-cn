---
title: 示例 API 管理策略 - 向后端服务添加功能
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何向后端服务添加功能。 例如，接受位置的名称而不是天气预报 API 中的纬度和经度。
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
ms.openlocfilehash: 46cd9766951ae2f3e8a87e3ae7be5c64bbb94768
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306464"
---
# <a name="add-capabilities-to-a-backend-service"></a>向后端服务添加功能

本文介绍 Azure API 管理策略示例，演示如何向后端服务添加功能。 例如，接受位置的名称而不是天气预报 API 中的纬度和经度。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```xml
<!-- The policy defined in this file shows how to use a few policies to add a capability to a backend service. -->

<!-- https://darksky.net/dev API was used as a backend service in this example. The snippet contains enough information for reconstituting the setup and testing the policy. -->
<!-- The snippet shows how to accept a name of the place instead of latitude and longitude in a weather forecast API. -->

<!-- Copy the following snippet into the inbound section and look at the trace window to see it work. -->

<policies>
  <inbound>
    <base />
    <!-- Check if lat/long has already been cached for this place and fetch it into a variable. -->
    <cache-lookup-value key="@(context.Request.MatchedParameters.GetValueOrDefault("place="""))" variable-name="latlong"/>

    <!-- If no lat/long found, use external API to get lat/long of the place and cache it. -->
    <choose>
      <when condition="@(!context.Variables.ContainsKey("latlong="""))">

        <!-- Lookup lat/long for the place. -->
        <send-request mode="new" response-variable-name="response" timeout="10" ignore-error="false">
          <set-url>
            @{
            var code = context.Request.MatchedParameters.GetValueOrDefault("place");
            var key = "{{google-geo-api-key}}";
            return $"https://maps.googleapis.com/maps/api/geocode/json?address={code}&key={key}";
            }
          </set-url>
          <set-method>GET</set-method>
        </send-request>

        <!-- Extract a JSON object containing lat/long from the response and serialize it into a variable. -->
        <set-variable name="latlong" value="@(((IResponse)context.Variables["response="""]).Body.As<JObject>
          ()["results"][0]["geometry"]["location"].ToString())"/>

          <!-- Cache lat/long for a an hour (coould be for much longer of course, since places don't move very often). -->
          <cache-store-value key="@(context.Request.MatchedParameters.GetValueOrDefault("latlong="""))" value="@((string)context.Variables["latlong="""])" duration="3600"/>
        </when>
    </choose>

    <!-- Change forwarding URL to the form expected by the backend, i.e. containing the key and lat/lng.  -->
    <rewrite-uri template="@{
            var loc = JObject.Parse((string)context.Variables["latlong=""
                               lat=""
                               lng=""
                         forecast=""/{{dark-sky-api-key}}/{lat},{lng}";}"/>

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

