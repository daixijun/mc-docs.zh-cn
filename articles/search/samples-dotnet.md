---
title: .NET 示例
titleSuffix: Azure Cognitive Search
description: 查找使用 .NET 客户端库的 Azure 认知搜索演示 C# 代码示例。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 11/17/2020
ms.date: 01/14/2021
ms.openlocfilehash: 51a1ccb7b6adfb5d2359d99243fe1ba65de02992
ms.sourcegitcommit: 01cd9148f4a59f2be4352612b0705f9a1917a774
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194716"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 .NET (C#) 代码示例

了解演示 Azure 认知搜索的特性和功能的 C# 代码示例。 主存储库如下所示：

| 存储库 | 说明 |
|------------|-------------|
| [azure-sdk-for-net/sdk/search/Azure.Search.Documents/samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | SDK 中 Azure.Search.Documents 客户端库附带的由 Azure SDK 团队生成的示例。 你还可以查看客户端库的[单元测试](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests)，以了解如何调用各种 API。 |
| [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | 文档中操作说明文章附带的示例，包括[如何使用 .NET 客户端库](search-howto-dotnet-sdk.md)。|
| [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) | 文档中快速入门和教程附带的示例。|

> [!Tip]
> 请尝试使用[示例浏览器](https://docs.microsoft.com/samples/browse/?languages=csharp&products=azure-cognitive-search)来搜索 Github 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。

## <a name="net-sdk-samples"></a>.NET SDK 示例

用于 .NET 的 Azure SDK 包含多个示例和一个描述每个示例的[示例自述文件](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md)。 下面提供了此列表，以方便查看。

| 示例 | 说明 |
|---------|-------------|
| [“Hello world”，同步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 演示如何使用同步方法创建客户端、验证身份和处理错误。|
| [“Hello world”，异步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 演示如何使用异步方法创建客户端、验证身份和处理错误。  |
| [服务级操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | 演示如何创建索引、索引器、数据源、技能组和同义词映射。 此示例还演示如何获取服务统计信息以及如何查询索引。  |
| [索引操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 演示如何对现有索引执行操作，在此示例中，演示如何获取索引中存储的文档数。  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | 演示用于处理不受支持的数据类型的方法。  |
| [为文档编制索引（推送模型）](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | 编制索引的“推送”模型，用于将 JSON 有效负载发送到服务的索引。   |
| [加密密钥示例](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | 演示如何使用客户管理的加密密钥为敏感内容添加额外保护层。  |

## <a name="documentation-samples"></a>文档示例

在 [Azure 认知搜索文档](./index.yml)中有与以下示例相关联的文章。

| 示例 | 说明 |
|---------|-------------|
| [快速入门](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | [快速入门：创建搜索索引](search-get-started-dotnet.md)的源代码。  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [如何使用 .NET 客户端库](search-howto-dotnet-sdk.md)的源代码 |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | 同义词列表用于扩展查询，可提供索引外部的匹配术语。 此示例包含在[示例：在 C# 中添加同义词](search-synonyms-tutorial-sdk.md)。 |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | 各项目中与索引器相关的代码片段的源代码。 此示例演示如何配置一个具有计划、字段映射和参数的索引器。  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [如何配置客户管理的密钥以进行数据加密](search-security-manage-encryption-keys.md)的源代码 |
| [使用 C# 创建自己的第一个应用](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [教程：创建自己的第一个搜索应用](tutorial-csharp-create-first-app.md)的源代码。 虽然大多数示例都是控制台应用程序，但此 MVC 示例使用网页作为酒店索引示例的前端，演示了基本搜索、分页、自动完成以及建议的查询、分面和筛选。 |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | [教程：从多个数据源编制索引](tutorial-multiple-data-sources.md)的源代码。 |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | [教程：使用推送 API 优化索引编制](tutorial-optimize-indexing-push-api.md)的源代码。  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | [教程：AI 使用 .NET SDK 从 Azure Blob 生成可搜索内容](cognitive-search-tutorial-blob-dotnet.md)的源代码。  |

## <a name="standalone-samples-and-solutions"></a>独立示例和解决方案

| 示例 | 说明 |
|---------|-------------|
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | 可合并到你自己的解决方案中的可耗用自定义技能的源代码。  |
| [知识挖掘解决方案加速器](https://docs.microsoft.com/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | 包括模板、支持文件和分析报表，以帮助你建立端到端知识挖掘解决方案的原型。  |
| [Covid-19 搜索应用存储库](https://github.com/liamca/covid19search) | 基于认知搜索的 [Covid-19 搜索应用](https://covid19search.azurewebsites.net/)的源代码存储库 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | 详细了解 [JFK 解决方案](https://www.microsoft.com/ai/ai-lab-jfk-files)。 |