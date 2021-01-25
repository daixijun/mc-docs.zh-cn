---
author: orspod
ms.service: data-explorer
ms.topic: include
origin.date: 12/22/2020
ms.date: 01/22/2021
ms.author: v-tawe
ms.openlocfilehash: 8738e291d75589701e5c5736ac9d2bcd9a0d4d34
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611516"
---
> [!NOTE]
> * 压缩数据不支持系统属性。
> * 对于表格数据，仅单记录事件消息支持系统属性。
> * 对于 JSON 数据，多记录事件消息也支持系统属性。 在这种情况下，系统属性仅添加到事件消息的第一条记录中。 
> * 对于 `csv` 映射，属性将按[系统属性](../ingest-data-event-hub-overview.md#system-properties)表中列出的顺序添加到记录的开头。
> * 对于 `json` 映射，将根据[系统属性](../ingest-data-event-hub-overview.md#system-properties)表中的属性名称添加属性。
