---
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: include
origin.date: 12/09/2019
ms.date: 11/09/2020
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 8f224748f3d6b9883564f695129650faeab1dec0
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330454"
---
如果已安装资源提供程序，则可能已完成以下先决条件，可跳过此部分。 否则，请在继续之前完成以下步骤： 

1. [向 Azure 注册 Azure Stack Hub 实例](../operator/azure-stack-registration.md)（如果尚未这样做）。 此步骤是必需的，因为需要连接到 Azure 并将项目从 Azure 下载到市场。

2. 如果不熟悉 Azure Stack Hub 管理员门户的“市场管理”功能，请参阅[从 Azure 下载市场项并将其发布到 Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md)。 此文逐步讲解如何将项目从 Azure 下载到 Azure Stack Hub 市场。 它涵盖了联网场景和离线场景。 如果 Azure Stack Hub 实例已断开连接或部分连接，则在准备安装时还需要完成其他先决条件操作。

3. 更新 Azure Active Directory (Azure AD) 主目录。 从内部版本 1910 开始，必须在主目录租户中注册新应用程序。 此应用将允许 Azure Stack Hub 使用 Azure AD 租户成功创建和注册更新的资源提供程序（如事件中心、IoT 中心等）。 这是在升级到内部版本 1910 或更高版本之后需要执行的一次性操作。 如果此步骤未完成，则商城资源提供程序安装会失败。 

   - 将 Azure Stack Hub 实例成功更新到 1910 或更高版本后，请按照[克隆/下载 Azure Stack Hub 工具的说明](../operator/azure-stack-powershell-download.md)一文进行操作。 
   - 然后，请按照[更新 Azure Stack Hub Azure AD 主目录（在安装更新或新的资源提供程序之后）的说明](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers)一文进行操作。 