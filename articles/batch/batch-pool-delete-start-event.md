---
title: Azure Batch 池删除开始事件
description: 批处理池删除开始事件参考。 当池删除操作开始时，会发出此事件。
ms.service: batch
ms.topic: article
origin.date: 12/28/2020
ms.date: 01/18/2021
ms.author: v-harrishe
ms.openlocfilehash: 71f4f994cec10f1a8212ac97bc30346a7148323f
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231020"
---
# <a name="pool-delete-start-event"></a>池删除开始事件

 当池删除操作开始时，会发出此事件。 由于池删除是异步事件，预计在删除操作完成后会发出池删除完成事件。

 以下示例显示了池删除开始事件的正文。

```
{
   "id": "myPool1"
}
```

|元素|类型|说明|
|-------------|----------|-----------|
|`id`|String|池的 ID。|

<!-- Update_Description: update metedata properties -->