---
title: include 文件
description: include 文件
services: virtual-wan
author: rockboyfor
ms.service: virtual-wan
ms.topic: include
origin.date: 07/09/2020
ms.date: 11/23/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: a4e4236ac670c0ede9f247bc0ca7d9c0138cba9a
ms.sourcegitcommit: 054636c134cc0f53c194a6b76668644e18d1c4fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025840"
---
<!--Verified successfully-->
从浏览器导航到 Azure 门户并使用 Azure 帐户登录。

1. 导航到“虚拟 WAN”页。 在门户中，单击“+创建资源”。 在搜索框中键入“虚拟 WAN”，然后选择“Enter” 。
1. 从结果中选择“虚拟 WAN”。 在“虚拟 WAN”页上，选择“创建”以打开“创建 WAN”页。
1. 在“创建 WAN”页的“基本信息”选项卡上，填写以下字段 ：

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="屏幕截图显示已选择“基本”选项卡的“创建 WAN”窗格。":::

    * **订阅** - 选择要使用的订阅。
    * **资源组** - 新建资源组或使用现有的资源组。
    * **资源组位置** - 从下拉列表中选择资源位置。 WAN 是一个全局资源，不会驻留在某个特定区域。 但是，必须选择一个区域才能管理和查找所创建的 WAN 资源。
    * **名称** - 键入要用于称呼 WAN 的名称。
    * **类型** - 免费、基本或标准。 选择“标准”。 如果选择基本 VWAN，请了解基本 VWAN 只能包含基本中心，这会将连接类型限制为站点到站点。
1. 填写完字段后，单击“审阅 + 创建”。
1. 验证通过后，选择“创建”以创建虚拟 WAN。

<!-- Update_Description: update meta properties, wording update, update link -->