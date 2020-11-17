---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 06/23/2020
title: 三级分析 - Azure Databricks
description: 了解如何对遗传变异进行总体规模的统计分析。
ms.openlocfilehash: f7065c75322d031c28c8d3760ab82d75c72fe0ef
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589687"
---
# <a name="tertiary-analysis"></a>三级分析

完成质量控制以后，请对遗传变异进行总体规模的统计分析。

* [联合基因分型管道](joint-genotyping-pipeline.md)
  * [演练](joint-genotyping-pipeline.md#walkthrough)
  * [安装](joint-genotyping-pipeline.md#setup)
  * [参数](joint-genotyping-pipeline.md#parameters)
  * [输出](joint-genotyping-pipeline.md#output)
  * [参考基因组](joint-genotyping-pipeline.md#reference-genomes)
  * [清单格式](joint-genotyping-pipeline.md#manifest-format)
  * [故障排除](joint-genotyping-pipeline.md#troubleshooting)
  * [其他使用信息](joint-genotyping-pipeline.md#additional-usage-info)
* [GloWGR：整个基因组回归](glowgr.md)
  * [创建 GloWGR 群集](glowgr.md#create-a-glowgr-cluster)
  * [示例笔记本](glowgr.md#example-notebook)
* [SAIGE 和 SAIGE-GENE（使用管道转换器）](saige.md)
  * [创建 SAIGE 群集](saige.md#create-a-saige-cluster)
  * [运行关联测试](saige.md#run-association-tests)
  * [示例笔记本](saige.md#example-notebooks)
* [Hail 0.2](hail.md)
  * [创建 Hail 群集](hail.md#create-a-hail-cluster)
  * [在笔记本中使用 Hail](hail.md#use-hail-in-a-notebook)
  * [限制](hail.md#limitations)