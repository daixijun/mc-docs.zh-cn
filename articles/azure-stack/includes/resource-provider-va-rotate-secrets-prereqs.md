---
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: include
origin.date: 10/10/2020
ms.date: 11/09/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 9cc755050891856f37e2cef1e93eb4959c4121f3
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330772"
---
> [!NOTE]
> 当前仅通过 PowerShell 支持增值资源提供程序的机密轮换。 

与 Azure Stack Hub 基础结构一样，增值资源提供程序同时使用内部和外部机密。 机密可以采取多种形式，包括密码和通过 X509 证书维护的加密密钥。 作为操作员，你负责：

- 提供更新的外部机密，如用于保护资源提供程序终结点的新 TLS 证书。
- 定期管理资源提供程序机密轮换。

在准备轮换的过程中：

1. 在获取/续订 X509 证书之前，请查看 [Azure Stack Hub 公钥基础结构 (PKI) 证书要求](../operator/azure-stack-pki-certs.md#certificate-requirements)以获取重要的先决条件信息，包括有关所需 PFX 格式的详细信息。 还要查看在[“可选 PaaS 证书”部分](../operator/azure-stack-pki-certs.md#optional-paas-certificates)中为你的特定增值资源提供程序指定的要求。

2. 在继续操作之前，请[为 Azure Stack Hub 安装 PowerShell Az 模块](../operator/powershell-install-az-module.md)（如果尚未这样做）。 Azure Stack Hub 机密轮换需要 2.0.2-preview 或更高版本。 有关详细信息，请参阅[在 Azure Stack Hub 中从 AzureRM 迁移到 Azure PowerShell Az](../operator/migrate-azurerm-az.md)。
