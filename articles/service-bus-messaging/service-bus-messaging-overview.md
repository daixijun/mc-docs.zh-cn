---
title: Azure 服务总线消息传送概述 | Azure
description: 本文粗略概述了 Azure 服务总线（一种完全托管的企业集成消息代理）。
ms.topic: overview
origin.date: 11/20/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: 6ae7188f0ebe7f0db05f232d42702458e93ffdb2
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105245"
---
# <a name="what-is-azure-service-bus"></a>什么是 Azure 服务总线？

Azure 服务总线是一个完全托管的企业消息代理，其中包含消息队列和公用订阅主题。 服务总线用于使应用程序和服务彼此分离、在争用辅助角色之间进行负载平衡、跨服务和应用程序边界安全地路由和传输数据和控制，以及协调需要高度可靠性的事务性工作。 

数据通过消息在不同的应用程序和服务之间传输。 消息是使用元数据修饰的容器，可以包含任何类型的信息，其中包括使用常用格式（例如 JSON、XML、Apache Avro 或纯文本）编码的结构化数据。 

一些常见的消息传送方案包括：

* *消息*。 传输业务数据，例如销售或采购订单、日志或库存变动。
* *分离应用程序*。 提高应用程序和服务的可靠性和可伸缩性。 制作者和使用者不必同时在线或可用，且[负载经过平衡](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling)，这样一来，流量高峰不会使服务负担过重。 
* *负载均衡*。 允许多个[竞争性使用者](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers)同间从队列读取内容，每个使用者都安全地获取对特定消息的独占所有权。 
* *主题和订阅*。 在[发布服务器和订阅服务器](https://docs.microsoft.com/azure/architecture/patterns/publisher-subscriber)之间启用 1:n 关系，使订阅服务器可以从已发布的消息流中选择特定消息。
* *Transactions*。 允许从一个队列中获取消息，并将处理结果发布到一个或多个不同的队列中，然后从原始队列中删除输入消息，所有操作都在原子事务的作用域中执行。 这样可确保事务的结果仅在成功时才对下游使用者可见（包括成功处置输入消息），从而允许使用一次性处理语义。 对于更大的解决方案上下文中的[补偿事务](https://docs.microsoft.com/azure/architecture/patterns/compensating-transaction)模式，此事务模型是一个可靠的基础。 
* *消息会话*。 对于需要严格消息排序或消息延迟的工作流和多路复用传输，实现大规模协调。

如果你熟悉 IBM MQ、VMWare RabbitMQ、Red Hat A-MQ 或 Apache ActiveMQ 等消息代理，你会发现 Azure 服务总线的概念与你所知的概念非常相似。 由于服务总线是一种平台即服务产品/服务，其与这些软件包的主要区别在于，你不必担心如何放置日志和管理磁盘空间、处理备份、持续修补操作系统或产品，也不必担心硬件故障以及故障转移到保留计算机。 Azure 会为你完成这些琐事。 

服务总线的主要线路协议为[高级消息队列协议 (AMQP) 1.0](service-bus-amqp-overview.md)，这是由大型平台供应商和用户联盟开发的开放 ISO/IEC 标准，允许客户编写可针对服务总线和本地代理（例如 ActiveMQ 或 RabbitMQ）交替工作的应用程序。 如果你想生成这样的抽象，[AMQP 协议指南](service-bus-amqp-protocol-guide.md)提供了详细信息。

[服务总线高级层](service-bus-premium-messaging.md)完全符合 Java/Jakarta EE [Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md) API 的要求，并且服务总线标准层支持专注于队列的 JMS 1.1 子集。 JMS 是 Java 开发人员的消息代理的一般抽象，可与许多应用程序和框架（包括热门的 Spring 框架）集成。 从上文提到的其中一个代理切换到 Azure 服务总线通常只涉及重新创建队列和主题的拓扑以及更改客户端提供程序依赖关系和配置，例如，如 [ActiveMQ 迁移指南](migrate-jms-activemq-to-servicebus.md)中所述。

## <a name="service-bus-concepts-and-terminology"></a>服务总线概念和术语 

本部分讨论了服务总线的概念和术语。
### <a name="namespaces"></a>命名空间

命名空间是一个适用于所有消息传送组件的容器。 多个队列和主题可以位于一个命名空间中，命名空间通常用作应用程序容器。 

<!--Not Available on [Azure availability zones](../availability-zones/az-overview.md)-->

### <a name="queues"></a>队列

消息可以发送到队列，也可以从其接收。 在能够使用接收应用程序接收并处理消息之前，可以通过队列来存储消息。

:::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-queue.png" alt-text="队列":::

队列中的消息会排队，并在到达时加盖时间戳。 代理接受消息后，该消息将长期保留在三冗余存储中；如果命名空间启用了区域，则这些冗余存储会分布在多个可用性区域中。 将消息报告给客户端后，服务总线不会将消息保留在内存或易失存储中。

消息以拉取模式传送，即仅按请求传送消息。 不同于其他某些云队列的繁忙轮询模式，拉取操作可能会长期存续，消息可用时才会完成。 

### <a name="topics"></a>主题

也可通过主题发送和接收消息。 队列通常用于点到点通信，而主题则用于发布/订阅方案。

:::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="主题":::

主题可以有多个独立的订阅，这些订阅附加到主题，其他方面与来自接收方的队列完全一样。 主题的订阅者可以收到发送到该主题的每个消息的副本。 订阅是命名实体。 订阅默认持久存续，但可为其配置过期时间，并在过期后自动将其删除。 通过 JMS API，服务总线高级版还允许创建在连接期间存在的易失订阅。

你可以定义订阅的规则。 订阅规则有一个筛选器，用于定义要复制到订阅中的消息应满足的条件，以及可以修改消息元数据的可选操作。  有关详细信息，请参阅[主题筛选器和操作](topic-filters.md)。 此功能在以下情况下很有用：

- 不要让订阅接收发送到某个主题的所有消息。
- 最好在消息通过订阅时使用额外的元数据来标记消息。

## <a name="advanced-features"></a>高级功能

服务总线包括用于解决更复杂消息传送问题的高级功能。 以下部分说明了这几个功能。

### <a name="message-sessions"></a>消息会话

若要在服务总线中创建先进先出 (FIFO) 保证，请使用会话。 使用消息会话，可以排他、有序的方式处理一系列无限多的相关消息。 为了允许在高度缩放的高可用性系统中处理会话，会话功能还允许存储会话状态，以便在处理程序间安全地移动会话。 有关详细信息，请参阅[消息会话：先进先出 (FIFO)](message-sessions.md)。

### <a name="autoforwarding"></a>自动转发

自动转发功能将序列或订阅链接到相同命名空间中的主题。 使用此功能时，服务总线会自动将消息从队列或订阅移动到目标队列或主题。 这样的移动都通过事务完成。 有关详细信息，请参阅[使用自动转发链接服务总线实体](service-bus-auto-forwarding.md)。

### <a name="dead-letter-queue"></a>死信队列

所有服务总线队列和主题订阅都有关联的死信队列 (DLQ)。 DLQ 包含满足以下条件的消息： 

- 无法成功传递到任何接收方。
- 已超时。
- 由接收方应用程序显式排除。 

死信队列中的消息将带有关于其进入队列原因的批注。 死信队列具有特殊的终结点，其他方面与任何常规队列类似。 应用程序或工具可以浏览 DLQ 或取消排队。 你也可以自动转发出死信队列。 有关详细信息，请参阅[服务总线死信队列概述](service-bus-dead-letter-queues.md)。

### <a name="scheduled-delivery"></a>计划的传递

可以将消息提交到队列或主题以进行延迟处理，并设置消息可用的时间。 还可以取消计划的消息。 有关详细信息，请参阅[计划的消息](message-sequencing.md#scheduled-messages)。

### <a name="message-deferral"></a>消息延迟

队列或订阅客户端可以延迟一段时间检索收到的消息。 消息可能是按预期顺序发布的，而客户端希望等到收到另一条消息。 延迟的消息保留在队列或订阅中，必须使用其服务分配的序列号显式重新激活。 有关详细信息，请参阅[消息延迟](message-deferral.md)。

### <a name="batching"></a>批处理

通过客户端批处理，队列或主题客户端可以累积一组消息并一起传输它们。 这样做通常是为了节约带宽或增加吞吐量。 有关详细信息，请参阅[客户端批处理](service-bus-performance-improvements.md#client-side-batching)。

### <a name="transactions"></a>事务

一个事务将两个或更多操作组合成执行作用域。 服务总线允许对针对单个事务作用域内的多个消息传递实体的操作进行分组。 消息实体可以是队列、主题或订阅。 有关详细信息，请参阅[服务总线事务处理概述](service-bus-transactions.md)。

### <a name="autodelete-on-idle"></a>出现空闲队列时自动删除

可以使用“出现空闲队列时自动删除”功能指定一个空闲时间间隔，该时间间隔过后系统会自动删除队列或主题订阅。 最短持续时间为 5 分钟。 有关详细信息，请参阅 [QueueDescription.AutoDeleteOnIdle 属性](https://docs.azure.cn/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)。

### <a name="duplicate-detection"></a>重复检测

重复检测功能使发送方能够再次发送相同的消息，并让代理能够删除可能的重复项。 重复检测基于跟踪消息的 `message-id` 属性，这意味着，当重新发送消息时，应用程序需要注意使用相同的值，该值可能是直接从某些特定于应用程序的上下文中派生的。 有关详细信息，请参阅[重复检测](duplicate-detection.md)。

### <a name="geo-disaster-recovery"></a>异地灾难恢复

在 Azure 区域遭遇停机时，可以使用灾难恢复功能在其他区域或数据中心进行数据处理，以实现连续运行。 此功能在次要区域中保留可用命名空间的结构化镜像，并允许命名空间标识切换到辅助命名空间。 可用性事件平息后，已发布的消息仍保留在以前的主命名空间中以便恢复。 有关详细信息，请参阅 [Azure 服务总线异地灾难恢复](service-bus-geo-dr.md)。

### <a name="security"></a>安全性

服务总线支持标准 [AMQP 1.0](service-bus-amqp-overview.md) 和 [HTTP 或 REST](https://docs.microsoft.com/rest/api/servicebus/) 协议及其各自的安全功能，包括传输层安全性 (TLS)。 可以使用[共享访问签名](service-bus-sas.md)或 [Azure Active Directory](service-bus-authentication-and-authorization.md) 基于角色的安全性，向客户端授予访问权限。 

为针对意外的流量进行保护，服务总线提供了多种[安全功能](network-security.md)，例如 IP 防火墙和与虚拟网络的集成。 

## <a name="client-libraries"></a>客户端库

可通过 Azure SDK 使用完全受支持的服务总线客户端库。

- [适用于 .NET 的 Azure 服务总线](https://docs.azure.cn/dotnet/api/overview/service-bus?preserve-view=true)
- [适用于 Java 的 Azure 服务总线库](https://docs.azure.cn/java/api/overview/servicebus?view=azure-java-stable&preserve-view=true)
- [适用于 Java JMS 2.0 的 Azure 服务总线提供程序](how-to-use-java-message-service-20.md)
- [适用于 JavaScript 和 TypeScript 的 Azure 服务总线模块](https://docs.microsoft.com/javascript/api/overview/azure/service-bus?view=azure-node-latest&preserve-view=true)
- [适用于 Python 的 Azure 服务总线库](https://docs.microsoft.com/python/api/overview/azure/servicebus?view=azure-python&preserve-view=true)

[Azure 服务总线的主要协议是 AMQP 1.0](service-bus-amqp-overview.md)，可从兼容 AMQP 1.0 的任何协议客户端使用它。 若干开源 AMQP 客户端具有显式演示服务总线互操作性的示例。 查看 [AMQP 1.0 协议指南](service-bus-amqp-protocol-guide.md)，了解如何通过 AMQP 1.0 客户端直接使用服务总线功能。

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>集成

服务总线与许多 Azure 服务完全集成，例如：

<!--Correct on Service Bus fully integrates with many Azure services-->

* [事件网格](https://www.azure.cn/event-grid/)
* [逻辑应用](https://www.azure.cn/home/features/logic-apps/)
* [Azure Functions](https://www.azure.cn/home/features/azure-functions/)

    <!--* [Power Platform](https://powerplatform.microsoft.com/)-->
    <!--* [Dynamics 365](https://dynamics.microsoft.com)-->
    
* [Azure 流分析](https://www.azure.cn/home/features/stream-analytics/)

## <a name="next-steps"></a>后续步骤

有关服务总线消息传送入门的内容，请参阅以下文章：

* 若要比较 Azure 消息服务，请参阅服务[服务对比](../event-grid/compare-messaging-services.md?toc=%2fservice-bus-messaging%2ftoc.json&bc=%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)。
* 尝试 [.NET](service-bus-dotnet-get-started-with-queues.md)、[Java](service-bus-java-how-to-use-queues.md) 或 [JMS](service-bus-java-how-to-use-jms-api-amqp.md) 的快速入门。
* 若要管理服务总线资源，请参阅[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/releases)。
* 若要详细了解标准和高级层及其定价，请参阅[服务总线定价](https://www.azure.cn/pricing/details/service-bus/)。
* 若要了解高级层的性能和延迟，请参阅[高级消息传送](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)。

<!-- Update_Description: update meta properties, wording update, update link -->