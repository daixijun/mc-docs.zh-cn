---
title: 创建基本查询
titleSuffix: Azure Cognitive Search
description: 了解如何在认知搜索中构造查询请求，使用哪些工具和 API 进行测试和编码，以及查询决策如何从索引设计开始。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 12/14/2020
ms.date: 01/14/2021
ms.openlocfilehash: b3801562ace36d3eedc0a059ed21e140c2d581f4
ms.sourcegitcommit: 01cd9148f4a59f2be4352612b0705f9a1917a774
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194850"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>在 Azure 认知搜索中创建查询

如果是第一次构建查询，本文将介绍将所需的工具和 API、用于创建查询的方法以及索引结构和内容如何影响查询结果。 有关查询请求的样式介绍，请首先参阅[查询类型和组合](search-query-overview.md)。

## <a name="choose-tools-and-apis"></a>选择工具和 API

你需要一个工具或 API 来创建查询。 以下任何建议都可用于测试和生产工作负载。

| 方法 | 说明 |
|-------------|-------------|
| 门户| [搜索资源管理器（门户）](search-explorer.md)是 Azure 门户中的一个查询界面，用于针对基础搜索服务上的索引运行查询。 该门户在后台对[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)操作进行 REST API 调用，但不能调用自动完成、建议或文档查找。<br/><br/> 你可以选择任何索引和 REST API 版本（包括预览版）。 查询字符串可以使用简单语法或完整语法，并支持所有查询参数（filter、select 和 searchFields 等）。 在门户中，打开索引时，可以使用搜索资源管理器以及并排选项卡中的索引 JSON 定义，以便轻松访问字段属性。 在测试查询时，检查哪些字段可搜索、可排序、可筛选和可分面。 <br/>建议用于早期调查、测试和验证。 [了解详细信息。](search-explorer.md) |
| Web 测试工具| [Postman 或 Visual Studio Code](search-get-started-rest.md) 是用 REST 表示[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)请求和任何其他请求的强大选择。 REST API 支持 Azure 认知搜索中所有可能的编程操作，当你使用 Postman 或 Visual Studio Code 等工具时，可以在编码前以交互方式发出请求，了解该功能的工作方式。 如果在 Azure 门户中没有参与者权限或管理权限，则 Web 测试工具是一个不错的选择。 只要有搜索 URL 和查询 API 密钥，就可以使用这些工具对现有索引运行查询。 |
| Azure SDK | 准备好编写代码时，可以在适用于 .NET、Python、JavaScript 或 Java 的 Azure SDK 中使用 Azure.Search.Document 客户端库。 每个 SDK 都有自己的发布计划，但你可以在所有 SDK 中创建和查询索引。 <br/><br/>[SearchClient (.NET)](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) 可用于查询 C# 中的搜索索引。  [了解详细信息。](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (Python)](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) 可用于查询 Python 中的搜索索引。 [了解详细信息。](search-get-started-python.md)<br/><br/>[SearchClient (JavaScript)](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) 可用于查询 JavaScript 中的搜索索引。 [了解详细信息。](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>设置搜索客户端

搜索客户端向搜索服务进行身份验证、发送请求和处理响应。 无论使用哪种工具或 API，搜索客户端都必须具有以下功能：

| 属性 | 说明 |
|------------|-------------|
| 端点 | 搜索服务的可寻址 URL 格式为：`https://[service-name].search.azure.cn`。 |
| API 访问密钥（管理员或查询） | 对搜索服务的请求进行身份验证。 |
| 索引名称 | 查询始终指向单个索引的文档集合。 不能联接索引或者创建自定义或临时数据结构作为查询目标。 |
| API 版本 | REST 调用在请求中显式要求 `api-version`。 相比之下，Azure SDK 中的客户端库则针对特定 REST API 版本进行版本控制。 对于 SDK，`api-version` 是隐式的。 |

### <a name="in-the-portal"></a>在门户中

搜索资源管理器和其他门户工具有一个连接到服务的内置客户端连接，可以从门户页面直接访问索引和其他对象。 访问工具、向导和对象需要具有服务的参与者角色或更高级别的成员身份。 

### <a name="using-rest"></a>使用 REST

对于 REST 调用，可以使用 [Postman 或类似工具](search-get-started-rest.md)作为客户端来指定[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)请求。 每个请求都是独立的，因此必须在每个请求中提供终结点、索引名称和 API 版本。 其他属性（内容类型和 API 密钥）通过请求头传递。 

可以使用 POST 或 GET 来查询索引。 POST 在请求正文中指定了参数，因此更易于使用。 如果使用 POST，请确保在 URL 中包含 `docs/search`：

```http
POST https://myservice.search.azure.cn/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>使用 Azure SDK

如果使用 Azure SDK，请在代码中创建客户端。 所有 SDK 都提供了可以保存状态的搜索客户端，允许连接重复使用。 对于查询操作，将实例化 `SearchClient` 并为以下属性提供值：终结点、密钥、索引。 然后可以调用 `Search method` 传递查询字符串。 

| 语言 | 客户端 | 示例 |
|----------|--------|---------|
| C# 和 .NET | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) | [发送使用 C# 编写的第一个搜索查询](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](https://docs.microsoft.com/python/api/azure-search-documents/azure.search.documents.searchclient) | [发送使用 Python 编写的第一个搜索查询](https://docs.microsoft.com/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](https://docs.microsoft.com/java/api/com.azure.search.documents.searchclient) | [发送使用 Java 编写的第一个搜索查询](https://docs.microsoft.com/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| Javascript  | [SearchClient](https://docs.microsoft.com/javascript/api/@azure/search-documents/searchclient) | [发送使用 JavaScript 编写的第一个搜索查询](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>选择一个分析器：简单 | 完整

如果查询是全文搜索，分析程序将用于处理搜索参数的内容。 Azure 认知搜索提供了两个查询分析程序。 简单分析程序理解[简单查询语法](query-simple-syntax.md)。 选择此分析程序作为默认分析程序是因为它在自由格式文本查询中的速度和有效性。 该语法支持将常见搜索运算符（AND、OR、NOT）用于术语和短语搜索，以及前缀 (`*`) 搜索（例如，使用“sea*”搜索 Seattle 和 Seaside）。 一般建议首先尝试使用简单版分析程序，如果应用程序需要更强大的查询，则可以改用完整版分析程序。

向请求添加 `queryType=full` 时启用的[完整 Lucene 查询语法](query-Lucene-syntax.md#bkmk_syntax)基于 [Apache Lucene 分析程序](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

虽然完整语法和简单语法存在重叠，都支持相同的前缀和布尔操作，但完整语法提供了更多的操作符。 在完整语法中，布尔表达式有了更多运算符，高级查询（如模糊搜索、通配符搜索、邻近搜索和正则表达式）也有了更多运算符。

## <a name="choose-query-methods"></a>选择查询方法

搜索本质上是一个用户驱动的活动，该活动从搜索框或页面上的单击事件中收集术语或短语。 下表总结了收集用户输入的机制以及预期的搜索体验。

| 输入 | 体验 |
|-------|---------|
| [搜索方法](https://docs.microsoft.com/rest/api/searchservice/search-documents) | 用户在搜索框中键入术语或短语（带或不带运算符），然后单击“搜索”发送请求。 搜索可与筛选器一起用于同一请求，但不能与自动完成或建议一起使用。 |
| [“自动完成”方法](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | 用户键入几个字符，然后在键入每个新字符后启动查询。 响应是索引中的已完成字符串。 如果提供的字符串有效，则用户单击“搜索”将该查询发送到服务。 |
| [“建议”方法](https://docs.microsoft.com/rest/api/searchservice/suggestions) | 与自动完成一样，用户键入几个字符并生成增量查询。 响应是匹配文档的下拉列表，通常由几个唯一或描述性字段表示。 如果任何一个选择有效，用户单击其中一个，就会返回匹配的文档。 |
| [多面导航](https://docs.microsoft.com/rest/api/searchservice/search-documents#query-parameters) | 页面显示可单击的导航链接或导航痕迹，缩小了搜索范围。 分面导航结构基于初始查询动态组合。 例如，`search=*` 可用于填充由每个可能的类别组成的分面导航树。 分面导航结构是从查询响应创建的，但它也是表示下一个查询的机制。 在 REST API 引用中，`facets` 作为“搜索文档”操作的查询参数记录，但是它可以在没有 `search` 参数的情况下使用。|
| [筛选器方法](https://docs.microsoft.com/rest/api/searchservice/search-documents#query-parameters) | 筛选器与分面一起使用，以缩小结果范围。 你还可以在页面后面实现一个筛选器，例如用特定于语言的字段初始化页面。 在 REST API 引用中，`$filter` 作为“搜索文档”操作的查询参数记录，但是它可以在没有 `search` 参数的情况下使用。|

## <a name="know-your-field-attributes"></a>了解字段属性

如果之前回顾过[查询请求的基础知识](search-query-overview.md)，则可能还记得查询请求上的参数取决于字段在索引中的属性。 例如，要在查询、筛选或排序顺序中使用，字段必须可搜索、可筛选和可排序  。 同样，只有标记为“可检索”的字段才能出现在结果中。 开始在请求中指定 `search`、`filter` 和 `orderby` 参数时，请确保检查属性以避免出现意外的结果。

在下面的[酒店示例索引](search-get-started-portal.md)门户屏幕截图中，只有最后两个字段“LastRenovationDate”和“Rating”可用于 `"$orderby"` only 子句。

![酒店示例的索引定义](./media/search-query-overview/hotel-sample-index-definition.png "酒店示例的索引定义")

有关字段属性的描述，请参阅[创建索引 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)。

## <a name="know-your-tokens"></a>了解标记

在编制索引期间，查询引擎使用分析器对字符串执行文本分析，最大限度地提高查询时匹配的可能性。 字符串至少是小写的，但也可能需要进行词形还原和停用词删除。 较大的字符串或复合词通常由空格、连字符或短划线分隔，并作为单独的标记编制索引。 

这里要注意的一点是，你认为索引包含的内容和索引实际包含的内容可能会有所不同。 如果查询没有返回预期的结果，则可以通过[分析文本 (REST API)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) 检查分析器创建的标记。 有关词汇切分及其对查询的影响的详细信息，请参阅[具有特殊字符的部分术语搜索和模式](search-query-partial-matching.md)。

## <a name="next-steps"></a>后续步骤

现在你已经更好地了解了查询请求的构造方式，可以尝试以下快速入门以获得实际操作经验。

+ [搜索资源管理器](search-explorer.md)
+ [如何在 REST 中进行查询](search-get-started-rest.md)
+ [如何在 .NET 中进行查询](search-get-started-dotnet.md)
+ [如何用 Python 查询](search-get-started-python.md)
+ [如何用 JavaScript 查询](search-get-started-javascript.md)