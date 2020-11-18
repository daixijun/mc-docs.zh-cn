---
title: Azure Spring Cloud 的服务计划和配额
description: 了解 Azure Spring Cloud 的服务配额和服务计划
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: v-junlch
ms.custom: devx-track-java
ms.openlocfilehash: e4ac425e9e76b0555b3ef0eb3b2314f73bde39ff
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94327996"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure Spring Cloud 的配额和服务计划

所有 Azure 服务都设置针对资源和功能的默认限制和配额。   Azure Spring Cloud 提供两个定价层：“基本”和“标准”。 本文将详细介绍这两个层的限制。

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure Spring Cloud 服务层级和限制

| 资源 | 基本 | Standard
------- | ------- | -------
vCPU | 每个服务实例 1 个 | 每个服务实例 4 个
内存 | 每个服务实例 2 GB | 每个服务实例 8 GB
每个区域每个订阅的 Azure Spring Cloud 服务实例数 | 10 | 10
每个 Azure Spring Cloud 服务实例的应用实例总数 | 25 | 500
永久性卷 | 1 GB/应用 x 10 个应用 | 50 GB/应用 x 10 个应用

## <a name="next-steps"></a>后续步骤

某些默认限制可以提高。 如果你的设置要求提高默认限制，请[创建支持请求](https://support.azure.cn/zh-cn/support/support-azure/)。

