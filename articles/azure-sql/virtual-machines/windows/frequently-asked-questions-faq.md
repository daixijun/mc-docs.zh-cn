---
title: Azure 的 Windows 虚拟机上的 SQL Server 常见问题解答 | Microsoft Docs
description: 本文提供有关运行 Azure VM 中的 SQL Server 时遇到的常见问题的解答。
services: virtual-machines-windows
documentationcenter: ''
author: WenJason
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
origin.date: 08/05/2019
ms.date: 01/04/2021
ms.author: v-jay
ms.openlocfilehash: 6415852ec317154c65b011ac4f87d5c14fb2c597
ms.sourcegitcommit: cf3d8d87096ae96388fe273551216b1cb7bf92c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829728"
---
<!--Verified Redirect file-->
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Azure VM 上的 SQL Server 常见问题解答
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

本文提供有关[在 Windows Azure 虚拟机 (VM) 上运行 SQL Server](https://www.azure.cn/home/features/virtual-machines/#virtual-machine-SQLserver) 时出现的一些最常见问题的解答。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>映像

1. **有哪些 SQL Server 虚拟机库映像可用？** 

   Azure 为所有 Windows 和 Linux 版本中的所有受支持 SQL Server 主要发行版维护虚拟机映像。 有关详细信息，请参阅 [Windows VM 映像](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)和 [Linux VM 映像](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)的完整列表。

    <!--CORRECT ON payinadvance-->
    
1. **现有的 SQL Server 虚拟机库映像是否会更新？**

   每隔两个月，都会使用最新的 Windows 和 Linux 更新对虚拟机库中的 SQL Server 映像进行更新。 对于 Windows 映像，这包括 Windows 更新中标记为重要的任何更新，以及重要的 SQL Server 安全更新和 Service Pack。 对于 Linux 映像，这包括最新的系统更新。 Linux 和 Windows 的 SQL Server 累积更新以不同的方式进行处理。 对于 Linux，SQL Server 累积更新也包含在刷新中。 但目前，Windows VM 不会连同 SQL Server 或 Windows Server 累积更新一起更新。

1. **是否可以从库中删除 SQL Server 虚拟机映像？**

   是的。 Azure 只为每个主要版本维护一个映像。 例如，发布新的 SQL Server Service Pack 时，Azure 会将新映像添加到该 Service Pack 的库。 先前 Service Pack 的 SQL Server 映像将立即从 Azure 门户中删除。 但是，在接下来的三个月，仍可以通过 PowerShell 预配该映像。 三个月之后，先前的 Service Pack 映像不再可用。 如果 SQL Server 版本由于生命周期结束而不受支持，则也会应用此删除策略。


1. **是否可以部署 Azure 门户中不可见的较旧的 SQL Server 映像？**

   是的，使用 PowerShell。 有关使用 PowerShell 部署 SQL Server VM 的详细信息，请参阅[如何使用 Azure PowerShell 预配 SQL Server 虚拟机](create-sql-vm-powershell.md)。
   
<!--Not Available on 1. **Is it possible to create a generalized Azure Marketplace SQL Server image of my SQL Server VM and use it to deploy VMs?**-->
<!--Not Available on [register each SQL Server VM with the SQL Server VM resource provider](sql-vm-resource-provider-register.md)-->

1. 如何使 Azure VM 上的 SQL Server 通用化并使用它部署新 VM？

    可以部署 Windows Server VM（不安装 SQL Server），并使用 [SQL sysprep](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) 进程和 SQL Server 安装媒体将 Azure VM (Windows) 上的 SQL Server 通用化。 如果客户有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3)，则可以从[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)获取其安装介质。 没有软件保障的客户可以使用具有所需版本的 Azure 市场 SQL Server VM 映像中的安装介质。

   另外，也可使用 Azure 市场中的 SQL Server 映像之一来将 Azure VM 上的 SQL Server 通用化。 请注意，在创建你自己的映像之前，必须在源映像中删除以下注册表项。 如果不这样做，可能会导致 SQL Server 安装程序的启动文件夹扩展以及/或者 SQL IaaS 扩展处于故障状态。

   注册表项路径：  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

    <!--Not Available on [registered with a SQL VM recourse provider](/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash)-->
    <!--Not Available on [registered with a SQL VM recourse provider](/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash)-->

    
1. **是否可以使用我自己的 VHD 来部署 SQL Server VM？**

   可以，但必须向 SQL IaaS 代理扩展注册每个 SQL Server VM，以便在门户中管理 SQL Server VM，以及利用自动修补和自动备份等功能。
    
    <!--Not Available on [register each SQL Server VM with the SQL Server VM resource provider](virtual-machines-windows-sql-register-with-resource-provider.md)-->
    
1. **是否可以设置虚拟机库中未显示的配置（例如 Windows 2008 R2 + SQL Server 2012）？**

   否。 对于包含 SQL Server 的虚拟机图库映像，必须通过 Azure 门户或 [PowerShell](create-sql-vm-powershell.md) 选择提供的某个映像。 但是，可以部署一个 Windows VM 并在其中自行安装 SQL Server。 然后必须向 SQL IaaS 代理扩展注册 SQL Server VM，以便在 Azure 门户中管理 SQL Server VM，以及利用自动修补和自动备份等功能。 

    <!--Not Available on [register your SQL Server VM with the SQL Server VM resource provider](sql-vm-resource-provider-register.md)-->

## <a name="creation"></a>创建

1. **如何创建装有 SQL Server 的 Azure 虚拟机？**

   最简单的方法是创建包含 SQL Server 的虚拟机。 有关注册 Azure 并从门户创建 SQL Server VM 的教程，请参阅[在 Azure 门户中预配 SQL Server 虚拟机](create-sql-vm-portal.md)。 可选择使用按秒付费 SQL Server 许可的虚拟机映像，或者可以使用允许自带 SQL Server 许可证的映像。 此外，你也可以选用免费许可版（开发人员版或速成版），或通过重新使用本地许可证在 VM 上手动安装 SQL Server。 请务必向 SQL IaaS 代理扩展注册 SQL Server VM，以便在 Azure 门户中管理 SQL Server VM，以及利用自动修补和自动备份等功能。 如果自带许可，必须[在 Azure 上通过软件保障实现许可证移动性](https://www.azure.cn/pricing/license-mobility/)。 有关详细信息，请参阅 [SQL Server Azure VM 定价指南](pricing-guidance.md)。

    <!--MOONCAKE: NOT AVAILABLE ON [register your SQL Server VM with the SQL Server VM resource provider](virtual-machines-windows-sql-register-with-resource-provider.md)-->
    <!--MOONCAKE: REPLACE WITH [configure your SQL Server VM in portal](virtual-machines-windows-portal-sql-server-provision.md#4-configure-sql-server-settings)-->
    
1. 如何将本地 SQL Server 数据库迁移到云中？

   首先，请创建装有 SQL Server 实例的 Azure 虚拟机。 然后将本地数据库迁转到该实例。 有关数据迁移策略，请参阅 [将 SQL Server 数据库迁移到 Azure VM 中的 SQL Server](migrate-to-vm-from-sql-server.md)。

## <a name="licensing"></a>授权

1. **如何在 Azure VM 上安装 SQL Server 的许可版本？**

    可通过两种方式来执行此操作。 企业协议 (EA) 客户可以预配[支持许可证的虚拟机映像](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)之一，也称为自带许可 (BYOL)。 或者，可将 SQL Server 安装媒体复制到 Windows Server VM，然后在 VM 上安装 SQL Server。 请务必更新 Azure 门户中“设置”边栏的 SQL Server 配置，以便能够使用门户管理、自动备份和自动修补等功能。 

<!--CORRECT ON two way to do this-->
<!--Not Available on 1. **Can I change a VM to use my own SQL Server license if it was created from one of the Standard Pay-in-Advance Offer gallery images?**-->
<!--NOTICE: this facility is available only for Public Cloud customers.-->
<!--Not Available on 1. **Will switching licensing models require any downtime for SQL Server?**-->
<!--Not Available on [Changing the licensing model](virtual-machines-windows-sql-ahb.md)-->

1. **是否可以在使用经典模型部署的 SQL Server VM 上切换许可模型？**

    否。 不支持在经典 VM 上更改许可模型。 可将 VM 迁移到 Azure 资源管理器模型，并将其注册到 SQL Server VM 资源提供程序。 将 VM 注册到 SQL Server VM 资源提供程序后，即可在 VM 上更改许可模型。

<!--Not Available on 1. **Can I use the Azure portal to manage multiple instances on the same VM?**-->
<!--Not Available on SQL Server VM resource provider-->
<!--Not Available on 1. **Can CSP subscriptions activate the Azure Hybrid Benefit?**-->
<!--Not Available on [Changing the licensing model](virtual-machines-windows-sql-ahb.md)-->

1. **如果 Azure VM 仅供备用/故障转移，是否必须支持该 VM 上的 SQL Server 许可费？**

   若要获得备用辅助可用性组或故障转移群集实例的免费被动许可证，必须满足[产品许可条款](https://www.microsoft.com/licensing/product-licensing/products)中所述的以下所有条件：

   1. 已通过[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)获得[许可移动性](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)。 
   1. 被动 SQL Server 实例不会为客户端提供 SQL Server 数据，也不会运行活动的 SQL Server 工作负荷。 它只用于与主服务器同步，或者使被动数据库保持热备用状态。 如果它正在提供数据（例如，向运行活动 SQL Server 工作负载的客户端报告，或执行未在产品条款中指定的任何工作），则它必须是付费许可的 SQL Server 实例。 允许对辅助实例执行以下活动：数据库一致性检查或 CheckDB、完整备份、事务日志备份，以及资源使用情况数据监视。 还可以在每隔 90 天运行一次灾难恢复测试的短暂时段内，同时运行主实例和相应的灾难恢复实例。 
   1. 活动的 SQL Server 许可证已涵盖在软件保障中，仅允许 **一个** 被动辅助 SQL Server 实例，允许的计算量不能超过已许可的活动服务器。 
   
    <!--Not Available on [Disaster Recovery](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)-->

<!--Not Avaialble on 1. **What is considered a passive instance?**-->
<!--Not Avaialble on 1. **What scenarios can utilize the Disaster Recovery (DR) benefit?**-->
<!--Not Avaialble on 1. **What scenarios can utilize the Distaster Recovery (DR) benefit?**-->
<!--Not Avaialble on 1. **Which subscriptions support the Disaster Recovery (DR) benefit?**-->

<!--Not Avaialble on ## Resource provider-->
<!--Not Available on 1. **Will registering my VM with the new SQL VM resource provider bring additional costs?**--> 
<!--Not Available on 1. **Is the SQL VM resource provider available for all customers?**-->
<!--Not Available on 1. **What happens to the resource provider (_Microsoft.SqlVirtualMachine_) resource if the VM resource is moved or dropped?**-->
<!--Not Available on 1. **What happens to the VM if the resource provider (_Microsoft.SqlVirtualMachine_) resource is dropped?**-->
<!--Not Available on 1. **Is it possible to register self-deployed SQL Server VMs with the SQL VM resource provider?**-->


## <a name="administration"></a>管理

1. **是否可以在同一 VM 上安装另一个 SQL Server 实例？是否可以更改默认实例的已安装功能？**

    是的。 SQL Server 安装介质位于 **C** 驱动器上的某个文件夹中。 可从该位置运行 **Setup.exe** 以添加新的 SQL Server 实例，或更改计算机上 SQL Server 的其他已安装功能。 请注意，某些功能（例如自动备份、自动修补和 Azure Key Vault 集成）仅对默认实例或配置正确的命名实例起作用（请参阅问题 3）。 

1. **是否可以卸载 SQL Server 的默认实例？**

   可以，但需注意以下事项。 首先，根据 VM 的许可模型，与 SQL Server 相关的计费可能会继续发生。 其次，如前面的解答中所述，某些功能依赖于 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。 如果卸载默认实例但未同时删除 IaaS 扩展，该扩展会继续查找默认实例并可能生成事件日志错误。 这些错误来自以下两个源：Microsoft SQL Server 凭据管理和 Microsoft SQL Server IaaS 代理 。 其中一个错误可能类似于以下内容：

      建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。

   如果决定卸载默认实例，还要卸载 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。 

1. **是否可从 SQL Server VM 中完全删除 SQL Server？**

    是的，但仍将按照 [SQL Server Azure VM 的定价指南](pricing-guidance.md)收取 SQL Server VM 费用。 如果不再需要 SQL Server，可以部署新的虚拟机并将数据和应用程序迁移到新的虚拟机。 然后可以删除 SQL Server 虚拟机。

## <a name="updating-and-patching"></a>更新和修补

<!--Not Available on 1. **How do I change to a different version/edition of the SQL Server in an Azure VM?**-->
<!--MOONCAKE: Not Avaialble on [change edition of a SQL Server VM](virtual-machines-windows-sql-change-edition.md)-->
<!--Not Available on 1. **Where can I get the setup media to change the edition or version of SQL Server?**-->
1. **如何将更新和服务包应用于 SQL Server VM？**

   虚拟机允许控制主机，包括应用更新的时间与方法。 对于操作系统，可以手动应用 Windows 更新，或者启用名为[自动修补](automated-patching.md)的计划服务。 自动修补将安装任何标记为重要的更新，包括该类别中的 SQL Server 更新。 必须手动安装其他可选的 SQL Server 更新。

<!--Not Available on 1. **Can I upgrade my SQL Server 2008 / 2008 R2 instance after registering it with the SQL Server VM resource provider?**-->
1. **如何获取终止支持的 SQL Server 2008 和 SQL Server 2008 R2 实例的免费扩展安全更新？**

   可以通过将 SQL Server 按原样迁移到 Azure 虚拟机，获取[免费扩展安全更新](sql-server-2008-extend-end-of-support.md)。 有关详细信息，请参阅[终止支持选项](https://docs.microsoft.com/sql/sql-server/end-of-support/sql-server-end-of-life-overview)。 
   
## <a name="general"></a>常规

1. **Azure VM 是否支持 SQL Server 故障转移群集实例 (FCI)？**

   是的。 可以使用存储子系统的[高级文件共享 (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) 或[存储空间直通 (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 安装故障转移群集实例。 高级文件共享提供可满足许多工作负载需求的 IOPS 和吞吐量。 对于 IO 密集型工作负载，请根据托管的高级磁盘考虑使用存储空间直通。 或者，可使用第三方群集或存储解决方案，如 [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)中所述。

    <!--Not Available on [premium file shares (PFS)](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)-->  <!--Not Available on For IO-intensive workloads, consider using storage spaces direct based on manged premium or ultra-disks-->
    
   > [!IMPORTANT]
   > 目前，Azure 上的 SQL Server FCI 不支持 _完整的_ [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。 我们建议你从参与 FCI 的 VM 中卸载 _完整_ 扩展，并改为在 _轻型_ 模式下安装该扩展。 此扩展支持自动备份和修补等功能，以及 SQL Server 的一些门户功能。 卸载 _完整_ 代理以后，这些功能将不适用于 SQL Server VM。

1. SQL Server VM 与 SQL 数据库服务之间的差别是什么？

   从概念上讲，在 Azure 虚拟机上运行 SQL Server 与在远程数据中心运行 SQL Server 并没什么不同。 相比之下，[Azure SQL 数据库](../../database/sql-database-paas-overview.md) 可提供数据库即服务。 使用 SQL 数据库时，无法访问托管数据库的计算机。 有关完整比较，请参阅[选择云 SQL Server 选项：Azure SQL (PaaS) 数据库或 Azure VM 上的 SQL Server (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md)。

1. **如何在 Azure VM 上安装 SQL Data Tools？**

    从 [Microsoft SQL Server 数据工具 - Visual Studio 2013 商业智能](https://www.microsoft.com/download/details.aspx?id=42313)下载并安装 SQL 数据工具。

1. **SQL Server VM 是否支持使用 MSDTC 的分布式事务？**
   
    是的。 SQL Server 2016 SP2 和更高版本支持本地 DTC。 但是，在使用 Always On 可用性组时必须对应用程序进行测试，因为故障转移期间正在进行的事务将会失败，并且必须重试。 从 Windows Server 2019 开始将会推出群集 DTC。 

## <a name="resources"></a>资源

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [在 Windows VM 上预配 SQL Server](create-sql-vm-portal.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)
* [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 虚拟机中 SQL Server 的性能最佳做法](performance-guidelines-best-practices.md)
* [Azure 虚拟机中的 SQL Server 的应用程序模式和开发策略](application-patterns-development-strategies.md)

<!--Verify successfully-->

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [在 Linux VM 上预配 SQL Server](../linux/sql-vm-create-portal-quickstart.md)
* [常见问题 (Linux)](../linux/frequently-asked-questions-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

<!-- Update_Description: new article about frequently asked questions faq -->
<!--NEW.date: 07/06/2020-->