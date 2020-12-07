---
title: 使用 Azure 门户创建服务总线队列
description: 在本快速入门中，你将了解如何使用 Azure 门户创建服务总线命名空间并在其中创建队列。
author: rockboyfor
ms.topic: quickstart
origin.date: 08/12/2020
ms.date: 08/24/2020
ms.testscope: no
ms.testdate: 07/20/2020
ms.author: v-yeche
ms.openlocfilehash: 5044ca10a2eae5516ddb2f810e6f0ed2c92e1f05
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96507315"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>使用 Azure 门户创建服务总线命名空间和队列
本快速入门介绍如何使用 [Azure 门户][Azure portal]创建服务总线命名空间和队列。 本快速入门还介绍了如何获取客户端应用程序向队列发送消息或从队列接收消息所使用的授权凭据。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保你有一个 Azure 订阅。 如果没有 Azure 订阅，可在开始前创建一个[试用帐户][]。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>后续步骤
在本文中，你创建了服务总线命名空间并在其中创建了队列。 若要了解如何向队列发送消息或从队列接收消息，请参阅“发送和接收消息”部分中的以下某个快速入门。 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues-new-package.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[试用帐户]: https://www.microsoft.com/china/azure/index.html?fromtype=cn
[Azure portal]: https://portal.azure.cn/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png

<!-- Update_Description: update meta properties, wording update, update link -->