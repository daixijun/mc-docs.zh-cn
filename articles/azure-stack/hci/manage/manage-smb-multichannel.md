---
title: 管理 SMB 多通道
description: 通过本主题了解如何使用 SMB 多通道提高吞吐量和网络容错能力。
author: WenJason
ms.author: v-jay
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
origin.date: 09/30/2020
ms.date: 11/09/2020
ms.openlocfilehash: cbab5f077b016b80ce2519b93c382352722b127a
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330709"
---
# <a name="manage-smb-multichannel"></a>管理 SMB 多通道

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019；Windows Server 2016；Windows Server 2012 R2；Windows Server 2012；Windows 10

SMB 多通道是服务器消息块 (SMB) 3.0 协议的一部分，它可以提高网络性能和文件服务器的可用性。

SMB 多通道允许文件服务器同时使用多个网络连接。 当 SMB 3.0 客户端和 SMB 3.0 服务器之间有多个可用路径时，SMB 多通道会促进网络带宽聚合和网络容错。 这使服务器应用程序能够充分利用所有可用的网络带宽，使其在应对网络故障时有更强的复原能力。

SMB 多通道提供以下功能：

- **提高吞吐量。** 文件服务器可以通过对高速网络适配器或多个网络适配器使用多个连接，同时传输更多的数据。

- **网络容错。** 当客户端同时使用多个网络连接时，即使某个网络连接断开，客户端也可以继续工作而不会中断。

- **自动配置。** SMB 多通道会自动发现多个可用的网络路径，并根据需要动态添加连接。

## <a name="requirements-for-smb-multichannel"></a>SMB 多通道的要求

由于默认情况下会启用 SMB 多通道，因此你不必安装其他角色、角色服务或功能。 确定了配置后，SMB 客户端会自动检测并使用多个网络连接。 但是，SMB 多通道有以下要求：

- 至少有两台运行 Azure Stack HCI、Windows Server 或 Windows 10 操作系统的计算机。

- 至少有以下配置之一：

    - 多个网络适配器

    - 一个或多个支持接收方缩放 (RSS) 的网络适配器

    - 多个已组合的网络适配器（请参阅 [NIC 组合](#nic-teaming)）

    - 一个或多个支持远程直接内存访问 (RDMA) 的网络适配器

## <a name="configure-smb-multichannel"></a>配置 SMB 多通道

此部分介绍了一些配置选项，这些选项允许使用一系列的网络适配器来部署 SMB 多通道。 这些配置只是示例配置。 本主题未包含许多其他的可能配置。

### <a name="single-rss-capable-network-adapter"></a>单个支持 RSS 的网络适配器

在此典型配置中，SMB 客户端和 SMB 服务器是使用单个 10 千兆以太网 (10 GbE) 网络适配器配置的。 在不使用 SMB 多通道来部署 SMB 的情况下，如果只建立了一个 SMB 会话，SMB 会创建一个 TCP/IP 连接。 仅使用单个 CPU 内核时，此配置在本质上会导致拥塞，尤其是在执行许多小型 I/O 操作时。 因此，出现性能瓶颈的可能性很高。

大多数最新的网络适配器提供一种称为“接收方缩放 (RSS)”的功能，该功能允许多个连接自动分散到多个 CPU 核心。 但是，如果你使用单个连接，RSS 将无用。 将 SMB 多通道与支持 RSS 的网络适配器配合使用时，SMB 会为该特定会话创建多个 TCP/IP 连接。 如果需要很多小型 I/O 操作，则此配置可避免单个 CPU 核心上可能会出现的瓶颈。

### <a name="multiple-network-adapters"></a>多个网络适配器

在此配置中，SMB 客户端和 SMB 服务器是使用多个 10 GbE 网络适配器配置的。 在不使用 SMB 多通道来部署 SMB 的情况下，如果只建立了一个 SMB 会话，SMB 会使用其中一个可用的网络适配器来创建一个 TCP/IP 连接。 在这种情况下，不可能聚合多个网络适配器的带宽。例如，使用两个 1-GbE 网络适配器时，达不到 2 千兆位/秒 (Gbps)。 如果所选网络适配器已断开连接或已禁用，也可能会出现故障。

使用 SMB 多通道部署 SMB 时，如果网络适配器支持 RSS，SMB 会为单个会话创建多个 TCP/IP 连接，每个接口至少有一个或多个连接。 此配置使 SMB 能够使用可用的组合网络适配器带宽，并使 SMB 客户端能够在网络适配器发生故障的情况下继续工作而不中断。

### <a name="nic-teaming"></a>NIC 组合

Azure Stack HCI 和 Windows Server 支持通过使用称为 NIC 组合的功能将多个网络适配器组合到一个网络适配器中。 尽管在不使用 SMB 多通道的情况下部署 SMB 时小组始终会提供容错，但 SMB 仅为每个小组创建一个 TCP/IP 连接。 此配置会导致对参与的 CPU 核心数和小组带宽的使用上限进行限制。

使用 SMB 多通道部署 SMB 时，SMB 会为单个会话创建多个 TCP/IP 连接，以在 CPU 核心之间实现更好的平衡，并更好地利用可用带宽。 NIC 组合会继续提供故障转移功能，其工作速度要快于独自使用 SMB 多通道的速度。 我们还建议采用 NIC 组合，因为它向不依赖于 SMB 的其他工作负荷提供故障转移功能，另外还因为这些工作负荷无法受益于 SMB 多通道的故障转移功能。

如果将一组专用的网络适配器用于存储空间直通流量（就像有时会在 Azure Stack HCI 中执行的那样），组合这些存储网络适配器的操作是完全可选的，它没有任何明显的优缺点。

>[!IMPORTANT]
>在 Windows Server 2012 R2 及更低版本中，如果你打算使用网络适配器的 RDMA 功能，请不要使用 NIC 组合。 在这些操作系统上，支持 RDMA 的网络适配器组始终报告为不支持 RDMA，因为组合会禁用网络适配器的 RDMA 功能。

### <a name="single-or-multiple-rdma-capable-network-adapters"></a>单个或多个支持 RDMA 的网络适配器

SMB 多通道会检测网络适配器的 RDMA 功能，这将启用 SMB 直通功能（称为“基于 RDMA 的 SMB 直通”）。 如果没有 SMB 多通道，SMB 会将常规 TCP/IP 连接与支持 RDMA 的网络适配器配合使用，其中的所有网络适配器都提供与新 RDMA 堆栈一起存在的 TCP/IP 堆栈。

使用 SMB 多通道部署 SMB 时，SMB 会检测网络适配器的 RDMA 功能，并为该单个会话创建多个 RDMA 连接，每个接口有两个 RDMA 连接。 此配置使 SMB 能够利用支持 RDMA 的网络适配器提供的高吞吐量、低延迟和低 CPU 使用率优势。 当你使用多个 RDMA 接口时，它还提供容错功能。

>[!IMPORTANT]
>创建 RDMA 连接后，将不再使用用于原始协议协商的 TCP/IP 连接。 但是，如果另一个 RDMA 连接失败，将保留该连接。

### <a name="smb-multichannel-rdma-capable-network-adapters-and-nic-teaming-compatibility"></a>SMB 多通道、支持 RDMA 的网络适配器和 NIC 组合兼容性

下表总结了在对 SMB 多通道、RDMA（SMB 直通）和 NIC 组合进行组合时可用的不同功能。

| Configuration                                           | 吞吐量 | SMB 的容错 | 非 SMB 的容错 | 降低 CPU 使用率 |
|:-------------------------------------------------------:|:----------:|:-----------------------:|:---------------------------:|:---------------------:|
| 单个网络适配器（无 RSS）                         | *          |                         |                             |                       |
| 多个网络适配器（无 RSS）                      | **         | *                       |                             |                       |
| 带 NIC 组合的多个网络适配器（无 RSS）     | **         | **                      | *                           |                       |
| 单个网络适配器（有 RSS）                         | *          |                         |                             |                       |
| 多个网络适配器（有 RSS）                      | **         | *                       |                             |                       |
| 带 NIC 组合的多个网络适配器（有 RSS）      | **         | **                      | *                           |                       |
| 单个支持 RDMA 的网络适配器                     | *          |                         |                             | *                     |
| 多个支持 RDMA 的网络适配器                  | ***        | *                       |                             | *                     |
| 带 NIC 组合的多个支持 RDMA 的网络适配器 | ***        | **                      | *                           | *                     |

如果运行的是 Windows Server 2016 或更高版本，理想的解决方案是使用多个支持 RDMA 的网络适配器并将 NIC 组合与 SMB 多通道组合在一起。 这种组合提供最佳吞吐量，为使用 SMB 和其他协议的应用程序提供容错能力，且对 CPU 的影响最小。

如上所述，在 Windows Server 2012 R2 或更低版本上使用支持 RDMA 的网络适配器时，NIC 组合并不是一个好选择，因为它禁用网络适配器的 RDMA 功能。

### <a name="example-configurations-without-smb-multichannel"></a>不包含 SMB 多通道的示例配置

如果计划使用不带 RSS 的单个网络适配器，则无法从多个网络连接中获益，并且因此不会使用 SMB 多通道。 此外，如果你计划使用不同速度的网络适配器，SMB 多通道会自动选择最快的网络适配器。 这是因为 SMB 多通道会同时使用类型（例如 RDMA、RSS，或者两者都不是）相同且速度相同的网络适配器。 较慢的网络适配器处于空闲状态。

## <a name="disable-smb-multichannel"></a>禁用 SMB 多通道
通常不需禁用 SMB 多通道。 但是，如果要禁用 SMB 多通道（例如在测试环境中禁用它），请使用以下 Windows PowerShell 过程。

首先，通过打开 PowerShell 会话连接到某个服务器：

```PowerShell
Enter-PSSession <server-name>
```

若要在服务器端禁用 SMB 多通道，请使用以下 cmdlet：

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $false
```

若要在客户端禁用 SMB 多通道，请使用以下 cmdlet：

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $false
```

>[!NOTE]
>在客户端或服务器上禁用 SMB 多通道会阻止两个系统使用它。

## <a name="re-enable-smb-multichannel"></a>重新启用 SMB 多通道

如果在禁用 SMB 多通道后想要重新启用它，请使用以下过程。

若要在服务器端重新启用 SMB 多通道，请使用以下 cmdlet：

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $true
```

若要在客户端重新启用 SMB 多通道，请使用以下 cmdlet：

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $true
```

>[!NOTE]
>必须在客户端和服务器上均重新启用 SMB 多通道，才能再次开始使用它。

## <a name="test-smb-multichannel"></a>测试 SMB 多通道

此部分介绍了一些用于测试 SMB 多通道的方案，其中包括对使用 SMB 多通道时和不使用 SMB 多通道时的文件复制进行比较，并有意导致网络适配器在文件复制操作过程中失败。

### <a name="compare-a-file-copy-with-and-without-smb-multichannel"></a>比较使用和不使用 SMB 多通道的情况下的文件复制

若要度量由 SMB 多通道提供的增加的那部分吞吐量，请执行以下测试。 在每次测试前重启服务器和客户端，以确保它们在相似条件下运行。

1. 使用前述配置之一配置 SMB 多通道。

1. 度量使用 SMB 多通道执行长时间运行的文件复制操作所需的时间。

1. 禁用 SMB 多通道。 有关说明，请参阅[禁用 SMB 多通道](#disable-smb-multichannel)。

1. 度量在不使用 SMB 多通道的情况下执行相同的文件复制所需的时间。

1. 重新启用 SMB 多通道。 有关说明，请参阅[重新启用 SMB 多通道](#re-enable-smb-multichannel)。

1. 比较这两个结果。

>[!IMPORTANT]
>若要避免缓存的性能影响，请先复制超过可用内存大小的大量数据。 然后再次执行复制操作，将第一个复制操作作为预备操作。 仅计算第二个复制操作的时间。

### <a name="cause-one-of-the-network-adapters-to-fail-during-a-file-copy-with-smb-multichannel"></a>使用 SMB 多通道时在文件复制过程中导致其中一个网络适配器出现故障

若要确认 SMB 多通道的故障转移功能，请执行以下操作：

1. 确保 SMB 多通道在采用了多网络适配器配置的情况下运行。

1. 执行长时间运行的文件复制操作。

1. 当文件复制操作正在运行时，通过断开一条电缆或禁用其中一个网络适配器来模拟其中一个网络路径发生故障的情况。

1. 确认文件复制继续使用剩余的网络适配器并在没有任何文件复制错误的情况下运行。

请确保没有其他工作负荷使用已断开的网络连接路径。 这种预防措施避免了不利用 SMB 多通道的工作负荷中可能会出现的故障。

## <a name="verify-that-smb-multichannel-works"></a>验证 SMB 多通道是否工作

使用以下过程来验证 SMB 多通道是否工作。

1. 若要验证网络适配器配置是否正确，请在 SMB 服务器和 SMB 客户端上的 Windows PowerShell 中均键入以下内容。

   ```PowerShell
   Get-NetAdapter
   Get-NetAdapterRSS
   Get-NetAdapterRDMA
   Get-NetAdapterHardwareInfo
   ```

2. 若要验证 SMB 多通道是否已启用，请确认 SMB 能够正确识别网络适配器，并且能够正确识别网络适配器的 RSS 和 RDMA 功能。为此，请执行以下操作：

   在 SMB 客户端上的 Windows PowerShell 中键入以下内容：

   ```PowerShell
   Get-SmbClientConfiguration | Select EnableMultichannel
   Get-SmbClientNetworkInterface
   ```

   在 SMB 服务器上的 Windows PowerShell 中键入以下内容：

   ```PowerShell
   Get-SmbServerConfiguration | Select EnableMultichannel
   Get-SmbServerNetworkInterface
   ```

3. 在 SMB 客户端上执行长时间运行的文件复制操作，该操作会通过 SMB 服务器创建一个持续的会话。 当复制操作正在运行时，请在 Windows PowerShell 中键入以下内容，以验证连接是否使用正确版本的 SMB 以及 SMB 多通道是否工作。

   ```PowerShell
   Get-SmbConnection
   Get-SmbMultichannelConnection
   Get-SmbMultichannelConnection -IncludeNotSelected
   ```

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [使用 Windows Server 中的 SMB 3 协议的文件共享概述](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)
- [简化的 SMB 多通道和多 NIC 群集网络](https://docs.microsoft.com/windows-server/failover-clustering/smb-multichannel)
- [SMB 直通](https://docs.microsoft.com/windows-server/storage/file-server/smb-direct)
