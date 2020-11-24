---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: Delta 缓存在自动缩放群集上的行为方式 - Azure Databricks
description: 了解 Delta 缓存在自动缩放群集上的行为方式。
ms.openlocfilehash: 45fa9455fc43be5c3eb7dbbb949a939fb724a0b4
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589741"
---
# <a name="how-delta-cache-behaves-on-an-autoscaling-cluster"></a>Delta 缓存在自动缩放群集上的行为方式

本文介绍 [Delta 缓存](/databricks/delta/optimizations/delta-cache)在自动缩放群集上的行为方式，该群集会根据需要删除或添加节点。

* 当群集缩减和终止节点时：

  Delta 缓存的行为方式与 RDD 缓存相同。 当节点出现故障时，该特定节点中的所有缓存数据都会丢失。 不会从丢失的节点移动 Delta 缓存数据。

* 当群集扩展和添加新节点时：

  当群集添加新节点时，数据不会在缓存之间移动。 当应用程序下次再次访问数据或表时，系统会重新缓存丢失的数据。