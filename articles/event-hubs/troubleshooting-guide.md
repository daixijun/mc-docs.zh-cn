---
title: 排查连接问题 - Azure 事件中心 | Microsoft Docs
description: 本文介绍如何排查 Azure 事件中心的连接问题。
ms.topic: article
origin.date: 06/23/2020
ms.date: 12/02/2020
ms.author: v-tawe
ms.openlocfilehash: d246c4b356599cfc25aae60bd5d7714b1cb7febe
ms.sourcegitcommit: f436acd1e2a0108918a6d2ee9a1aac88827d6e37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508942"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>排查连接问题 - Azure 事件中心
客户端应用程序无法连接到事件中心的原因有很多。 你遇到的连接问题可能是永久性的，也可能是暂时性的。 如果问题一直发生（永久性的），则可能需要检查连接字符串、组织的防火墙设置、IP 防火墙设置、网络安全设置（服务终结点、专用终结点等），等等。 对于暂时性问题，升级到最新版本的 SDK、运行命令来检查丢弃的数据包以及获取网络跟踪可能有助于解决问题。 

本文提供了排查 Azure 事件中心的连接问题的技巧。 

## <a name="troubleshoot-permanent-connectivity-issues"></a>排查永久性连接问题
如果应用程序根本无法连接到事件中心，请按此部分的步骤操作来解决问题。 

### <a name="check-if-there-is-a-service-outage"></a>检查是否存在服务中断
在 [Azure 服务状态站点](https://azure.microsoft.com/status/)上检查是否存在 Azure 事件中心服务中断的情况。

### <a name="verify-the-connection-string"></a>验证连接字符串 
验证你使用的连接字符串是否正确。 请参阅[获取连接字符串](event-hubs-get-connection-string.md)，以便使用 Azure 门户、CLI 或 PowerShell 获取连接字符串。 

对于 Kafka 客户端，请验证是否正确配置了 producer.config 或 consumer.config 文件。 有关详细信息，请参阅[在事件中心内使用 Kafka 发送和接收消息](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs)。

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>验证网络安全组中是否允许使用 AzureEventGrid 服务标记
如果你的应用程序在子网内运行，并且存在关联的网络安全组，请确认是否允许 Internet 出站或是否允许 AzureEventGrid 服务标记。 请参阅[虚拟网络服务标记](../virtual-network/service-tags-overview.md)并搜索 `EventHub`。

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>检查应用程序是否需要在 vnet 的特定子网中运行
确认应用程序在有权访问该命名空间的虚拟网络子网中运行。 如果没有，请在有权访问命名空间的子网中运行应用程序或将运行应用程序的计算机的 IP 地址添加到 [IP 防火墙](event-hubs-ip-filtering.md)。 

为事件中心命名空间创建虚拟网络服务终结点时，该命名空间仅接受来自绑定到服务终结点的子网的流量。 此行为有一个例外。 可以在 IP 防火墙中添加特定 IP 地址，以便启用对事件中心公共终结点的访问权限。 有关详细信息，请参阅[网络服务终结点](event-hubs-service-endpoints.md)。

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>检查命名空间的 IP 防火墙设置
确认运行应用程序的计算机的公共 IP 地址未被 IP 防火墙阻止。  

默认情况下，只要请求附带有效的身份验证和授权，就可以从 Internet 访问事件中心命名空间。 使用 IP 防火墙，可以将其进一步限制为采用 CIDR（无类域间路由）表示法的一组 IPv4 地址或一个 IPv4 地址。

IP 防火墙规则应用于事件中心命名空间级别。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与事件中心命名空间上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

有关详细信息，请参阅[为 Azure 事件中心命名空间配置 IP 防火墙规则](event-hubs-ip-filtering.md)。 若要检查你是否有 IP 筛选、虚拟网络或证书链问题，请参阅[排查网络相关问题](#troubleshoot-network-related-issues)。

<!-- ### Check if the namespace can be accessed using only a private endpoint
If the Event Hubs namespace is configured to be accessible only via private endpoint, confirm that the client application is accessing the namespace over the private endpoint. 

[Azure Private Link service](../private-link/private-link-overview.md) enables you to access Azure Event Hubs over a **private endpoint** in your virtual network. A private endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. The private endpoint uses a private IP address from your virtual network, effectively bringing the service into your virtual network. All traffic to the service can be routed through the private endpoint, so no gateways, NAT devices, ExpressRoute or VPN connections, or public IP addresses are needed. Traffic between your virtual network and the service traverses over the Microsoft backbone network, eliminating exposure from the public Internet. You can connect to an instance of an Azure resource, giving you the highest level of granularity in access control.

For more information, see [Configure private endpoints](private-link-service.md). See the **Validate that the private endpoint connection works** section to confirm that a private endpoint is used. 
-->

### <a name="troubleshoot-network-related-issues"></a>排查网络相关问题
若要排查事件中心的网络相关问题，请执行以下步骤： 

浏览至 `https://<yournamespacename>.servicebus.chinacloudapi.cn/` 或使用 [wget](https://www.gnu.org/software/wget/)。 这可帮助检查是否存在 IP 筛选或虚拟网络或证书链问题（使用 Java SDK 时最常见）。

**成功消息** 的示例：

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

**失败错误消息** 的示例：

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>排查暂时性连接问题
如果遇到间歇性连接问题，请参阅以下部分来了解排查技巧。 

### <a name="use-the-latest-version-of-the-client-sdk"></a>使用最新版本的客户端 SDK
在较高的 SDK 版本（高于你所用版本）中，一些暂时性的连接问题可能已经修复。 确保在应用程序中使用最新版本的客户端 SDK。 SDK 通过新的/更新的功能和 bug 修复持续进行改进，因此请始终使用最新的包进行测试。 请查看发行说明，了解已修复的问题以及已添加/更新的功能。 

有关客户端 SDK 的信息，请参阅 [Azure 事件中心 - 客户端 SDK](sdks.md) 一文。 

### <a name="run-the-command-to-check-dropped-packets"></a>运行命令来检查丢弃的数据包
出现间歇性连接问题时，请运行以下命令，检查是否存在任何丢弃的数据包。 此命令会尝试通过服务每隔 1 秒建立 25 个不同的 TCP 连接。 然后，可以检查其中有多少成功/失败，还可以查看 TCP 连接延迟。 可以从[此处](https://docs.microsoft.com/sysinternals/downloads/psping)下载 `psping` 工具。

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.chinacloudapi.cn:5671 -nobanner
```
如果使用的是其他工具（如 `tnc`、`ping` 等），则可以使用等效的命令。 

如果上述步骤没有帮助，请获取网络跟踪，并使用 [Wireshark](https://www.wireshark.org/) 之类的工具对其进行分析。 如果需要，请联系 [Microsoft 支持部门](https://support.azure.cn/)。

### <a name="service-upgradesrestarts"></a>服务升级/重启
由于后端服务升级和重启，可能会出现暂时性连接问题。 出现这种情况时，可能会看到以下症状： 

- 传入的消息/请求可能会减少。
- 日志文件可能包含错误消息。
- 应用程序可能会在几秒内断开与服务的连接。
- 可能会暂时限制请求。

如果应用程序代码使用 SDK，则重试策略已内置且处于活动状态。 应用程序会重新连接，此操作不会对应用程序/工作流产生重大影响。 捕获这些暂时性错误，后退然后重试调用，将确保代码能够从这些暂时性错误中复原。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

* [排查身份验证和授权问题](troubleshoot-authentication-authorization.md)
