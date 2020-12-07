---
title: 示例 API 管理策略 - 添加包含相关 ID 的标头
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何将包含相关 ID 的标头添加到入站请求。
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
ms.openlocfilehash: 8898e859195ada46812ee45530f7883f4ccdbbd5
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306305"
---
# <a name="add-a-header-containing-a-correlation-id"></a>添加包含相关 ID 的标头

本文介绍 Azure API 管理策略示例，该示例演示如何将包含相关 ID 的标头添加到入站请求。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```xml
<!-- The policy defined in this file demonstrates how to add a header containing a correlation id to the inbound request. -->
<!-- The id could be used to correlate requests forwarded by Azure API Management to requests in your backend. -->
<!-- The snippet uses a COMB GUID as an id value for efficient query performance. -->

<!-- NOTE: If COMB format is not needed, context.RequestId should be used as a value of correlation id. -->
<!-- context.RequestId is unique for each request and is stored as part of gateway log records. -->

<!-- Copy the following snippet into the inbound section. Applying this snippet at the global level will ensure that correlation id is added to all requests. -->




<policies>
    <inbound>
        <base />
        <set-header name="correlationid" exists-action="skip">
            <value>@{ var guidBinary = new byte[16];
                        Array.Copy(Guid.NewGuid().ToByteArray(), 0, guidBinary, 0, 10);
                        long time = DateTime.Now.Ticks;
                        byte[] bytes = new byte[8];
                        unchecked
                        {
                            bytes[7] = (byte)(time >> 56);
                            bytes[6] = (byte)(time >> 48);
                            bytes[5] = (byte)(time >> 40);
                            bytes[4] = (byte)(time >> 32);
                            bytes[3] = (byte)(time >> 24);
                            bytes[2] = (byte)(time >> 16);
                            bytes[1] = (byte)(time >> 8);
                            bytes[0] = (byte)(time);
                        }
                        Array.Copy(bytes, 0, guidBinary, 8, 6);
                        return new Guid(guidBinary).ToString();
                    }
            </value>
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
+ [策略示例](../policy-reference.md)

