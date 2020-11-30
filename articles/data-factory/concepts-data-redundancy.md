---
title: Azure 数据工厂中的数据冗余 | Microsoft Docs
description: 了解 Azure 数据工厂中的元数据冗余机制
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 11/05/2020
ms.date: 11/23/2020
ms.author: v-jay
ms.openlocfilehash: dc44fcbeaf25019e1bd305e9f4e5f7ab924c8c09
ms.sourcegitcommit: c89f1adcf403f5845e785064350136698eed15b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680612"
---
# <a name="azure-data-factory-data-redundancy"></a>**Azure 数据工厂数据冗余**

Azure 数据工厂数据包括元数据（管道、数据集、链接服务、集成运行时和触发器）以及监视数据（管道、触发器和活动运行）。 

Azure 数据工厂数据会被存储并复制到[配对区域](/best-practices-availability-paired-regions#azure-regional-pairs)，以防元数据丢失。 在发生区域性数据中心故障时，Azure 可能会启动 Azure 数据工厂实例的区域性故障转移。 在大多数情况下，你不需要执行任何操作。 Microsoft 管理的故障转移完成后，你将能够在故障转移区域中访问 Azure 数据工厂。 

> [!NOTE]
> Microsoft 管理的故障转移不适用于自承载集成运行时 (SHIR)，因为此基础结构通常由客户管理。 如果 SHIR 是在 Azure VM 上设置的，建议使用 [Azure Site Recovery](/site-recovery/site-recovery-overview) 来处理到另一个区域的 [Azure VM 故障转移](/site-recovery/azure-to-azure-architecture)。

## <a name="data-stores"></a>**数据存储**

使用 Azure 数据工厂可以在本地和云中的数据存储之间移动数据。 若要确保数据存储的业务连续性，应参阅这些数据存储中的每一个的业务连续性建议。 

 

## <a name="see-also"></a>请参阅

- [Azure 区域对](/best-practices-availability-paired-regions)
