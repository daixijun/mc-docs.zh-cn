---
title: Azure Stack Hub 上的事件中心操作员概述
description: 了解 Azure Stack Hub 上的事件中心资源提供程序。
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: how-to
origin.date: 12/11/2020
ms.date: 01/11/2021
ms.reviewer: jfggdl
ms.lastreviewed: 12/11/2020
ms.openlocfilehash: 91bae85a5b256ee8427f193718f900b527245a3f
ms.sourcegitcommit: a978c5f2c6b53494d67e7c3c5a44b2aa648219a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629049"
---
# <a name="event-hubs-on-azure-stack-hub-operator-overview"></a>Azure Stack Hub 上的事件中心操作员概述

使用 Azure Stack Hub 上的事件中心可以实现混合云方案。 支持使用基于流式处理和事件的解决方案进行本地处理和 Azure 云处理。 无论方案是混合（联网）还是离线的，解决方案都支持大规模的事件/流处理。 方案仅受群集大小的约束，但你可以根据需要预配群集大小。 

## <a name="features"></a>功能

请参阅 [Azure Stack Hub 用户文档](../user/event-hubs-overview.md)，以便在 Azure Stack 上的事件中心与 Azure 事件中心之间进行功能比较。

## <a name="feature-documentation"></a>功能文档

若要详细了解事件中心用户体验，请参阅 [Azure 事件中心文档](/event-hubs/)。 此文档适用于两个版本的事件中心，并且包含以下主题：

- 有关[事件中心概念](/event-hubs/event-hubs-features)的详细信息
- 如何创建[事件中心](/event-hubs/event-hubs-create#create-an-event-hub)
- 如何[使用 Kafka 协议](/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)进行流式传输


## <a name="next-steps"></a>后续步骤

在开始安装过程之前，请查看 [Azure Stack Hub 上事件中心的容量规划](event-hubs-rp-capacity-planning.md)。 了解容量规划有助于确保用户拥有所需的容量。