---
title: 为 Azure Stack HCI 规划主机网络
description: 了解如何为 Azure Stack HCI 群集规划主机网络
author: WenJason
ms.topic: how-to
origin.date: 11/09/2020
ms.date: 12/07/2020
ms.author: v-jay
ms.reviewer: JasonGerend
ms.openlocfilehash: cbef0e23e3dcb53b25553207a4880cce7be4f255
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508021"
---
# <a name="plan-host-networking-for-azure-stack-hci"></a>为 Azure Stack HCI 规划主机网络

> 适用于 Azure Stack HCI 版本 20H2

本主题讨论非拉伸和拉伸 Azure Stack HCI 群集环境中的主机网络规划注意事项和要求。

## <a name="node-interconnect-requirements"></a>节点互连要求

本部分讨论了站点中服务器之间的特定网络要求，即互连。 可以使用和支持有交换机或无交换机节点互连：

- **有交换机：** 最常用的方案是，服务器节点通过那些使用网络交换机的以太网彼此连接。 必须正确配置交换机以处理带宽和网络类型。 如果使用的 RDMA 实现了 RoCE 协议，则网络设备和交换机配置很重要。
- **无交换机：** 服务器节点还可以使用直接以太网连接进行互连，不使用交换机。 在这种情况下，每个服务器节点必须与同一站点中的所有其他群集节点直接连接。

### <a name="interconnects-for-2-3-node-clusters"></a>具有 2-3 个节点的群集的互连

下面是具有两个或三个节点的单站点群集的最低互连要求。 这些要求适用于每个服务器节点：

- 一个或多个用于管理功能的 1 Gb 网络适配器卡
- 一个或多个 10 Gb（或更快）的网络接口卡，用于存储和工作负荷流量
- 建议在各个节点之间使用两个或更多个网络连接以确保冗余和性能

### <a name="interconnects-for-4-node-and-greater-clusters"></a>具有 4 个节点的群集和更大群集的互连

下面是具有四个或更多个节点的群集和高性能群集的最低互连要求。 这些要求适用于每个服务器节点：

- 一个或多个用于管理功能的 1 Gb 网络适配器卡。
- 一个或多个 25 Gb（或更快）的网络接口卡，用于存储和工作负荷流量。 建议使用两个或更多个网络连接以确保冗余和性能。
- 支持远程直接内存访问 (RDMA) 的网卡：iWARP（推荐）或 RoCE。

### <a name="site-to-site-requirements-stretched-cluster"></a>站点到站点要求（延伸群集）

在延伸群集的站点之间连接时，每个站点中的互连要求仍适用，此外还有其他必须考虑的存储副本和 Hyper-V 实时迁移流量要求：

- 在进行同步复制的站点之间至少有一个 1 Gb RDMA 或以太网/TCP 连接。 首选 25 Gb 连接。
- 对于同步复制，站点之间的网络需要有足够的带宽来包含 I/O 写入工作负荷，往返延迟平均为 5 毫秒或更低。 异步复制没有延迟建议。
- 如果在站点之间使用单个连接，请使用 PowerShell 设置存储副本的 SMB 带宽限制。 有关详细信息，请参阅 [Set-SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit)。
- 如果在站点之间使用多个连接，请在连接之间分隔流量。 例如，将存储副本流量与使用 PowerShell 的 Hyper-V 实时迁移流量分别置于不同的网络上。 有关详细信息，请参阅 [Set-SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint)。

## <a name="rdma-considerations"></a>RDMA 注意事项

远程直接内存访问 (RDMA) 是从一台计算机的内存直接访问另一台计算机的内存，而不涉及任何一台计算机的操作系统。 这样可允许高吞吐量、低延迟的网络，同时最大限度地降低 CPU 使用率，这在群集中尤其有用。

所有主机 RDMA 流量都利用 SMB 直通。 SMB 直通是通过 RDMA 发送的 SMB 3.0 流量，并通过端口 445 进行多路复用。 RDMA 流量必须至少使用两个启用了基于优先级的流控制 (PFC) 的流量类 (TC)，才能与市场上大多数当前和未来的物理交换机兼容。

Internet 广域 RDMA 协议 (iWARP) 通过 TCP 运行 RDMA，而通过聚合以太网的 RDMA (RoCE) 可避免使用 TCP，但需要支持它的 NIC 和物理交换机。 有关通过 RoCE 的 RDMA 的聚合网络要求，请参阅 [Windows Server 2016 和 2019 RDMA 部署指南](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx)。

对于同一子网上的站点中的群集节点之间的所有东/西流量，默认启用 RDMA。 对于不同子网上站点之间的北/南拉伸群集流量，禁用且不支持 RDMA。

下面是 Azure Stack HCI 的 RDMA 要求：

- 子网之间以及站点之间的所有流量（拉伸群集）必须使用 WinSock TCP。 任何中间网络跃点都不在 Azure Stack HCI 的视图和控制范围内。
- 不支持子网之间以及站点之间的 RDMA（拉伸群集）。 使用上行链路和多个网络设备意味着有多个故障点，在这种情况下，可能会变得不稳定和不受支持。
- 对于拉伸群集的存储副本流量，无需额外的虚拟 NIC。 但出于故障排除目的，将跨站点流量和跨子网流量与东-西 RDMA 流量分隔开来可能会很有用。 如果无法以原生方式为每个流跨站点或跨子网禁用 SMB 直通，请执行以下操作：
    - 应为存储副本预配一个或多个附加 vNIC
    - 存储副本 vNIC 必须使用 PowerShell [Disable-NetAdapterRDMA](https://docs.microsoft.com/powershell/module/netadapter/disable-netadapterrdma) cmdlet 禁用 RDMA，因为就其定义来看，它可以跨站点和跨子网
    - 为了满足上述站点/子网要求，原生 RDMA 适配器需要 vSwitch 和 vNIC 来支持存储副本
    - 站点内 RDMA 带宽要求需要了解每种流量类型的带宽百分比，如“流量带宽分配”部分所述。 这将确保可为东/西（节点到节点）流量应用适当的带宽预留和限制
- 实时迁移和存储副本流量必须受 SMB 带宽限制，否则就可能占用所有带宽，从而影响高优先级的存储流量。 有关详细信息，请参阅 [Set-SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) 和 [Set-SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint) PowerShell cmdlet。

> [!NOTE]
> 使用 `Set-SmbBandwidthLimit` cmdlet 时，需要将位转换为字节。

## <a name="traffic-bandwidth-allocation"></a>流量带宽分配

下表显示了各种流量类型的带宽分配，其中：

- 所有单位均为 Gbps
- 值适用于拉伸和非拉伸群集
- SMB 流量获取总带宽分配的 50%
- 存储总线层/群集共享卷 (SBL/CSV) 流量获取剩余 50% 分配的 70%
- 实时迁移 (LM) 流量获取剩余 50% 分配的 15%
- 存储副本 (SR) 流量获取剩余 50% 分配的 14%
- 检测信号 (HB) 流量获取剩余 50% 分配的 1%
- *= 应使用压缩而不是 RDMA，前提是 LM 流量的带宽分配 < 5 Gbps

|NIC 速度|组合带宽|SMB 50% 预留|SBL/CSV %|SBL/CSV 带宽|LM %|LM 带宽|SR % |SR 带宽|HB %|HB 带宽|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10|20|10|70%|7|14%*|1.4*|14%|1.4|2%|0.2|
|25|50|25|70%|17.5|15%*|3.75*|14%|3.5|1%|0.25|
|40|80| 40|70%|28|15%|6|14%|5.6|1%|0.4|
|50|100|50|70%|35|15%|7.5|14%|7|1%|0.5|
|100|200|100|70%|70|15%|15|14%|14|1%|1|
|200|400|200|70%|140|15%|30|14%|28|1%|2|

## <a name="network-port-requirements"></a>网络端口要求

请确保站点内和站点间的所有服务器节点之间的相应网络端口处于打开状态（对于延伸群集）。 你需要适当的防火墙和路由器规则，以允许在群集中的所有服务器之间进行 ICMP、SMB（端口 445，以及适用于 SMB Direct 的端口 5445）和 WS-MAN（端口 5985）双向通信。

使用 Windows Admin Center 中的群集创建向导来创建群集时，向导会针对故障转移群集、Hyper-V 和存储副本自动打开群集中每台服务器上的相应防火墙端口。 如果在每台服务器上使用不同的防火墙，请打开以下端口：

### <a name="failover-clustering-ports"></a>故障转移群集端口

- ICMPv4 和 ICMPv6
- TCP 端口 445
- RPC 动态端口
- TCP 端口 135
- TCP 端口 137
- TCP 端口 3343
- UDP 端口 3343

### <a name="hyper-v-ports"></a>Hyper-V 端口

- TCP 端口 135
- TCP 端口 80（HTTP 连接）
- TCP 端口 443（HTTPS 连接）
- TCP 端口 6600
- TCP 端口 2179
- RPC 动态端口
- RPC 终结点映射程序
- TCP 端口 445

### <a name="storage-replica-ports-stretched-cluster"></a>存储副本端口（延伸群集）

- TCP 端口 445
- TCP 5445（如果使用 iWarp RDMA）
- TCP 端口 5985
- ICMPv4 和 ICMPv6（如果使用 `Test-SRTopology` PowerShell cmdlet）

可能需要其他端口，但上面未列出。 这些是用于基本的 Azure Stack HCI 功能的端口。

## <a name="network-switch-requirements"></a>网络交换机要求

本部分定义了与 Azure Stack HCI 一起使用的物理交换机的要求。 这些要求列出了行业规范、组织标准和所有 Azure Stack HCI 部署必需的协议。 除非另有说明，否则要求使用标准的最新有效（未被取代）版本。

这些要求有助于确保 Azure Stack HCI 群集部署中的节点之间的通信可靠。 节点之间的通信可靠性至关重要。 若要为 Azure Stack HCI 提供所需的可靠性级别，需要交换机满足以下条件：

- 符合适用的行业规范、标准和协议
- 提供交换机支持的规范、标准和协议的可见性
- 提供有关已启用哪些功能的信息

请务必询问交换机供应商你的交换机是否支持以下各项：

#### <a name="standard-ieee-8021q"></a>标准：IEEE 802.1Q

以太网交换机必须符合定义 VLAN 的 IEEE 802.1Q 规范。 Azure Stack HCI 的多个方面都需要 VLAN，并且所有场景下都需要 VLAN。

#### <a name="standard-ieee-8021qbb"></a>标准：IEEE 802.1Qbb

以太网交换机必须符合定义优先级流控制 (PFC) 的 IEEE 802.1Qbb 规范。 使用数据中心桥接 (DCB) 的情况下需要 PFC。 由于 DCB 可以在 RoCE 和 iWARP RDMA 场景中使用，因此所有场景中都需要 802.1Qbb。 在不降低交换机功能或端口速度的情况下，至少需要三个服务等级 (CoS) 优先级。

#### <a name="standard-ieee-8021qaz"></a>标准：IEEE 802.1Qaz

以太网交换机必须符合定义增强传输选择 (ETS) 的 IEEE 802.1Qaz 规范。 使用 DCB 时需要 ETS。 由于 DCB 可以在 RoCE 和 iWARP RDMA 场景中使用，因此所有场景中都需要 802.1Qaz。 在不降低交换机功能或端口速度的情况下，至少需要三个 CoS 优先级。

#### <a name="standard-ieee-8021ab"></a>标准：IEEE 802.1AB

以太网交换机必须符合定义链接层发现协议 (LLDP) 的 IEEE 802.1AB 规范。 Windows 需要 LLDP 来发现交换机配置。 必须动态启用 LLDP Type-Length-Value (TLV) 配置。 这些交换机不需要附加配置。

例如，启用 802.1 子类型 3 应会自动播发交换机端口上所有可用的 VLAN。

#### <a name="tlv-requirements"></a>TLV 要求

LLDP 使组织可定义并编码自己的自定义 TLV。 这些称为组织特定的 TLV。 所有组织特定的 TLV 都以 LLDP TLV 类型值 127 开始。 下表显示了哪些组织特定的自定义 TLV（TLV 类型 127）子类型是必需的，哪些子类型是可选的：

|条件|组织|TLV 子类型|
|-|-|-|
|必需|IEEE 802.1|VLAN 名称（子类型 = 3）|
|必需|IEEE 802.3|最大帧大小（子类型 = 4）|
|可选|IEEE 802.1|端口 VLAN ID（子类型 = 1）|
|可选|IEEE 802.1|端口和协议 VLAN ID（子类型 = 2）|
|可选|IEEE 802.1|链路聚合（子类型 = 7）|
|可选|IEEE 802.1|拥塞通知（子类型 = 8）|
|可选|IEEE 802.1|ETS 配置（子类型 = 9）|
|可选|IEEE 802.1|ETS 建议（子类型 = A）|
|可选|IEEE 802.1|PFC 配置（子类型 = B）|
|可选|IEEE 802.1|EVB（子类型 = D）|
|可选|IEEE 802.3|链路聚合（子类型 = 3）|

> [!NOTE]
> 所列可选功能的其中一些将来可能需要。

## <a name="traffic-types-supported"></a>支持的流量类型

Azure Stack HCI 使用服务器消息块 (SMB)。 Azure Stack HCI 上的 SMB 支持以下流量类型：

- 存储总线层 (SBL) - 由存储空间直通使用；最高优先级流量
- 群集共享卷 (CSV)
- 实时迁移 (LM)
- 存储副本 (SR) - 用于拉伸群集
- 文件共享 (FS) - FS 传统和横向扩展文件服务器 (SOFS)
- 群集检测信号 (HB)
- 群集通信（节点加入、群集更新、注册表更新）

SMB 流量可以流经以下协议：

- 传输控制协议 (TCP) - 在站点之间使用
- 远程直接内存访问 (RDMA)


## <a name="next-steps"></a>后续步骤

- 温习故障转移群集基础知识。 请参阅[故障转移群集网络基础知识](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- 温习如何使用 SET。 请参阅[远程直接内存访问 (RDMA) 和交换机嵌入式组合 (SET)](https://docs.microsoft.com/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming)
- 有关部署，请参阅[使用 Windows Admin Center 创建群集](/azure-stack/hci/deploy/create-cluster)
- 有关部署，请参阅[使用 Windows PowerShell 创建群集](/azure-stack/hci/deploy/create-cluster-powershell)