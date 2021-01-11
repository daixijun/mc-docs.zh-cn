---
title: Windows 停止错误 - 0x0000007E 系统线程异常未处理
description: 本文提供解决以下问题的步骤：来宾操作系统遇到问题并希望重启 Azure VM。 消息将声明系统线程异常待处理。
services: virtual-machines-windows
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
origin.date: 11/04/2020
author: rockboyfor
ms.date: 01/04/2021
ms.testscope: yes
ms.testdate: 01/04/2021
ms.author: v-yeche
ms.openlocfilehash: 8d875a4a1296ea15bdf133c59166fc483b4041d0
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857607"
---
<!--Verified successfully-->
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows 停止错误 - 0x0000007E 系统线程异常未处理

本文提供解决以下问题的步骤：来宾操作系统（来宾 OS）遇到问题并尝试重启 Azure 虚拟机 (VM)。 错误消息显示：“未处理系统线程异常。”

## <a name="symptoms"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 输出的屏幕截图时，你将看到 Windows 需要重启，显示“系统线程异常未处理”停止代码或“0x0000007E”错误代码。

![屏幕截图显示 Windows 在启动过程中卡住，显示“电脑出现问题，需要重启。 我们将为你重启。” 错误消息和“系统线程异常未处理”停止代码。](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>原因

在分析内存转储文件之前，无法确定原因。 继续收集内存转储文件。

## <a name="solution"></a>解决方案

若要解决此问题，需要先收集故障的内存转储文件，然后将文件发送到 Azure 支持。 如需收集转储文件，请遵循后面两个部分中的说明。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将 OS 磁盘附加到新的修复 VM

1. 若要准备一个修复 VM，请按照 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步骤 1-3 进行操作。
1. 使用远程桌面连接来连接到修复 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

1. 在修复 VM 上，转到附加的 OS 磁盘中的 Windows 文件夹。 例如，如果分配给附加的 OS 磁盘的驱动器号为 F，请转到 `F:\Windows`。
1. 查找 memory.dmp 文件，然后[提交附加该内存转储文件的支持工单](https://support.azure.cn/support/support-azure/)。
1. 如果在查找 memory.dmp 文件时遇到问题，请按照指南[使用不可屏蔽的中断 (NMI) 调用来生成故障转储文件](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)。

<!--Not Available on [NMI calls in Azure Serial Console](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)-->

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [对 Azure 虚拟机启动错误进行故障排除](./boot-error-troubleshoot.md)

<!-- Update_Description: new article about windows stop error system thread exception not handled -->
<!--NEW.date: 01/04/2021-->