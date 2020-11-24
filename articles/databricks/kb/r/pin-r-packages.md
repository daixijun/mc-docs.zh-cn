---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 修正 R 包的版本 - Azure Databricks
description: 了解如何修正 R 包的版本。
ms.openlocfilehash: 7dea10d8a7924e70cf7c5d4dc4be5c21349ab3c3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589516"
---
# <a name="fix-the-version-of-r-packages"></a><a id="cran_version"> </a><a id="fix-the-version-of-r-packages"> </a>修正 R 包的版本

使用 [install.packages()](https://www.rdocumentation.org/packages/utils/versions/3.6.2/topics/install.packages) 函数安装 CRAN 包时，无法指定包的版本，因为预期会安装该包的最新版本，并且该版本应与其依赖项的最新版本兼容。 如果你安装了过时的依赖项，则也会对其进行更新。

有时，你需要修正 R 包的版本。 有若干方法可实现此操作：

* 使用 [devtools 包](https://cran.r-project.org/web/packages/devtools/index.html)。
* 从 [CRAN 存档](https://cran.r-project.org/src/contrib/Archive/)下载并安装包文件。
* 使用 CRAN 快照。

使用[库 UI 或 API](/databricks/libraries) 在群集的所有实例上安装 R 包时，建议你使用第三个选项。

Microsoft R 应用程序网络维护着一个 [CRAN 时间机器](https://mran.microsoft.com/timemachine)，该机器每天晚上存储 CRAN 的快照。 快照可以在 `https://cran.microsoft.com/snapshot/<date>` 上获得，其中 `<date>` 是所需快照的日期（例如，2019-05-01）。 若要安装 R 包的特定版本，请在创建库时将此 URL 指定为 [CRAN 库](/databricks/libraries#cran-libraries)的存储库。