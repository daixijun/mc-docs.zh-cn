---
title: 在 Azure 门户中为 VM 创建 FQDN
description: 了解如何在 Azure 门户中为 Linux 虚拟机创建完全限定的域名或 FQDN。
author: Johnnytechn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: v-johya
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de2251398060b834137f45759ab684c965730ce6
ms.sourcegitcommit: d30cf549af09446944d98e4bd274f52219e90583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94638189"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>在 Azure 门户中为 Linux VM 创建完全限定的域名

在 [Azure 门户](https://portal.azure.cn)中创建虚拟机 (VM) 时，此门户会自动为虚拟机创建公共 IP 资源。 可以使用此 IP 地址远程访问 VM。 虽然此门户不会创建完全限定的域名 (FQDN)，但可以在创建 VM 后添加完全限定的域名。 本文演示了创建 DNS 名称或 FQDN 的步骤。

## <a name="create-a-fqdn"></a>创建 FQDN
阅读本文的前提是已创建 VM。 如果需要，可以[在门户中创建 VM](quick-create-portal.md)，也可以[使用 Azure CLI 创建 VM](quick-create-cli.md)。 在 VM 正常运行后，请按照以下步骤操作：

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

现在可以使用此 DNS 名称（例如搭配 `ssh azureuser@mydns.chinanorth.cloudapp.chinacloudapi.cn`）远程连接至 VM。

<!--MOONCAKE: CORRECT ON cloudapp.azure.com to cloudapp.chinacloudapi.cn -->

## <a name="next-steps"></a>后续步骤
VM 已经有公共 IP 和 DNS 名称，现在可以部署通用应用程序框架或服务，例如 nginx、MongoDB、Docker 等等。

也可以深入了解如何[使用 Resource Manager](../../azure-resource-manager/management/overview.md)，以获取生成 Azure 部署的相关提示。

<!-- Update_Description: update meta properties, wording update, update link -->