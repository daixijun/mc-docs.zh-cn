---
title: Java 示例
titleSuffix: Azure Cognitive Search
description: 找到 Azure 认知搜索的 Java 代码演示示例，这些示例使用适用于 Java 的 Azure .NET SDK。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 11/17/2020
ms.date: 01/14/2021
ms.openlocfilehash: a26e9db2162a835c0c72a5da367d7b3ef535f8f7
ms.sourcegitcommit: 01cd9148f4a59f2be4352612b0705f9a1917a774
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194711"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 Java 代码示例

了解演示 Azure 认知搜索的特性和功能的 Java 代码示例。 主存储库如下所示：

| 存储库 | 说明 |
|------------|-------------|
| [azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | 由 Azure SDK 团队生成的随 SDK 中 Azure.Search.Documents 客户端库一起提供的示例。 你还可以查看客户端库的[单元测试](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test)，以了解如何调用各种 API。 |
| [Azure-Samples/azure-search-java-samples](https://github.com/Azure-Samples/azure-search-java-samples) | 操作方法文章附带的代码示例。 此存储库中的示例尚未更新为使用适用于 Java 的 Azure SDK。 目前，这些示例在 Java 代码中调用 REST API。|

> [!Tip]
> 请尝试使用[示例浏览器](https://docs.microsoft.com/samples/browse/?languages=java&products=azure-cognitive-search)来搜索 Github 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。

## <a name="java-sdk-samples"></a>Java SDK 示例

适用于 Java 的 Azure SDK 包含大量示例和一个涉及包安装的[入门页面](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples)。 该页面还列出了多种示例。 为方便起见，下面列出了几项比较常见的操作。

| 示例 | 说明 |
|---------|-------------|
| [搜索索引创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | 演示如何创建[搜索索引](search-what-is-an-index.md)。 |
| [同义词创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | 演示如何创建[同义词映射](search-synonyms.md)。  |
| [搜索索引器创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | 演示如何创建[索引器](search-indexer-overview.md)。 |
| [搜索索引器数据源创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | 演示如何创建索引器数据源，对于基于索引器为[受支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [技能组创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  演示如何创建[技能组](cognitive-search-working-with-skillsets.md)，这些技能组是附加的索引器，并且在编制索引期间执行基于 AI 的扩充。 |
| [加载文档](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | 演示如何在[数据导入](search-what-is-data-import.md)操作中将文档上传或合并到索引中。 |
| [查询语法](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | 演示如何设置[基本查询](search-query-overview.md)。 |

## <a name="documentation-samples"></a>文档示例

在 [Azure 认知搜索文档](./index.yml)中有与以下示例相关联的文章。

| 示例 | 说明 | 
|---------|-------------|
| [快速入门](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | 用于[快速入门：用 Java 创建搜索索引](search-get-started-java.md)的源代码。 此示例调用 REST API。 |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | 演示使用 Java 编写的 Azure Cosmos DB 索引器。 此示例调用 REST API。 |