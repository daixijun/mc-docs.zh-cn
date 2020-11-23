---
title: 隔离网络上的 Azure 磁盘加密
description: 本文介绍适用于 Linux VM 上的 Azure 磁盘加密的故障排除提示。
author: Johnnytechn
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: v-johya
ms.date: 11/11/2020
ms.custom: seodec18
ms.openlocfilehash: 99e41e3bc232ff7a843d0b55cc2a9587565d45d0
ms.sourcegitcommit: d30cf549af09446944d98e4bd274f52219e90583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637775"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>隔离网络上的 Azure 磁盘加密

如果连接受到防火墙、代理要求或网络安全组 (NSG) 设置的限制，扩展执行所需任务的能力可能会受到干扰。 此干扰可能会导致出现类似于“VM 上未提供扩展状态”的状态消息。

## <a name="package-management"></a>包管理

Azure 磁盘加密取决于多个组件，这些组件通常在启用 ADE 的过程中安装（如果这些组件尚不存在）。 位于防火墙之后或在其他情况下与 Internet 隔离时，这些包必须预先安装或在本地提供。

下面是每次发行所需的包。 有关支持的发行版和卷类型的完整列表，请参阅[支持的 VM 和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)。

- **Ubuntu 14.04、16.04、18.04**：lsscsi、psmisc、at、cryptsetup-bin、python-parted、python-six、procps、grub-pc-bin
- **CentOS 7.2 - 7.7**：lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup、cryptsetup-reencrypt、pyparted、procps-ng、util-linux
- **CentOS 6.8**：lsscsi、psmisc、lvm2、uuid、at、cryptsetup-reencrypt、pyparted、python-six

    <!--Not Available on - **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux-->
    <!--Not Available on - **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencrypt-->
    
- **openSUSE 42.3、SLES 12-SP4、12-SP3**：lsscsi、cryptsetup

<!--Not Available on On Red Hat, when a proxy is required, you must make sure that the subscription-manager and yum are set up properly. For more information, see [How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533)-->

手动安装包时，还必须在发布新版本时手动升级包。

## <a name="network-security-groups"></a>网络安全组
应用的任何网络安全组设置仍必须允许终结点满足所述的与磁盘加密相关的网络配置先决条件。  请参阅 [Azure 磁盘加密：网络要求](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>使用 Azure AD 执行 Azure 磁盘加密（以前版本）

如果[对 Azure AD（以前的版本）使用 Azure 磁盘加密](disk-encryption-overview-aad.md)，则除了适用于此发行版的包（如[上面所列](#package-management)）外，还需为所有发行版手动安装 [Azure Active Directory 库](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)。

使用 [Azure AD 凭据](disk-encryption-linux-aad.md)启用加密时，目标 VM 必须允许连接到 Azure Active Directory 终结点和密钥保管库终结点。 当前 Azure Active Directory 身份验证终结点在 [Microsoft 365 URL 和 IP 地址范围](https://docs.microsoft.com/microsoft-365/enterprise/urls-and-ip-address-ranges)文档中的第 56 和 59 节中进行维护。 在有关如何[访问防火墙保护下的 Azure 密钥保管库](../../key-vault/general/access-behind-firewall.md)的文档中提供了密钥保管库说明。

### <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务 

虚拟机必须能够访问这样的 [Azure 实例元数据服务](instance-metadata-service.md)终结点：该终结点使用只能从 VM 内访问的已知不可路由 IP 地址 (`169.254.169.254`)。  不支持将本地 HTTP 流量更改为此地址的代理配置（例如，添加 X-Forwarded-For 标头）。

## <a name="next-steps"></a>后续步骤

- 请查看用于 [Azure 磁盘加密故障排除](disk-encryption-troubleshooting.md)的更多步骤
- [Azure 静态数据加密](../../security/fundamentals/encryption-atrest.md)

<!-- Update_Description: new article about disk encryption isolated network -->
<!--NEW.date: 03/09/2020-->