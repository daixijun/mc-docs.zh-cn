---
title: Azure Stack Hub 中的虚拟网络对等互连
description: 了解如何使用虚拟网络对等互连来连接 Azure Stack Hub 中的虚拟网络。
author: WenJason
ms.author: v-jay
origin.date: 11/11/2020
ms.date: 12/07/2020
ms.topic: conceptual
ms.reviewer: sranthar
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: f4220f9437b83df936f4d2ad0e36888042fd3cce
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508244"
---
# <a name="virtual-network-peering"></a>虚拟网络对等互连

使用虚拟网络对等互连可以无缝连接 Azure Stack Hub 环境中的虚拟网络。 出于连接目的，两个虚拟网络会显示为一个。 虚拟机之间的流量使用底层 SDN 基础结构。 与同一网络中的虚拟机之间的流量一样，这些流量仅通过 Azure Stack Hub 专用网络路由。

Azure Stack Hub 不支持全球对等互连，因为“区域”的概念不适用。

使用虚拟网络对等互连的优点如下：

- 不同虚拟网络中资源之间的连接延迟低且带宽高。
- 一个虚拟网络中的资源可与另一个虚拟网络中的资源通信。
- 可以在跨不同订阅和 Azure Active Directory 租户的虚拟网络之间传输数据。
- 在创建对等互连之时或之后，虚拟网络中的资源不会出现停机的现象。

对等虚拟网络之间的网络流量是专用的。 虚拟网络之间的流量保留在基础结构层中。 在虚拟网络之间通信不需要公共 Internet、网关或加密。

## <a name="connectivity"></a>连接

对于对等互连的虚拟网络，任一虚拟网络中的虚拟机资源可直接连接到对等互连虚拟网络中的资源。

同一区域中对等互连虚拟网络上的虚拟机之间的网络延迟与单个虚拟网络中的延迟相同。 网络吞吐量取决于可供虚拟机使用的与其大小成比例的带宽。 对等互连的带宽没有任何其他限制。

对等互连虚拟网络中虚拟机之间的流量直接通过 SDN 层路由，而不通过网关或公共 Internet 路由。

可以在虚拟网络中应用网络安全组，以阻止访问其他虚拟网络或子网。 配置虚拟网络对等互连时，可以打开或关闭虚拟网络之间的网络安全组规则。 如果在对等互连的虚拟网络之间建立了完全连接，则可以应用网络安全组来阻止或拒绝特定的访问。 完全连接是默认选项。 若要详细了解网络安全组，请参阅[安全组](/virtual-network/security-overview)。

## <a name="service-chaining"></a>服务链

使用服务链，可以通过用户定义的路由将流量从一个虚拟网络定向到对等互连网络中的虚拟设备或网关。

若要启用服务链，请将指向对等互连虚拟网络中虚拟机的用户定义的路由配置为下一跃点 IP 地址。 

可以部署中心辐射型网络，其中，中心虚拟网络托管网络虚拟设备或 VPN 网关等基础结构组件。 然后，可将所有分支虚拟网络对等互连到中心虚拟网络。 流量流经中心虚拟网络中的网络虚拟设备或 VPN 网关。

通过虚拟网络对等互连，用户定义的路由中的下一个跃点可以成为对等虚拟网络中虚拟机的 IP 地址。 若要深入了解用户定义的路由，请参阅[用户定义的路由概述](/virtual-network/virtual-networks-udr-overview#user-defined)。 要了解如何创建中心辐射型网络拓扑，请参阅 [Azure 中的中心辐射型网络拓扑](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="gateways-and-on-premises-connectivity"></a>网关和本地连接

每个虚拟网络（包括对等互连的虚拟网络）都可以有自身的网关。 虚拟网络可以使用其网关连接到本地网络。 请查看[虚拟网络网关文档](/vpn-gateway/)。

还可以将对等互连的虚拟网络中的网关配置为本地网络的传输点。 在这种情况下，使用远程网关的虚拟网络不能有自身的网关。 一个虚拟网络只能有一个网关。 网关是对等互连虚拟网络中的本地网关或远程网关，如下图所示：

:::image type="content" source="media/virtual-network-peering/virtual-network-gateway.png" alt-text="VPN 网关拓扑":::

请注意，在对等互连中启用“UseRemoteGateways”选项之前，必须在 VPN 网关中创建“连接”对象 。

## <a name="virtual-network-peering-configuration"></a>虚拟网络对等互连配置

**允许虚拟网络访问：** 启用虚拟网络之间的通信可允许资源连接到任意虚拟网络，并以相同的带宽和延迟互相之间进行通信，就如同它们是连接到同一个虚拟网络一样。 这两个虚拟网络中的资源之间的所有通信都通过内部 SDN 层路由。  

不启用网络访问的一个原因可能是你已将一个虚拟网络与另一个虚拟网络对等互连，但有时想要禁用这两个虚拟网络之间的流量流动。 你会发现，启用/禁用比删除并重新创建对等互连更加方便。 当禁用此设置时，流量不会在已对等互连的虚拟网络间流动。

**允许转发的流量：** 选中此框将允许某个虚拟网络中通过网络虚拟设备转发的（不是从该虚拟网络发起的）流量通过对等互连流动到此虚拟网络。 例如，假设有名为 Spoke1、Spoke2 和 Hub 的三个虚拟网络。 每个辐射虚拟网络与中心虚拟网络之间存在一个对等互连，但各个辐射虚拟网络之间不存在对等互连。 一个网络虚拟设备部署在中心虚拟网络中，用户定义的路由应用于通过该网络虚拟设备在各个子网之间路由流量的每个辐射虚拟网络。 如果没有为每个辐射虚拟网络与中心虚拟网络之间的对等互连选中此复选框，则流量不会在各个辐射虚拟网络之间流动，因为中心i不在各个虚拟网络之间转发流量。 虽然启用此功能即可通过对等互连转发流量，但不会创建任何用户定义的路由或网络虚拟设备。 用户定义的路由和网络虚拟设备是单独创建的。 了解[用户定义的路由](/virtual-network/virtual-networks-udr-overview#user-defined)。 如果流量通过 VPN 网关在虚拟网络之间转发，则无需检查此设置。

**允许网关传输：** 如果有附加到此虚拟网络的虚拟网络网关并且想要允许来自已对等互连的虚拟网络的流量流经网关，请选中此框。 例如，此虚拟网络可以通过虚拟网关附加到本地网络。 选中此框将允许来自所对等互连的虚拟网络的流量通过附加到此虚拟网络的网关流到本地网络。 如果选中此框，则已对等互连的虚拟网络不能有已配置的网关。 设置从另一虚拟网络到此虚拟网络的对等互连时，必须选中对等互连的虚拟网络的“使用远程网关”框。 如果保留此框的未选中状态（默认），则来自已对等互连的虚拟网络的流量仍可流动到此虚拟网络，但无法流经附加到此虚拟网络的虚拟网关。

**使用远程网关：** 选中此框可允许来自此虚拟网络的流量流经附加到正与之对等互连的虚拟网络的虚拟网络网关。 例如，要与之对等互连的虚拟网络附加了一个 VPN 网关，因此可与本地网络通信。 选中此框即可让来自此虚拟网络的流量流经附加到已对等互连的虚拟网络的 VPN 网关。 如果选中此框，已对等互连的虚拟网络必须附加有虚拟网关，并且必须已选中“允许网关传输”框。 如果保留此框的未选中状态（默认），则来自已对等互连的虚拟网络的流量仍可流动到此虚拟网络，但无法流经附加到此虚拟网络的虚拟网关。

如果已在虚拟网络中配置了网关，则无法使用远程网关。

### <a name="permissions"></a>权限

请确保在不同订阅和 Azure AD 租户中使用 VNET 创建对等互连时，帐户已分配有“参与者”角色。 此外，在不同的 Azure AD 租户之间不存在用于对等互连的用户界面功能。 可使用 Azure CLI 和 PowerShell 来创建对等互连。

## <a name="virtual-network-peering-frequently-asked-questions-faq"></a>虚拟网络对等互连常见问题解答 (FAQ)

### <a name="what-is-virtual-network-peering"></a>什么是虚拟网络对等互连？

使用虚拟网络对等互连可连接虚拟网络。 使用虚拟网络之间的 VNet 对等互连连接，可通过 IPv4 地址在这些虚拟网络之间私下路由流量。 对等互连的 VNet 中的虚拟机可相互通信，如同它们处于同一网络中一样。 还可跨多个订阅创建 VNet 对等互连连接。

### <a name="does-azure-stack-hub-support-global-vnet-peering"></a>Azure Stack Hub 是否支持全球 VNET 对等互连？

Azure Stack Hub 不支持全球对等互连，因为“区域”的概念不适用。

### <a name="on-which-azure-stack-hub-update-will-virtual-network-peering-be-available"></a>虚拟网络对等互连在哪次 Azure Stack Hub 更新中提供？

从 2008 更新开始，Azure Stack Hub 中提供了虚拟网络对等互连。

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub-to-a-virtual-network-in-azure"></a>能否将 Azure Stack Hub 中的虚拟网络与 Azure 中的虚拟网络对等互连？

不能，目前不支持在 Azure 与 Azure Stack Hub 之间建立对等互连。

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub1-to-a-virtual-network-in-azure-stack-hub2"></a>能否将 Azure Stack Hub1 中的虚拟网络与 Azure Stack Hub2 中的虚拟网络对等互连？

不能，只能在一个 Azure Stack Hub 系统中的虚拟网络之间创建对等互连。 若要详细了解如何从不同标记连接两个虚拟网络，请参阅[在 Azure Stack Hub 中建立 VNET 到 VNET 的连接](azure-stack-network-howto-vnet-to-vnet-stacks.md)。

### <a name="can-i-enable-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>如果虚拟网络所属的订阅位于不同的 Azure Active Directory 租户中，能否启用对等互连？

可以。 订阅属于不同的 Azure Active Directory 租户时，可以建立 VNet 对等互连。 可以通过 PowerShell 或 CLI 来执行此操作。 尚不支持门户。

### <a name="can-i-peer-my-virtual-network-with-a-virtual-network-in-a-different-subscription"></a>能否将我的虚拟网络与另一订阅中的虚拟网络对等互连？

可以。 可以跨订阅进行虚拟网络对等互连。

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>对等互连连接是否存在带宽限制？

否。 虚拟网络对等互连不会施加任何带宽限制。 带宽仅受 VM 或计算资源的限制。

### <a name="my-virtual-network-peering-connection-is-in-an-initiated-state-why-cant-i-connect"></a>我的虚拟网络对等互连连接处于“已启动”状态，为什么我不能连接？

如果对等互连连接处于“已启动”状态，则意味着只创建了一个链路。 必须创建双向链接才能成功建立连接。 例如，要将 VNet A 对等互连到 VNet B，必须创建从 VNet A 到 VNet B 的链路以及从 VNet B 到 VNet A 的链路。创建这两个链路后，连接状态更改为“已连接”。

### <a name="my-virtual-network-peering-connection-is-in-a-disconnected-state-why-cant-i-create-a-peering-connection"></a>我的虚拟网络对等互连连接处于“已断开连接”状态，为什么我无法创建对等互连连接？

如果虚拟网络对等互连连接处于“已断开连接”状态，则意味着创建的某个链路已被删除。 要重新建立对等互连连接，请删除该链路并重新创建它。

### <a name="is-virtual-network-peering-traffic-encrypted"></a>虚拟网络对等互连流量是否已加密？

不是。 对等互连的虚拟网络中的资源之间的流量是专用的，处于隔离状态。 它完全保留在 Azure Stack Hub 系统的 SDN 层中。

### <a name="if-i-peer-vnet-a-to-vnet-b-and-i-peer-vnet-b-to-vnet-c-does-that-mean-vnet-a-and-vnet-c-are-peered"></a>如果我从 VNet A 对等互连到 VNet B，然后又从 VNet B 对等互连到 VNet C，这是否意味着 VNet A 和 VNet C 已对等互连？

否。 不支持可传递对等互连。 必须将 VNet A 与 VNet C 对等互连。

## <a name="next-steps"></a>后续步骤

- [关于 Azure 虚拟网络](/virtual-network/virtual-networks-overview)
- [用户定义的路由概述](/virtual-network/virtual-networks-udr-overview#user-defined)
- [Azure 中的中心辐射型网络拓扑](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
