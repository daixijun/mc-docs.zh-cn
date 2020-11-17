---
title: 为 Azure Stack Hub 上的 AKS 引擎启用服务帐户令牌卷投影
description: 了解如何为 Azure Stack Hub 上的 AKS 引擎启用服务帐户令牌卷投影
author: WenJason
ms.topic: article
origin.date: 10/23/2020
ms.date: 11/09/2020
ms.author: v-jay
ms.reviewer: waltero
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 38b2d96c316e2291bb5ea8c24c11e970e2fd8e8f
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330703"
---
# <a name="enabling-service-account-token-volume-projection-for-the-aks-engine-on-azure-stack-hub"></a>为 Azure Stack Hub 上的 AKS 引擎启用服务帐户令牌卷投影

Istio 是可配置的开源服务网格层，用于连接、监视和保护 Kubernetes 群集中的容器。 Istio 1.3 及更高版本使用 Kubernetes 中名为服务帐户令牌卷投影的功能。 默认情况下，AKS 引擎部署的 Kubernetes 群集中不启用此功能。 在本文中，你可以在 `apiServerConfig` 元素中找到 API 模型 json 属性，该元素显示为群集启用服务帐户令牌卷投影时所需的 Kubernetes API 服务器标志。

有关服务帐户令牌卷投影的更多信息，请参阅[服务帐户令牌卷投影](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection)。

## <a name="enable-service-account-token-volume-projection"></a>启用服务帐户令牌卷投影

若要启用服务帐户令牌卷投影，请将以下设置添加到 API 模型 json 文件。 

```json
{
    "kubernetesConfig": {
        "apiServerConfig": {
            "--service-account-api-audiences": "api,istio-ca",
            "--service-account-issuer": "kubernetes.default.svc",
            "--service-account-signing-key-file": "/etc/kubernetes/certs/apiserver.key"
        }
    }
}
```

> [!Note]  
> 可能需要根据具体使用情况调整 `--service-account-api-audiences` 和 `--service-account-issuer`。

有关完整的示例 API 模型，请参阅 [istio.json](https://github.com/Azure/aks-engine/blob/master/examples/service-mesh/istio.json)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)
- [升级 Azure Stack Hub 上的 Kubernetes 群集](azure-stack-kubernetes-aks-engine-upgrade.md)
