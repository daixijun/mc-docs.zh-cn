---
title: API 管理策略示例 - 生成共享访问签名
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何使用表达式生成共享访问签名并使用 rewrite-uri 策略将请求转发到 Azure 存储。
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
ms.openlocfilehash: a2948b10a6862bd4825a1713c71885474e8bb7d7
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306480"
---
# <a name="generate-shared-access-signature"></a>生成共享访问签名

本文介绍 Azure API 管理策略示例，该示例演示如何使用表达式生成[共享访问签名](../../storage/common/storage-sas-overview.md)并使用 rewrite-uri 策略将请求转发到 Azure 存储。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

```xml
<!-- The policy defined in this file shows how to generate Shared Access Signature (https://docs.azure.cn/storage/common/storage-dotnet-shared-access-signature-part-1) using expressions. -->
<!-- The snippet forwards the request to Azure storage with rewrite-uri policy. -->

<!-- Copy the following snippet into the inbound section. -->

<policies>
    <inbound>
        <base />
        <!-- Initialize context variables with property values. -->
        <set-variable name="accessKey" value="{{storageAccountAccessKey}}" />
        <set-variable name="storageAccount" value="{{storageAccountName}}" />
        <set-variable name="Content-Type" value="application/json" />
        <set-variable name="resourcePath" value="TableName()" />
        <set-variable name="x-ms-date" value="@(DateTime.UtcNow.ToString("R"))" />

        <!-- Set required headers. -->
        <set-header name="Content-Type" exists-action="override">
            <value>@((string)context.Variables["Content-Type"])</value>
        </set-header>
        <set-header name="Accept" exists-action="override">
            <value>application/json;odata=nometadata</value>
        </set-header>
            <set-header name="Accept-Charset" exists-action="override">
        <value>UTF-8</value>
        </set-header>
            <set-header name="x-ms-date" exists-action="override">
        <value>@((string)context.Variables["x-ms-date"])</value>
            </set-header>
        <set-header name="x-ms-version" exists-action="override">
            <value>2015-04-05</value>
        </set-header>

        <!-- Beginning with version 2009-09-19, the Table service requires that all REST calls include the DataServiceVersion and MaxDataServiceVersion headers. -->
        <!-- See https://docs.microsoft.com/rest/api/storageservices/fileservices/setting-the-odata-data-service-version-headers for more information. -->
        <set-header name="MaxDataServiceVersion" exists-action="override">
            <value>3.0</value>
        </set-header>
        <set-header name="DataServiceVersion" exists-action="override">
            <value>1.0;NetFx</value>
        </set-header>

        <set-variable name="CanonicalizedResource" value="@{
            // /{storageAccount}/{resourcePath}
            return string.Format("/{0}/{1}",(string)context.Variables["storageAccount"],(string)context.Variables["resourcePath"]);
            }" />

        <!-- SharedKeyLite is legacy and SharedKey should be used. -->
        <!--set-variable name="SharedKeyLite" value="@{
                // SharedKeyLite is there for backwards compatibility. SharedKey is preferred for new work.
                // System.Security.Cryptography.HMACSHA256 hasher = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String((string)context.Variables["accessKey"]));
                // return Convert.ToBase64String(hasher.ComputeHash(System.Text.Encoding.UTF8.GetBytes(string.Format("{0}\n{1}",(string)context.Variables["x-ms-date"],(string)context.Variables["CanonicalizedResource"]))));
            }" /-->

        <set-variable name="StringToSign" value="@{
                // https://docs.microsoft.com/rest/api/storageservices/fileservices/authentication-for-the-azure-storage-services
                // {VERB}\n{Content-MD5}\n{Content-Type}\n{Date}\n{CanonicalizedResource} e.g. "GET\n\n{0}\n{1}\n{2}",
                return string.Format(
                "GET\n\n{0}\n{1}\n{2}",
                (string)context.Variables["Content-Type"],
                (string)context.Variables["x-ms-date"],
                (string)context.Variables["CanonicalizedResource"]);
            }" />

        <set-variable name="SharedKey" value="@{
                // https://docs.microsoft.com/rest/api/storageservices/fileservices/authentication-for-the-azure-storage-services
                // Hash-based Message Authentication Code (HMAC) using SHA256 hash
                System.Security.Cryptography.HMACSHA256 hasher = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String((string)context.Variables["accessKey"]));
                return Convert.ToBase64String(hasher.ComputeHash(System.Text.Encoding.UTF8.GetBytes((string)context.Variables["StringToSign"])));
            }" />

        <set-header name="Authorization" exists-action="override">
            <value>@(string.Format("SharedKey {0}:{1}", (string)context.Variables["storageAccount"], (string)context.Variables["SharedKey"]))</value>
            <!-- SharedKeyLite is legaacy and SharedKey preferred, but here it is if needed (2 of 2) -->
            <!--value>@(string.Format("SharedKeyLite {0}:{1}", (string)context.Variables["storageAccount"], (string)context.Variables["SharedKeyLite"]))</value-->
        </set-header>

        <!-- Add $filter here: -->
        <rewrite-uri template="/TableName()" />
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

