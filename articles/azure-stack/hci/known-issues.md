---
title: Azure Stack HCI 的已知问题
description: 本主题详细介绍 Azure Stack HCI 的已知问题。
author: WenJason
ms.author: v-jay
ms.topic: troubleshooting
origin.date: 10/27/2020
ms.date: 11/09/2020
ms.openlocfilehash: ae7b3a9a0326631948620d1c72d4ab1445f7e870
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330640"
---
# <a name="known-issues-for-azure-stack-hci"></a>Azure Stack HCI 的已知问题

>适用于：Azure Stack HCI，版本 20H2

本主题详细介绍 Azure Stack HCI 的已知问题

- 如果任何虚拟机 (VM) 预计在 CAU 期间执行实时迁移，则请注意，2020 年 10 月 20 日的预览版更新 (KB4580388) 可能会导致群集感知更新 (CAU) 操作失败。 为避免出现这种情况，请暂时更改默认行为，以便 CAU 使用快速迁移而不是实时迁移。 有关详细信息，请参阅[发行说明](release-notes.md#october-20-2020-preview-update-kb4580388)。
- 本地群集与 Azure Stack HCI 云服务之间的连接尚不支持 Azure 专用链接。
- Azure Stack HCI 云服务当前仅在选定区域可用。
- 当你以交互方式登录到操作系统时，你会发现，显示“欢迎使用 Azure Stack HCI”的欢迎屏幕尚未本地化为捷克语、匈牙利语、荷兰语、波兰语、瑞典语和土耳其语（区域设置代码为 cs-cz、hu-hu、nl-nl、pl-pl、pt-pt、sv-se、tr-tr）。 此外，在除英语以外的所有语言中，输入提示（如“[Y]es/[N]o”）只接受值“Y”和“N”，即使提示本身错误地显示为已本地化（例如法语的“[O]ui/[N]”或德语的“[J]a/[N]ein”）。
- 如果使用嵌套虚拟化评估 Azure Stack HCI，你可能会遇到类似于“由于未启用虚拟化支持而无法安装 Hyper-V”的错误，因为 Azure Stack HCI 依赖于基于虚拟化的安全性。 有两种可能的解决方法：(1) 改用 Hyper-V 第 1 代 VM；或 (2) 将 Hyper-V 功能脱机注入到 VM 的 VHDX。 从主机处，在当 Azure Stack HCI 节点关闭时充当其节点的 VM 上运行以下 PowerShell 命令：Install-WindowsFeature -Vhd \<path> -Name Hyper-V, RSAT-Hyper-V-Tools, Hyper-V-Powershell。
