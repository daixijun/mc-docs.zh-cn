---
title: 消息复制任务和应用程序 - Azure 服务总线 | Azure Docs
description: 本文概述了如何使用 Azure Functions 生成消息复制任务和应用程序
ms.service: service-bus-messaging
ms.topic: article
author: rockboyfor
ms.date: 01/11/2021
ms.testscope: yes|no
ms.testdate: 01/11/2021null
ms.author: v-yeche
ms.openlocfilehash: 48d34478b0b5b656109cca8cb5213a6160455b2a
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023914"
---
<!--Verified Successfully-->
# <a name="message-replication-tasks-and-applications"></a>消息复制任务和应用程序

如[消息复制和跨区域联合](service-bus-federation-overview.md)一文中所述，在服务总线实体对之间以及服务总线与其他消息源和目标之间的消息序列复制通常依赖于 Azure Functions。

[Azure Functions](../azure-functions/functions-overview.md) 是一种可缩放且可靠的执行环境，用于配置和运行无服务器应用程序，包括[消息复制和联合](service-bus-federation-overview.md)任务。

在本概述中，你将了解 Azure Functions 针对此类应用程序提供的内置功能、可为转换任务调整和修改的代码块，以及如何配置 Azure Functions 应用程序，使其与服务总线和其他 Azure 消息传送服务完美集成。 有关更多详细信息，本文将指向 Azure Functions 文档。

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]



<!-- Update_Description: new article about service bus federation replicator functions -->
<!--NEW.date: 01/11/2021-->