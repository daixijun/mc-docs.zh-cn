---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/04/2020
title: 二级分析 - Azure Databricks
description: 了解如何使用包含在用于基因组学的 Databricks Runtime 中的预包装管道。
ms.openlocfilehash: dd0a0c005e713f2df848a3477c6f12eacd64e973
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589704"
---
# <a name="secondary-analysis"></a>二级分析

[用于基因组学的 Databricks Runtime](../../../runtime/genomicsruntime.md#dbr-genomics) 包含预包装的管道，可以协调读取操作并检测和批注单个样本中的变异（使用 Apache Spark 实现并行化）。

* [DNASeq 管道](dnaseq-pipeline.md)
  * [安装](dnaseq-pipeline.md#setup)
  * [参数](dnaseq-pipeline.md#parameters)
  * [自定义](dnaseq-pipeline.md#customization)
  * [参考基因组](dnaseq-pipeline.md#reference-genomes)
  * [清单格式](dnaseq-pipeline.md#manifest-format)
  * [支持的输入格式](dnaseq-pipeline.md#supported-input-formats)
  * [输出](dnaseq-pipeline.md#output)
  * [故障排除](dnaseq-pipeline.md#troubleshooting)
  * [以编程方式运行](dnaseq-pipeline.md#run-programmatically)
* [RNASeq 管道](rnaseq-pipeline.md)
  * [安装](rnaseq-pipeline.md#setup)
  * [参数](rnaseq-pipeline.md#parameters)
  * [演练](rnaseq-pipeline.md#walkthrough)
  * [其他使用信息和故障排除](rnaseq-pipeline.md#additional-usage-info-and-troubleshooting)
* [“肿瘤/正常”管道](tumor-normal-pipeline.md)
  * [演练](tumor-normal-pipeline.md#walkthrough)
  * [安装](tumor-normal-pipeline.md#setup)
  * [参数](tumor-normal-pipeline.md#parameters)
  * [参考基因组](tumor-normal-pipeline.md#reference-genomes)
  * [清单格式](tumor-normal-pipeline.md#manifest-format)
  * [其他使用信息和故障排除](tumor-normal-pipeline.md#additional-usage-info-and-troubleshooting)
* [变异批注方法](annotate-variants.md)
  * [预先打包的 SnpEff 批注管道](snpeff-pipeline.md)
  * [预先打包的 VEP 批注管道](vep-pipeline.md)
  * [使用管道转换器进行变体批注](variant-annotation-pipe.md)