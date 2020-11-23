---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
origin.date: 04/13/2020
ms.date: 11/20/2020
ms.author: v-tawe
ms.openlocfilehash: 88e6f51f9be551bab62414ed9cae1af2f439a5a5
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94979656"
---
语音服务的核心功能之一是能够识别人类语音并将其翻译成其他语言。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音翻译。 此快速入门将麦克风中的语音翻译成另一种语言的文本。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>设置源语言和目标语言

此命令会调用语音 CLI，将麦克风中的语音从意大利语翻译成法语。

```shell
 spx translate --microphone --source it-IT --target fr
```
