---
title: Azure Active Directory 证书颁发机构
description: Azure 中使用的受信任证书的列表
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: v-junlch
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d5d5ac5fba1f1ce975107143b1164a62f226e3c
ms.sourcegitcommit: 59810f8eba5e430d85a595e346d3b7fb6e4a0102
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94501978"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Azure Active Directory 使用的证书颁发机构

> [!IMPORTANT]
> 此页中的信息仅与用于显式指定可接受证书颁发机构 (CA) 列表的实体相关。 除非没有其他选择，否则应该避免这种被称为证书固定的做法。

尝试通过 TLS/SSL 协议访问 Azure Active Directory (Azure AD) 标识服务的任何实体都将获得下列 CA 提供的证书。 如果该实体信任那些 CA，它可以使用这些证书来验证标识和标识服务的合法性并建立安全连接。

证书颁发机构可以划分为根 CA 和中间 CA。 通常，根 CA 具有一个或多个关联的中间 CA。 本文列出了 Azure AD 标识服务使用的根 CA 以及其中每一个根 CA 所关联的中间 CA。 对于每个 CA，我们都会提供统一资源标识符 (URI)，以用于下载关联的颁发机构信息访问 (AIA) 文件和证书吊销列表分发点 (CDP) 文件。 在适当的时候，我们还会提供联机证书状态协议 (OCSP) 终结点的 URI。

## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Azure 中国世纪互联云中使用的 CA

### <a name="digicert-global-root-ca"></a>DigiCert 全局根 CA


| 根 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - | - | - | - | - |
| DigiCert 全局根 CA| 083be056904246b 1a1756ac95991c74a| 2006 年 11 月 9 日<br>2031 年 11 月 9 日| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>关联的中间 CA

| 颁发 CA 和中间 CA| 序列号| 颁发日期 到期日期| SHA1 指纹| URI |
| - | - | - | - | - | - |
| DigiCert 基本 RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 2020 年 3 月 4 日<br>2030 年 3 月 4 日| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>后续步骤
[了解 Microsoft 365 加密链](https://docs.microsoft.com/microsoft-365/compliance/encryption-office-365-certificate-chains?view=o365-worldwide)

