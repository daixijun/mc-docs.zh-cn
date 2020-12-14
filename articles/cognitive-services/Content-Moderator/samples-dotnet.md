---
title: 代码示例 - 内容审查器、.NET
titleSuffix: Azure Cognitive Services
description: 了解如何通过 SDK 在 .NET 应用程序中使用 Azure 认知服务内容审查器。
services: cognitive-services
author: Johnnytechn
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
origin.date: 01/10/2019
ms.date: 12/11/2020
ms.author: v-johya
ms.openlocfilehash: a37adc3ec01d6bcad5e47104c1abc7d4cd0f6d76
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105320"
---
# <a name="content-moderator-net-sdk-samples"></a>内容审查器 .NET SDK 示例

下面列出了使用用于 .NET 的 Azure 内容审查器 SDK 生成的代码示例的链接。

## <a name="moderation"></a>审查

- **图像审查**：[评估图像中是否有成人和挑逗性、文本和人脸](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs)。 请参阅 [.NET SDK 快速入门](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)。
- **自定义图像**：[使用自定义图像列表进行审查](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs)。 请参阅 [.NET SDK 快速入门](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)。

> [!NOTE]
> 最多只能使用 5 个图像列表  ，每个列表中的图像数不得超过 10,000 张  。
>

- **文本审查**：[筛查文本中是否有猥亵内容和个人数据](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs)。 请参阅 [.NET SDK 快速入门](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)。
- **自定义术语**：[使用自定义术语列表进行审查](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs)。 请参阅 [.NET SDK 快速入门](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)。

> [!NOTE]
> 最多只能使用 5 个术语列表  ，每个列表中的术语数不得超过 10,000 个  。
>

- **视频审查**：[扫描视频中是否有成人内容和挑逗性内容，并获取结果](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs)。 请参阅[快速入门](video-moderation-api.md)。

请参阅 [GitHub 上内容审查器 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)中的所有 .NET 示例。

