---
title: 为 VPN 用户启用 MFA：Azure AD 身份验证
description: 为 VPN 用户启用多重身份验证
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: how-to
origin.date: 09/03/2020
ms.date: 12/07/2020
ms.author: v-jay
ms.openlocfilehash: f55d0c1536cbcdf4997a3254622c4486490cf0a2
ms.sourcegitcommit: ac1cb9a6531f2c843002914023757ab3f306dc3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747052"
---
# <a name="enable-azure-ad-multi-factor-authentication-mfa-for-vpn-users"></a>为 VPN 用户启用 Azure AD 多重身份验证 (MFA)

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>启用身份验证

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>配置登录设置

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>选项 1 -“按用户”访问

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>选项 2 - 条件访问

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 VPN 客户端以建立 P2S VPN 连接](openvpn-azure-ad-client.md)。
