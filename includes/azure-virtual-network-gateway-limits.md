---
title: include 文件
description: include 文件
services: networking
ms.service: VPN Gateway
ms.topic: include
origin.date: 08/25/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: no
ms.testdate: 12/14/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 878213bf3f39e317ed12dbcb41ba3457eda872af
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004227"
---
<!--Verified successfully on valid feature-->

| 资源                                | 限制        |
|-----------------------------------------|------------------------------|
| VNet 地址前缀                   | 600/VPN 网关          |
| 聚合 BGP 路由                    | 4,000/VPN 网关        |
| 本地网关地址前缀  | 1000/本地网关               |
| S2S 连接                         | [取决于网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| P2S 连接                         | [取决于网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| P2S 路由限制 - IKEv2                 | 256（非 Windows） **/** 25 (Windows)           |
| P2S 路由限制 - OpenVPN               | 1000                         |
| 最大 flows                              | 100K (VpnGw1/AZ) **/** 512K (VpnGw2-4/AZ)|

<!-- Update_Description: new article about azure virtual network gateway limits -->
<!--NEW.date: 12/14/2020-->