---
title: 存储函数管理概述 - Azure 数据资源管理器 | Microsoft Docs
description: 本文概述了 Azure 数据资源管理器中的存储函数管理。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/18/2020
ms.date: 01/22/2021
ms.openlocfilehash: 2affd078e8104b419e7515d03b9bb0b9fdb99e39
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611685"
---
# <a name="stored-functions-management-overview"></a>存储函数管理概述
本部分介绍用于创建和更改以下[用户定义函数](../query/functions/user-defined-functions.md)的控制命令：

|函数 |说明|
|---------|-----------|
|[`.alter function`](alter-function.md) |更改现有函数并将其存储在数据库元数据中 |
|[`.alter function docstring`](alter-docstring-function.md) |更改现有函数的 DocString 值 |
|[`.alter function folder`](alter-folder-function.md) |更改现有函数的 Folder 值 |
|[`.create function`](create-function.md) |创建存储函数 |
|[`.create-or-alter function`](create-alter-function.md) |创建存储函数或更改现有函数，并将其存储在数据库元数据中 |
|[`.drop function` 和 `.drop functions`](drop-function.md) |从数据库中删除一个（或多个）函数 |
|[`.show functions` 和 `.show function`](show-function.md) |列出当前所选数据库中的所有存储函数或特定函数 |