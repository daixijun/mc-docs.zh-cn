---
title: include 文件
description: include 文件
services: service-bus-messaging
ms.service: service-bus-messaging
ms.topic: include
origin.date: 10/15/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: yes|no
ms.testdate: 12/14/2020null
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 75e09318049380eb1e80e18659803ac4c2023bbb
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97104803"
---
## <a name="prerequisites"></a>先决条件
如果没有 [Azure 订阅](https://docs.azure.cn/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，请在开始前创建[试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间
请遵照以下教程中的说明：[快速入门：使用 Azure 门户创建服务总线主题和主题的订阅](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)来执行以下任务：

- 创建一个 **高级** 服务总线命名空间。 
- 获取连接字符串。 
- 创建服务总线主题。
- 创建主题的订阅。 本教程只需要一个订阅，因此无需创建订阅 S2 和 S3。 

## <a name="send-messages-to-the-service-bus-topic"></a>向服务总线主题发送消息
在此步骤中，你将使用示例应用程序将消息发送到在上一步中创建的服务总线主题。 

1. 克隆 [GitHub azure-service-bus 存储库](https://github.com/Azure/azure-service-bus/)。
2. 在 Visual Studio 中转到 \samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration 文件夹，然后打开 SBEventGridIntegration.sln 文件 。
3. 在解决方案资源管理器窗口中，展开“MessageSender”项目，然后选择“Program.cs” 。
4. 将 `<SERVICE BUS NAMESPACE - CONNECTION STRING>` 替换为服务总线命名空间的连接字符串，并将 `<TOPIC NAME>` 替换为主题的名称。 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. 生成并运行程序，以将 5 条测试消息 (`const int numberOfMessages = 5;`) 发送到服务总线主题。 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="控制台应用输出":::

<!-- Update_Description: new article about service bus event grid prerequisites -->
<!--NEW.date: 12/14/2020-->