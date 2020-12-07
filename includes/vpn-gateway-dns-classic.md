---
title: include 文件
description: include 文件
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 10/08/2020
ms.date: 11/23/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 84657a142399df15d5064029a2897545d848d79c
ms.sourcegitcommit: 054636c134cc0f53c194a6b76668644e18d1c4fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95970749"
---
在此配置过程中不需进行 DNS 设置，但如果希望在 VM 之间进行名称解析，则 DNS 是必需的。 指定一个值不会创建新的 DNS 服务器。 指定的 DNS 服务器 IP 地址应该是可以解析所连接的资源名称的 DNS 服务器。

创建虚拟网络后，可以添加 DNS 服务器的 IP 地址来处理名称解析。 打开虚拟网络的设置，选择“DNS 服务器”，并添加要用于名称解析的 DNS 服务器的 IP 地址。

1. 在门户中找到虚拟网络。
2. 在虚拟网络页的“设置”部分，选择“DNS 服务器”。 
3. 添加 DNS 服务器。
4. 若要保存设置，请选择页面顶部的“保存”。