---
title: 企业安全性
titleSuffix: Azure Machine Learning
description: 安全使用 Azure 机器学习：身份验证、授权、网络安全性、数据加密和监视。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: ca22a6edf14bcf5f5da8d8f53557f28a082c1225
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "95970836"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure 机器学习的企业安全性和治理

本文介绍 Azure 机器学习可用的安全性功能。

使用某个云服务时，最佳做法是仅限需要该服务的用户访问它。 首先需要了解服务使用的身份验证和授权模型。 此外，你可能想要限制网络访问，或者安全地将本地网络中的资源加入云中。 静态数据以及在服务之间移动的数据的加密也至关重要。 你可能还希望创建策略以强制实施特定配置，或者在创建了不符合要求的配置时进行日志记录。 最后，需要能够监视服务并生成所有活动的审核日志。

> [!NOTE]
> 本文中的信息适用于 Azure 机器学习 Python SDK 1.0.83.1 或更高版本。

## <a name="authentication--authorization"></a>身份验证和授权

向 Azure 机器学习资源进行的大多数身份验证使用 Azure Active Directory (Azure AD) 进行身份验证，并使用基于角色的访问控制 (Azure RBAC) 进行授权。 此情况的例外有：

* __SSH__：你可以启用对某些计算资源（例如 Azure 机器学习计算实例）的 SSH 访问。 SSH 访问使用基于密钥的身份验证。 有关创建 SSH 密钥的详细信息，请参阅[创建和管理 SSH 密钥](../virtual-machines/linux/create-ssh-keys-detailed.md)。 有关启用 SSH 访问的信息，请参阅[创建和管理 Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)。
* __部署为 Web 服务的模型__：Web 服务部署可以使用基于密钥或令牌的访问控制。  密钥是静态字符串。 令牌是使用 Azure AD 帐户检索的。 有关详细信息，请参阅[如何为部署为 Web 服务的模型配置身份验证](how-to-authenticate-web-service.md)。

Azure 机器学习依赖的特定服务（例如 Azure 数据存储服务）有其自己的身份验证和授权方法。 有关存储服务身份验证的详细信息，请参阅[连接到存储服务](how-to-access-data.md)。

### <a name="azure-ad-authentication"></a>Azure AD 身份验证

如果 Azure Active Directory (Azure AD) 已配置为使用多重身份验证，则支持多重身份验证。 下面是身份验证过程：

1. 客户端登录到 Azure AD 并获取 Azure 资源管理器令牌。  完全支持用户和服务主体。
1. 客户端将令牌提供给 Azure 资源管理器和所有 Azure 机器学习服务。
1. 机器学习服务将机器学习服务令牌提供给用户计算目标（例如机器学习计算）。 运行完成后，用户计算目标使用此令牌回调机器学习服务。 范围限制为工作区。

[![Azure 机器学习中的身份验证](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

有关详细信息，请参阅 [Azure 机器学习工作区的身份验证](how-to-setup-authentication.md)。

### <a name="azure-rbac"></a>Azure RBAC

你可以创建多个工作区，并且每个工作区可由多个用户共享。 可以通过将用户的 Azure AD 帐户分配给 Azure RBAC 角色，来控制用户可以访问工作区的哪些功能或操作。 下面是内置角色：

* “所有者”
* 参与者
* 读取器

所有者和参与者可以使用已附加到工作区的所有计算目标和数据存储。  

下表列出了一些主要 Azure 机器学习操作，以及可执行这些操作的角色：

| Azure 机器学习操作 | “所有者” | 参与者 | 读取器 |
| ---- |:----:|:----:|:----:|
| 创建工作区 | ✓ | ✓ | |
| 共享工作区 | ✓ | |  |
| 创建计算目标 | ✓ | ✓ | |
| 附加计算目标 | ✓ | ✓ | |
| 附加数据存储 | ✓ | ✓ | |
| 运行试验 | ✓ | ✓ | |
| 查看运行/指标 | ✓ | ✓ | ✓ |
| 注册模型 | ✓ | ✓ | |
| 创建映像 | ✓ | ✓ | |
| 部署 Web 服务 | ✓ | ✓ | |
| 查看模型/映像 | ✓ | ✓ | ✓ |
| 调用 Web 服务 | ✓ | ✓ | ✓ |

如果内置角色不符合你的需求，可以创建自定义角色。 自定义角色控制工作区内的所有操作，例如创建计算、提交运行、注册数据存储或部署模型。 自定义角色可以具有对工作区各种资源（如群集、数据存储、模型和终结点）的读取、写入或删除权限。 可以使角色在特定工作区级别、特定资源组级别或特定订阅级别可用。 有关详细信息，请参阅[管理 Azure 机器学习工作区中的用户和角色](how-to-assign-roles.md)。

> [!IMPORTANT]
> Azure 机器学习依赖于其他 Azure 服务，例如 Azure Blob 存储和 Azure Kubernetes 服务。 每个 Azure 服务都有其自己的 Azure RBAC 配置。 若要实现所需级别的访问控制，你可能需要同时对 Azure 机器学习和用于 Azure 机器学习的服务应用 Azure RBAC 配置。

> [!WARNING]
> Azure Active Directory 企业对企业协作支持 Azure 机器学习，但目前 Azure Active Directory 的企业对消费者协作不支持。

### <a name="managed-identities"></a>托管标识

每个工作区还有一个关联的系统分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)，该标识与工作区同名。 托管标识用于安全地访问工作区使用的资源。 它具有针对附加的资源的以下权限：

| 资源 | 权限 |
| ----- | ----- |
| 工作区 | 参与者 |
| 存储帐户 | 存储 Blob 数据参与者 |
| 密钥保管库 | 访问所有密钥、机密和证书 |
| Azure 容器注册表 | 参与者 |
| 包含工作区的资源组 | 参与者 |
| 包含 Key Vault 的资源组（如果不同于包含工作区的资源组） | 参与者 |

不建议管理员撤销托管标识对上表中所述资源的访问权限。 可以使用重新同步密钥操作来恢复访问权限。

对于每个工作区区域，Azure 机器学习将在订阅中创建一个拥有参与者级别访问权限的附加应用程序（名称以 `aml-` 或 `Microsoft-AzureML-Support-App-` 开头）。 例如，在同一订阅中，如果在美国东部和欧洲北部各有一个工作区，则会看到两个这样的应用程序。 Azure 机器学习可以通过这些应用程序来帮助你管理计算资源。

（可选）你可以配置自己的托管标识，以便将其与 Azure 虚拟机和 Azure 机器学习计算群集一起使用。 使用 VM 时，可以从 SDK 使用托管标识来访问工作区，而不是使用各个用户的 Azure AD 帐户。 使用计算群集时，可以使用托管标识来访问运行训练作业的用户可能无权访问的资源，例如安全数据存储。 有关详细信息，请参阅 [Azure 机器学习工作区的身份验证](how-to-setup-authentication.md)。

## <a name="network-security-and-isolation"></a>网络安全性和隔离

若要限制对 Azure 机器学习资源的物理访问，可以使用 Azure 虚拟网络 (VNet)。 VNet 允许你创建与公共 Internet 部分隔离或完全隔离的网络环境。 这会减少解决方案的受攻击面以及数据外泄的几率。

有关详细信息，请参阅以下文档：

* [保护工作区资源](how-to-secure-workspace-vnet.md)
* [保护训练环境](how-to-secure-training-vnet.md)
* [保护推理环境](how-to-secure-inferencing-vnet.md)
* [在受保护的虚拟网络中使用工作室](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>数据加密

Azure 机器学习使用各种计算资源和数据存储。 若要详细了解其中每项如何支持静态数据加密和传输中数据加密，请参阅 [Azure 机器学习的数据加密](concept-data-encryption.md)。

### <a name="microsoft-generated-data"></a>Microsoft 生成的数据

使用自动化机器学习等服务时，Microsoft 可能会生成经过预处理的暂用数据用于训练多个模型。 此数据存储在工作区中的数据存储内，使你可以适当地强制实施访问控制和加密。

你可能还想要加密[从已部署的终结点记录到 Azure Application Insights 实例的诊断信息](how-to-enable-app-insights.md)。

## <a name="monitoring"></a>监视

有多个适用于 Azure 机器学习的监视方案，具体取决于角色和所监视的内容。

| 角色 | 要使用的监视 | 描述 |
| ---- | ----- | ----- |
| 管理员、DevOps、MLOps | [Azure Monitor 指标](#azure-monitor)、[活动日志](#activity-log)、[漏洞扫描](#vulnerability-scanning) | 服务级别信息 |
| 数据科学家、MLOps | [监视运行](#monitor-runs) | 训练运行期间记录的信息 |
| MLOps | [收集模型数据](how-to-enable-data-collection.md)、[使用 Application Insights 进行监视](how-to-enable-app-insights.md) | 部署为 Web 服务或 IoT Edge 模块的模型记录的信息|

### <a name="monitor-runs"></a>监视运行

你可以监视 Azure 机器学习中的试验运行，包括来自训练脚本的日志记录信息。 可以通过 SDK、Azure CLI 和工作室查看此信息。 有关详细信息，请参阅以下文章：

* [启动、监视和取消训练运行](how-to-manage-runs.md)
* [启用日志](how-to-track-experiments.md)
* [查看日志](how-to-monitor-view-training-logs.md)
* [使用 TensorBoard 将运行可视化](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

可以使用 Azure Monitor 指标来查看和监视 Azure 机器学习工作区的指标。 在 [Azure 门户](https://portal.azure.cn)中选择你的工作区，然后选择“指标”：

[![显示工作区示例指标的屏幕截图](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

指标包含有关运行、部署和注册的信息。

有关详细信息，请参阅 [Azure Monitor 中的指标](/azure-monitor/platform/data-platform-metrics)。

### <a name="activity-log"></a>活动日志

可以查看工作区的活动日志，以了解在工作区中执行的各项操作。 日志包含操作名称、事件发起端和时间戳等基本信息。

以下屏幕截图显示了工作区的活动日志：

[![显示工作区活动日志的屏幕截图](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

评分请求详细信息存储在 Application Insights 中。 创建工作区时，订阅中会创建 Application Insights。 记录的信息包括如下所述的字段：

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* 持续时间

> [!IMPORTANT]
> Azure 机器学习工作区中的一些操作不会将信息记录到活动日志中。 例如，不会记录训练运行的启动和模型的注册。
>
> 其中的某些操作显示在工作区的“活动”区域，但这些通知不会指出活动是谁发起的。

### <a name="vulnerability-scanning"></a>漏洞扫描

Azure 安全中心跨混合云工作负荷提供统一的安全管理和高级威胁防护。 对于 Azure 机器学习，应启用对 Azure 容器注册表资源和 Azure Kubernetes 服务资源的扫描。 请参阅 [Azure Kubernetes 服务与安全中心的集成](../security-center/defender-for-kubernetes-introduction.md)。

## <a name="audit-and-manage-compliance"></a>审核和管理合规性

[Azure Policy](/governance/policy) 是一种管理工具，你可用它来确保 Azure 资源符合你的策略。 通过 Azure 机器学习，你可分配以下策略：

* **客户管理的密钥**：审核或强制执行工作区是否必须使用客户管理的密钥。
* **专用链接**：审核工作区是否使用专用终结点与虚拟网络进行通信。

有关 Azure Policy 的详细信息，请参阅 [Azure Policy 文档](/governance/policy/overview)。

若要详细了解特定于 Azure 机器学习的策略，请参阅[使用 Azure Policy 审核和管理合规性](how-to-integrate-azure-policy.md)。

## <a name="resource-locks"></a>资源锁

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>后续步骤

* [使用 TLS 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [将 Azure 机器学习与 Azure 防火墙配合使用](how-to-access-azureml-behind-firewall.md)
* [静态数据加密和传输中数据加密](concept-data-encryption.md)
* [在 Azure 上生成实时建议 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
