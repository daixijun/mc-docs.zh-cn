---
title: 更改隐私设置
description: 本主题提供有关如何在 Azure Stack HCI 操作系统或 Windows Server 中更改隐私设置的指南。
author: WenJason
ms.author: v-jay
ms.topic: how-to
origin.date: 10/05/2020
ms.date: 11/09/2020
ms.openlocfilehash: be4414783a5c7d93bf5727f2fed15a7f0f2a57dc
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330711"
---
# <a name="change-privacy-settings-on-individual-servers"></a>更改各个服务器上的隐私设置

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019、Windows Server 2016

这里介绍如何在运行 Azure Stack HCI 操作系统或 Windows Server 的服务器中更改隐私设置的指南。 若要同时在多个服务器上或在整个企业中使用组策略管理隐私设置，请参阅[管理企业诊断数据](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)。

## <a name="azure-stack-hci-or-server-core"></a>Azure Stack HCI 或 Server Core
如果服务器使用 Azure Stack HCI 操作系统或带有 Server Core 安装选项的 Windows Server，请使用以下步骤：
1. 通过使用带有 KVM 的远程桌面（一种远程管理 [无外设或 BMC] 控制器）或通过使用键盘和监视器在本地登录，连接到 Azure Stack HCI 群集中的服务器。 
1. 如果连接到运行 Azure Stack HCI 的服务器，则服务器配置工具 (Sconfig) 将自动打开。 如果连接到运行带有 Server Core 的 Windows Server 的服务器，则在命令提示符处输入 `Sconfig`。
1. 在“输入数字以选择一个选项:”提示处，键入 10 并按 Enter 。
1. 在“更改遥测”确认提示处，选择“是”以显示以下选项 ：

    可用的遥测设置：1 安全、2 基本、3 增强、4 全面   

    >[!NOTE]
    > Azure Stack HCI 的默认设置为“1 安全”。

1. 在“输入新的遥测设置:”提示处，键入所需选项并按 Enter。

## <a name="full-desktop"></a>Full Desktop
如果服务器正在运行 Windows Server 并具有 Full Desktop 安装选项，请使用以下步骤：
1. 连接到 Windows Server 的服务器管理器仪表板。

    可以通过使用键盘和监视器在本地进行连接，也可以使用远程管理（无外设或 BMC）控制器或远程桌面在本地进行连接。 

1. 在“仪表板”下的“服务器管理”中，选择“本地服务器” 。
1. 在服务器的“属性”页上，选择“反馈和诊断”旁边的“设置”  。

    在“设置”页上，将显示“反馈频率”和“诊断和使用情况数据”设置  。 
 
1. 展开“诊断和使用情况数据”设置，选择以下选项之一：
    - **必需的诊断数据**
    - **增强版**
    - **可选的诊断数据**

    >[!NOTE]
    > 在“设置”页上，如果显示通知“某些设置由你的组织管理”，则“诊断和使用情况数据”设置可能不可用  。

## <a name="next-steps"></a>后续步骤
若要使用“组策略”同时在多个服务器上管理隐私设置或在整个企业中将其作为一个整体来进行管理，请参阅：
-   [管理企业诊断数据](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)
