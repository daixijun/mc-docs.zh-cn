---
title: 使用现有播放器播放内容 - Azure | Microsoft 文档
description: 本文列出了可以用来播放内容的现有播放器。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 11/17/2020
ms.date: 12/14/2020
ms.author: v-jay
ms.openlocfilehash: aa69fa6b6374ebe5c69eb7baf0023678ca21071c
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97003934"
---
# <a name="playing-your-content-with-existing-players"></a>使用现有播放器播放内容

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure 媒体服务支持多种常用的流式处理格式，如平滑流、HTTP 实时流和 MPEG-Dash。 本主题列出了可用于测试流的现有播放器。

## <a name="the-azure-portal-media-services-content-player"></a>Azure 门户媒体服务内容播放器

**Azure** 门户提供了可用于测试视频的内容播放器。

单击所需的视频（确保它[已发布](media-services-portal-publish.md)），并单击门户底部的“播放”按钮。

请注意以下事项：

* **媒体服务内容播放器** 从默认的流式处理终结点播放。 如果要从非默认流式处理终结点播放，请使用其他播放器。 例如 [Azure Media Player](https://aka.ms/azuremediaplayer)。

### <a name="azure-media-player"></a>Azure Media Player

使用 [Azure Media Player](https://aka.ms/azuremediaplayer)按以下列任意格式播放内容（清除或受保护）：

* 平滑流
* MPEG DASH
* HLS
* 渐进式 MP4

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>带令牌的 PlayReady

[http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html](http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html)

### <a name="dash-players"></a>DASH 播放器

[dash 播放器](http://players.akamai.com/players/dashjs)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>其他

若要测试 HLS URL，还可以使用：

*  或
*  。

## <a name="media-services-learning-paths"></a>媒体服务学习路径

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png