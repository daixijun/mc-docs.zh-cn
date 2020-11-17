---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 08/10/2020
title: 2018 年 12 月 - Azure Databricks
description: Azure Databricks 新功能和改进的 2018 年 12 月发行说明。
ms.openlocfilehash: 4bab0c2bf71dae24abcd9509fcba7318638a3bb8
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329414"
---
# <a name="december-2018"></a>2018 年 12 月

这些功能和 Azure Databricks 平台改进已于 2018 年 12 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="databricks-runtime-51-for-machine-learning-beta-release"></a>现已发布用于机器学习的 Databricks Runtime 5.1 (Beta)

**2018 年 12 月 18 日**

Databricks Runtime 5.1 ML 是基于 Databricks Runtime 5.1 构建的。 若要了解 Databricks Runtime 5.1 中的新增功能，请参阅 [Databricks Runtime 5.1（不受支持）](../../runtime/5.1.md)发行说明。 除 [Databricks Runtime 5.0 ML（beta 版本）](../../runtime/5.0ml.md)中对现有库的更新外，Databricks Runtime 5.1 ML 还包含以下新功能：

* 用于构建深度学习网络的 [PyTorch](../../../applications/machine-learning/train-model/pytorch.md)。

请参阅 [Databricks Runtime 5.1 ML（beta 版本）](../../runtime/5.1ml.md)的完整发行说明。

## <a name="databricks-runtime-51-release"></a>现已发布 Databricks Runtime 5.1

**2018 年 12 月 18 日**

Databricks Runtime 5.1 现已推出。  Databricks Runtime 5.1 包括 Apache Spark 2.4.0、新增的 Delta Lake 和结构化流功能与升级，以及升级的 Python、R、Java 和 Scala 库。 有关详细信息，请参阅 [Databricks Runtime 5.1（不受支持）](../../runtime/5.1.md)。

## <a name="library-ui"></a>库 UI

在 2018 年 12 月 7 日，还原了最初在 2.85 版中发布的[库 UI 更新](november.md#library-ui-update)。

## <a name="access-azure-data-lake-storage-gen1-automatically-with-your-azure-ad-credentials"></a>使用 Azure AD 凭据自动访问 Azure Data Lake Storage Gen1

**2018 年 12 月 5 日至 11 日：版本 2.86**

现可使用登录 Azure Databricks 时所用的相同 Azure Active Directory (Azure AD) 标识，自动从 Azure Databricks 群集向 Azure Data Lake Storage Gen1 进行身份验证。

只需为群集启用 Azure AD 凭据传递，在该群集上运行的命令就可在 Azure Data Lake Storage Gen1 中读取和写入数据，无需你配置用于访问存储的服务主体凭据。

> [!IMPORTANT]
>
> 使用 Azure AD 凭据访问 Azure Data Lake Storage Gen1 的功能以[公共预览版](../../release-types.md)的形式提供。 它需要 Databricks Runtime 5.1，后者当前为 beta 版本，但很快就将正式发布。

有关详细信息，请参阅[使用 Azure Active Directory 凭据自动访问](../../../data/data-sources/azure/azure-datalake.md#adls1-aad-credentials)。