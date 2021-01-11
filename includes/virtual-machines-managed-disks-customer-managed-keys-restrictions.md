---
title: 包含文件
description: 包含文件
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: v-yeche
ms.custom: include file
origin.date: 07/10/2020
ms.openlocfilehash: 8b4fe1bbd22b65e0d7aa66c7ad14a9f2628f2313
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857042"
---
<!--Verify Successfully-->
- 仅支持大小为 2048 位、3,072 位和 4,096 位的[软件密钥](../articles/key-vault/keys/about-keys.md)，不支持其他密钥或其他大小。

    <!--Not Available on and HSM RSA keys-->
    <!--Not Available on - [HSM](../articles/key-vault/keys/hsm-protected-keys.md)-->
    
- 从使用服务器端加密和客户托管密钥加密的自定义映像创建的磁盘必须使用相同的客户托管密钥进行加密，且必须位于同一订阅中。
- 从使用服务器端加密和客户托管密钥加密的磁盘创建的快照必须使用相同的客户托管密钥进行加密。
- 与客户托管密钥相关的所有资源（Azure Key Vault、磁盘加密集、VM、磁盘和快照）都必须位于同一订阅和区域中。
- 使用客户托管密钥加密的磁盘、快照和映像不能移至其他资源组和订阅。
- 当前或以前使用 Azure 磁盘加密加密的托管磁盘不能使用客户管理的密钥进行加密。
- 每个区域、每个订阅最多只能创建 50 个磁盘加密集。

<!--Not Available on [Preview: Use customer-managed keys for encrypting images](../articles/virtual-machines/image-version-encryption.md)-->
<!-- Update_Description: new article about virtual machines managed disks customer managed keys restrictions -->
<!--NEW.date: 08/10/2020-->

