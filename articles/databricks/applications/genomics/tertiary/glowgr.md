---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 06/23/2020
title: GloWGR - 全基因组回归 - Azure Databricks
description: 了解有关在用于基因组学的 Databricks Runtime 中使用 GloWGR 的信息。
ms.openlocfilehash: ff99a2f217f9df14519c99f97996a7913a502d40
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589697"
---
# <a name="glowgr-whole-genome-regression"></a>GloWGR：整个基因组回归

[GloWGR](https://glow.readthedocs.io/en/latest/tertiary/whole-genome-regression.html) 是借助 [Project Glow](http://projectglow.io/)，采用开放源代码的形式与 [Regeneron 遗传学中心](https://www.regeneron.com/genetics-center)协作开发的可缩放全基因组回归方法。 GloWGR 是单节点工具 [regenie](https://rgcgithub.github.io/regenie/) 的发行版（请参阅 [bioRxiv 预打印第二版](https://www.biorxiv.org/content/10.1101/2020.06.19.162354v2)）。
GloWGR 是一个企业级工具，它为全基因组回归提供了与其他方法相当的准确性，但在速度上有数量级的提高。

## <a name="create-a-glowgr-cluster"></a>创建 GloWGR 群集

在用于基因组学的 Databricks Runtime 7.0 及更高版本中运行 GloWGR。

* 群集配置应使用[用于基因组学的 Databricks Runtime](../../../runtime/genomicsruntime.md#dbr-genomics)。
* 若要获得最佳性能，请使用针对存储进行了优化的 VM。 建议使用 Standard_L16s_v2。

## <a name="example-notebook"></a>示例笔记本

### <a name="glowgr-notebook"></a>GloWGR 笔记本

[获取笔记本](../../../_static/notebooks/genomics/glowgr.html)