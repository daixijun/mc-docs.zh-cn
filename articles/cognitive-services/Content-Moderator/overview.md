---
title: 什么是 Azure 内容审查器？
titleSuffix: Azure Cognitive Services
description: 了解如何使用内容审查器跟踪、标记、评估和筛选用户生成的内容中不适合的材料。
services: cognitive-services
author: Johnnytechn
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
origin.date: 07/03/2019
ms.date: 12/11/2020
ms.author: v-johya
ms.custom: cog-serv-seo-aug-2020
keywords: 内容审查器, azure 内容审查器, 联机审查器, 内容筛选软件, 内容审查服务, 内容审查
ms.openlocfilehash: 76630dace887d41e2d7527909d9d180e1717e9f2
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105321"
---
# <a name="what-is-azure-content-moderator"></a>什么是 Azure 内容审查器？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

<!--Not available in MC: Review Tool-->
Azure 内容审查器是一种 AI 服务，可用于处理可能的冒犯性、危险或不可取内容。 它包括 AI 支持的内容审核服务，该服务扫描文本、图像和视频，并自动应用内容标志。

你可能想在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。

## <a name="where-its-used"></a>使用场合

下面是软件开发人员或团队需要使用内容审核服务的一些场景：

- 在联机市场中审查产品目录和其他用户生成的内容。
- 在游戏公司中审查用户生成的游戏项目和聊天室。
- 在社交消息平台中审查用户添加的图像、文本和视频。
- 企业媒体公司对其内容进行集中式审查。
- K-12 教育解决方案提供商为学生和教师筛选掉不当的内容。

> [!IMPORTANT]
> 不能使用内容审查器检测非法儿童剥削图像。 不过，合格组织可以使用 [PhotoDNA 云服务](https://www.microsoft.com/photodna "Microsoft PhotoDNA 云服务")来筛查此类内容。

## <a name="what-it-includes"></a>组成部分

<!--Not available in MC: Review Tool-->
内容审查器服务包含多个可以通过 REST 调用和 .NET SDK 使用的 Web 服务 API。

## <a name="moderation-apis"></a>审查 API

内容审查器服务包括了审查 API，这些 API 检查内容中是否存在可能不适当或令人反感的材料。

![内容审查器审查 API 的框图](images/content-moderator-mod-api.png)

下表介绍了各种类型的审查 API。

| API 组 | 说明 |
| ------ | ----------- |
|[**文本审查**](text-moderation-api.md)| 扫描文本中是否存在冒犯性内容、明确的或暗示性的色情内容、不雅内容和个人数据。|
|[**自定义术语列表**](try-terms-list-api.md)| 扫描文本中是否存在自定义术语列表以及内置的术语。 根据你自己的内容策略，使用自定义列表阻止或允许内容。|  
|[**图像审查**](image-moderation-api.md)| 扫描图像中是否存在成人内容或不雅内容，通过光学字符识别 (OCR) 功能检测图像中的文本，以及检测人脸。|
|[**自定义图像列表**](try-image-list-api.md)| 针对自定义图像列表对图像进行扫描。 使用自定义图像列表筛选掉常常反复出现的内容的实例，这些实例不需再次分类。|
|[**视频审查**](video-moderation-api.md)| 扫描视频中是否存在成人内容或挑逗性内容，并针对上述内容返回时间标记。|

<!--Not available in MC: Review Tool-->
## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用内容审查器服务的开发人员应该了解 Microsoft 针对客户数据的政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.trustcenter.cn)来了解详细信息。

