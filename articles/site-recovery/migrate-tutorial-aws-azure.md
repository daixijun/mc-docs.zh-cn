---
title: 使用 Azure Migrate 将 AWS VM 迁移到 Azure
description: 本文介绍用于将 AWS 实例迁移到 Azure 的选项，并推荐 Azure Migrate。
services: site-recovery
manager: digimobile
ms.service: site-recovery
ms.topic: tutorial
origin.date: 07/27/2019
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: yes
ms.testdate: 09/07/2020
ms.author: v-yeche
ms.custom: MVC
ms.openlocfilehash: 1c85b63ede88ac04c9fae2af3a8a474a09b337bf
ms.sourcegitcommit: a6aca2f2d1295cd5ed07e38bf9f18f8c345ba409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96190310"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>将 Amazon Web Services (AWS) VM 迁移到 Azure

本文介绍用于将 Amazon Web Services (AWS) 实例迁移到 Azure 的选项。

<!--Not Available on ## Migrate with Azure Migrate-->

<!--Not Available on We recommend that you migrate AWS EC2 instances to Azure using the [Azure Migrate](../migrate/migrate-services-overview.md)-->

<!--Not Available on [Learn how](../migrate/tutorial-migrate-aws-virtual-machines.md)-->

## <a name="migrate-with-site-recovery"></a>使用 Site Recovery 进行迁移

Site Recovery 应仅用于灾难恢复，而不用于迁移。

如果已在使用 Azure Site Recovery，并且要继续使用它进行 AWS 迁移，请按照用于设置[物理计算机灾难恢复](physical-azure-disaster-recovery.md)的相同步骤进行操作。


> [!NOTE]
> 运行故障转移进行灾难恢复时，最后一步是提交故障转移。 迁移 AWS 实例时，“提交”选项不相关。 而是选择“完成迁移”选项。 

<!--Not Available on  [Review common questions](../migrate/resources-faq.md)-->
<!-- Update_Description: update meta properties, wording update -->