---
title: Azure Service Fabric 灾难恢复
description: Azure Service Fabric 提供应对灾难的功能。 本文介绍可能发生的灾难类型，以及如何应对这些灾难。
ms.topic: conceptual
origin.date: 08/18/2017
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: 41764bb0dcaa7c4015ed5b0dd7aea7d2cbd42109
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94327891"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Azure Service Fabric 中的灾难恢复
提供高可用性的关键一环是确保服务能够经受各种不同类型的故障。 对于计划外和不受控制的故障，这一点尤其重要。 

本文介绍一些常见的故障模式，如果未正确建模和管理，这些故障可能成为灾难。 本文还介绍发生灾难时应采取的缓解措施和行动。 目标是在发生计划内故障或其他故障时，限制或消除停机或数据丢失风险。

## <a name="avoiding-disaster"></a>避免灾难
Azure Service Fabric 的主要目标是帮助你针对环境和服务进行建模，使常见故障类型不构成灾难。 

一般而言，有两种类型的灾难/故障场景：
- 硬件和软件故障
- 操作故障

### <a name="hardware-and-software-faults"></a>硬件和软件故障
硬件和软件故障是不可预知的。 解决故障的最简单方法是跨硬件或软件故障边界运行服务的多个副本。 

例如，如果服务仅在一台计算机上运行，则该计算机的故障是该服务的灾难。 避免此灾难的简单方法是，确保服务在多台计算机上运行。 另外，必须进行测试以确保一台计算机的故障不会中断正在运行的服务。 容量规划确保可以在其他位置创建替换实例，并且容量减少不会使剩余服务过载。 

无论尝试避免哪种故障，都可以使用这一相同模式。 例如，如果你担心某一 SAN 发生故障，则可跨多个 SAN 运行。 如果担心服务器机架丢失，则可跨多个机架运行。 如果担心数据中心丢失，则应跨多个 Azure 区域或你自己的数据中心运行服务。 

<!--Not Available on  across multiple Azure Availability Zones-->

当服务跨多个物理实例（计算机、机架、数据中心、区域）时，仍可能发生某些类型的同步故障。 但是，系统会自动处理特定类型的单个甚至多个故障（例如，单个虚拟机故障或网络链接故障），因此这些故障不再是“灾难”。 

Service Fabric 提供用于扩展群集的机制，并负责恢复发生故障的节点和服务。 使用 Service Fabric 还可以运行许多服务实例，以防止计划外故障转变成真正的灾难。

可能有一些原因导致通过运行足够大的部署来解决故障并不可行。 例如，相对于发生故障的可能性，需要的硬件资源可能超过你愿意支付的数目。 处理分散式应用程序时，不同地理距离之间的附加通信跃点或状态复制成本可能导致不可接受的延迟。 具体界限因不同的应用程序而异。 

具体而言，软件故障可能发生在尝试缩放的服务中。 在这种情况下，运行多个副本不能防止灾难发生，因为故障条件与所有实例相关。

### <a name="operational-faults"></a>操作故障
即使服务遍布全球且存在许多冗余，仍然可能遇到灾难性事件。 例如，某人意外重新配置或彻底删除了服务的 DNS 名称。 

举例来说，假设有一个有状态 Service Fabric 服务，有人意外删除了该服务。 除非有一些其他的缓解措施，否则该服务及其所有状态现在就不复存在了。 这些类型的操作灾难（“事故”）需要采取不同于常规计划外故障的缓解措施和步骤才能恢复。 

避免这些类型的操作故障的最佳方法是：
- 限制对环境的操作访问。
- 严格审核危险操作。
- 强制实行自动化、防止手动或带外更改，并在执行特定更改之前针对环境验证这些更改。
- 确保破坏性操作为“软”操作。 软操作不会立即生效，或者可以在某个时间范围内撤消。

Service Fabric 提供了用于防止操作故障的机制，例如提供针对群集操作的[基于角色](service-fabric-cluster-security-roles.md)的访问控制。 但是，大多数操作故障都需要组织和其他系统来配合解决。 Service Fabric 提供用于解决操作故障的机制，最值得注意的是[有状态服务的备份和还原](service-fabric-backuprestoreservice-quickstart-azurecluster.md)。

## <a name="managing-failures"></a>管理故障
Service Fabric 的目标是自动管理故障。 但是，若要处理某些类型的故障，服务必须有其他代码。 出于安全性和业务连续性的原因，不应自动处理其他类型的故障  。 

### <a name="handling-single-failures"></a>处理单个故障
单台计算机可能由于各种原因发生故障。 有时，原因与硬件相关，例如电源和网络硬件故障。 其他故障与软件有关。 这些故障包括操作系统和服务本身的故障。 Service Fabric 会自动检测这些类型的故障，包括由于网络问题导致某一计算机与其他计算机隔离的情况。

无论服务是什么类型，如果代码的单个副本由于任何原因而发生故障，则运行单个实例会导致该服务停机。 

若要处理任何单一故障，可执行的最简单操作是确保服务默认在多个节点上运行。 对于无状态服务，请确保 `InstanceCount` 大于 1。 对于有状态服务，最低建议是将 `TargetReplicaSetSize` 和 `MinReplicaSetSize` 都设置为 3。 运行服务代码的更多副本可确保服务能够自动处理任何单一故障。 

### <a name="handling-coordinated-failures"></a>处理协调性故障
由于计划内或计划外基础结构故障和更改，或计划内软件更改，群集中可能发生协调性故障。 Service Fabric 将遇到协调性故障的基础结构区域建模为容错域  。 将遇到协调性软件更改的区域建模为升级域  。 有关容错域、升级域和群集拓扑的详细信息，请参阅[使用群集资源管理器描述 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)。

默认情况下，Service Fabric 会在规划服务应该运行的位置时考虑容错域和升级域。 默认情况下，Service Fabric 尝试确保服务跨多个容错域和升级域运行，以便发生计划内或计划外更改时，服务仍然可用。 

例如，假设电源故障导致机架上的所有计算机同时发生故障。 运行多个服务副本时，容错域中丢失多台计算机的故障会变成服务单一故障的另一个示例。 因此，管理容错域和升级域对于确保服务高可用性至关重要。 

在 Azure 中运行 Service Fabric 时，系统会自动管理容错域和升级域。 在其他环境中，可能不是这样。 若要在本地生成自己的群集，请务必正确映射和规划容错域布局。

升级域可用于对那些同时会在其中升级软件的区域建模。 因此，升级域通常还可以定义在计划升级期间停用软件的边界。 Service Fabric 和服务的升级均遵循相同的模型。 若要详细了解滚动升级、升级域以及有助于防止意外更改影响群集与服务的 Service Fabric 运行状况模型，请参阅：

 - [应用程序升级](service-fabric-application-upgrade.md)
 - [应用程序升级教程](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric 运行状况模型](service-fabric-health-introduction.md)

可以使用 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 中提供的群集映射来可视化群集布局：

<center>

![Service Fabric Explorer 中分散在容错域之间的节点][sfx-cluster-map]

</center>

> [!NOTE]
> 建模故障区域、滚动升级、运行服务代码和状态的多个实例、确保服务在容错域和升级域中运行的放置规则，以及内置运行状况监视仅仅是 Service Fabric 提供的一些功能，目的是防止正常操作问题和故障变成灾难  。 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>处理同步硬件或软件故障
前面讨论了单一故障。 可以看到，只需跨容错域和升级域运行代码（和状态）的多个副本，即可轻松处理无状态和有状态服务的单一故障。 

也可能发生多个同时随机故障。 这些故障更有可能造成停机或实际灾难。

#### <a name="stateless-services"></a>无状态服务
无状态服务的实例计数指示必须处于运行状态的实例的所需数目。 当任一（或所有）实例发生故障时，Service Fabric 会通过在其他节点上自动创建替代实例来做出响应。 Service Fabric 会不断地创建替代实例，直到服务恢复到所需的实例计数。

例如，假设无状态服务的 `InstanceCount` 值为 -1。 此值意味着应在群集中的每个节点上运行一个实例。 如果其中一些实例发生故障，Service Fabric 会检测到服务不处于所需状态，因此会尝试在缺少这些实例的节点上创建实例。

#### <a name="stateful-services"></a>有状态服务
有两种类型的有状态服务：
- 具有永久性状态的有状态服务。
- 具有非永久性状态的有状态服务。 （状态存储在内存中。）

在有状态服务发生故障后如何进行恢复取决于该有状态服务的类型、该服务拥有的副本数，以及发生故障的副本数。

在有状态服务中，传入的数据在副本（主要副本和任何活动的次要副本）之间复制。 如果大多数副本接收数据，则将数据视为已提交仲裁  。 （如果有五个副本，其中三个副本将是仲裁。）这意味着，在任何时候，都至少有一个副本仲裁包含最新数据。 如果副本发生故障（例如，五个副本中的两个发生故障），我们可以使用仲裁值来评估是否可以恢复。 （由于五个副本中的剩余三个副本仍处于运行状态，因此可以保证至少有一个副本包含完整数据。）

当副本仲裁发生故障时，会将分区声明为处于仲裁丢失状态  。 假设某个分区包含五个副本，这意味着，可以保证至少有三个副本包含完整数据。 如果副本的仲裁（五个副本中的三个）发生故障，Service Fabric 将无法确定剩余的副本（五个副本中的两个）是否包含足够的用于还原分区的数据。 如果 Service Fabric 检测到仲裁丢失，其默认行为是阻止对分区进行更多的写入，声明仲裁丢失，并等待副本仲裁还原。

请根据以下 3 个阶段确定有状态服务是否发生灾难并对其进行管理：

1. 确定有无仲裁丢失。

    当有状态服务的大多数副本同时关闭时，将声明仲裁丢失。
2. 确定仲裁丢失是否属于永久性。

    大多数情况下，故障是暂时性的。 重启进程、节点和虚拟机，网络分区将会修复。 但是，故障有时是永久性的。 故障是否为永久性取决于有状态服务是否保存其状态，或者是否仅将其状态保存在内存中： 

    - 对于没有永久性状态的服务，仲裁或多个副本故障会立即导致永久性仲裁丢失  。 Service Fabric 在有状态、非永久性服务中检测到仲裁丢失时，会立即通过声明（潜在的）数据丢失转到步骤 3。 转到数据丢失状态是有意义的，因为 Service Fabric 知道等待副本恢复没有任何意义。 由于服务的非永久性，即使副本恢复，数据也会丢失。
    - 对于有状态的永久性服务，仲裁或多个副本故障会导致 Service Fabric 等待副本恢复并还原仲裁。 这会对服务的受影响分区（或“副本集”）的任何写入造成服务中断  。 但是，仍有可能可以读取，但其一致性保证会降低。 Service Fabric 等待仲裁恢复的默认时间是无限的，因为处理是（潜在的）数据丢失事件并伴有其他风险  。 这意味着，除非管理员通过操作来声明数据丢失，否则 Service Fabric 不会继续执行下一步。
3. 确定数据是否丢失，并从备份还原。

    如果已声明仲裁丢失（自动声明或通过管理操作声明），Service Fabric 和服务会继续确定数据是否实际丢失。 此时，Service Fabric 还知道其他副本不会恢复。 这是当我们停止等待仲裁丢失自行解决时所做的决策。 服务采取的最佳做法通常是冻结并等待特定的管理员介入。

    Service Fabric 调用 `OnDataLossAsync` 方法始终是因为疑似数据丢失  。 Service Fabric 可确保将此调用传送到最合适的剩余副本  。 也就是进度最大的副本。 

    我们总是将其称为疑似数据丢失，这是因为剩余副本在仲裁丢失时可能与主要副本具有完全相同的状态  。 但是，如果没有该状态作为对比，Service Fabric 或操作者就没有很好的方法来明确这一点。     

    那么 `OnDataLossAsync` 方法所执行的典型实现是什么？
    1. 该实现会记录 `OnDataLossAsync` 已被触发，并触发任何必要的管理警报。
    1. 通常，该实现会暂停并等待进一步决策和要采取的手动操作。 这是因为即使备份可用，也可能需要进行准备。 

        例如，如果两个不同的服务协调信息，则可能需要修改这些备份，以确保发生还原后，这两个服务所关注的信息一致。 
    1. 通常有一些其他遥测或服务消耗。 此元数据可能包含在其他服务或日志中。 可根据需要使用此信息确定主要副本是否收到并处理了任何调用，这些调用未存在于备份中或未复制到此特定副本中。 可能需要重播或向备份添加这些调用才能进行还原。  
    1. 该实现将剩余副本的状态与任何可用备份中所含的状态进行比较。 如果使用 Service Fabric 可靠集合，则可使用[工具和进程](service-fabric-reliable-services-backup-restore.md)执行此操作。 目标是查看副本中的状态是否充足，以及查看备份可能丢失的内容。
    1. 完成比较并根据需要完成还原后，如果更改了任何状态，则服务代码应返回 true  。 如果副本确定它是该状态的最佳可用副本，并且未进行任何更改，则代码将返回 false  。 

        true 值表示任何其他剩余副本现在可能与此副本不一致   。 应删除这些副本并根据此副本重新生成。 false 值表示未进行任何状态更改，因此其他副本可以保留其所含的内容  。 

在生产环境中部署服务之前，服务创建者务必对潜在的数据丢失和故障场景进行实践。 为了防止数据丢失的可能性，务必定期将任何有状态服务的[状态备份](service-fabric-reliable-services-backup-restore.md)到异地冗余存储。 

还需确保能够还原状态。 由于许多不同服务进行备份的时间有所不同，因此需要确保还原后各个服务具有相互一致的视图。 

例如，考虑这样一种情况：某一服务生成并存储一个数值，并将其发送给同样存储该数值的另一服务。 还原后，可能发现第二个服务含有该数值但第一个没有，因为其备份未包含该操作。

如果发现剩余副本不足以在数据丢失场景中继续运行，并且不能通过遥测或消耗重构服务状态，则备份频率决定最可行的恢复点目标 (RPO)。 Service Fabric 提供许多工具来测试各种故障场景，包括需要从备份还原的永久性仲裁和数据丢失。 这些场景都包括在 Service Fabric 的可测试性工具中，由故障分析服务管理。 有关这些工具和模式的详细信息，请参阅[故障分析服务简介](service-fabric-testability-overview.md)。 

> [!NOTE]
> 系统服务也可能遭受仲裁丢失。 这种影响特定于相关的服务。 例如，命名服务的仲裁丢失会影响名称解析，而故障转移管理器服务的仲裁丢失会阻止新服务的创建与故障转移。 
> 
> Service Fabric 系统服务采用与状态管理服务相同的模式，但我们不建议你尝试将其从仲裁丢失转为潜在的数据丢失。 我们建议你[寻求支持](service-fabric-support.md)来确定针对具体情况的解决方案。 通常最好是单纯地等待，直到关闭的副本恢复为止。
>

#### <a name="troubleshooting-quorum-loss"></a>排查仲裁丢失问题

由于发生暂时性故障，副本可能会间歇性关闭。 请等待一段时间，让 Service Fabric 尝试将其启动。 如果副本关闭的持续时间超过预期，请执行以下故障排除操作：
- 副本可能会崩溃。 检查副本级别的运行状况报告和应用程序日志。 收集故障转储并采取必要的措施进行恢复。
- 副本进程可能变得无响应。 检查应用程序日志以验证这种情况。 收集进程转储，然后停止无响应的进程。 Service Fabric 会创建一个替代进程，并会尝试恢复副本。
- 托管副本的节点可能已关闭。 重启基础虚拟机以使节点启动。

有时可能无法恢复副本。 例如，驱动器出现故障或者计算机实际上无响应。 在这些情况下，需要告知 Service Fabric 不要等待副本恢复。

如果使服务联机造成的潜在数据丢失不可接受，请不要使用这些方法  。 在这种情况下，应尽全力恢复物理计算机。

以下操作可能导致数据丢失。 在执行这些操作之前请进行检查。

> [!NOTE]
> 为了确保安全，请务必针对特定分区有目标地使用这些方法  。 
>

- 使用 `Repair-ServiceFabricPartition -PartitionId` 或 `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API。 使用此 API 可以指定分区 ID，使其从仲裁丢失转为潜在的数据丢失。
- 如果群集频繁出现故障，导致服务进入仲裁丢失状态，并且可以接受潜在的数据丢失，则指定适当的 [QuorumLossWaitDuration](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) 值有助于服务自动恢复  。 在执行恢复之前，Service Fabric 会等待提供的 `QuorumLossWaitDuration` 值（默认为 infinite）。 我们不建议使用此方法，因为它可能导致意外的数据丢失  。

## <a name="availability-of-the-service-fabric-cluster"></a>Service Fabric 群集的可用性
一般情况下，Service Fabric 群集是一个分散程度很高的环境，没有任何单一故障点。 任何一个节点发生故障不会给群集造成可用性或可靠性问题，主要是因为 Service Fabric 系统服务遵循前面提供的准则。 即，默认情况下，它们始终运行三个或三个以上的副本，并且无状态系统服务在所有节点上运行。 

基础 Service Fabric 网络和故障检测层是完全分布式层。 大多数系统服务可以根据群集中的元数据重新生成，或者知道如何从其他位置重新同步其状态。 如果系统服务遇到前面所述的仲裁丢失情况，则群集可用性可能会受到影响。 在这些情况下，可能无法在群机上执行某些操作（例如启动升级或部署新服务），但群集本身仍处于启动状态。 

运行中群集上的服务会继续在这些情况下运行，除非它们要求将数据写入系统服务才能继续运行。 例如，如果故障转移管理器处于仲裁丢失状态，则所有服务会继续运行。 但是，发生故障的任何服务无法自动重启，因为这需要故障转移管理器的介入。 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>数据中心或 Azure 区域的故障
在极少数情况下，物理数据中心可能会由于电源或网络连接中断而变得暂时不可用。 在这些情况下，Service Fabric 群集和该数据中心或 Azure 区域中的服务不可用。 但会保留数据  。 

对于在 Azure 中运行的群集，可以在 [Azure 状态页][azure-status-dashboard]上查看有关中断的最新信息。 物理数据中心遭到部分或完全损坏（这种情况很少见）时，托管在此处的任何 Service Fabric 群集或其中的服务可能会丢失。 这种丢失包括未在该数据中心或区域外部备份的任何状态。

可通过两种不同的策略解决单个数据中心或区域出现的永久性或持续性故障： 

- 在多个此类区域中运行单独的 Service Fabric 群集，并使用某些机制在这些环境之间进行故障转移和故障回复。 这种多群集主动/主动或主动/被动模型需要额外的管理工作和操作代码。 此模型还需要协调来自同一数据中心或区域中服务的备份，以便某一数据中心或区域失败时，其他数据中心或区域可以使用该备份。 
- 运行跨多个数据中心或区域的单个 Service Fabric 群集。 此策略支持的最低配置是三个数据中心或区域。 建议的区域或数据中心数量为 5。 

    此模型需要更复杂的群集拓扑。 但是，好处是一个数据中心或区域的故障可从灾难转变为正常故障。 可通过适用于单个区域内群集的机制来处理这些故障。 容错域、升级域和 Service Fabric 放置规则可确保工作负载得以分散，使其能够承受正常故障。 

    若要详细了解那些有助于在此类群集中运行服务的策略，请参阅 [Service Fabric 服务的放置策略](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)。

### <a name="random-failures-that-lead-to-cluster-failures"></a>导致群集故障的随机故障
Service Fabric 具有种子节点的概念  。 种子节点可以维护基础群集的可用性。 

种子节点可以与其他节点签署租约，在发生某些类型的故障时充当断路器，这样可确保群集保持启动状态。 如果随机故障删除了群集中的大部分种子节点并且无法快速将其恢复，则群集会自动关闭。 群集随后会发生故障。 

在 Azure 中，Service Fabric 资源提供程序管理 Service Fabric 群集配置。 默认情况下，资源提供程序在主节点类型的容错域和升级域之间分配种子节点  。 如果将主节点类型标记为“银”或“金”级耐久性，则在通过缩减主节点类型或手动删除种子节点的方式删除种子节点时，群集会尝试从主节点类型的可用容量中提升另一个非种子节点。 如果你的可用容量少于群集可靠性级别对主节点类型的要求，则此尝试会失败。

在独立 Service Fabric 群集和 Azure 中，主节点类型是运行种子的节点类型。 定义主节点类型时，Service Fabric 会自动利用为每个系统服务创建最多九个种子节点和七个副本时提供的节点数。 如果一组随机故障同时导致其中的大部分副本关闭，则系统服务会进入仲裁丢失状态。 如果大部分种子节点丢失，群集会在不久之后关闭。

## <a name="next-steps"></a>后续步骤
- 了解如何使用[可测试性框架](service-fabric-testability-overview.md)模拟各种故障。
- 阅读有关灾难恢复和高可用性的其他资源。 Azure 已发布大量有关这些主题的指导。 尽管其中有些资源提到的是在其他产品中使用的特定技术，但它们包含的许多常规最佳做法是可在 Service Fabric 上下文中应用的：
    
    <!--Not Available on - [Availability checklist](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)-->
    
    - [执行灾难恢复演练](../azure-sql/database/disaster-recovery-drills.md)
    - [Azure 应用程序的灾难恢复和高可用性][dr-ha-guide]
    
- 了解 [Service Fabric 支持选项](service-fabric-support.md)。

<!-- External links -->

[repair-partition-ps]: https://docs.microsoft.com/windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]: https://status.azure.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://docs.microsoft.com/previous-versions/azure/dn251004(v=azure.100)

<!--MOONCAKE CUSTOMIZATION ON https://docs.microsoft.com/previous-versions/azure/dn251004(v=azure.100)-->
<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png

<!-- Update_Description: update meta properties, wording update, update link -->