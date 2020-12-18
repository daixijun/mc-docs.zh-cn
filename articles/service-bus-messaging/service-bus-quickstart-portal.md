---
title: 使用 Azure 门户创建服务总线队列
description: 在本快速入门中，你将了解如何使用 Azure 门户创建服务总线命名空间并在其中创建队列。
ms.topic: quickstart
origin.date: 08/12/2020
author: rockboyfor
ms.date: 12/15/2020
ms.testscope: no
ms.testdate: 07/20/2020
ms.author: v-yeche
ms.openlocfilehash: 476f68dba7da9a91d4233d6ac097f61707c44599
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105238"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>使用 Azure 门户创建服务总线命名空间和队列
本快速入门介绍如何使用 [Azure 门户][Azure portal]创建服务总线命名空间和队列。 本快速入门还介绍了如何获取客户端应用程序向队列发送消息或从队列接收消息所使用的授权凭据。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保你有一个 Azure 订阅。 如果没有 Azure 订阅，可先创建一个[试用版订阅][trial subscription]，再开始操作。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>后续步骤
在本文中，你创建了服务总线命名空间并在其中创建了队列。 若要了解如何向队列发送消息或从队列接收消息，请参阅“发送和接收消息”部分中的以下某个快速入门。 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[trial subscription]: https://www.microsoft.com/china/azure/index.html?fromtype=cn/
[Azure portal]: https://portal.azure.cn/

<!--Not Avaialble on [service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png-->

<!-- Update_Description: update meta properties, wording update, update link -->