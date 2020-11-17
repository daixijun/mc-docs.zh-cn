---
title: include 文件
description: include 文件
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2020
ms.author: v-johya
ms.custom: include
ms.openlocfilehash: 4b178cbfd6bb08098722828b72897960014b56f9
ms.sourcegitcommit: 537d52cb783892b14eb9b33cf29874ffedebbfe3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "93106456"
---
## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

数据是使用符合 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 的 [256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 加密法加密和解密的。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 你还可以选择使用自己的密钥（称为“客户管理的密钥 (CMK)”）来管理订阅。 使用 CMK 可以更灵活地创建、轮换、禁用和撤销访问控制。 还可以审核用于保护数据的加密密钥。 如果你为订阅配置了 CMK，我们还提供双重加密，它提供额外的一层保护，同时允许你通过 Azure Key Vault 控制加密密钥。

