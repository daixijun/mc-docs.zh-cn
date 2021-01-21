---
title: 函数 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的函数。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 10/23/2018
ms.date: 01/22/2021
ms.localizationpriority: high
ms.openlocfilehash: 85995aaae84dc8e3f9e8b04b7963161bbeeaf60f
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611643"
---
# <a name="function-types"></a>函数类型

**函数** 是可重用的查询或查询部件。 Kusto 支持多种函数：

* **存储函数**，是用户定义的函数，用于存储和管理数据库的某类架构实体。
  请参阅[存储函数](../schema-entities/stored-functions.md)。
* **查询定义函数**，是用户定义的函数，在单个查询范围内定义和使用。 定义此类函数需使用 [let 语句](../letstatement.md)。
  请参阅[用户定义函数](./user-defined-functions.md)。
* **内置函数**，采用硬编码形式（由 Kusto 定义，不能由用户修改）。