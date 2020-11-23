---
title: 参考 - Azure API 管理资源日志
description: Azure API 管理 GatewayLogs 资源日志的架构参考
services: api-management
author: Johnnytechn
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/18/2020
ms.author: v-johya
ms.openlocfilehash: 8b6623297ccd368688c8817a8cc2e701a651a20e
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978339"
---
# <a name="reference-api-management-resource-log-schema"></a>参考：Azure API 管理资源日志架构

本文提供 Azure API 管理 GatewayLogs 资源日志的架构参考。 日志条目还包括[顶级通用架构](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema)中的字段。

若要在 API 管理中启用资源日志的收集，请参阅[监视已发布的 API](api-management-howto-use-azure-monitor.md#resource-logs)。

## <a name="gatewaylogs-schema"></a>GatewayLogs 架构

对于每个 API 请求，记录以下属性。

| 属性  | 类型 | 说明 |
| ------------- | ------------- | ------------- |
| method | 字符串 | 传入请求的 HTTP 方法 |
| url | 字符串 | 传入请求的 URL |
| responseCode | integer | 发送到客户端的 HTTP 响应的状态代码 |
| responseSize | integer | 在请求处理过程中发送到客户端的字节数 | 
| cache | 字符串 | 在请求处理过程中涉及的 API 管理缓存的状态（命中、未命中、无） | 
| apiId | 字符串 | 当前请求的 API 实体标识符 | 
| operationId | 字符串 | 当前请求的操作实体标识符 | 
| clientProtocol | 字符串 | 传入请求的 HTTP 协议版本 |
| clientTime | integer | 花在整个客户端 I/O（连接、发送和接收字节）上的时间（毫秒） | 
| apiRevision | 字符串 | 当前请求的 API 修订版本 | 
| clientTlsVersion| 字符串 | 客户端发送请求使用的 TLS 版本 |
| lastError | 对象 | 对于未成功的请求，有关上次请求处理错误的详细信息 | 
| backendMethod | 字符串 | 发送到后端的请求的 HTTP 方法 |
| backendUrl | 字符串 | 发送到后端的请求的 URL |
| backendResponseCode | integer | 从后端收到的 HTTP 响应代码 |
| backedProtocol | 字符串 | 发送到后端的请求的 HTTP 协议版本 |
| backendTime | integer | 花在整个后端 IO（连接、发送和接收字节）上的时间（毫秒） | 


## <a name="next-steps"></a>后续步骤

* 有关在 API 管理中监视 API 的信息，请参阅[监视已发布的 API](api-management-howto-use-azure-monitor.md)
* 了解有关 [Azure 资源日志的通用和特定于服务的架构](../azure-monitor/platform/resource-logs-schema.md)的详细信息


