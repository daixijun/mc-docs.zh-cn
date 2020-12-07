---
title: Azure 媒体服务区域可用性 | Microsoft Docs
description: 本文概述 Microsoft Azure 媒体服务功能和服务区域可用性。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 11/03/2020
ms.date: 11/30/2020
ms.author: v-jay
ms.custom: references_regions
ms.openlocfilehash: 3cc62ed462ed0226cc3ce8d77b888a3ec3c21bc0
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301014"
---
# <a name="media-services-regional-availability"></a>媒体服务区域可用性

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Google Widevine 内容保护服务目前在 Azure 中国区域不可用。

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 查看最新版本：[媒体服务 v3](../latest/media-services-overview.md)。 另请参阅[从 v2 到 v3 的迁移指南](../latest/migrate-from-v2-to-v3.md)

可以使用 Azure 媒体服务 (AMS) 安全地上传、存储、编码和打包视频或音频内容，以便将点播流和实时传送视频流交付到各种客户端（例如，电视、电脑和移动设备）。

AMS 在多个区域中运行，让你可以灵活选择构建应用程序的位置。 本文概述 Azure 媒体服务功能和服务区域可用性。

## <a name="ams-accounts"></a>AMS 帐户

使用 [Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?regions=china-non-regional,china-east,china-east-2,china-north,china-north-2&products=all)，确定媒体服务是否在特定区域可用。

## <a name="streaming-endpoints"></a>流式处理终结点

媒体服务客户可以选择“标准”  或“高级”  流式处理终结点。

|名称|状态|区域
|---|---|---|
|标准|GA|全部|
|高级|GA|全部|

## <a name="live-encoding"></a>实时编码

在所有区域中可用。

## <a name="encoding-media-processors"></a>编码媒体处理器

有关详细信息，请参阅 [Azure 点播媒体编码器的概述和比较](media-services-encode-asset.md)。

|媒体处理器名称|状态|区域
|---|---|---|
|Media Encoder Standard|GA|全部|

## <a name="analytics-media-processors"></a>分析媒体处理器

媒体分析是语音和视觉组件的集合，便于组织和企业从视频文件中汲取可以实施的见解。 有关详细信息，请参阅 [Azure 媒体服务分析概述](./legacy-components.md)。

> [!NOTE]
> 一些分析媒体处理器将停用。 有关停用日期，请参阅此[旧组件](legacy-components.md)主题。

|媒体处理器名称|状态|区域
|---|---|---|
|Azure 媒体面部检测器|预览|全部|
|Azure Media Indexer|GA|全部|
|Azure 媒体动作检测器|预览|全部|
|Azure 媒体 OCR|预览|全部|
|Azure 媒体编修器|GA|全部|
|Azure 媒体视频缩略图|预览|全部|

## <a name="protection"></a>保护

使用 Azure 媒体服务，可以在媒体从离开计算机到存储、处理和传送的整个过程中确保其安全。 有关详细信息，请参阅[保护 AMS 内容](media-services-content-protection-overview.md)。

|Encryption|状态|区域|
|---|---|---| 
|存储|GA|全部|
|AES-128 密钥|GA|全部|
|Fairplay|GA|全部|
|PlayReady|GA|全部|

## <a name="reserved-units-rus"></a>预留单位 (RU)

设置的预留单位数决定了给定帐户中可并发处理的媒体任务数。

在所有区域中可用。

## <a name="reserved-unit-ru-type"></a>预留单位 (RU) 类型

媒体服务帐户与预留单位类型关联，后者决定了完成媒体处理任务的速度。 可以在以下预留单位类型中进行选择：S1、S2 或 S3。

|RU 类型名称|状态|区域
|---|---|---|
|S1|GA|全部|
|S2|GA|全部|
|S3|GA|全部|

## <a name="next-steps"></a>后续步骤

[迁移到媒体服务 v3](../latest/media-services-overview.md)

