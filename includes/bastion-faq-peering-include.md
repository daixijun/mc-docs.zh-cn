---
title: include 文件
description: include 文件
services: bastion
ms.service: bastion
ms.topic: include
origin.date: 11/05/2020
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: yes|no
ms.testdate: 11/23/2020null
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 08832feb47302a20607953b9708d12a56ba1bb3b
ms.sourcegitcommit: b072689d006cbf9795612acf68e2c4fee0eccfbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849547"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>是否仍可将多个 Bastion 主机部署到对等互连虚拟网络中？

是的。 默认情况下，用户会看到在 VM 所在的虚拟网络中部署的 Bastion 主机。 但是，在“连接”菜单中，用户可以看到在对等互连网络上检测到的多个 Bastion 主机。 他们可以选择首选用于连接到虚拟网络中部署的 VM 的 Bastion 主机。

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>如果将对等互连的 VNet 部署在不同的订阅中，通过 Bastion 进行的连接是否可以正常工作？

可以，对于单个租户的跨不同订阅的对等互连 VNet，通过 Bastion 进行的连接会继续正常工作。 不支持跨两个不同租户的订阅。 若要在“连接”下拉菜单中查看 Bastion，用户必须在“订阅”>“全局订阅”中选择他们有权访问的订阅。

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="全局订阅筛选器" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>我有权访问对等互连 VNet，但看不到在其中部署的 VM。

请确保用户对 VM 和对等互连 VNet 都具有“读取”访问权限。 此外，请在 IAM 下检查用户是否对以下资源具有“读取”访问权限：

* 虚拟机上的读者角色。
* NIC 上的读者角色（使用虚拟机的专用 IP）。
* Azure Bastion 资源上的读者角色。
* 虚拟网络上的读者角色（如果没有对等互连虚拟网络，则不需要）。

|权限|描述|权限类型|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |获取守护主机|操作|
|Microsoft.Network/virtualNetworks/BastionHosts/action |获取虚拟网络中的守护主机引用。|操作|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|获取虚拟网络中的守护主机引用。|操作|
|Microsoft.Network/networkInterfaces/read|获取网络接口定义。|操作|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|获取网络接口 IP 配置定义。|操作|
|Microsoft.Network/virtualNetworks/read|获取虚拟网络定义|操作|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|获取对虚拟网络子网中的所有虚拟机的引用|操作|
|Microsoft.Network/virtualNetworks/virtualMachines/read|获取对虚拟网络中的所有虚拟机的引用|操作|

<!-- Update_Description: new article about bastion faq peering include -->
<!--NEW.date: 11/23/2020-->