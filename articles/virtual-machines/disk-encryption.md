---
title: Azure 托管磁盘的服务器端加密
description: Azure 存储在将数据保存到存储群集之前会对其进行静态加密，以此保护数据。 可使用客户管理的密钥通过自己的密钥来管理加密，也可依赖于 Azure 托管的密钥来加密托管磁盘。
origin.date: 10/22/2020
author: rockboyfor
ms.date: 11/16/2020
ms.testscope: no
ms.testdate: 11/16/2020
ms.topic: conceptual
ms.author: v-yeche
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 63699cf58cacc91fe578975cfdad1eb998be1998
ms.sourcegitcommit: 39288459139a40195d1b4161dfb0bb96f5b71e8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590960"
---
<!--Verified Succcessfully-->
<!--Verified successfully-->
<!--PG notified the Customer-managed keys GA on global-->
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure 磁盘存储的服务器端加密

服务器端加密 (SSE) 可保护数据，并帮助实现组织安全性和符合性承诺。 将存储在 Azure 托管磁盘（OS 和数据磁盘）上的数据保存到云时，SSE 在默认情况下会自动对这些数据进行静态加密。 

Azure 托管磁盘中的数据使用 256 位 AES 加密（可用的最强大分组加密之一）以透明方式加密，且符合 FIPS 140-2 规范。 有关加密模块基础 Azure 托管磁盘的详细信息，请参阅[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

<!--Not Avaialble on [AES encryption](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)-->

服务器端加密不会影响托管磁盘的性能，并且不会产生额外的费用。 

> [!NOTE]
> 临时磁盘不是托管磁盘，不会由 SSE 加密，除非在主机上启用了加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

可以依赖于平台托管的密钥来加密托管磁盘，也可以使用自己的密钥来管理加密。 如果选择使用自己的密钥管理加密，可以指定一个客户托管密钥，用于加密和解密托管磁盘中的所有数据。 

以下部分更详细地介绍了密钥管理的每个选项。

### <a name="platform-managed-keys"></a>平台托管的密钥

默认情况下，托管磁盘使用平台托管的加密密钥。 所有写入现有托管磁盘的托管磁盘、快照、映像和数据都会自动使用平台托管密钥进行静态加密。

### <a name="customer-managed-keys"></a>客户管理的密钥

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>限制

目前，客户托管密钥具有以下限制：

- 如果为磁盘启用了此功能，则无法禁用它。
    如果需要解决此问题，必须使用 [Azure PowerShell 模块](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)或 [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 将所有数据复制到完全不同的托管磁盘（未使用客户管理的密钥）。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>支持的区域

可在提供托管磁盘的所有区域中使用客户管理的密钥。

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识（Azure Active Directory (Azure AD) 的一项功能）。 配置客户托管密钥时，实际上会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个目录，则与托管磁盘关联的托管标识不会转移到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅[在 Azure AD 目录之间转移订阅](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

<a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>

<!--Not Available on ## Encryption at host - End-to-end encryption for your VM data-->

<!--Not Available on ## Double encryption at rest-->
## <a name="server-side-encryption-versus-azure-disk-encryption"></a>服务器端加密与 Azure 磁盘加密

[Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)利用 Linux 的 DM-Crypt 功能或 Windows 的 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 功能，在来宾 VM 中使用客户管理的密钥来加密托管磁盘。  使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。

<!--Not Available on [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)-->

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的托管标识（Azure Active Directory (Azure AD) 的一项功能）。 配置客户托管密钥时，实际上会自动将托管标识分配给你的资源。 如果随后将订阅、资源组或托管磁盘从一个 Azure AD 目录移动到另一个目录，则与托管磁盘关联的托管标识不会转移到新租户，因此，客户托管密钥可能不再有效。 有关详细信息，请参阅[在 Azure AD 目录之间转移订阅](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="next-steps"></a>后续步骤

<!--Not Avaialble on [PowerShell](windows/disks-enable-host-based-encryption-powershell.md)-->
<!--Not Available on [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)-->
<!--Not Available on [Azure portal](disks-enable-host-based-encryption-portal.md)-->
<!--Not Available on [PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md)-->
<!--Not Avaialble on [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md)-->
<!--Not Available on [Azure portal](disks-enable-double-encryption-at-rest-portal.md)-->
    
- 通过 [Azure PowerShell 模块](windows/disks-enable-customer-managed-keys-powershell.md)或 [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) 为托管磁盘启用客户管理的密钥。

    <!--Not Available on [Azure portal](disks-enable-customer-managed-keys-portal.md)-->
    
- [探索 Azure 资源管理器模板以使用客户管理密钥创建加密磁盘](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

<!-- Update_Description: update meta properties, wording update, update link -->
<!-- Update_Description: new article about disk encryption -->
<!--NEW.date: 11/16/2020-->