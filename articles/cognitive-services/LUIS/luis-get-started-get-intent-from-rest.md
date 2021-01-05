---
title: 如何使用 REST API 获取意图
description: 在本文中，你将使用可用的公共 LUIS 应用从会话文本中确定用户的意向。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
origin.date: 02/03/2020
ms.date: 12/28/2020
ms.author: v-johya
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 95a656d7ff770b66ea5645ff45e0462cf6608310
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97856761"
---
# <a name="how-to-get-an-intent-using-the-rest-apis"></a>如何使用 REST API 获取意图

在本文中，你将使用 LUIS 应用从会话文本中确定用户的意向。 将用户的意向作为文本发送到 Pizza 应用的 HTTP 预测终结点。 在终结点处，LUIS 应用 Pizza 应用的模型来分析自然语言文本的含义，确定总体意向并提取与应用的主题域相关的数据。

对于本文，需要一个免费的 [LUIS](https://luis.azure.cn) 帐户。

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end

