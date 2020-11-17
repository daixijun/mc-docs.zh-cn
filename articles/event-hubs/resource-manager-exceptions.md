---
title: Azure 事件中心 - 资源管理器异常 | Microsoft Docs
description: Azure 资源管理器公开的 Azure 事件中心异常列表以及建议的处理措施。
ms.topic: article
origin.date: 06/23/2020
ms.date: 11/05/2020
ms.author: v-tawe
ms.openlocfilehash: dcd1186d6bce40ff9630e89ecab01df263906f30
ms.sourcegitcommit: b217474b15512b0f40b2eaae66bd3c521383d321
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375736"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure 事件中心 - 资源管理器异常
本文列出了使用 Azure 资源管理器通过模板或直接调用来与 Azure 事件中心交互时生成的异常。

> [!IMPORTANT]
> 本文档经常更新。 请回头查看是否有更新。

以下部分提供了通过 Azure 资源管理器公开的各种异常/错误。

## <a name="error-code-conflict"></a>错误代码：Conflict

| 错误代码 | 错误子代码 | 错误消息 | 说明 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflict | 40300 | 已达到或超过 EventHub 类型的资源数上限。 实际数目: #，最大允许数目: # | 命名空间已达到其可以包含的事件中心数[配额](event-hubs-quotas.md)。 | 从命名空间中删除所有未使用或无关的事件中心。 |
| Conflict | 无 | 后端存在冲突，命名空间更新失败。 | 当前正在对此命名空间执行另一项操作。 | 等待当前操作完成，然后重试。 |

<!-- | Conflict | none | Disaster recovery (DR) config can't be deleted because replication is in progress. Fail over or break pairing before attempting to delete the DR Config. | [GeoDR replication](event-hubs-geo-dr.md) is in progress, so the config can't be deleted at this time. | To unblock deletion of the config, either wait until replication has completed, trigger a failover, or break the GeoDR pairing. | -->

## <a name="error-code-429"></a>错误代码：429

| 错误代码 | 错误子代码 | 错误消息 | 说明 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 无 | 命名空间预配正在转换 | 当前正在对此命名空间执行另一项操作。 | 等待当前操作完成，然后重试。 |

<!-- | 429 | none | Disaster recovery operation in progress. | A [GeoDR](event-hubs-geo-dr.md) operation is currently being done on this namespace or pairing. | Wait until the current GeoDR operation completes, and then retry. | -->

## <a name="error-code-badrequest"></a>错误代码：BadRequest

| 错误代码 | 错误子代码 | 错误消息 | 说明 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | 无法更改事件中心的 PartitionCount。 | Azure 事件中心基本层或标准层不支持更改分区。 | 在基本或标准层命名空间中使用所需的分区数创建新的事件中心。 |
| BadRequest | 40000 | MessageRetentionInDays 的 '#' 值对于基本层无效。 该值不能超过 '1' 天。 | 基本层事件中心命名空间仅支持最长 1 天的消息保留期。 | 如果需要一天以上的消息保留期，请[创建标准事件中心命名空间](event-hubs-create.md)。 | 
| BadRequest | 无 | 指定的名称不可用。 | 命名空间名称必须唯一，而指定的名称已被使用。 | 如果你是使用指定名称的现有命名空间的所有者，可将其删除，但这会导致数据丢失。 然后重试相同的名称。 如果不能安全删除该命名空间（或者你不是所有者），请选择另一个命名空间名称。 |
| BadRequest | 无 | 指定的订阅已达到其命名空间配额。 | 订阅已达到其可以包含的命名空间数[配额](event-hubs-quotas.md)。 | 考虑删除此订阅中未使用的命名空间，创建另一个订阅。 |
| BadRequest | 无 | 无法在基本 SKU 中设置自动扩充 | 无法在基本层事件中心命名空间中启用自动扩充。 | 若要在命名空间中[启用自动扩充](event-hubs-auto-inflate.md)，请确保它位于标准层。 |
| BadRequest | 无 | 容量不足，无法创建命名空间。 请联系事件中心管理员。 | 所选区域已达到容量限制，无法创建更多命名空间。 | 选择另一个区域来容纳命名空间。 |
| BadRequest | 无 | 无法对实体类型 'ConsumerGroup' 执行该操作，因为命名空间 '<命名空间名称>' 使用的是“基本”层。  | 基本层事件中心命名空间的[配额](event-hubs-quotas.md)为一个使用者组（默认）。 不支持创建更多使用者组。 | 继续使用默认使用者组 ($Default)，如果需要更多使用者组，请考虑改用标准层事件中心命名空间。 | 
| BadRequest | 无 | 命名空间 '<命名空间名称>' 不存在。 | 找不到提供的命名空间。 | 仔细检查命名空间名称是否正确，以及是否可以在订阅中找到该名称。 如果找不到，请[创建事件中心命名空间](event-hubs-create.md)。 | 
| BadRequest | 无 | 资源的 location 属性与其包含命名空间不匹配。 | 在特定区域中创建事件中心失败，因为此区域与命名空间的区域不匹配。 | 尝试在命名空间所在的同一区域中创建事件中心。 | 

<!-- | BadRequest | none | Can't update a namespace that is secondary | The namespace can't be updated because it's the secondary namespace in a [GeoDR pairing](event-hubs-geo-dr.md). | If appropriate, make the change to the primary namespace in this pairing instead. Otherwise break the GeoDR pairing to make the change. | -->

## <a name="error-code-internal-server-error"></a>错误代码：内部服务器错误

| 错误代码 | 错误子代码 | 错误消息 | 说明 | 建议 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 内部服务器错误 | 无 | 内部服务器错误。 | 事件中心服务出现内部错误。 | 重试失败的操作。 如果操作仍然失败，请联系支持人员。 |