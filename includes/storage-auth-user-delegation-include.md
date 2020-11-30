---
title: include 文件
description: include 文件
services: storage
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 01/15/2020
ms.date: 11/16/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 7575614c5d23bdd4c8fabd4a1439016217181c90
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96153027"
---
## <a name="about-the-user-delegation-sas"></a>关于用户委托 SAS

可使用 Azure AD 凭据或帐户密钥来保护用于访问容器或 Blob 的 SAS 令牌。 使用 Azure AD 凭据保护的 SAS 称为用户委托 SAS，因为用于签署 SAS 的 OAuth 2.0 令牌是代表用户请求的。

作为安全最佳做法，Azure 建议尽可能地使用 Azure AD 凭据，而不要使用更容易透露的帐户密钥。 当应用程序设计需要共享访问签名时，请使用 Azure AD 凭据创建可提供超高安全性的用户委托 SAS。 有关用户委托 SAS 的详细信息，请参阅[创建用户委托 SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)。

> [!CAUTION]
> 拥有有效 SAS 的任何客户端都可以访问该 SAS 允许的存储帐户中的数据。 防止 SAS 被恶意使用或意料之外的使用很重要。 请谨慎分发 SAS，并制定撤销受到安全威胁的 SAS 的计划。

有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../articles/storage/common/storage-sas-overview.md)。
