---
title: 语言支持 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务支持多种语言，可用于语音到文本和文本到语音转换，以及语音翻译。 本文提供了按服务功能列出的语言支持的完整列表。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 03/26/2020
ms.date: 12/10/2020
ms.author: v-tawe
ms.custom: seodec18
ms.openlocfilehash: 2918b86219150686fc118dfb45c447d16b7f7b55
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004143"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>语音服务的语言和语音支持

语言支持因语音服务功能而异。 下表汇总了对[语音转文本](#speech-to-text)、[文本转语音](#text-to-speech)和[语音翻译](#speech-translation)服务产品的语言支持。

## <a name="speech-to-text"></a>语音转文本

Microsoft 语音 SDK 和 REST API 都支持以下语言（区域设置）。 

为了提高准确性，已为一部分语言提供了自定义功能，你可通过上传音频和人工标记的脚本或相关文本（语句）进行自定义。 若要了解有关自定义的详细信息，请参阅[自定义语音识别入门](how-to-custom-speech.md)。

<!--
To get the AM and ML bits:
https://chinaeast2.dev.cognitive.azure.cn/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.azure.cn -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| 语言                          | 区域设置 (BCP-47) | 自定义                                    |
| --------------------------------- | --------------- | ------------------------------------------------- |
| 阿拉伯语(巴林)，现代标准 | `ar-BH`         | 语言模型                                    |
| 阿拉伯语（埃及）                    | `ar-EG`         | 语言模型                                    |
| 阿拉伯语（伊拉克）                     | `ar-IQ`         | 语言模型                                    |
| 阿拉伯语（约旦）                   | `ar-JO`         | 语言模型                                    |
| 阿拉伯语(科威特)                   | `ar-KW`         | 语言模型                                    |
| 阿拉伯语（黎巴嫩）                  | `ar-LB`         | 语言模型                                    |
| 阿拉伯语（阿曼）                     | `ar-OM`         | 语言模型                                    |
| 阿拉伯语（卡塔尔）                    | `ar-QA`         | 语言模型                                    |
| 阿拉伯语（沙特阿拉伯）             | `ar-SA`         | 语言模型                                    |
| 阿拉伯语（叙利亚）                    | `ar-SY`         | 语言模型                                    |
| 阿拉伯语（阿拉伯联合酋长国）     | `ar-AE`         | 语言模型                                    |
| 中文(粤语，繁体)  | `zh-HK`         | 语言模型                                    |
| 中文（普通话，简体）    | `zh-CN`         | 声学模型<br>语言模型                  |
| 中文(台湾普通话)      | `zh-TW`         | 语言模型                                    |
| 英语（澳大利亚）               | `en-AU`         | 声学模型<br>语言模型                  |
| 英语（加拿大）                  | `en-CA`         | 声学模型<br>语言模型                  |
| 英语（香港）               | `en-HK`         | 语言模型                                    |
| 英语（爱尔兰）                 | `en-IE`         | 语言模型                                    |
| 英语（新西兰）             | `en-NZ`         | 声学模型<br>语言模型                  |
| 英语（菲律宾）             | `en-PH`         | 语言模型                                    |
| 英语（新加坡）               | `en-SG`         | 语言模型                                    |
| 英语（南非）            | `en-ZA`         | 语言模型                                    |
| 英语（美国）           | `en-US`         | 声学模型<br>语言模型<br>发音 |
| 西班牙语（阿根廷）               | `es-AR`         | 语言模型                                    |
| 西班牙语（玻利维亚）                 | `es-BO`         | 语言模型                                    |
| 西班牙语（智利）                   | `es-CL`         | 语言模型                                    |
| 西班牙语（哥伦比亚）                | `es-CO`         | 语言模型                                    |
| 西班牙语（哥斯达黎加）              | `es-CR`         | 语言模型                                    |
| 西班牙语（古巴）                    | `es-CU`         | 语言模型                                    |
| 西班牙语（多米尼加共和国）      | `es-DO`         | 语言模型                                    |
| 西班牙语（厄瓜多尔）                 | `es-EC`         | 语言模型                                    |
| 西班牙语（萨尔瓦多）             | `es-SV`         | 语言模型                                    |
| 西班牙语（危地马拉）               | `es-GT`         | 语言模型                                    |
| 西班牙语（洪都拉斯）                | `es-HN`         | 语言模型                                    |
| 西班牙语（墨西哥）                  | `es-MX`         | 声学模型<br>语言模型                  |
| 西班牙（尼加拉瓜）               | `es-NI`         | 语言模型                                    |
| 西班牙语（巴拿马）                  | `es-PA`         | 语言模型                                    |
| 西班牙语（巴拉圭）                | `es-PY`         | 语言模型                                    |
| 西班牙语（秘鲁）                    | `es-PE`         | 语言模型                                    |
| 西班牙语（波多黎各）             | `es-PR`         | 语言模型                                    |
| 西班牙语(西班牙)                   | `es-ES`         | 声学模型<br>语言模型                  |
| 西班牙语（乌拉圭）                 | `es-UY`         | 语言模型                                    |
| 西班牙语（美国）                     | `es-US`         | 语言模型                                    |
| 西班牙语（委内瑞拉）               | `es-VE`         | 语言模型                                    |

## <a name="text-to-speech"></a>文本转语音

Microsoft 语音 SDK 和 REST API 支持以下语音，其中的每种语音都支持特定语言和方言（按区域设置标识）。 还可以通过[语音/列表 API](rest-text-to-speech.md#get-a-list-of-voices) 获取每个特定区域/终结点支持的语言和语音的完整列表。

> [!IMPORTANT]
> 标准语音和神经语音的定价各不相同。 有关其他信息，请访问[定价](https://www.azure.cn/pricing/details/cognitive-services/)页。

### <a name="neural-voices"></a>神经语音

神经文本到语音转换是由深度神经网络提供支持的新型语音合成。 使用神经语音时，几乎无法将合成的语音与人类录音区分开来。

使用神经语音可使得与聊天机器人和语音助手的交互更加自然且富有吸引力、将数字文本（如电子书）转换为有声读物以及增强车载导航系统。 随着类人的自然韵律和字词的清晰发音，用户在与 AI 系统交互时，神经语音显著减轻了听力疲劳。

有关区域可用性的详细信息，请参阅[区域](regions.md#standard-and-neural-voices)。

| 语言                                   | 区域设置 (BCP-47)        | 性别 | 语音名称             | 风格支持                                               |
| ------------------------------------------ | ---------------------- | ------ | ---------------------- | ----------------------------------------------------------- |
| 粤语（繁体中文，香港） | `zh-HK`                | Female | `zh-HK-HiuGaaiNeural`  | 常规                                                     |
| 英语（澳大利亚）                        | `en-AU`                | Female | `en-AU-NatashaNeural`  | 常规                                                     |
| 英语（加拿大）                           | `en-CA`                | Female | `en-CA-ClaraNeural`    | 常规                                                     |
| 英语(英国)                   | `en-GB`                | Female | `en-GB-LibbyNeural`    | 常规                                                     |
| 英语(英国)                   | `en-GB`                | Female | `en-GB-MiaNeural`      | 常规                                                     |
| 英语(英国)                   | `en-GB` <sup>新建</sup> | 男   | `En-GB-RyanNeural`     | 常规                                                     |
| 英语（美国）                    | `en-US`                | 女 | `en-US-AriaNeural`     | 常规，提供了多种语音风格                    |
| 英语（美国）                    | `en-US`                | 男   | `en-US-GuyNeural`      | 常规                                                     |
| 英语（美国）                    | `en-US` <sup>新建</sup> | 女 | `en-US-JennyNeural`    | 常规，提供了多种语音风格                    |
| 普通话（简体中文，中国）       | `zh-CN`                | 女 | `zh-CN-XiaoxiaoNeural` | 常规，提供了多种语音风格                    |
| 普通话（简体中文，中国）       | `zh-CN`                | 女 | `zh-CN-XiaoyouNeural`  | 儿童语音，针对讲故事进行了优化                    |
| 普通话（简体中文，中国）       | `zh-CN`                | 男   | `zh-CN-YunyangNeural`  | 针对新闻播报进行了优化，提供了多种语音风格 |
| 普通话（简体中文，中国）       | `zh-CN`                | 男   | `zh-CN-YunyeNeural`    | 针对讲故事进行了优化                               |
| 西班牙语（墨西哥）                           | `es-MX`                | Female | `es-MX-DaliaNeural`    | 常规                                                     |
| 西班牙语（墨西哥）                           | `es-MX` <sup>新建</sup> | 男   | `es-MX-JorgeNeural`    | 常规                                                     |
| 西班牙语(西班牙)                            | `es-ES`                | Female | `es-ES-ElviraNeural`   | 常规                                                     |
| 西班牙语(西班牙)                            | `es-ES` <sup>新建</sup> | 男   | `es-ES-AlvaroNeural`   | 常规                                                     |

> [!IMPORTANT]
> `en-US-JessaNeural` 语音已更改为 `en-US-AriaNeural`。 如果以前使用了“Jessa”，请转换为“Aria”。

若要了解如何配置和调整神经语音，请参阅[语音合成标记语言](speech-synthesis-markup.md#adjust-speaking-styles)。

> [!TIP]
> 可以继续在语音合成请求中使用完整的服务名称映射，如“Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)”。

### <a name="standard-voices"></a>标准语音

40 多种标准语音在 10 多种语言和区域设置中提供，允许你将文本转换为合成语音。 有关区域可用性的详细信息，请参阅[区域](regions.md#standard-and-neural-voices)。

| 语言                                   | 区域设置 (BCP-47) | 性别 | 语音名称           |
| ------------------------------------------ | --------------- | ------ | -------------------- |
| 阿拉伯语（阿拉伯）                           | `ar-EG`         | Female | `ar-EG-Hoda`         |
| 阿拉伯语（沙特阿拉伯）                      | `ar-SA`         | 男   | `ar-SA-Naayf`        |
| 粤语（繁体中文，香港） | `zh-HK`         | 男   | `zh-HK-Danny`        |
| 粤语（繁体中文，香港） | `zh-HK`         | Female | `zh-HK-TracyRUS`     |
| 克罗地亚语（克罗地亚）                         | `hr-HR`         | 男   | `hr-HR-Matej`        |
| 英语（澳大利亚）                        | `en-AU`         | Female | `en-AU-Catherine`    |
| 英语（澳大利亚）                        | `en-AU`         | Female | `en-AU-HayleyRUS`    |
| 英语（加拿大）                           | `en-CA`         | Female | `en-CA-HeatherRUS`   |
| 英语（加拿大）                           | `en-CA`         | Female | `en-CA-Linda`        |
| 英语（英国）                   | `en-GB`         | 男   | `en-GB-George`       |
| 英语（英国）                   | `en-GB`         | Female | `en-GB-HazelRUS`     |
| 英语（英国）                   | `en-GB`         | Female | `en-GB-Susan`        |
| 英语（美国）                    | `en-US`         | 男   | `en-US-BenjaminRUS`  |
| 英语（美国）                    | `en-US`         | 男   | `en-US-GuyRUS`       |
| 英语（美国）                    | `en-US`         | Female | `en-US-JessaRUS`     |
| 英语（美国）                    | `en-US`         | Female | `en-US-ZiraRUS`      |
| 法语（加拿大）                            | `fr-CA`         | Female | `fr-CA-Caroline`     |
| 法语（加拿大）                            | `fr-CA`         | Female | `fr-CA-HarmonieRUS`  |
| 法语（瑞士）                       | `fr-CH`         | 男   | `fr-CH-Guillaume`    |
| 德语（奥地利）                           | `de-AT`         | 男   | `de-AT-Michael`      |
| 德语（德国）                           | `de-DE`         | 男   | `de-DE-Stefan`       |
| 印地语（印度）                              | `hi-IN`         | 男   | `hi-IN-Hemant`       |
| 印地语（印度）                              | `hi-IN`         | Female | `hi-IN-Kalpana`      |
| 意大利语（意大利）                            | `it-IT`         | 男   | `it-IT-Cosimo`       |
| 意大利语（意大利）                            | `it-IT`         | Female | `it-IT-LuciaRUS`     |
| 韩语(韩国)                             | `ko-KR`         | Female | `ko-KR-HeamiRUS`     |
| 普通话（简体中文，中国）       | `zh-CN`         | Female | `zh-CN-HuihuiRUS`    |
| 普通话（简体中文，中国）       | `zh-CN`         | 男   | `zh-CN-Kangkang`     |
| 普通话（简体中文，中国）       | `zh-CN`         | Female | `zh-CN-Yaoyao`       |
| 普通话（繁体中文，台湾）     | `zh-TW`         | Female | `zh-TW-HanHanRUS`    |
| 普通话（繁体中文，台湾）     | `zh-TW`         | Female | `zh-TW-Yating`       |
| 普通话（繁体中文，台湾）     | `zh-TW`         | 男   | `zh-TW-Zhiwei`       |
| 俄语（俄罗斯）                           | `ru-RU`         | Female | `ru-RU-EkaterinaRUS` |
| 俄语（俄罗斯）                           | `ru-RU`         | Female | `ru-RU-Irina`        |
| 俄语（俄罗斯）                           | `ru-RU`         | 男   | `ru-RU-Pavel`        |
| 西班牙语（墨西哥）                           | `es-MX`         | Female | `es-MX-HildaRUS`     |
| 西班牙语（墨西哥）                           | `es-MX`         | 男   | `es-MX-Raul`         |
| 西班牙语(西班牙)                            | `es-ES`         | Female | `es-ES-HelenaRUS`    |
| 西班牙语(西班牙)                            | `es-ES`         | 女 | `es-ES-Laura`        |
| 西班牙语(西班牙)                            | `es-ES`         | 男   | `es-ES-Pablo`        |


> [!IMPORTANT]
> `en-US-Jessa` 语音已更改为 `en-US-Aria`。 如果以前使用了“Jessa”，请转换为“Aria”。

> [!TIP]
> 可以继续在语音合成请求中使用完整的服务名称映射，如“Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)”。

### <a name="customization"></a>自定义

语音自定义适用于 `en-GB`、`en-IN`、`en-US`、`es-MX` 和 `zh-CN`。 选择与训练自定义语音模型所需的训练数据相匹配的正确区域设置。 例如，如果你的录音数据是以带英国口音的英语说出的，请选择 `en-GB`。

> [!NOTE]
> 除了中英双语模型之外，我们在自定义语音中不支持其他双语模型训练。 如果要训练一种也可以说英语的中文语音，请选择“中英双语”。 对于 `en-US` 和 `zh-CN` 之外的所有区域设置，语音训练都从一个包含 2000 条以上言语的数据集开始。对于这例外的两种区域设置，你可以从任何大小的训练数据开始。

## <a name="speech-translation"></a>语音翻译

**语音翻译** API 支持使用不同的语言进行语音转语音和语音转文本的翻译。 源语言必须始终来自“语音转文本”语言表。 可用的目标语言取决于翻译目标是语音还是文本。 可以将传入的语音翻译成 [60 种以上的语言](https://www.microsoft.com/translator/business/languages/)。 这些语言的子集可用于[语音合成](language-support.md#text-languages)。

### <a name="text-languages"></a>文本语言

| 文本语言       | 语言代码 |
| :------------------ | :-----------: |
| 阿拉伯语              |     `ar`      |
| 简体中文  |   `zh-Hans`   |
| 中文(繁体) |   `zh-Hant`   |
| 英语             |     `en`      |
| 法语              |     `fr`      |
| 德语              |     `de`      |
| Hindi               |     `hi`      |
| 朝鲜语              |     `ko`      |
| 俄语             |     `ru`      |
| 西班牙语             |     `es`      |

<!-- ## Speaker Recognition -->

## <a name="more-languages"></a>更多语言

> [!NOTE]
> 如果前面的列表中不支持你指定的语言，请参考[主权云支持的区域设置](sovereign-clouds.md)和[联系支持](https://www.azure.cn/support/contact/)，可以根据要求部署该语言。

## <a name="next-steps"></a>后续步骤

- [创建一个 Azure 试用帐户](https://www.azure.cn/home/features/cognitive-services/)
- [了解如何在 C# 中识别语音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
