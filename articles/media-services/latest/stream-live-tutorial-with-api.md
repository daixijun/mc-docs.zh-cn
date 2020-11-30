---
title: 使用媒体服务 v3 进行实时流式传输
titleSuffix: Azure Media Services
description: 了解如何通过 Azure 媒体服务 v3 进行实时流式传输。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
origin.date: 06/13/2019
ms.date: 11/30/2020
ms.author: v-jay
ms.openlocfilehash: 1eb8d7c786bdbf7f592fb7e44130afbf317b9c5c
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300825"
---
# <a name="tutorial-stream-live-with-media-services"></a>教程：使用媒体服务进行实时流式传输

> [!NOTE]
> 尽管本教程使用了 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 示例，但 [REST API](https://docs.microsoft.com/rest/api/media/liveevents)、[CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) 或其他受支持的 [SDK](media-services-apis-overview.md#sdks) 的常规步骤是相同的。

在 Azure 媒体服务中，[直播活动](https://docs.microsoft.com/rest/api/media/liveevents)负责处理实时传送视频流内容。 直播活动提供输入终结点（引入 URL），然后由你将该终结点提供给实时编码器。 直播活动从实时编码器接收实时输入流，并通过一个或多个[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)使其可用于流式处理。 直播活动还提供可用于预览的预览终结点（预览 URL），并在进一步处理和传递流之前对流进行验证。 本教程演示如何使用 .NET Core 创建 **直通** 类型的直播活动。

本教程介绍如何：

> [!div class="checklist"]
> * 下载本主题中所述的示例应用。
> * 检查执行实时传送视频流的代码。
> * 使用 Azure Media Player 在 https://ampdemo.azureedge.net 观看事件。
> * 清理资源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

以下项目是完成本教程所需具备的条件：

- 安装 Visual Studio Code 或 Visual Studio。
- [创建媒体服务帐户](./create-account-howto.md)。<br/>请务必记住用于资源组名称和媒体服务帐户名称的值。
- 遵循[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 中的步骤并保存凭据。 你将需要使用这些凭据来访问 API。
- 一个用于广播事件的相机或设备（例如便携式计算机）。
- 一个本地实时编码器，用于将来自相机的信号转换为发送到媒体服务实时传送视频流服务的流，请参阅[建议的本地实时编码器](recommended-on-premises-live-encoders.md)。 流必须为 **RTMP** 或“平滑流式处理”  格式。

> [!TIP]
> 确保在继续操作之前查看[使用媒体服务 v3 的实时传送视频流](live-streaming-overview.md)。 

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将包含流式处理 .NET 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

实时传送视频流示例位于 [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) 文件夹中。

打开下载的项目中的 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json)。 将值替换为从[访问 API](./access-api-howto.md) 获得的凭据。

> [!IMPORTANT]
> 此示例为每个资源使用唯一的后缀。 如果取消调试操作或者中途终止应用，则帐户中会有多个直播活动。 <br/>请务必停止正在运行的直播活动， 否则，将会对你“收费”  ！

## <a name="examine-the-code-that-performs-live-streaming"></a>检查执行实时传送视频流的代码

此部分讨论 MediaV3LiveApp 项目的 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) 文件中定义的函数  。

此示例为每个资源创建唯一的后缀，因此即使在没有清理的情况下运行示例多次，也不会有名称冲突。

> [!IMPORTANT]
> 此示例为每个资源使用唯一的后缀。 如果取消调试操作或者中途终止应用，则帐户中会有多个直播活动。 <br/>
> 请务必停止正在运行的直播活动， 否则，将会对你“收费”  ！

### <a name="start-using-media-services-apis-with-net-sdk"></a>开始结合使用媒体服务 API 与 .NET SDK

若要开始将媒体服务 API 与 .NET 结合使用，需要创建 AzureMediaServicesClient 对象  。 若要创建对象，需要提供客户端所需凭据以使用 Azure AD 连接到 Azure。 在本文开头克隆的代码中，**GetCredentialsAsync** 函数根据本地配置文件中提供的凭据创建 ServiceClientCredentials 对象。 

```c#
private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
{
    var credentials = await GetCredentialsAsync(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-a-live-event"></a>创建直播活动

本部分介绍如何创建 **直通** 类型的实时事件（LiveEventEncodingType 设置为 None）。 有关实时事件的可用类型的详细信息，请参阅[实时事件类型](live-events-outputs-concept.md#live-event-types)。 
 
可能需要在创建直播活动时指定的一些事项包括：

* 媒体服务位置。
* 直播活动的流式处理协议（目前支持 RTMP 和平滑流式处理协议）。<br/>运行直播活动或其关联的实时输出时，无法更改协议选项。 如果需要其他协议，请为每个流式处理协议创建单独的直播活动。  
* 对引入和预览的 IP 限制。 可定义允许向该直播活动引入视频的 IP 地址。 允许的 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”）或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.252.0)”）。<br/>如果未指定 IP 地址并且没有规则定义，则不会允许任何 IP 地址。 若要允许任何 IP 地址，请创建一个规则并设置 0.0.0.0/0。<br/>IP 地址必须采用以下格式之一：具有四个数字或 CIDR 地址范围的 IpV4 地址。
* 创建事件时，可以将其启动方式指定为自动启动。 <br/>如果将 autostart 设置为 true，则直播活动会在创建后启动。 这意味着，只要直播活动开始运行，就会开始计费。 必须显式对直播活动资源调用停止操作才能停止进一步计费。 有关详细信息，请参阅[直播活动状态和计费](live-event-states-billing.md)。
* 要使引入 URL 具有预测性，请设置“vanity”模式。 有关详细信息，请参阅[实时事件引入 URL](live-events-outputs-concept.md#live-event-ingest-urls)。

```c#
Console.WriteLine($"Creating a live event named {liveEventName}");
Console.WriteLine();

// Note: When creating a LiveEvent, you can specify allowed IP addresses in one of the following formats:                 
//      IpV4 address with 4 numbers
//      CIDR address range

IPRange allAllowIPRange = new IPRange(
    name: "AllowAll",
    address: "0.0.0.0",
    subnetPrefixLength: 0
);

// Create the LiveEvent input IP access control.
LiveEventInputAccessControl liveEventInputAccess = new LiveEventInputAccessControl
{
    Ip = new IPAccessControl(
            allow: new IPRange[]
            {
                allAllowIPRange
            }
        )

};

// Create the LiveEvent Preview IP access control
LiveEventPreview liveEventPreview = new LiveEventPreview
{
    AccessControl = new LiveEventPreviewAccessControl(
        ip: new IPAccessControl(
            allow: new IPRange[]
            {
                allAllowIPRange
            }
        )
    )
};

// To get the same ingest URL for the same LiveEvent name:
// 1. Set vanityUrl to true so you have ingest like: 
//        rtmps://liveevent-hevc12-eventgridmediaservice-cne21.channel.media.chinacloudapi.cn:2935/live/522f9b27dd2d4b26aeb9ef8ab96c5c77           
// 2. Set accessToken to a desired GUID string (with or without hyphen)

LiveEvent liveEvent = new LiveEvent(
    location: mediaService.Location,
    description: "Sample LiveEvent for testing",
    vanityUrl: false,
    encoding: new LiveEventEncoding(
                // When encodingType is None, the service simply passes through the incoming video and audio layer(s) to the output
                // When the encodingType is set to Standard or Premium1080p, a live encoder is used to transcode the incoming stream
                // into multiple bit rates or layers. See https://docs.azure.cn/media-services/latest/live-event-types-comparison for more information
                encodingType: LiveEventEncodingType.None,
                presetName: null
            ),
    input: new LiveEventInput(LiveEventInputProtocol.RTMP, liveEventInputAccess),
    preview: liveEventPreview,
    streamOptions: new List<StreamOptionsFlag?>()
    {
        // Set this to Default or Low Latency
        // When using Low Latency mode, you must configure the Azure Media Player to use the 
        // quick start hueristic profile or you won't notice the change. 
        // In the AMP player client side JS options, set -  heuristicProfile: "Low Latency Heuristic Profile". 
        // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
        StreamOptionsFlag.LowLatency
    }
);

Console.WriteLine($"Creating the LiveEvent, be patient this can take time...");

// When autostart is set to true, the Live Event will be started after creation. 
// That means, the billing starts as soon as the Live Event starts running. 
// You must explicitly call Stop on the Live Event resource to halt further billing.
// The following operation can sometimes take awhile. Be patient.
liveEvent = await client.LiveEvents.CreateAsync(config.ResourceGroup, config.AccountName, liveEventName, liveEvent, autoStart: true);
```

### <a name="get-ingest-urls"></a>获取引入 URL

创建直播活动后，可以获得要提供给实时编码器的引入 URL。 编码器将使用这些 URL 来输入实时流。

```c#
string ingestUrl = liveEvent.Input.Endpoints.First().Url;
Console.WriteLine($"The ingest url to configure the on premise encoder with is:");
Console.WriteLine($"\t{ingestUrl}");
Console.WriteLine();
```

### <a name="get-the-preview-url"></a>获取预览 URL

使用 previewEndpoint 预览来自编码器的输入并验证其是否已确实收到。

> [!IMPORTANT]
> 确保视频流向预览 URL，然后再继续操作。

```c#
string previewEndpoint = liveEvent.Preview.Endpoints.First().Url;
Console.WriteLine($"The preview url is:");
Console.WriteLine($"\t{previewEndpoint}");
Console.WriteLine();

Console.WriteLine($"Open the live preview in your browser and use the Azure Media Player to monitor the preview playback:");
Console.WriteLine($"\thttps://ampdemo.azureedge.net/?url={previewEndpoint}&heuristicprofile=lowlatency");
Console.WriteLine();
```

### <a name="create-and-manage-live-events-and-live-outputs"></a>创建和管理直播活动与实时输出

将流传输到实时事件后，可以通过创建资产、实时输出和流定位符来启动流式传输事件。 这会存档流，并使观看者可通过流式处理终结点使用该流。

#### <a name="create-an-asset"></a>创建资产

创建供实时输出使用的资产。

```c#
Console.WriteLine($"Creating an asset named {assetName}");
Console.WriteLine();
Asset asset = await client.Assets.CreateOrUpdateAsync(config.ResourceGroup, config.AccountName, assetName, new Asset());
```

#### <a name="create-a-live-output"></a>创建实时输出

实时输出在创建时启动，在删除后停止。 删除实时输出不会删除基础资产和该资产中的内容。

```c#
string manifestName = "output";
Console.WriteLine($"Creating a live output named {liveOutputName}");
Console.WriteLine();

LiveOutput liveOutput = new LiveOutput(assetName: asset.Name, manifestName: manifestName, archiveWindowLength: TimeSpan.FromMinutes(10));
liveOutput = await client.LiveOutputs.CreateAsync(config.ResourceGroup, config.AccountName, liveEventName, liveOutputName, liveOutput);
```

#### <a name="create-a-streaming-locator"></a>创建流定位符

> [!NOTE]
> 创建媒体服务帐户后，一个处于“已停止”  状态的“默认”  流式处理终结点会添加到帐户。 若要开始流式传输内容并利用[动态打包](dynamic-packaging-overview.md)和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态  。

如果已使用流定位符发布了实时输出资产，则直播活动（长达 DVR 窗口长度）将继续可见，直到流定位符过期或被删除（以先发生为准）。

```c#
Console.WriteLine($"Creating a streaming locator named {streamingLocatorName}");
Console.WriteLine();

StreamingLocator locator = new StreamingLocator(assetName: asset.Name, streamingPolicyName: PredefinedStreamingPolicy.ClearStreamingOnly);
locator = await client.StreamingLocators.CreateAsync(config.ResourceGroup, config.AccountName, streamingLocatorName, locator);

// Get the default Streaming Endpoint on the account
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, streamingEndpointName);

// If it's not running, Start it. 
if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
{
    Console.WriteLine("Streaming Endpoint was Stopped, restarting now..");
    await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, streamingEndpointName);
}
```

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>清理媒体服务帐户中的资源

如果已完成流式处理事件，并想要清理先前设置的资源，请遵循以下过程：

* 停止从编码器推送流。
* 停止直播活动。 直播活动在停止后，不会产生任何费用。 当需要重新启动它时，它会采用相同的引入 URL，因此，无需重新配置编码器。
* 除非想要继续以点播流形式提供直播活动的存档，否则可以停止流式处理终结点。 如果直播活动处于停止状态，则不会产生任何费用。

```c#
private static async Task CleanupLiveEventAndOutputAsync(IAzureMediaServicesClient client, string resourceGroup, string accountName, string liveEventName)
{
    try
    {
        LiveEvent liveEvent = await client.LiveEvents.GetAsync(resourceGroup, accountName, liveEventName);

        if (liveEvent != null)
        {
            if (liveEvent.ResourceState == LiveEventResourceState.Running)
            {
                // If the LiveEvent is running, stop it and have it remove any LiveOutputs
                await client.LiveEvents.StopAsync(resourceGroup, accountName, liveEventName, removeOutputsOnStop: true);
            }

            // Delete the LiveEvent
            await client.LiveEvents.DeleteAsync(resourceGroup, accountName, liveEventName);
        }
    }
    catch (ApiErrorException e)
    {
        Console.WriteLine("CleanupLiveEventAndOutputAsync -- Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();
    }
}
```

```c#
private static async Task CleanupLocatorandAssetAsync(IAzureMediaServicesClient client, string resourceGroup, string accountName, string streamingLocatorName, string assetName)
{
    try
    {
        // Delete the Streaming Locator
        await client.StreamingLocators.DeleteAsync(resourceGroup, accountName, streamingLocatorName);

        // Delete the Archive Asset
        await client.Assets.DeleteAsync(resourceGroup, accountName, assetName);
    }
    catch (ApiErrorException e)
    {
        Console.WriteLine("CleanupLocatorandAssetAsync -- Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();
    }
}
```

## <a name="watch-the-event"></a>观看事件

若要观看事件，请复制流式传输 URL（在运行“创建流定位符”中所述的代码时获得）。 你可以使用所选的媒体播放器。 使用 Azure Media Player 在 https://ampdemo.azureedge.net 中测试流。 

直播活动在停止后会自动转换为点播内容。 即使你停止并删除了事件，只要没有删除资产，用户也能够按需将已存档内容作为视频进行流式传输。 如果资产被某个事件使用，则无法将其删除，必须先删除该事件。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组中的任何一个资源（包括为本教程创建的媒体服务和存储帐户），请删除之前创建的资源组。

执行以下 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> 让直播活动保持运行会产生费用。 请注意，如果项目/节目崩溃或因某种原因而关闭，可能会导致直播活动保持运行状态，从而产生费用。

## <a name="next-steps"></a>后续步骤

[对文件进行流式处理](stream-files-tutorial-with-api.md)
 
