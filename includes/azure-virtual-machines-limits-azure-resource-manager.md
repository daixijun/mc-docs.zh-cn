---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 02/10/2020
ms.date: 08/03/2020
ms.testscope: no
ms.testdate: 07/06/2020
ms.author: v-yeche
ms.openlocfilehash: 0d90e06283f3bda57025dee8ae7ee193a7000c82
ms.sourcegitcommit: 415fb60a99f3ff239e38670f16e6daab021a675b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2020
ms.locfileid: "97793441"
---
<!--MOONCAKE: CORRECT ON https://www.azure.cn/pricing/details/virtual-machines-->
<!--MOONCAKE: CORRECT ON /billing/billing-add-change-azure-subscription-administrator/-->

| 资源 | 限制 |
| --- | --- |
| 每个[订阅](https://docs.azure.cn/billing/billing-recharge-an-existing-pia-subscription)的 VM 数 |每个区域 25,000 个<sup>1</sup>。 |
| 每个[订阅](https://docs.azure.cn/billing/billing-recharge-an-existing-pia-subscription)的 VM 核心总数 |每个区域 20 个<sup>1</sup> 若要提高限制，请与支持人员联系。 |
| VM 系列（例如 Dv2 和 F）、每个[订阅](https://docs.azure.cn/billing/billing-recharge-an-existing-pia-subscription)的核心数 |每个区域 20 个<sup>1</sup> 若要提高限制，请与支持人员联系。 |
| 每个订阅的[可用性集数](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每个区域 2,500 个。 |
| 每个可用性集的虚拟机数 | 200 |
| 每个订阅的证书数 |无限制<sup>2</sup> |

<!--Not Available on Line 16+1 | Azure Spot VM total cores per subscription -->

<sup>1</sup>默认限制因套餐类别类型（如试用和标准预付费套餐）和系列（如 Dv2 和 F）而异。例如，企业协议订阅的默认值为 350。

<!--Not Available on G series-->

<sup>2</sup>使用 Azure 资源管理器时，证书存储在 Azure Key Vault 中。 对于每个订阅，证书个数没有限制。 对于每个部署（包括单一 VM 或可用性集），证书的限制为 1-MB。

> [!NOTE]
> 虚拟机核心数存在区域总数限制。 区域大小系列（例如 Dv2 和 F）也存在限制。这些限制是单独实施的。 例如，假设某个订阅在中国东部的 VM 核心数限制为 30 个，即 A 系列的核心数限制为 30，D 系列的核心数限制也为 30。 此订阅可以部署 30 个 A1 VM、30 个 D1 VM，或两者的组合，但总共不能超过 30 个核心。 例如，10 个 A1 VM 和 20 个 D1 VM 就是一种组合。  
> 
>

<!-- Update_Description: update meta properties, wording update, update link -->