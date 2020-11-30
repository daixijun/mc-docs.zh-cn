---
title: 访问防火墙后的密钥保管库 - Azure 密钥保管库 | Microsoft Docs
description: 了解要打开哪些端口、主机或 IP 地址，才能允许防火墙后面的密钥保管库客户端应用程序访问密钥保管库。
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
origin.date: 08/12/2019
ms.date: 11/27/2020
ms.author: v-tawe
ms.openlocfilehash: e7360c89802e0968860db873bee7b214aa7e1dfb
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300390"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>访问防火墙后面的 Azure Key Vault

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>我应该打开哪些端口、主机或 IP 地址，才能允许防火墙后面的密钥保管库客户端应用程序访问密钥保管库？

若要访问密钥保管库，密钥保管库客户端应用程序必须访问多个终结点才能使用各种功能：

* 通过 Azure Active Directory (Azure AD) 进行身份验证。
* 管理 Azure Key Vault。 这包括通过 Azure Resource Manager 创建、读取、更新、删除和设置访问策略。
* 通过密钥保管库特定的终结点（例如 `https://yourvaultname.vault.azure.cn`），访问和管理密钥保管库本身存储的对象（密钥和机密）。  

根据配置和环境，会有一些变化。

## <a name="ports"></a>端口

针对所有 3 项功能（身份验证、管理和数据平面访问）的所有密钥保管库流量都会通过 HTTPS（端口 443）传递。 但是，对于 CRL，偶尔会有 HTTP（端口 80）流量。 支持 OCSP 的客户端不应到达 CRL，但有时可能会到达 [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl)。  

## <a name="authentication"></a>身份验证

密钥保管库客户端应用程序需要访问 Azure Active Directory 终结点进行身份验证。 使用的终结点取决于 Azure AD 租户配置、主体类型（用户主体或服务主体）以及帐户类型（如 Microsoft 帐户或者工作或学校帐户）。  

<!-- no need to change -->
| 主体类型 | 终结点：端口 |
| --- | --- |
| 使用 Microsoft 帐户的用户<br> （例如：user@hotmail.com） |**Azure China：**<br> login.chinacloudapi.cn:443<br><br> and <br>login.live.com:443 |
| 使用 Azure AD 的工作或学校帐户的用户或服务主体（如 user@contoso.com） |**Azure China：**<br> login.chinacloudapi.cn:443<br><br> |

还有其他可能的复杂情况。 有关其他信息，请参阅 [Azure Active Directory 身份验证流](/active-directory/develop/authentication-vs-authorization)、[将应用程序与 Azure Active Directory 集成](/active-directory/develop/active-directory-how-to-integrate)和 [Active Directory 身份验证协议](/active-directory/develop/active-directory-developers-guide)。  

## <a name="key-vault-management"></a>Key Vault 管理

对于 Key Vault 管理（CRUD 和设置访问策略），Key Vault 客户端应用程序需要访问 Azure Resource Manager 终结点。  

<!-- no need to change -->
| 操作类型 | 终结点：端口 |
| --- | --- |
| 通过 Azure Resource Manager 进行的<br> Key Vault 控制平面操作 |**Azure China：**<br> management.chinacloudapi.cn:443<br><br> |
| Microsoft Graph API |**Azure China：**<br> graph.chinacloudapi.cn:443<br><br> |

## <a name="key-vault-operations"></a>Key Vault 操作

对于所有密钥保管库对象（密钥和密码）管理和加密操作，密钥保管库客户端需要访问密钥保管库终结点。 根据密钥保管库的位置，终结点 DNS 后缀会有所不同。 密钥保管库终结点的格式是 vault-name.region-specific-dns-suffix，如下表所示 。  

<!-- no need to change -->
| 操作类型 | 终结点：端口 |
| --- | --- |
| 操作包括对密钥的加密操作；创建、读取、更新和删除密钥和密码；设置或获取密钥保管库对象（密钥或密码）上的标记和其他属性 |**Azure China：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> |

## <a name="ip-address-ranges"></a>IP 地址范围

Key Vault 服务使用其他 Azure 资源，例如 PaaS 基础结构。 因此，不可能提供 Key Vault 服务终结点在任意特定时间会有的特定 IP 地址范围。 如果防火墙仅支持 IP 地址范围，请参阅“Microsoft Azure 数据中心 IP 范围”文档，网址为：
* [中国](https://www.microsoft.com/en-us/download/details.aspx?id=57062)

身份验证和标识 (Azure Active Directory) 是一项全球性服务，可能会故障转移到其他区域或移动流量，恕不另行通知。 在这种情况下，[身份验证和标识 IP 地址](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip)中列出的所有 IP 范围都应添加到防火墙中。

## <a name="next-steps"></a>后续步骤

如果对密钥保管库有任何疑问，请访问[面向 Azure 密钥保管库的 Microsoft Q&A 问题页面](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。
