---
title: 搜索索引上查询扩展的同义词
titleSuffix: Azure Cognitive Search
description: 创建一个同义词映射，用于扩展 Azure 认知搜索索引上搜索查询的范围。 扩宽了范围，使其包括你在列表中提供的同义术语。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 12/18/2020
ms.date: 01/14/2021
ms.openlocfilehash: 63c50b31c8698300d54cf05182c071452aed1fd8
ms.sourcegitcommit: 01cd9148f4a59f2be4352612b0705f9a1917a774
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194757"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure 认知搜索中的同义词

使用同义词映射，你可以关联等效字词，从而在无需用户实际提供字词的情况下扩展查询范围。 例如，假设“dog”、“canine”和“puppy”是同义词，则对“canine”的查询会匹配包含“dog”的文档。

## <a name="create-synonyms"></a>创建同义词

同义词映射是一种可以在创建一次后由许多索引使用的资产。 [服务层级](search-limits-quotas-capacity.md#synonym-limits)决定了你可以创建多少个同义词映射，范围为 3 个同义词映射（适用于“免费”层级和“基本”层级）到最多 20 个同义词映射（适用于“标准”层级）。 

如果你的内容包含技术或模糊术语，则你可以为不同的语言（例如英语和法语版本）或词典创建多个同义词映射。 虽然你可以创建多个同义词映射，但是，一个字段当前只能使用其中一个映射。

同义词映射由名称、格式和充当同义词映射条目的规则组成。 唯一受支持的格式是 `solr`，`solr` 格式决定了规则构造。

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

若要创建同义词映射，请使用[创建同义词映射 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map) 或 Azure SDK。 对于 C# 开发人员，建议从[使用 C# 在 Azure 认知搜索中添加同义词](search-synonyms-tutorial-sdk.md)开始。

## <a name="define-rules"></a>定义规则

映射规则遵循 Apache Solr 的开源同义词筛选器规范，详情请参阅此文档：[SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter)。`solr` 格式支持两种类型的规则：

+ 等效（例如，字词在查询中是等效替代项）

+ 显式映射（例如，字词在查询之前映射到一个显式字词）

每个规则都必须由换行符 (`\n`) 分隔。 在免费服务中可为每个同义词映射定义最多 5,000 条规则，在其他层级中可为每个映射定义最多 20,000 条规则。 每条规则可包含最多 20 个扩展（或规则中的项）。 有关详细信息，请参阅[同义词限制](search-limits-quotas-capacity.md#synonym-limits)。

查询分析程序会将任何大写或混合大小写的字词转换为小写，但如果你想在字符串中保留特殊字符（例如逗号或短划线），请在创建同义词映射时添加合适的转义字符。 

### <a name="equivalency-rules"></a>等效规则

等效字词的规则在同一规则中用逗号分隔。 在第一个示例中，针对 `USA` 的查询将扩展为 `USA` OR `"United States"` OR `"United States of America"`。 请注意，如果想要匹配某个短语，则查询本身必须是带引号的短语查询。

对于等效情况，对 `dog` 的查询会对查询进行扩展，以同时包括 `puppy` 和 `canine`。

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>显式映射

显式映射的规则由箭头 `=>` 表示。 在指定它的情况下，与 `=>` 左侧内容匹配的一系列搜索查询字词在查询时会被替换为右侧的替代项。

对于显式情况，对 `Washington`、`Wash.` 或 `WA` 的查询会被重写为 `WA`，查询引擎将仅查找 `WA` 一词的匹配项。 显式映射只会按指定方向应用，在这种情况下，不会将查询 `WA` 重写为 `Washington`。

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>转义特殊字符

如果需要定义包含逗号或其他特殊字符的同义词，可以使用反斜杠对其进行转义，如以下示例所示：

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

由于反斜杠本身是其他语言（例如 JSON 和 C#）中的特殊字符，因此你可能需要对其进行双重转义。 例如，发送到上述同义词映射的 REST API 的 JSON 如下所示：

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>上传和管理同义词映射

如前文所述，若要创建或更新同义词映射，你无需中断查询，也无需为工作负荷编制索引。 同义词映射是一个独立的对象（像索引或数据源一样），只要没有字段使用它，更新便不会导致索引或查询失败。 但是，一旦你将同义词映射添加到字段定义，则在删除同义词映射后，包含相关字段的任何查询都会失败并出现 404 错误。

创建、更新和删除同义词映射始终是一项全文档操作。也就是说，无法逐个更新或删除同义词映射的各个部分。 甚至更新单个规则也需要重新加载。

## <a name="assign-synonyms-to-fields"></a>向字段分配同义词

上传同义词映射后，你可以在类型为 `Edm.String` 或 `Collection(Edm.String)` 且设置了 `"searchable":true` 的字段上启用同义词。 如上所述，一个字段定义只能使用一个同义词映射。

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>对等效或映射的字段进行查询

添加同义词不会对查询构造施加新的要求。 你可以像添加同义词之前一样发出字词和短语查询。 唯一的区别是，如果同义词映射中存在查询字词，则查询引擎会根据规则扩展或重写该字词或短语。

## <a name="how-synonyms-interact-with-other-features"></a>同义词如何与其他功能进行交互

同义词功能将使用 OR 操作符将原始查询重写为同义词。 出于这个原因，突出显示和计分配置文件会将原始术语和同义词视为等效项。

同义词仅适用于搜索查询，不支持筛选器、facet、自动完成或建议。 自动完成和建议仅基于原始字词；同义词匹配项不会在响应中显示。

同义词扩展不适用于通配符搜索术语；也不会扩展前缀、模糊和正则表达式术语。

如果需要执行应用同义词扩展和通配符、正则表达式或模糊搜索的单个查询，则可以使用 OR 语法组合查询。 例如，若要将同义词与通配符组合用于简单查询语法，则术语将为 `<query> | <query>*`。

如果开发（非生产）环境中具有现有索引，请使用一个小字典进行试验，了解添加同义词如何更改搜索体验，包括对计分配置文件、突出显示和建议造成的影响。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建同义词映射 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)