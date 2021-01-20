---
title: JavaScript 示例
titleSuffix: Azure Cognitive Search
description: 查找 Azure 认知搜索 JavaScript 代码示例演示，这些示例使用适用于 JavaScript 的 Azure .NET SDK。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 11/17/2020
ms.date: 01/14/2021
ms.openlocfilehash: ab890096501e2688dc2186202ce60210478f8278
ms.sourcegitcommit: 01cd9148f4a59f2be4352612b0705f9a1917a774
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194699"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 JavaScript 代码示例

了解演示 Azure 认知搜索的特性和功能的 JavaScript 代码示例。 主存储库如下所示：

| 存储库 | 说明 |
|------------|-------------|
| [azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | 由 Azure SDK 团队生成的随 SDK 中 Azure.Search.Documents 客户端库附带的示例。 你还可以查看客户端库的[单元测试](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test)，以了解如何调用各种 API。 |
| [Azure-Samples/azure-search-javascript-samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | 操作方法文章附带的代码示例，包括[快速入门：在 JavaScript 中创建搜索索引](search-get-started-javascript.md)。|

> [!Tip]
> 请尝试使用[示例浏览器](https://docs.microsoft.com/samples/browse/?languages=javascript&products=azure-cognitive-search)来搜索 Github 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。

## <a name="javascript-sdk-samples"></a>JavaScript SDK 示例

适用于 Java 的 Azure SDK 包含大量示例和一个介绍包安装、客户端设置和故障排除的[入门页面](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started)。 此页面还介绍了以下示例类别，为方便起见，这些示例类别在此处列出。

| 示例 | 说明 |
|---------|-------------|
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | 演示如何创建、更新、获取、列出和删除[搜索索引](search-what-is-an-index.md)。 此样本类别还包括一个服务统计信息示例。 |
| [dataSourceConnections（用于索引器）](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | 演示如何创建、更新、获取、列出和删除索引器数据源，对于基于索引器为[支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [索引器](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  演示如何创建、更新、获取、列出、重置和删除[索引器](search-indexer-overview.md)。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   演示如何创建、更新、获取、列出和删除[技能组](cognitive-search-working-with-skillsets.md)，这些技能组是附加的索引器，并且在编制索引期间执行基于 AI 的扩充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | 演示如何创建、更新、获取、列出和删除[同义词映射](search-synonyms.md)。  |
| [查询](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | 演示如何针对 Microsoft 托管的只读公共索引执行查询。  |

## <a name="typescript-samples"></a>TypeScript 示例

SDK 还提供 TypeScript 示例，为方便起见，这些示例在此处列出。

| 示例 | 说明 |
|---------|-------------|
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | 演示如何创建、更新、获取、列出和删除[搜索索引](search-what-is-an-index.md)。 此样本类别还包括一个服务统计信息示例。 |
| [dataSourceConnections（用于索引器）](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | 演示如何创建、更新、获取、列出和删除索引器数据源，对于基于索引器为[支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [索引器](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  演示如何创建、更新、获取、列出、重置和删除[索引器](search-indexer-overview.md)。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   演示如何创建、更新、获取、列出和删除[技能组](cognitive-search-working-with-skillsets.md)，这些技能组是附加的索引器，并且在编制索引期间执行基于 AI 的扩充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | 演示如何创建、更新、获取、列出和删除[同义词映射](search-synonyms.md)。  |
| [查询](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | 演示如何针对 Microsoft 托管的只读公共索引执行查询。  |

## <a name="documentation-samples"></a>文档示例

在 [Azure 认知搜索文档](./index.yml)中有与以下示例相关联的文章。

| 示例 | 说明 | 
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | [快速入门：在 JavaScript 中创建搜索索引](search-get-started-javascript.md)的源代码。  |

## <a name="standalone-samples"></a>独立示例

| 示例 | 说明 |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | Azure 认知搜索的 React 模板 (github.com) |