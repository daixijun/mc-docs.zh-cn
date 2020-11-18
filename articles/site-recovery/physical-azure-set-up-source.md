---
title: 使用 Azure Site Recovery 设置配置服务器，以便将物理服务器灾难恢复到 Azure | Azure
description: 本文介绍如何设置本地配置服务器，以便将本地物理服务器灾难恢复到 Azure。
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
origin.date: 07/03/2019
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: f65e15dbee494a0e476cecb027ccf8f81f7c00e5
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94328867"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>设置配置服务器，以便将物理服务器灾难恢复到 Azure

本文介绍如何设置本地环境，以便开始将运行 Windows 或 Linux 的物理服务器复制到 Azure。

## <a name="prerequisites"></a>必备条件

本文假设已具有：
- [Azure 门户](https://portal.azure.cn "Azure 门户")中的恢复服务保管库。
- 安装配置服务器的物理计算机。
- 如果已在要安装配置服务器的计算机上禁用了 TLS 1.0，请确保该计算机上已启用 TLS 1.2 并已安装 .NET Framework 4.6 或更高版本（已启用强加密）。 [了解详细信息](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1)。

### <a name="configuration-server-minimum-requirements"></a>配置服务器的最低要求
下表列出了配置服务器的最低硬件、软件和网络要求。
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> 配置服务器不支持基于 HTTPS 的代理服务器。

## <a name="choose-your-protection-goals"></a>选择保护目标

1. 在 Azure 门户中，转到“恢复服务保管库”  边栏选项卡，然后选择保管库。
2. 在保管库的“资源”  菜单中，单击“开始使用”   > “Site Recovery”   > “步骤 1: 准备基础结构”   > “保护目标”  。

    :::image type="content" source="./media/physical-azure-set-up-source/choose-goals.png" alt-text="屏幕截图，显示在何处选择保护目标。":::
3. 在“保护目标”  中，依次选择“到 Azure”  和“未虚拟化/其他”  ，并单击“确定”  。

    :::image type="content" source="./media/physical-azure-set-up-source/physical-protection-goal.png" alt-text="选择目标":::

## <a name="set-up-the-source-environment"></a>设置源环境

1. 如果没有配置服务器，请在“准备源”中单击“+配置服务器”添加一个。  

    :::image type="content" source="./media/physical-azure-set-up-source/plus-config-srv.png" alt-text="屏幕截图，显示如何选择配置服务器。":::
2. 在“添加服务器”边栏选项卡中，检查“配置服务器”是否已出现在“服务器类型”中。   
4. 下载站点恢复统一安装程序安装文件。
5. 下载保管库注册密钥。 运行统一安装程序时，需要注册密钥。 生成的密钥有效期为 5 天。

    :::image type="content" source="./media/physical-azure-set-up-source/set-source2.png" alt-text="设置源":::
6. 在用作配置服务器的计算机上，运行 **Azure Site Recovery 统一安装程序** 安装配置服务器、进程服务器和主目标服务器。

#### <a name="run-azure-site-recovery-unified-setup"></a>运行 Azure Site Recovery 统一安装程序

> [!TIP]
> 如果计算机上的系统时钟时间与本地时间相差 5 分钟以上，则配置服务器注册会失败。 开始安装前，请将系统时钟与[时间服务器](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-time-service-top)同步。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 可通过命令行安装配置服务器。 [了解详细信息](physical-manage-configuration-server.md#install-from-the-command-line)。

## <a name="common-issues"></a>常见问题

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>后续步骤

下一步涉及在 Azure 中[设置目标环境](physical-azure-set-up-target.md)。

<!-- Update_Description: update meta properties, wording update, update link -->