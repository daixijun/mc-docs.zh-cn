---
title: 针对 Microsoft 365 服务的管理角色文档 - Azure AD |Microsoft Docs
description: 在 Azure Active Directory 中查找针对 Microsoft 365 服务的管理员角色的内容和 API 参考
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 01/07/2021
ms.author: v-junlch
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed74ff828fc47a670a88d78394d075d8dd1973c1
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023037"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Azure Active Directory 中 Microsoft 365 服务的角色

在 Azure Active Directory (Azure AD) 中，可以使用管理角色管理 Microsoft 365 中的所有产品。 某些产品还提供特定于该产品的其他角色。 有关每个产品所支持角色的信息，请参阅下表。 有关委派问题的常规讨论，请参阅 [Azure Active Directory 中的角色委派计划](concept-delegation.md)。

## <a name="where-to-find-content"></a>查找内容位置

Microsoft 365 服务 | 角色内容 | API 内容
---------------------- | ------------------ | -----------------
Office 365 和 Microsoft 365 业务计划中的管理员角色 | [Microsoft 365 管理员角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide&preserve-view=true) | 不可用
Azure Active Directory (Azure AD) | [Azure AD 管理员角色](permissions-reference.md) | [图形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0&preserve-view=true&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Exchange Online| [Exchange 基于角色的访问控制](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
SharePoint Online | [Azure AD 管理员角色](permissions-reference.md)<br>还有[关于 Microsoft 365 中的 SharePoint 管理员角色](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [图形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Teams/Skype for Business | [Azure AD 管理员角色](permissions-reference.md) | [图形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
安全与合规中心（Office 365 高级威胁防护、Exchange Online Protection、信息保护） | [Office 365 管理员角色](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
安全评分 | [Azure AD 管理员角色](permissions-reference.md) | [图形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
合规性管理器 | [合规性管理器角色](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 不可用
Azure 信息保护 | [Azure AD 管理员角色](permissions-reference.md) | [图形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Windows Defender 高级威胁防护 | [Windows Defender ATP 基于角色的访问控制](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 不可用
Privileged Identity Management | [Azure AD 管理员角色](permissions-reference.md) | [图形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Intune | [Intune 基于角色的访问控制](https://docs.microsoft.com/intune/role-based-access-control) | [图形 API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
托管桌面 | [Azure AD 管理员角色](permissions-reference.md) | [图形 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[提取角色分配](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)

## <a name="next-steps"></a>后续步骤

* [如何分配或删除 Azure AD 管理员角色](manage-roles-portal.md)
* [Azure AD 管理员角色参考](permissions-reference.md)

