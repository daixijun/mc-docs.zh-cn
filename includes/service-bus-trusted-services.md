---
title: include 文件
description: 包含文件
services: service-bus-messaging
ms.service: service-bus-messaging
ms.topic: include
origin.date: 11/05/2020
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: yes
ms.testdate: 11/23/2020null
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: e32492d90c4dabe937e561c3febb5fbf51be1232
ms.sourcegitcommit: 054636c134cc0f53c194a6b76668644e18d1c4fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95970626"
---
## <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服务
启用“允许受信任的 Microsoft 服务绕过此防火墙”设置时，将授权以下服务访问你的服务总线资源。

<!--CORRECT ON Trusted Microsoft services-->

| 受信服务 | 支持的使用方案 | 
| --------------- | ------------------------- | 
| Azure 事件网格 | 允许 Azure 事件网格将事件发送到服务总线命名空间中的队列或主题。 还需要执行以下步骤： <ul><li>为主题或域启用系统分配的标识</li><li>为服务总线命名空间中的“Azure 服务总线数据发送方”角色添加标识</li><li>然后，将使用服务总线队列或主题的事件订阅配置为终结点，以使用系统分配的标识。</li></ul> <p>有关详细信息，请参阅[使用托管标识进行事件传递](../articles/event-grid/managed-service-identity.md)</p>|

<!-- Update_Description: new article about service bus trusted services -->
<!--NEW.date: 11/23/2020-->