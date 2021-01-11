---
title: include 文件
description: include 文件
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/17/2020
ms.date: 01/04/2021
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 3dade3822378fe5831e8cd2bddfa99662518b722
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97856919"
---
|  | **点到站点** | **站点到站点** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure 支持的服务** |云服务和虚拟机 |云服务和虚拟机 |[服务列表](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **典型带宽** |基于网关 SKU |通常 < 1 Gbps（总计） |50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、2 Gbps、5 Gbps、10 Gbps |
| **支持的协议** |安全套接字隧道协议 (SSTP)、OpenVPN 和 IPsec |IPsec |通过 VLAN、NSP 的 VPN 技术（MPLS、VPLS...）直接连接 |
| **路由** |基于路由（动态） |支持基于策略（静态路由）和基于路由（动态路由 VPN） |BGP |
| **连接复原能力** |主动-被动 |主动-被动或主动-主动 |主动-主动 |
| **典型用例** |远程用户对 Azure 虚拟网络的安全访问 |云服务和虚拟机的开发/测试/实验室方案和中小型生产工作负载 |访问所有 Azure 服务（已验证列表）、企业级和任务关键型工作负荷、备份、大数据、Azure 即 DR 站点 |
| **SLA** |[SLA](https://www.azure.cn/support/legal/sla/) |[SLA](https://www.azure.cn/support/legal/sla/) |[SLA](https://www.azure.cn/support/legal/sla/) |
| **定价** |[定价](https://www.azure.cn/pricing/details/vpn-gateway/) |[定价](https://www.azure.cn/pricing/details/vpn-gateway/) |[定价](https://www.azure.cn/pricing/details/expressroute/) |
| **技术文档** |[VPN 网关文档](https://www.azure.cn/home/features/vpn-gateway/) |[VPN 网关文档](https://www.azure.cn/home/features/vpn-gateway/) |[ExpressRoute 文档](https://www.azure.cn/home/features/expressroute/) |
| **常见问题** |[VPN 网关常见问题](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[VPN 网关常见问题](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[ExpressRoute 常见问题](../articles/expressroute/expressroute-faqs.md) |
