---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
origin.date: 05/06/2019
ms.date: 12/10/2020
ms.author: wolfma
ms.openlocfilehash: 539facb7ddd8507f842c2310bfc02e9c7798fd6a
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004098"
---
### <a name="standard-and-neural-voices"></a>标准语音和神经语音

使用下表按区域/终结点确定标准语音和神经语音的可用性：

| 区域 | 端点 | 标准语音 | 神经语音 |
|--------|----------|-----------------|---------------|
| 中国东部 2 | `https://chinaeast2.tts.speech.azure.cn/cognitiveservices/v1` | 是 | 是 |

### <a name="custom-voices"></a>自定义语音

如果已经创建了自定义语音字体，请使用已创建的终结点。 还可以使用下面列出的终结点，并将 `{deploymentId}` 替换为语音模型的部署 ID。

| 区域 | 终结点 |
|--------|----------|
| 中国东部 2 | `https://chinaeast2.voice.speech.azure.cn/cognitiveservices/v1?deploymentId={deploymentId}` |
