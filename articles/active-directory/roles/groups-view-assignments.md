---
title: 查看分配给 Azure Active Directory 中的组的角色 | Microsoft 文档
description: 了解如何使用 Azure AD 管理中心查看分配给组的角色。 查看组和分配的角色是默认的用户权限。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/04/2020
ms.author: v-junlch
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8fb34b08e6265b177e3d875253478c04c369bb3
ms.sourcegitcommit: 33f2835ec41ca391eb9940edfcbab52888cf8a01
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94326878"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>查看分配给 Azure Active Directory 中的组的角色

本部分介绍如何使用 Azure AD 管理中心查看分配给组的角色。 查看组和分配的角色是默认的用户权限。

1. 使用任何非管理员或管理员凭据登录到 [Azure AD 管理中心](https://portal.azure.cn/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。

1. 选择所需的组。

1. 选择“分配的角色”。 你现在可以查看分配给此组的所有 Azure AD 角色。

   ![查看分配给所选组的所有角色](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>使用 PowerShell

### <a name="get-object-id-of-the-group"></a>获取组的对象 ID

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>查看组的角色分配

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>获取组的对象 ID

```powershell
GET https://microsoftgraph.chinacloudapi.cn/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>获取组的角色分配

```powershell
GET https://microsoftgraph.chinacloudapi.cn/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](groups-concept.md)
- [分配给云组的角色疑难解答](groups-faq-troubleshooting.md)

