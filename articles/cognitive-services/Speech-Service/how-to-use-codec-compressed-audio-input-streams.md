---
title: 使用语音 SDK 流式传输编解码器压缩的音频 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音 SDK 将压缩音频流式传输到语音服务。 适用于用于 Linux 的 C++、C# 和 Java，Android 中的 Java 和 iOS 中的 Objective-C。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 03/30/2019
ms.date: 12/30/2020
ms.author: v-tawe
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 12a20354d465deed7662df68537ff281767326bc
ms.sourcegitcommit: eb742dcade404c9909d01e2570188f0bc4076992
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97820296"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>在语音 SDK 中使用编解码器压缩的音频输入

语音服务 SDK 压缩音频输入流 API 提供了一种使用 `PullStream` 或 `PushStream` 将压缩音频流式传输到语音服务的方法。

平台 | 语言 | 支持的 GStreamer 版本
| :--- | ---: | :---:
Windows（不包括 UWP）  | C++、C#、Java、Python | [1.15.1](https://gstreamer.freedesktop.org/releases/gstreamer/1.5.1.html)
Linux  | C++、C#、Java、Python | [受支持的 Linux 发行版和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>压缩的音频输入所需的语音 SDK 版本
* RHEL 8 和 CentOS 8 需要语音 SDK 1.10.0 或更高版本
* Windows 需要语音 SDK 版本 1.11.0 或更高版本。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>处理压缩音频所需的 GStreamer

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>使用编解码器压缩的音频输入的示例代码

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何识别语音](./get-started-speech-to-text.md)