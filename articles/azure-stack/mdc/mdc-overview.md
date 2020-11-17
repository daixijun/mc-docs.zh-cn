---
title: Modular Data Center 概述 |
description: Modular Data Center 是可移植的、可快速部署的数据中心，适用于在临时指挥所和固定指挥所中支持大规模作战行动。
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: overview
origin.date: 01/13/2020
ms.date: 11/09/2020
ms.reviewer: prchint
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: 9a2cacbbb54f22e89f9f24cd56f24e08db7188a0
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330700"
---
# <a name="modular-data-center-overview"></a>Modular Data Center 概述 

Modular Data Center (MDC) 以 Azure Stack Hub 为基础。 MDC 是可移植的、可快速部署的数据中心，适用于在临时指挥所和固定指挥所中支持大规模作战行动。

Azure Stack Hub 是可横向缩放且可纵向缩放的解决方案，它为边缘环境提供适用于 IaaS 和 PaaS 服务的多租户、原生混合云功能，并且支持适用于临时指挥所和固定指挥所以及远征军的多种不同模块化方案。 Azure Stack Hub 是集成的硬件和软件设备，它基于通过节点缩放单元进行的缩放提供了可供商业领域使用的多种不同容量，并且提供了扩展，包括启用了常规用途图形处理单元 (GPU) 的配置和可扩展的外部存储。

## <a name="how-you-can-use-the-mdc"></a>如何使用 MDC

Azure Stack 实行四项核心原则，这些原则与 Azure 的用于模块化边缘方案的功能相符，并且扩展了这些功能。 

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>使用通用的 DevOps 模型（包括与 Azure 的 API 对称性）开发和交付应用

Azure 和 Azure Stack 之间的一致性意味着，适用于作战人员的解决方案会被开发一次、为支持多种不同用例而部署，并使用一组通用工具（例如用于密钥管理的 Azure Key Vault 以及用于资源监视和管理的 Azure Monitor）来进行保护和维护。 Azure Stack 可与本地数据、应用以及适用于 DevOps 和安全操作（例如密钥管理）的工具交互。

### <a name="deliver-azure-services-on-premises"></a>在本地提供 Azure 服务

Azure Stack 可在条件很差（发生争夺、拥堵或拒绝等情形）的通信环境以及可靠的通信环境中运行，并且不依赖于连接到 Azure 来执行任务应用和启用本地操作。 

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>使用集成的硬件和软件交付体验

模块化边缘需要一系列功能，这些功能不仅提供基准计算和存储服务，而且还提供用于机器学习、AI 和分析的高级功能。 在可行的情况下能够从模块化边缘连接到安全云，或者反过来说，在严酷的环境中能够从模块化边缘独立运行，对于为作战人员提供决策所需数据的访问权限至关重要。

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>通过混合云安全操作使数据中心保持安全且可用

Azure Stack 采用云原生设计，允许管理员选择何时进行修补，并且通过安装进程和系统中以原生方式内置的 Azure Stack 管理结构来协调整个操作，从而消除了传统虚拟化环境在操作方面的复杂性。

通过 RBAC 向用户提供适当的访问权限，以便其通过管理门户、特权管理终结点或命令行接口来执行操作，可以实现在 Azure Stack 设备的现场由 Azure 或 Azure 托管产品/服务实施远程辅助管理。 这样就可以由 Azure 来执行所有的修补以及其他管理和监视活动。 Azure Stack 是可以通过安全管理门户或者命令行接口 (CLI) 命令在现场升级和更新的，这两种方式都会应用安全更新和 IaaS 和 PaaS 功能更新，以便在本地或远程网络中的适当位置与 Azure 保持商业对等。 

## <a name="benefits-of-using-the-mdc"></a>使用 MDC 的好处

在条件很差的环境中，MDC 支持与 Azure 一致的环境：
 - 静态、模块化、可快速部署的数据中心，其中提供 Azure 云服务，以便支持 Modular Operations Centers (TOC) 中的大型分析应用。
 - 通过用于支持 IaaS 基元（如虚拟机、存储和虚拟网络）的单一方法，Azure 的模块化边缘产品/服务保持云和边缘之间的一致性
 - Azure Active Directory 和基于角色的访问控制支持
 - 通用管理界面
 - API 对称性以及对 Azure、第三方工具和开源 DevOps 工具的支持
 - 通过 Azure Log Analytics 和 Azure 安全中心进行管理和监视
 - 通过实现混合云，使本地环境和边缘具备云计算的敏捷性。<br>方法：
     - 重复使用代码并在 Azure 与本地环境中一致地运行云原生应用。
     - 使用 Azure 服务的可选连接来运行传统的虚拟化工作负荷。
     - 将数据传输到云，或将数据保留在主权数据中心，以保持符合性。
     - 运行硬件加速的机器学习、容器化或虚拟化工作负荷，所有这些操作都可以在智能边缘进行。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 容量计划](../operator/azure-stack-capacity-planning-overview.md)
