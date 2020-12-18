---
title: 定价和关联的成本
description: 了解与 Defender for IoT 相关的成本及其控制方式。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 10ecc35ca4e0740424b1b2f1a90531bf592812dc
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105635"
---
# <a name="pricing-and-associated-costs"></a>定价和关联的成本

本文介绍 Defender for IoT 定价模型，概述所有相关成本及其管理方式。

## <a name="pricing"></a>定价

Defender for IoT 定价模型由两部分组成，在 Defender for IoT 中[启用](quickstart-onboard-iot-hub.md) IoT 中心后即在其中一次性计费：

- 设备成本 - 内置安全功能，基于对 IoT 中心日志的分析。

- 消息成本 - 增强的安全功能，基于来自 IoT Edge 或叶设备的安全消息。

有关详细信息，请参阅[安全中心定价](https://www.azure.cn/pricing/details/security-center/)。

## <a name="associated-costs"></a>关联成本

Defender for IoT 具有关联成本，直接定价不包含这部分成本：

- 日志分析存储成本

可以通过选择退出特定解决方案功能来降低关联成本。 通过更改设置选择退出。

若要更改设置，请执行以下操作：

1. 打开 IoT 中心。

1. 在“安全性”下，单击“设置” 。

1. 单击“数据收集”。

下表提供了每个选项的关联成本和影响的摘要。

| 选项 | 使用情况 | 评论 |
| --- | --- | --- |
| **日志分析存储** |  |
| 设备建议和警报| 服务生成的安全建议和警报 | 并非可选 |
| 原始安全数据| 由安全代理从 IoT 设备收集的原始安全数据 | 禁用“存储原始设备安全事件” |
|

>[!Important]
> 选择退出对于 Defender for IoT 安全功能可用性有严重影响。

| 选择退出 | 含义 |
| --- | --- |
| _克隆元数据集合_ | 禁用[自定义警报](quickstart-create-custom-alerts.md) |
| | 禁用 IoT Edge 清单建议 |
| | 禁用基于设备标识的建议和警报 |
| _存储原始设备安全事件_ | 有关设备 OS 基线建议的详细信息不可用 |
| | 有关[警报](concept-security-alerts.md)和[建议](concept-recommendations.md)调查的详细信息不可用 |
|

## <a name="see-also"></a>请参阅

- 访问[原始安全数据](how-to-security-data-access.md)
- [调查设备](how-to-investigate-device.md)
- 了解并浏览[安全建议](concept-recommendations.md)
- 了解并浏览[安全警报](concept-security-alerts.md)
