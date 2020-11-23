---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 解决包或命名空间加载错误 - Azure Databricks
description: 了解如何解决 Azure Databricks 笔记本中的包或命名空间加载错误。
ms.openlocfilehash: b348777462fcb66c53574af84db7b3e034e996e1
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589519"
---
# <a name="resolving-package-or-namespace-loading-error"></a>解决包或命名空间加载错误

本文介绍如何解决包或命名空间加载错误。

## <a name="problem"></a>问题

在笔记本单元格中安装和加载某些库时，例如：

```r
library(BreakoutDetection)
```

你可能会发现以下错误：

```console
Loading required package: BreakoutDetection:

Error : package or namespace load failed for ‘BreakoutDetection’ in loadNamespace(i, c(lib.loc, .libPaths()), versionCheck = vI[[i]]):
namespace ‘rlang’ 0.3.1 is already loaded, but >= 0.3.4 is required
```

## <a name="cause"></a>原因

将笔记本附加到群集时，无法刷新 R 命名空间。 当 R 包依赖于较新的包版本时，所需的包已下载但无法加载。 加载包时，可看到此错误。

## <a name="solution"></a>解决方案

若要解决此错误，请将所需的包作为[群集安装的库](/databricks/libraries#install-a-library-on-a-cluster)安装。