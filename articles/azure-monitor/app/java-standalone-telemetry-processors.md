---
title: 遥测处理器（预览版）- 适用于 Java 的 Azure Monitor Application Insights
description: 如何在适用于 Java 的 Azure Monitor Application Insights 中配置遥测处理器
ms.topic: conceptual
ms.date: 01/12/2021
ms.custom: devx-track-java
ms.author: v-johya
ms.openlocfilehash: 9b73a819216e41c0f3845027ae081fdb8f79eb71
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98229926"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>遥测处理器（预览版）- 适用于 Java 的 Azure Monitor Application Insights

> [!NOTE]
> 此功能目前仍为预览版。

适用于 Application Insights 的 Java 3.0 代理现在具有在导出遥测数据之前处理该数据的功能。

### <a name="some-use-cases"></a>下面是一些用例：
 * 屏蔽敏感数据
 * 有条件地添加自定义维度
 * 更新用于聚合和显示的遥测名称

### <a name="supported-processors"></a>支持的处理器：
 * 属性处理器
 * 范围处理器

## <a name="to-get-started"></a>入门指南

使用以下模板创建一个名为 `applicationinsights.json` 的配置文件，并将其置于 `applicationinsights-agent-***.jar` 所在的目录中。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>包括/排除范围

属性处理器和范围处理器公开了相关选项来提供某个范围的要用作匹配依据的一组属性，以确定应将该范围包括在处理器中还是排除在处理器外。 若要配置此选项，则在 `include` 和/或 `exclude` 下至少必须有一个 `matchType` 以及 `spanNames` 和 `attributes` 中的一个。 允许包括/排除配置有多个指定的条件。 所有指定条件的评估结果都必须为 true 才会被视为匹配。 

必填字段： 
* `matchType` 控制如何解释 `spanNames` 和 `attributes` 数组中的项。 可能的值包括 `regexp` 或 `strict`。 

可选字段： 
* `spanNames` 必须至少与一个项匹配。 
* `attributes` 指定要用作匹配依据的属性列表。 所有这些属性必须完全匹配才会被视为匹配。

> [!NOTE]
> 如果同时指定了 `include` 和 `exclude`，则会在 `exclude` 属性之前检查 `include` 属性。

#### <a name="sample-usage"></a>示例用法

下面的示例演示了如何指定范围属性组，以指示此处理器应当应用于哪些范围。 属性的 `include` 指示应该包括哪些属性，而 `exclude` 属性则进一步筛选掉不应处理的范围。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

使用以上配置时，以下范围与属性匹配，处理器操作得到应用：

* Span1 Name: 'svcB' Attributes: {env: production, test_request:123, credit_card:1234, redact_trace: "false"}

* Span2 Name: 'svcA' Attributes: {env: staging, test_request: false, redact_trace: true}

以下范围与 include 属性不匹配，处理器操作得不到应用：

* Span3 Name: 'svcB' Attributes: {env: production, test_request: true, credit_card:1234, redact_trace: false}

* Span4 Name: 'svcC' Attributes: {env: dev, test_request: false}

## <a name="attribute-processor"></a>属性处理器 

属性处理器修改范围的属性。 它还可以支持用于包括/排除范围的功能。
它接受一个操作列表，这些操作按配置文件中指定的顺序执行。 支持的操作有：

* `insert`：在不存在键的范围中插入新属性
* `update`：更新存在键的范围中的属性
* `delete`：从范围中删除属性
* `hash`：将现有属性值哈希化 (SHA1)

对于 `insert` 和 `update` 操作，
* `key` 是必需的
* 需要 `value` 和 `fromAttribute` 中的一个
* 需要 `action`。

对于 `delete` 操作，
* `key` 是必需的
* `action`：`delete` 是必需的。

对于 `hash` 操作，
* `key` 是必需的
* `action`：`hash` 是必需的。

可以编写操作列表来创建丰富的方案，例如回填属性、将值复制到新键、编修敏感信息。

#### <a name="sample-usage"></a>示例用法

以下示例演示了如何在范围中插入键/值：

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

以下示例演示了如何将处理器配置为仅更新属性中的现有键：

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

以下示例演示了如何处理范围名称与正则表达式模式匹配的范围。
此处理器会删除“token”属性，并在范围名称与“auth.\*”匹配但与“login.\*”不匹配的范围中对“password”属性进行模糊处理。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>范围处理器

范围处理器修改范围名称或根据范围名称修改范围的属性。 它还可以支持用于包括/排除范围的功能。

### <a name="name-a-span"></a>为范围命名

作为 name 节的一部分，以下设置是必需的：

* `fromAttributes`：键的属性值用于按配置中指定的顺序创建新名称。 需要在范围中指定所有属性键，处理器才能对其进行重命名。

还可以配置以下设置：

* `separator`：一个指定的字符串，将用来拆分值
> [!NOTE]
> 如果重命名依赖于属性处理器修改的属性，请确保在管道规范中的属性处理器之后指定范围处理器。

#### <a name="sample-usage"></a>示例用法

以下示例指定属性“db.svc”、“operation”和“id”的值将按该顺序形成范围的新名称，以值“::”作为分隔符。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>从范围名称中提取属性

接受正则表达式的列表，以据其匹配范围名称，并根据子表达式从中提取属性。 必须在 `toAttributes` 节下指定。

以下设置是必需的：

`rules`：用于从范围名称中提取属性值的规则列表。 范围名称中的值将替换为提取的属性名称。 列表中的每项规则都是一个正则表达式模式字符串。 将依据正则表达式来检查范围名称。 如果正则表达式匹配，则正则表达式的所有已命名子表达式都会被提取为属性并添加到范围中。 每个子表达式名称会成为一个属性名称，子表达式匹配部分会成为属性值。 范围名称中的匹配部分会替换为提取的属性名称。 如果属性已存在于范围中，则会被覆盖。 将按指定顺序为所有规则重复此过程。 每项后续规则都应用于处理上一规则后输出的范围名称。

#### <a name="sample-usage"></a>示例用法

让我们假设输入范围名称为 /api/v1/document/12345678/update。 将以下结果应用于输出范围名称 /api/v1/document/{documentId}/update 会将新属性 "documentId"="12345678" 添加到范围。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

下面演示了如何在范围具有以下属性时将范围名称重命名为“{operation_website}”并添加属性 {Key: operation_website, Value: oldSpanName }：
- 范围名称在字符串中的任意位置包含“/”。
- 范围名称不是“donot/change”。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```

