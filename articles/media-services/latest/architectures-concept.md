---
title: 媒体服务体系结构
titleSuffix: Azure Media Services
description: 本文介绍媒体服务的体系结构。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
origin.date: 11/20/2020
ms.date: 01/11/2021
ms.author: v-jay
ms.openlocfilehash: 4b1941e71bd7e7b75231e11c6d50e2bb3e21569e
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023972"
---
# <a name="media-services-architectures"></a>媒体服务体系结构

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>实时传送视频流数字媒体

实时流式处理解决方案用于实时捕获视频，并将其实时广播给使用者，例如在线进行的流式访谈、会议和体育活动。 在此解决方案中，摄像机捕获视频，并将视频发送到频道输入终结点。 频道接收实时输入流，并使其可通过流式处理终结点流式传输到 Web 浏览器或移动应用。 频道还提供了一个预览监视终结点，用于在进一步处理和传送流之前对流进行预览和验证。 频道还可以记录和存储引入的内容，以便稍后进行流式处理（视频点播）。

此解决方案基于 Azure 托管服务：媒体服务。 这些服务在高可用性环境中运行，经过修补和支持改进，让你可专注于解决方案而不是其运行环境。

## <a name="video-on-demand-digital-media"></a>点播视频数字媒体

一个基本的视频点播解决方案，让你能够将电影、新闻剪辑、体育片段、培训视频和客户支持教程等录制的视频内容流式传输到任何支持视频的终结点设备、移动应用程序或桌面浏览器。 视频文件上传到 Azure Blob 存储，编码为多比特率标准格式，然后通过所有主要的自适应比特率流式处理协议（HLS、MPEG-DASH、平滑）分发到 Azure Media Player 客户端。

此解决方案基于 Azure 托管服务：Blob 存储和 Azure Media Player。 这些服务在高可用性环境中运行，经过修补和支持改进，让你可专注于解决方案而不是其运行环境。

## <a name="gridwich-media-processing-system"></a>Gridwich 媒体处理系统

Gridwich 系统体现了在 Azure 上处理和传送媒体资产的最佳做法。 虽然 Gridwich 系统特定于媒体，但消息处理和事件框架可以应用于任何无状态事件处理工作流。

## <a name="next-steps"></a>后续步骤

> [Azure 媒体服务概述](media-services-overview.md)