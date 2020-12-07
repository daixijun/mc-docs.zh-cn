---
title: 修改网关 IP 地址设置：PowerShell
description: 本文逐步介绍了如何使用 PowerShell 更改本地网关的 IP 地址前缀
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: how-to
origin.date: 06/19/2017
ms.date: 12/07/2020
ms.author: v-jay
ms.openlocfilehash: f5abc7b430c832317078b7be5a01f60dda243c9a
ms.sourcegitcommit: ac1cb9a6531f2c843002914023757ab3f306dc3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747214"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>使用 PowerShell 修改本地网络网关设置

有时本地网络网关 AddressPrefix 或 GatewayIPAddress 的设置会变更。 本文演示如何修改本地网络网关设置。 还可以选择以下列表中的其他选项，使用另一种方法来修改这些设置：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>准备工作

安装最新版本的 Azure 资源管理器 PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/) 。

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>修改 IP 地址前缀

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>修改网关 IP 地址

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>后续步骤

可验证网关连接。 请参阅[验证网关连接](vpn-gateway-verify-connection-resource-manager.md)。
