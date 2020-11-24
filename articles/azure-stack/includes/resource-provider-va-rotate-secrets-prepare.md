---
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: include
origin.date: 10/10/2020
ms.date: 11/09/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 29edc11d254a6b9ac52540fb44ac9f249e1958c4
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330773"
---
接下来，创建或续订 TLS 证书，以保护增值资源提供程序终结点：

1. 完成[为资源提供程序的证书续订生成证书签名请求 (CSR)](../operator/azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal) 中的步骤。 在这里，可使用 Azure Stack Hub 就绪性检查器工具来创建 CSR。 请确保在“为其他 Azure Stack Hub 服务生成证书请求”步骤中为资源提供程序运行正确的 cmdlet。 例如，`New-AzsHubEventHubsCertificateSigningRequest` 用于事件中心。 完成后，将生成的 .REQ 文件提交到新证书的证书颁发机构 (CA)。

2. 收到来自 CA 的证书文件后，请完成[为部署或轮换准备证书](../operator/azure-stack-prepare-pki-certs.md)中的步骤。 再次使用就绪性检查器工具处理从 CA 返回的文件。

3. 最后，完成[验证 Azure Stack Hub PKI 证书](../operator/azure-stack-validate-pki-certs.md)中的步骤。 再次使用就绪性检查器工具对新证书执行验证测试。


