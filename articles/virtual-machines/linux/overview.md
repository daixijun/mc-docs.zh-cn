---
title: Azure 中 Linux VM 的概述
description: Azure 中的 Linux 虚拟机概述。
author: Johnnytechn
ms.service: virtual-machines-linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 01/05/2021
ms.author: v-johya
ms.custom: mvc
ms.openlocfilehash: 0f1cdb2332217226c40f9a813f346c58cfbc47df
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023140"
---
# <a name="linux-virtual-machines-in-azure"></a>Azure 中的 Linux 虚拟机

Azure 虚拟机 (VM) 是 Azure 提供的多种可缩放按需分配计算资源之一。 通常情况下，如果需要以更大的力度（相对于其他控制选项）控制计算环境，则应选择 VM。 本文介绍创建 VM 之前的注意事项，以及 VM 的创建方法和管理方式。

<!--Not Available on [on-demand, scalable computing resources](https://docs.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree)-->

使用 Azure VM 可以灵活进行虚拟化，而无需购买和维护运行 VM 的物理硬件。 不过，仍然需要通过执行任务来维护 VM，例如，配置、修补和安装在 VM 上运行的软件。

可通过多种方式使用 Azure 虚拟机。 下面是一些示例：

* **开发和测试** – 在 Azure VM 上，可以快速轻松地创建具有特定配置的计算机来满足编程和应用程序测试的需要。
* **云中的应用程序** – 由于应用程序的需求会不断变化，在 Azure 中的 VM 上运行应用程序可能会节省成本。 使用 VM 时，需要支付额外的费用；关闭 VM 时，则无需付费。
* **扩展的数据中心** – Azure 虚拟网络中的虚拟机可以轻松连接到组织的网络。

可以根据需要，将应用程序使用的 VM 纵向和横向扩展为任意数目。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>在创建 VM 之前需要考虑哪些因素？
在 Azure 中构建应用程序基础结构时，始终要考虑多个设计注意事项。 在开始之前，必须考虑到 VM 的以下重要方面：

<!--Not Avaialable on [design considerations](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm)

* The names of your application resources
* The location where the resources are stored
* The size of the VM
* The maximum number of VMs that can be created
* The operating system that the VM runs
* The configuration of the VM after it starts
* The related resources that the VM needs

<!--MOONCAKE: CUSTOMIZE ON CHINA-->

### <a name="locations"></a>位置
在 Azure 中创建的所有资源分布在中国的多个[地理区域](https://status.azure.com/status/)。 创建 VM 时，区域通常称为 **位置** 。 位置指定 VM 虚拟硬盘的存储位置。

<!--CHANGE the world TO China-->

下表显示了获取可用位置列表的一些方法。

| 方法 | 说明 |
| --- | --- |
| Azure 门户 |创建 VM 时，可从列表中选择位置。 |
| Azure PowerShell |使用 [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) 命令。 |
| REST API |使用[列出位置](https://docs.microsoft.com/rest/api/resources/subscriptions)操作。 |
| Azure CLI |使用 [az account list-locations](/cli/account?view=azure-cli-latest) 操作。 |

<!--Not availalbe in MC: ### Singapore data residency-->
## <a name="availability"></a>可用性
Azure 宣布了行业领先的单实例虚拟机服务级别协议：可用性达到 99.9%（前提是为所有磁盘使用高级存储部署 VM）。  为了使部署符合标准 99.95% 的 VM 服务级别协议，仍需要在可用性集中部署两个或更多个运行工作负荷的 VM。 可用性集可确保 VM 分布在 Azure 数据中心内的多个容错域，并使用不同的维护时段部署到主机。 完整 [Azure SLA](https://www.azure.cn/support/legal/sla/) 说明了 Azure 作为整体的保证可用性。

## <a name="vm-size"></a>VM 大小
VM 的[大小](../sizes.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)由所要运行的工作负荷决定。 然后，选择的大小决定了处理能力、内存和存储容量等因素。 Azure 提供各种大小来支持多种类型的用途。

Azure 根据 VM 的大小和操作系统[按小时进行收费](https://www.azure.cn/pricing/details/virtual-machines/)。 对于不足一小时的部分，Azure 仅根据使用的分钟数计费。 存储将另行定价和收费。
<!--Correct in MC: https://www.azure.cn/pricing/details/virtual-machines/-->

## <a name="vm-limits"></a>VM 限制
订阅附带默认的[配额限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)，在为项目部署大量 VM 时，这些限制可能会造成影响。 每个订阅的当前限制是每区域 20 个 VM。 可以[开具支持票证](https://support.azure.cn/support/support-azure/)来请求提高限制

## <a name="managed-disks"></a>托管磁盘

托管磁盘为用户在后台处理 Azure 存储帐户的创建和管理，确保用户无需担心存储帐户的可伸缩性限制。 只需指定磁盘大小和性能层（标准或高级），Azure 就会自动创建和管理磁盘。 在添加磁盘或者扩展和缩减 VM 时，无需考虑所用的存储。 如果要创建新的 VM，请[使用 Azure CLI](quick-create-cli.md) 或 Azure 门户，通过托管 OS 和数据磁盘创建 VM。 如果 VM 具有非托管磁盘，则可以[将 VM 转换为由托管磁盘支持](convert-unmanaged-to-managed-disks.md)。

用户还可以按 Azure 区域在一个存储帐户中管理自定义映像，并使用这些映像在同一订阅中创建数百台 VM。 有关托管磁盘的详细信息，请参阅[托管磁盘概述](../managed-disks-overview.md)。

## <a name="distributions"></a>分发 
Azure 支持运行由多家合作伙伴提供和维护的众多热门 Linux 分发版。  可以在 Azure 市场中找到可用的分发版。 Azure 积极与各大 Linux 社区合作以便为 [Azure 认可的 Linux 发行版](endorsed-distros.md)列表添加更多成员。

如果首选的 Linux 分发版目前不在库中，可以通过[在 Azure 中创建和上传 Linux VHD](create-upload-generic.md) 来“自带 Linux”VM。

Azure 与合作伙伴紧密合作，以确保及时更新可用映像并针对 Azure 运行时进行优化。  有关 Azure 合作伙伴产品/服务的详细信息，请参阅以下链接：

* Azure 上的 Linux - [认可的分发](endorsed-distros.md)
* SUSE - [Azure 市场 - SUSE Linux Enterprise Server](https://market.azure.cn/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat - [Azure 市场 - Red Hat Enterprise Linux](https://market.azure.cn/marketplace/apps/filter?search=redhat)
* Canonical - [Azure 市场 - Ubuntu Server](https://market.azure.cn/marketplace/apps/Canonical.UbuntuServer)
* Debian - [Azure 市场 - Debian](https://market.azure.cn/marketplace/apps/filter?search=debian)
* FreeBSD - [Azure 市场 - FreeBSD](https://market.azure.cn/marketplace/apps/filter?search=FreeBSD)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Jenkins - [Azure 市场 - CloudBees Jenkins Platform](https://market.azure.cn/marketplace/apps/filter?search=jenkins)


<!--MOONCAKE CUSTOMIZE * Red Hat - [Azure Marketplace - Red Hat Enterprise Linux](https://market.azure.cn/marketplace/apps?search=redhat)-->
<!--MOONCAKE CUSTOMIZE * FreeBSD - [Azure Marketplace - FreeBSD](https://market.azure.cn/marketplace/apps?search=FreeBSD)-->

<!-- Not Availalbe on 79-80 * RancherOS - [Azure Marketplace - RancherOS](https://market.azure.cn/marketplace/partners/rancher/rancheros/)-->
<!-- Not Availalbe on 80-81 * Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://market.azure.cn/marketplace/partners/mesosphere/dcosdcos/)-->
<!-- Not Availalbe on 80-81 * Docker - [Azure Marketplace - Azure Container Service with Docker Swarm](https://market.azure.cn/marketplace/partners/microsoft/acsswarms/)-->
<!-- Notice: URL is correct on [Azure Marketplace - Jenkins Platform](https://market.azure.cn/marketplace/apps?search=jenkins)-->


## <a name="cloud-init"></a>Cloud-init 

若要实现适当的 DevOps 区域性，所有基础结构都必须是代码。  当所有基础结构都存在于代码中时，便可以轻松重新创建它。  Azure 可与所有主要自动化工具（如 Ansible、SaltStack 和 Puppet）配合使用。  Azure 也有自己的自动化工具：

<!--Not Available on  Chef -->

* [Azure 模板](create-ssh-secured-vm-from-template.md)
* [Azure `VMaccess`](../extensions/vmaccess.md)

Azure 在支持它的大多数 Linux 发行版中支持 [cloud-init](https://cloud-init.io/)。  我们正在积极地与我们认可的 Linux 发行版合作伙伴合作，以便在 Azure 市场中提供已启用 cloud-init 的映像。 这些映像可使 cloud-init 部署和配置无缝地应用于 VM 和虚拟机规模集。

* [在 Azure Linux VM 上使用 cloud-init](using-cloud-init.md)

## <a name="storage"></a>存储
* [Azure 存储简介](../../storage/common/storage-introduction.md)
* [使用 azure-cli 将磁盘添加到 Linux VM](add-disk.md)
* [如何在 Azure 门户中将数据磁盘附加到 Linux VM](attach-disk-portal.md)

## <a name="networking"></a>网络
* [虚拟网络概述](../../virtual-network/virtual-networks-overview.md)
* [Azure 中的 IP 地址](../../virtual-network/public-ip-addresses.md)
* [在 Azure 中打开 Linux VM 的端口](nsg-quickstart.md)
* [在 Azure 门户中创建完全限定的域名](../create-fqdn.md)


## <a name="next-steps"></a>后续步骤

创建第一个 VM！

- [门户](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)

<!-- Update_Description: update meta properties, wording update, update link -->