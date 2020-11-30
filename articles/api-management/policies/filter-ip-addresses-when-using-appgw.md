---
title: 示例 API 管理策略 - 使用应用程序网关时对 IP 地址进行筛选
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何在使用应用程序网关时对请求 IP 地址进行筛选。
services: api-management
documentationcenter: ''
author: Johnnytechn
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
origin.date: 01/13/2020
ms.date: 11/18/2020
ms.author: v-johya
ms.custom: fasttrack-new
ms.openlocfilehash: 453164d28812e4e586acb17a9a35645091af4d7f
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306300"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>使用应用程序网关时对请求 IP 地址进行筛选

本文展示了一个 Azure API 管理策略示例，该示例演示了当通过应用程序网关或其他中介访问 API 管理实例时，如何筛选请求 IP 地址。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```XML
<!-- 
    The policy defined in this file shows how to filter request IPs when the API Management instance 
    is accessed from an Application Gateway.  The Application Gateway will write the original request
    IP to the "X-Forwarded-For" header.  The set-header policy evaluates this IP address against a list of
    IP ranges (if any).  If a match is found, a value is written to the X-Forwarded-For header and the
    following check-header policy will validate the match.  If no match is found 
-->
<policies>
    <inbound>
        <base />
        <!-- Save the X-Forwarded-For value -->
        <set-variable name="originalXForwardedForValue" value="@(context.Request.Headers.GetValueOrDefault("X-Forwarded-For"))" />
        <!-- Check the client IP value in the X-Forwarded-For header-->
        <set-header name="X-Forwarded-For" exists-action="override">
            <value>@{
            int HostToNetworkOrder(int host)
            {
                return (((int)HostToNetworkOrderShort((short)host) & 0xFFFF) << 16)
                    | ((int)HostToNetworkOrderShort((short)(host >> 16)) & 0xFFFF);
            }
            short HostToNetworkOrderShort(short host)
            {
                return (short)((((int)host & 0xFF) << 8) | (int)((host >> 8) & 0xFF));
            }
            
            string ipAddress = context.Request.Headers.GetValueOrDefault("x-forwarded-for",""); 
            if (!string.IsNullOrEmpty(ipAddress))
            {
                string[] tokens = ipAddress.Split(':'); 
                if(tokens.Length == 2) 
                { ipAddress = tokens[0]; } 
                //Place IP Ranges into this list in CIDR notation (e.g. "0.0.0.0/0") and separate with commas
                List<string> cidrList = new List<string>(){
                    "10.0.0.0/8",
                    "172.0.0.0/24",
                    "192.168.0.0/24"
                };
                foreach (string cidrAddress in cidrList)
                {
                    string[] cidrParts = cidrAddress.Split('/');
                    string[] inputIPParts = ipAddress.Split('.');
                    string[] cidrIPArray = cidrParts[0].Split('.');

                    if (inputIPParts.Length == 4 && cidrIPArray.Length == 4)
                    {
                        byte[] inputIPBytes = new byte[] {Convert.ToByte(int.Parse(inputIPParts[0])), 
                            Convert.ToByte(int.Parse(inputIPParts[1])), 
                            Convert.ToByte(int.Parse(inputIPParts[2])),
                            Convert.ToByte(int.Parse(inputIPParts[3])), };
                        byte[] cidrIPBytes = new byte[] {Convert.ToByte(int.Parse(cidrIPArray[0])), 
                            Convert.ToByte(int.Parse(cidrIPArray[1])), 
                            Convert.ToByte(int.Parse(cidrIPArray[2])),
                            Convert.ToByte(int.Parse(cidrIPArray[3])), };
                
                        int cidrAddr = BitConverter.ToInt32(inputIPBytes,0);
                        int ipAddr = BitConverter.ToInt32(cidrIPBytes,0);
                        
                        var host = int.Parse(cidrParts[1]);
                        host = -1 << (32-host);
                        var mask = HostToNetworkOrder(host);
                        
                        if (((ipAddr & mask) == (cidrAddr & mask)))
                        {
                            return "{{ipValidated}}";
                        }
                    }
                }
            }
            return ipAddress; }</value>
        </set-header>
        <check-header name="x-forwarded-for" failed-check-httpcode="403" failed-check-error-message="Unauthorized" ignore-case="true">
            <value>10.33.215.173</value>
            <value>10.33.215.175</value>
            <value>{{ipValidated}}</value>
        </check-header>
        <!-- Revert X-Forwarded-For to its original value-->
        <set-header name="X-Forwarded-For" exists-action="override">
            <value>@{
                return context.Variables.GetValueOrDefault<string>("originalXForwardedForValue");
                }
            </value>
        </set-header>
    </inbound>
</policies>
```

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [访问限制策略](../api-management-access-restriction-policies.md)
+ [策略示例](../policy-reference.md)

