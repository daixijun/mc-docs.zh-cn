---
title: 认知服务容器映像标记
titleSuffix: Azure Cognitive Services
description: 所有认知服务容器映像标记的全面列表。
services: cognitive-services
author: Johnnytechn
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/23/2020
ms.author: v-johya
ms.openlocfilehash: c804b59c6ae9a74c67edf0c09c09dab2423c13bb
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306208"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Azure 认知服务容器映像标记和发行说明

Azure 认知服务提供了许多容器映像。 容器注册表和相应的存储库因容器映像而异。 每个容器映像名称都提供多个标记。 容器映像标记是对容器映像进行版本控制的机制。 本文旨在作为综合性的参考资料使用，其中列出了所有认知服务容器映像及其可用标记。

> [!TIP]
> 在使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 时，请密切注意容器注册表、存储库、容器映像名称和相应标记的大小写，因为它们是区分大小写的。

## <a name="language-understanding-luis"></a>语言理解 (LUIS)

在 `mcr.microsoft.com` 容器注册表联合项中可以找到 [LUIS][lu-containers] 容器映像。 它驻留在 `azure-cognitive-services/language` 存储库中，名为 `luis`。 完全限定的容器映像名称为 `mcr.microsoft.com/azure-cognitive-services/language/luis`。

此容器映像提供了以下标记。 还可以[在 MCR 上找到标记的完整列表](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list)。

# <a name="latest-version"></a>[最新版本](#tab/current)

| 映像标记                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[先前版本](#tab/previous)

| 映像标记                    | 说明 |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="key-phrase-extraction"></a>关键短语提取

在 `mcr.microsoft.com` 容器注册表联合项中可以找到容器映像。 该映像驻留在 `azure-cognitive-services/textanalytics/` 存储库中，名为 `keyphrase`。 完全限定的容器映像名称为 `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase`。

此容器映像提供了以下标记。 还可以[在 MCR 上找到标记的完整列表](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list)。

# <a name="latest-version"></a>[最新版本](#tab/current)


| 映像标记                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[旧版](#tab/previous)

| 映像标记                    | 说明 |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>文本语言检测

在 `mcr.microsoft.com` 容器注册表联合项中可以找到[语言检测][ta-la]容器映像。 该映像驻留在 `azure-cognitive-services/textanalytics/` 存储库中，名为 `language`。 完全限定的容器映像名称为 `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


此容器映像提供了以下标记。 还可以[在 MCR 上找到标记的完整列表](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list)。

# <a name="latest-versions"></a>[最新版本](#tab/current)

| 映像标记                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[旧版](#tab/previous)


| 映像标记                    | 说明 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>情绪分析

在 `mcr.microsoft.com` 容器注册表联合项中可以找到[情绪分析][ta-se]容器映像。 该映像驻留在 `azure-cognitive-services/textanalytics/` 存储库中，名为 `sentiment`。 完全限定的容器映像名称为 `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

此容器映像提供了以下标记。 还可以[在 MCR 上找到标记的完整列表](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list)。

| 映像标记 | 说明                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | 情绪分析 v3（英语）               |
| `3.0-es`   | 情绪分析 v3（西班牙语）               |
| `3.0-fr`   | 情绪分析 v3（法语）                |
| `3.0-it`   | 情绪分析 v3（意大利语）               |
| `3.0-de`   | 情绪分析 v3（德语）                |
| `3.0-zh`   | 情绪分析 v3（简体中文）  |
| `3.0-zht`  | 情绪分析 v3（繁体中文） |
| `3.0-ja`   | 情绪分析 v3（日语）              |
| `3.0-pt`   | 情绪分析 v3（葡萄牙语）            |
| `3.0-nl`   | 情绪分析 v3（荷兰语）                 |
| `2.1`    | 情绪分析 v2      |

[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[lu-containers]: ../luis/luis-container-howto.md
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment

