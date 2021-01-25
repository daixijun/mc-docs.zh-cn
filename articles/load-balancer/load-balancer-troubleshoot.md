---
title: 排查 Azure 负载均衡器常见问题
description: 了解如何排查 Azure 负载均衡器的常见问题。
services: load-balancer
documentationcenter: na
author: WenJason
manager: digimobile
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 01/28/2020
ms.date: 01/18/2021
ms.author: v-jay
ms.openlocfilehash: ef0e500bfa46fda845ab0c3ff7cd5f73001434c8
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230993"
---
# <a name="troubleshoot-azure-load-balancer"></a>排查 Azure 负载均衡器问题

此页介绍基本和标准 Azure 负载均衡器的常见问题的疑难解答信息。 有关标准负载均衡器的详细信息，请参阅[标准负载均衡器概述](load-balancer-standard-diagnostics.md)。

当负载均衡器连接不可用时，最常见的症状如下：

- 负载均衡器后端的 VM 不响应运行状况探测 
- 负载均衡器后端的 VM 不响应已配置端口上的流量

当后端 VM 的外部客户端通过负载均衡器时，将使用客户端的 IP 地址进行通信。 请确保将客户端的 IP 地址添加到 NSG 允许列表中。

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>没有来自标准内部负载均衡器 (ILB) 的出站连接

验证及解决方法

标准 ILB 在默认情况下是安全的。 基本 ILB 允许通过隐藏的公共 IP 地址连接到 Internet。 不建议将其用于生产工作负载，因为该 IP 地址既不是静态的，也不是通过你拥有的 NSG 进行锁定的。 如果你最近从基本 ILB 迁移到了标准 ILB，则应通过[仅出站](egress-only.md)配置显式创建公共 IP，该配置会通过 NSG 锁定 IP。 你还可以在子网上使用 [NAT 网关](../virtual-network/nat-overview.md)。

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>无法更改已在后端池中部署了虚拟机规模集的负载均衡器的现有 LB 规则的后端端口。

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>原因：无法针对虚拟机规模集引用的负载均衡器的运行状况探测所使用的负载均衡规则修改后端端口

解决方案：为了更改端口，可以通过更新虚拟机规模集来删除运行状况探测，更新端口，然后重新配置运行状况探测。

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>从负载均衡器的后端池中删除 VM 后，仍有少量流量通过负载均衡器。

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>原因：从后端池中删除的 VM 不应再接收流量。 少量的网络流量可能与 Azure 中的存储、DNS 和其他功能有关。

若要进行验证，可以执行网络跟踪。 用于 blob 存储帐户的 FQDN 会列在每个存储帐户的属性中。  在 Azure 订阅中的虚拟机上，可以执行 nslookup 以确定分配给该存储帐户的 Azure IP。

## <a name="additional-network-captures"></a>附加网络捕获

如果决定打开支持案例，请收集下列信息，以更快获得解决方案。 选择单个后端 VM 执行下列测试：

- 使用来自 VNet 的某个后端 VM 的 ps ping 进行探测端口响应测试（例如 ps ping 10.0.0.4:3389）并记录结果。 
- 如果这些 ping 测试未收到响应，请在运行 PsPing 时，在后端 VM 和 VNet 测试 VM 上同时运行 Netsh 跟踪，并停止 Netsh 跟踪。

<!--## Load Balancer in failed state-->
<!--Not Available on [Azure Resource Explorer](https://resources.azure.com/)-->

## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://www.azure.cn/support/contact/)。

