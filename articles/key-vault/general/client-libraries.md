---
title: Azure Key Vault 的客户端库 | Microsoft Docs
description: Azure Key Vault 的客户端库
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
origin.date: 08/14/2020
ms.date: 09/16/2020
ms.author: v-tawe
ms.openlocfilehash: 74a8aa1f1afe1bef5af283d2e74b78338cce13d5
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300809"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault 的客户端库

Azure 密钥保管库的客户端库允许以编程方式通过各种语言（包括 .NET、Python、Java 和 JavaScript）访问密钥保管库功能。

## <a name="client-libraries-per-language-and-object"></a>每种语言和每个对象的客户端库

每个 SDK 都有单独的用于机密、密钥和证书的客户端库，如下表所示。

| 语言 | 机密 | 键 | 证书 |
|--|--|--|--|
| .NET | - [API 参考](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.secrets)<br>- [NuGet 包](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [快速入门](../secrets/quick-create-net.md) | - [API 参考](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys)<br>- [NuGet 包](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API 参考](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates)<br>- [NuGet 包](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [API 参考](https://docs.microsoft.com/python/api/overview/azure/keyvault-secrets-readme)<br>- [PyPi 包](https://pypi.org/project/azure-keyvault-secrets/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [快速入门](../secrets/quick-create-python.md) |- [API 参考](https://docs.microsoft.com/python/api/overview/azure/keyvault-keys-readme)<br>- [PyPi 包](https://pypi.org/project/azure-keyvault-keys/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [快速入门](../keys/quick-create-python.md) | - [API 参考](https://docs.microsoft.com/python/api/overview/azure/keyvault-certificates-readme)<br>- [PyPi 包](https://pypi.org/project/azure-keyvault-certificates/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [快速入门](../certificates/quick-create-python.md) |
| Java | - [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [快速入门](../secrets/quick-create-java.md) |- [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [API 参考](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/)<br>- [npm 包](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [快速入门](../secrets/quick-create-node.md) |- [API 参考](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/)<br>- [npm 包](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API 参考](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/)<br>- [npm 包](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure Key Vault 开发人员指南](developers-guide.md)
- 了解有关[向密钥保管库进行身份验证](authentication.md)的详细信息
