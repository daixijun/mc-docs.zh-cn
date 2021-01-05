---
title: 文本分析 API 中的新增功能
titleSuffix: Text Analytics - Azure Cognitive Services
description: 本文介绍了 Azure 认知服务文本分析的新版本和新功能。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
origin.date: 06/03/2020
ms.date: 12/31/2020
ms.author: v-johya
ms.openlocfilehash: 90735427225beadb5f3e2b1f84ae3ec54b781db6
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857147"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文本分析 API 中有哪些新功能？

文本分析 API 会持续更新。 为了让大家随时了解最新的开发成果，本文介绍了新版本和新功能。

## <a name="may-2020"></a>2020 年 5 月

### <a name="text-analytics-api-v3-general-availability"></a>文本分析 API v3 正式发布

文本分析 API v3 现已正式发布，具有以下更新：

* 模型版本 `2020-04-01`
* 每个功能的新[数据限制](concepts/data-limits.md)
* 更新了[情绪分析 (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) 的[语言支持](language-support.md)
* 用于实体链接的单独终结点 
* [命名实体识别 (NER) v3](how-tos/text-analytics-how-to-entity-linking.md) 中的新“Address”实体类别。
* NER v3 中的新子类别：
   * 位置 - 地理
   * 位置 - 结构
   * 组织 - 证券交易所
   * 组织 - 医疗
   * 组织 - 体育
   * 事件 - 文化
   * 事件 - 自然
   * 事件 - 体育

已在 JSON 响应中添加了以下属性：
   * 情绪分析中的 `SentenceText`
   * 每个文档的 `Warnings` 

JSON 响应中以下属性的名称已更改（如果适用）：

* `score` 已重名为 `confidenceScore`
    * `confidenceScore` 的精度为小数点后两位。 
* `type` 已重名为 `category`
* `subtype` 已重名为 `subcategory`


> [!div class="nextstepaction"]
> [详细了解文本分析 API v3](https://chinaeast2.dev.cognitive.azure.cn/docs/services/TextAnalytics-v3-0/operations/Languages)


> [!div class="nextstepaction"]
> [详细了解文本分析 API v3.1 预览版](https://dev.cognitive.azure.cn/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK 对文本分析 API v3 公共预览版的支持

作为[统一 Azure SDK 版本](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)的一部分，文本分析 API v3 SDK 现已作为以下编程语言的公共预览版提供：
   * [C#](/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [详细了解文本分析 API v3 SDK](/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)


## <a name="next-steps"></a>后续步骤

* [什么是文本分析 API？](overview.md)  
* [示例用户方案](text-analytics-user-scenarios.md)
* [情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [语言检测](how-tos/text-analytics-how-to-language-detection.md)
* [实体识别](how-tos/text-analytics-how-to-entity-linking.md)
* [关键短语提取](how-tos/text-analytics-how-to-keyword-extraction.md)
