---
title: 搜索纯文本 blob
titleSuffix: Azure Cognitive Search
description: 配置搜索索引器以从 Azure Blob 中提取纯文本，以便在 Azure 认知搜索中进行全文搜索。
manager: nitinme
author: mgottein
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 09/25/2020
ms.date: 09/25/2020
ms.openlocfilehash: 38293785034c1f6a1adeb7a7977651aae9781053
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300530"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中为纯文本 blob 编制索引

使用 [Blob 索引器](search-howto-indexing-azure-blob-storage.md)提取可搜索文本进行全文搜索时，可以调用各种分析模式以获得更好的索引结果。 默认情况下，该索引器会将分隔的文本 blob 分析为单个文本块。 然而，如果所有 blob 都包含采用同一编码的纯文本，则可以通过使用文本分析模式显著提高索引编制性能。

## <a name="set-up-plain-text-indexing"></a>设置纯文本编制索引

若要对纯文本 blob 编制索引，请使用 `text` 的 `parsingMode` 配置属性根据[创建索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)请求创建或更新索引器：

```http
    PUT https://[service name].search.azure.cn/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

默认情况下将采用 `UTF-8` 编码。 若要指定不同的编码，请使用 `encoding` 配置属性： 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>请求示例

在索引器定义中指定分析模式。

```http
    POST https://[service name].search.azure.cn/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

<!--
## Help us make Azure Cognitive Search better

If you have feature requests or ideas for improvements, provide your input on [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). If you need help using the existing feature, post your question on [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).
-->

## <a name="next-steps"></a>后续步骤

* [Azure 认知搜索中的索引器](search-indexer-overview.md)
* [如何配置 Blob 索引器](search-howto-indexing-azure-blob-storage.md)
* [Blob 索引概述](search-blob-storage-integration.md)