---
title: include 文件
description: include 文件
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 11/04/2019
ms.date: 11/23/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 3a3dfcaad700a3fb7029fff7a3fa31165a249e92
ms.sourcegitcommit: db15d6cc591211c0e531d636f45e9cbe24cfb15b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "95970635"
---
可以使用 `Resize-AzVirtualNetworkGateway` PowerShell cmdlet 升级或降级第 1 代或第 2 代 SKU（除基本 SKU 外，所有 VpnGw SKU 都可以调整大小）。 如果使用的是基本网关 SKU，[请改用这些说明](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)来调整网关大小。

以下 PowerShell 示例演示如何将网关 SKU 的大小调整为 VpnGw2。

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```
