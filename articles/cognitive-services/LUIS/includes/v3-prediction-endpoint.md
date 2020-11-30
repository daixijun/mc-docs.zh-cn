---
title: 如何获取 V3 预测终结点
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 11/23/2020
ms.author: v-johya
ms.openlocfilehash: ccfb9174ae815ece5b3ad64b7d580a3de764db1f
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306458"
---
1. 在 LUIS 门户中的“Azure 资源”页面（左菜单）上的“预测资源”选项卡的“管理”部分（右上菜单）中，复制页面底部的“示例查询”   。 该 URL 具有应用 ID、密钥和槽名称。 V3 预测终结点 URL 的格式如下：`https://YOUR-RESOURCE-NAME.api.cognitive.azure.cn/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="预测资源部分中的示例查询" lightbox="../media/prediction-resources-example-query.png":::
    
    将 URL 粘贴到新的浏览器选项卡中。如果看不到该 URL，则表明你没有预测资源，需要创建一个。 

    

    


