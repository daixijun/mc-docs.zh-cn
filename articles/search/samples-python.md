---
title: Python 示例
titleSuffix: Azure Cognitive Search
description: 查找 Azure 认知搜索 Python 代码示例演示，这些示例使用适用于 Python 或 REST 的 Azure .NET SDK。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 11/17/2020
ms.date: 12/10/2020
ms.openlocfilehash: 1eda15d575bcfc9a6575d2d62a66a3d5f59767ef
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004277"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 Python 代码示例

了解演示 Azure 认知搜索的特性和功能的 Python 代码示例。 主存储库如下所示：

| 存储库 | 说明 |
|------------|-------------|
| [azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | SDK 中 Azure.Search.Documents 客户端库附带的由 Azure SDK 团队生成的示例。 你还可以查看客户端库的[单元测试](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests)，以了解如何调用各种 API。 |
| [Azure-Samples/azure-search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples) | 操作方法文章附带的代码示例，包括[快速入门：使用 Python 创建搜索索引](search-get-started-python.md)。|

> [!Tip]
> 请尝试使用[示例浏览器](https://docs.microsoft.com/samples/browse/?languages=csharp&products=azure-cognitive-search)来搜索 Github 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。

## <a name="python-sdk-samples"></a>Python SDK 示例

适用于 Python 的 Azure SDK 包含大量示例和一个介绍了先决条件和包安装的[入门页面](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples)。 此页面还包含指向以下示例的链接，为方便起见，这些示例在此处列出。

| 示例 | 说明 |
|---------|-------------|
| [身份验证](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | 演示如何配置客户端以及向服务进行身份验证。 | 
| [索引创建-读取-更新-删除操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | 演示如何创建、更新、获取、列出和删除[搜索索引](search-what-is-an-index.md)。 |
| [索引器创建-读取-更新-删除操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | 演示如何创建、更新、获取、列出、重置和删除[索引器](search-indexer-overview.md)。 |
| [搜索索引器数据源](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | 演示如何创建、更新、获取、列出和删除索引器数据源，对于基于索引器为[支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [同义词](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | 演示如何创建、更新、获取、列出和删除[同义词映射](search-synonyms.md)。  |
| [加载文档](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | 演示如何在[数据导入](search-what-is-data-import.md)操作中将文档上传或合并到索引中。 |
| [简单查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | 演示如何设置[基本查询](search-query-overview.md)。 |
| [筛选器查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | 演示如何设置[筛选器表达式](search-filters.md)。 |
| [Facet 查询](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | 演示如何使用 [facet](search-filters-facets.md)。 |

## <a name="documentation-samples"></a>文档示例

在 [Azure 认知搜索文档](https://docs.azure.cn/search/)中有与以下示例相关联的文章。

| 示例 | 说明 | 
|---------|-------------|
| [快速入门](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | [快速入门：使用 Python 创建搜索索引](search-get-started-python.md)的源代码。  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | [教程：使用 Python 和 AI 从 Azure Blob 生成可搜索的内容](cognitive-search-tutorial-blob-python.md)的源代码。  |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | [示例：使用 Python 创建自定义技能](cognitive-search-custom-skill-python.md)的源代码。  |