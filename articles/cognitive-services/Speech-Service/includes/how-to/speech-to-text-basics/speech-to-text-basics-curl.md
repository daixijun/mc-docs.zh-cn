---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
origin.date: 10/09/2020
ms.date: 11/20/2020
ms.author: v-tawe
ms.openlocfilehash: 9020d62d3c9ce22082b9e66816531db9e94d58da
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "95970856"
---
本快速入门介绍如何使用语音服务和 cURL 将语音转换为文本。

若要深入了解语音转文本的概念，请参阅[概述](../../../speech-to-text.md)一文。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="convert-speech-to-text"></a>将语音转换为文本

请在命令提示符处运行以下命令。 需要将以下值插入到命令中。
- 语音服务订阅密钥。
- 你的语音服务区域。
- 输入的音频文件路径。 可以使用[语音转文本](../../../get-started-text-to-speech.md)来生成音频文件。

```curl
curl --location --request POST 'https://INSERT_REGION_HERE.stt.speech.azure.cn/speech/recognition/conversation/cognitiveservices/v1?language=en-US' \
--header 'Ocp-Apim-Subscription-Key: INSERT_SUBSCRIPTION_KEY_HERE' \
--header 'Content-Type: audio/wav' \
--data-binary 'INSERT_AUDIO_FILE_PATH_HERE'
``````

应收到类似于下面的响应。

```curl
{
    "RecognitionStatus": "Success",
    "DisplayText": "My voice is my passport, verify me.",
    "Offset": 6600000,
    "Duration": 32100000
}
```

有关详细信息，请参阅[语音转文本 REST API 参考](../../../rest-speech-to-text.md)。