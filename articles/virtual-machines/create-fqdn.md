---
title: 在 Azure 门户中为 VM 创建 FQDN
description: 了解如何在 Azure 门户中为虚拟机创建完全限定的域名 (FQDN)。
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
origin.date: 11/03/2020
author: rockboyfor
ms.date: 01/04/2021
ms.testscope: yes
ms.testdate: 08/31/2020
ms.author: v-yeche
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0b9fe216b347084dca167512c9b98f05150ab9ae
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857034"
---
<!--Verified successfully from rename articles.-->
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>在 Azure 门户中为 Linux VM 创建完全限定的域名

在 [Azure 门户](https://portal.azure.cn)中创建虚拟机 (VM) 时，此门户会自动为虚拟机创建公共 IP 资源。 可以使用此 IP 地址远程访问 VM。 虽然此门户不会创建完全限定的域名 (FQDN)，但可以在创建 VM 后添加完全限定的域名。 本文演示了创建 DNS 名称或 FQDN 的步骤。 

<!--Not Available om [fully qualified domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)-->

## <a name="create-a-fqdn"></a>创建 FQDN
阅读本文的前提是已创建 VM。 如果需要，可以在门户中创建 [Linux](./linux/quick-create-portal.md) 或 [Windows](./windows/quick-create-portal.md) VM。 在 VM 正常运行后，请按照以下步骤操作：

1. 在门户中选择 VM。 在“DNS 名称”下选择“配置” 。
2. 输入 DNS 名称，然后在页面顶部选择“保存”。
3. 若要返回 VM 概述边栏选项卡，请选择右上角的 X 以关闭“配置”边栏选项卡 。 
4. 验证现在是否正确显示“DNS 名称”。

## <a name="next-steps"></a>后续步骤
VM 已经有公共 IP 和 DNS 名称，现在可以部署通用应用程序框架或服务，例如 nginx、MongoDB 和 Docker。

也可以深入了解如何[使用 Resource Manager](../azure-resource-manager/management/overview.md)，以获取生成 Azure 部署的相关提示。

<!-- Update_Description: update meta properties, wording update, update link -->