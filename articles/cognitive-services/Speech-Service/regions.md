---
title: 区域 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务的可用区域和终结点列表，包括语音转文本、文本转语音和语音翻译。
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 08/20/2020
ms.date: 12/10/2020
ms.author: v-tawe
ms.custom: seodec18
ms.openlocfilehash: 835cba37a1ccb3d1905b136adbef68b3dc8cb88c
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004097"
---
# <a name="speech-service-supported-regions"></a>语音服务支持的区域

通过语音服务，应用程序可将音频转换为文本、执行语音翻译以及将文本转换为语音。 多个区域中均提供该服务，这些区域为语音 SDK 和 REST API 使用唯一终结点。

此处提供了对所有区域的语音体验执行自定义配置的语音门户： https://speech.azure.cn

考虑区域时，请注意以下几点：

* 如果应用程序使用[语音 SDK](speech-sdk.md)，请在创建语音配置时提供区域标识符，例如 `chinaeast2`。
* 如果应用程序使用某个语音服务的 [REST API](./overview.md#reference-docs)，则区域是你在发出请求时使用的终结点 URI 的一部分。
* 为某个区域创建的密钥仅在该区域有效。 尝试在其他区域使用此类密钥会导致身份验证错误。

## <a name="speech-sdk"></a>语音 SDK

在[语音 SDK](speech-sdk.md) 中，区域指定为字符串（例如，在 C# 语音 SDK 中用作 `SpeechConfig.FromSubscription` 的参数）。

### <a name="speech-to-text-text-to-speech-and-translation"></a>语音转文本、文本转语音和翻译

语音自定义门户在此处提供： https://speech.azure.cn

可以在以下区域使用语音服务，以进行 **语音识别**、**文本转语音** 和 **翻译**：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

如果使用 [语音 SDK](speech-sdk.md)，则区域由 **区域标识符** 指定（例如，作为 `SpeechConfig.FromSubscription` 的参数）。 请确保该区域与订阅的区域匹配。

### <a name="intent-recognition"></a>意向识别

通过语音 SDK 实现 **意向识别** 的可用区域如下：

| 全球区域 | 区域           | 区域标识符 |
| ------------- | ---------------- | ----------------- |
| 中国东部    | 中国东部 2     | `chinaeast2`      |

这是[语言理解服务 (LUIS)](../luis/luis-reference-regions.md) 支持的发布区域的子集。

<!-- ### Voice assistants -->

<!-- ### Speaker Recognition -->
## <a name="rest-apis"></a>REST API

语音服务还为语音转文本和文本转语音请求公开 REST 终结点。

### <a name="speech-to-text"></a>语音转文本

有关语音转文本的参考文档，请参阅[语音转文本 REST API](rest-speech-to-text.md)。

REST API 的终结点具有以下格式：

```
https://<REGION_IDENTIFIER>.stt.speech.azure.cn/speech/recognition/conversation/cognitiveservices/v1
```

将 `<REGION_IDENTIFIER>` 替换为与下表中的订阅区域匹配的标识符：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必须将语言参数追加到 URL 以避免收到 4xx HTTP 错误。 例如，使用“中国东部 2”终结点设置为美国英语的语言为：`https://chinaeast2.stt.speech.azure.cn/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

### <a name="text-to-speech"></a>文本转语音

有关文本转语音的参考文档，请参阅[文本转语音 REST API](rest-text-to-speech.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
