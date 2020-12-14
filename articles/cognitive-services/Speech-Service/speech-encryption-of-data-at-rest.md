---
title: 语音识别服务静态数据的加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍适用于语音服务的静态数据加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 08/28/2020
ms.date: 12/10/2020
ms.author: v-tawe
ms.openlocfilehash: 137d5f0679de1194e786a2dbf1bb0139d2cd3da2
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004154"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>语音识别服务静态数据的加密

语音识别服务在将数据保存到云时会自动加密数据。 语音识别服务加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

数据将使用符合 FIPS 140-2 的 256 位 AES 加密法进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

使用自定义语音识别和自定义语音时，语音识别服务可能会将以下数据存储在云中：  

* 语音识别跟踪数据 - 仅当你为自定义终结点启用了跟踪时才存储
* 已上传的训练和测试数据

默认情况下，你的数据存储在 Microsoft 的存储中，你的订阅使用 Microsoft 托管的加密密钥。 你还可以选择准备你自己的存储帐户。 对应用商店的访问是由托管标识管理的，语音识别服务无法直接访问你自己的数据，例如语音识别跟踪数据、自定义训练数据和自定义模型。

有关托管标识的详细信息，请参阅[什么是托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

<!-- 
## Bring your own storage (BYOS) for customization and logging

To request access to bring your own storage, fill out and submit the [Speech service - bring your own storage (BYOS) request form](https://aka.ms/cogsvc-cmk). Once approved, you'll need to create your own storage account to store the data required for customization and logging. When adding a storage account, the Speech service resource will enable a system assigned managed identity. After the system assigned managed identity is enabled, this resource will be registered with Azure Active Directory (AAD). After being registered, the managed identity will be given access to the storage account. You can learn more about Managed Identities here. For more information about Managed Identity, see [What are managed identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> If you disable system assigned managed identities, access to the storage account will be removed. This will cause the parts of the Speech service that require access to the storage account to stop working.  

The Speech service doesn't currently support Customer Lockbox. However, customer data can be stored using BYOS, allowing you to achieve similar data controls to [Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md). Keep in mind that Speech service data stays and is processed in the region where the Speech resource was created. This applies to any data at rest and data in transit. When using customization features, like Custom Speech and Custom Voice, all customer data is transferred, stored, and processed in the same region where your BYOS (if used) and Speech service resource reside.

> [!IMPORTANT]
> Microsoft **does not** use customer data to improve its Speech models. Additionally, if endpoint logging is disabled and no customizations are used, then no customer data is stored. 
-->

## <a name="next-steps"></a>后续步骤

<!-- * [Speech service - bring your own storage (BYOS) request form](https://aka.ms/cogsvc-cmk) -->

* [什么是托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。