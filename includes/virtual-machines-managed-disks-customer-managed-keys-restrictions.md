---
title: 包含文件
description: 包含文件
services: virtual-machines
author: Johnnytechn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/16/2020
ms.author: v-yeche
ms.custom: include file
origin.date: 07/10/2020
ms.openlocfilehash: a1d8119d430b0536054a2021bccfc4e7e258eda8
ms.sourcegitcommit: 39288459139a40195d1b4161dfb0bb96f5b71e8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590847"
---
<!--Verify Successfully-->
- 仅支持大小为 2048 位、3,072 位和 4,096 位的[软件密钥](../articles/key-vault/keys/about-keys.md)，不支持其他密钥或其他大小。

    <!--Not Available on and HSM RSA keys-->
    <!--Not Available on - [HSM](../articles/key-vault/keys/hsm-protected-keys.md)-->
    
- 从使用服务器端加密和客户托管密钥加密的自定义映像创建的磁盘必须使用相同的客户托管密钥进行加密，且必须位于同一订阅中。
- 从使用服务器端加密和客户托管密钥加密的磁盘创建的快照必须使用相同的客户托管密钥进行加密。
- 与客户托管密钥相关的所有资源（Azure Key Vault、磁盘加密集、VM、磁盘和快照）都必须位于同一订阅和区域中。
- 使用客户托管密钥加密的磁盘、快照和映像不能移至另一个订阅。
- 当前或以前使用 Azure 磁盘加密加密的托管磁盘不能使用客户管理的密钥进行加密。
- 每个区域、每个订阅最多只能创建 50 个磁盘加密集。

<!--Not Available on [Preview: Use customer-managed keys for encrypting images](../image-version-encryption.md)-->
<!-- Update_Description: new article about virtual machines managed disks customer managed keys restrictions -->
<!--NEW.date: 08/10/2020-->

