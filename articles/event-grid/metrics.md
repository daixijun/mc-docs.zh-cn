---
title: Azure 事件网格支持的指标
description: 本文提供 Azure 事件网格服务支持的 Azure Monitor 指标。
author: Johnnytechn
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: v-johya
ms.openlocfilehash: a8eb800864e83ffcf1d7226c83c131fa6a5eb6ea
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94977254"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure 事件网格支持的指标
本文提供按命名空间分类的事件网格指标的列表。 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|否|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|是|发送的事件数|计数|总计|发送到此事件订阅的事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|否|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|是|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|是|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|是|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|Topic, ErrorType, Error|
|PublishSuccessCount|是|发布的事件数|计数|总计|发布到此主题的事件总数|主题|
|PublishSuccessLatencyInMs|是|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无维度|
| AdvancedFilterEvaluationCount | 是 | 高级筛选器评估 | 计数 | 总计 | 跨事件订阅评估的高级筛选器总数 | EventSubscriptionName |



## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason|
|DeliveryAttemptFailCount|否|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error, ErrorType|
|DeliverySuccessCount|是|发送的事件数|计数|总计|发送到此事件订阅的事件总数|无维度|
|DestinationProcessingDurationInMs|否|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|无维度|
|DroppedEventCount|是|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason|
|MatchedEventCount|是|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|无维度|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|PublishFailCount|是|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|PublishSuccessCount|是|发布的事件数|计数|总计|发布到此主题的事件总数|无维度|
|PublishSuccessLatencyInMs|是|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无维度|
|UnmatchedEventCount|是|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无维度|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|否|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|是|发送的事件数|计数|总计|发送到此事件订阅的事件总数|EventSubscriptionName|
|DestinationProcessingDurationInMs|否|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|EventSubscriptionName|
|DroppedEventCount|是|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason、EventSubscriptionName|
|MatchedEventCount|是|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|EventSubscriptionName|
|PublishFailCount|是|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|PublishSuccessCount|是|发布的事件数|计数|总计|发布到此主题的事件总数|无维度|
|PublishSuccessLatencyInMs|是|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无维度|
|UnmatchedEventCount|是|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无维度|
| AdvancedFilterEvaluationCount | 是 | 高级筛选器评估 | 计数 | 总计 | 跨事件订阅评估的高级筛选器总数 | EventSubscriptionName |



## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|指标|是否可通过诊断设置导出？|指标显示名称|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|死信事件数|计数|总计|与此事件订阅匹配的死信事件总数|DeadLetterReason、EventSubscriptionName|
|DeliveryAttemptFailCount|否|发送失败的事件数|计数|总计|未能发送到此事件订阅的事件总数|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|是|发送的事件数|计数|总计|发送到此事件订阅的事件总数|EventSubscriptionName|
|DestinationProcessingDurationInMs|否|目标处理持续时间|毫秒|平均值|目标处理持续时间（毫秒）|EventSubscriptionName|
|DroppedEventCount|是|删除的事件数|计数|总计|与此事件订阅匹配的已删除事件总数|DropReason、EventSubscriptionName|
|MatchedEventCount|是|匹配的事件数|计数|总计|与此事件订阅匹配的事件总数|EventSubscriptionName|
|PublishFailCount|是|发布失败的事件数|计数|总计|未能发布到此主题的事件总数|ErrorType, Error|
|PublishSuccessCount|是|发布的事件数|计数|总计|发布到此主题的事件总数|无维度|
|PublishSuccessLatencyInMs|是|发布成功延迟|毫秒|总计|发布成功延迟（毫秒）|无维度|
|UnmatchedEventCount|是|不匹配的事件数|计数|总计|不匹配本主题任何事件订阅的事件总数|无维度|
| AdvancedFilterEvaluationCount | 是 | 高级筛选器评估 | 计数 | 总计 | 跨事件订阅评估的高级筛选器总数 | Topic,EventSubscriptionName, DomainEventSubscriptionName |

## <a name="next-steps"></a>后续步骤
请参阅以下文章：[诊断日志](diagnostic-logs.md)

