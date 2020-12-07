---
title: 关于密钥 - Azure Key Vault
description: 概述了 Azure Key Vault REST 接口，并面向开发人员提供了有关密钥的详细信息。
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
origin.date: 09/15/2020
ms.date: 11/27/2020
ms.author: v-tawe
ms.openlocfilehash: e32def09d4f50882b4c556e270f32bd35e9dcacd
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300474"
---
# <a name="about-keys"></a>关于密钥

Azure Key Vault 提供了两种类型的资源来存储和管理加密密钥：

|资源类型|密钥保护方法|数据平面终结点基 URL|
|--|--|--|
| **保管库** | 受软件保护<br/><br/></li></ul> | https://{vault-name}.vault.azure.cn |
||||

- **保管库** - 保管库提供低成本、易部署、多租户、区域复原（若可用）且高度可用的密钥管理解决方案，适用于最常见的云应用程序方案。

> [!NOTE]
> 除了加密密钥外，保管库还能让你存储和管理多种类型的对象（如机密、证书和存储帐户密钥）。

Key Vault 中的加密密钥表示为 JSON Web 密钥 [JWK] 对象。 JavaScript 对象表示法 (JSON) 和 JavaScript 对象签名和加密 (JOSE) 规范如下：

-   [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web 加密 (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web 算法 (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web 签名 (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

此外，还扩展了基本 JWK/JWA 规范，以启用对于 Azure Key Vault 实现唯一的密钥类型。

## <a name="key-types-and-protection-methods"></a>密钥类型和保护方法

Key Vault 支持 RSA、EC 和对称密钥。 

### <a name="software-protected-keys"></a>受软件保护的密钥

|密钥类型|保管库|托管 HSM 池|
|--|--|--|--|
**RSA**：“受软件保护的”RSA 密钥|FIPS 140-2 级别 1|不支持
**EC**：“受软件保护的”椭圆曲线密钥|FIPS 140-2 级别 1|不支持
||||

有关每种密钥类型、算法、操作、属性和标记的详细信息，请参阅[密钥类型、算法和操作](about-keys-details.md)。

## <a name="next-steps"></a>后续步骤
- [关于 Key Vault](../general/overview.md)
- [关于机密](../secrets/about-secrets.md)
- [关于证书](../certificates/about-certificates.md)
- [Key Vault REST API 概述](../general/about-keys-secrets-certificates.md)
- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
