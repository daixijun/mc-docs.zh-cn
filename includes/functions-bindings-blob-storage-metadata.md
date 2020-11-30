---
title: include 文件
description: include 文件
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
origin.date: 08/02/2019
ms.date: 10/29/2019
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 463294cd6f37e3aacd44498f0a7324b9cef79851
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301140"
---
Blob 触发器提供了几个元数据属性。 这些属性可在其他绑定中用作绑定表达式的一部分，或者用作代码中的参数。 这些值的语义与 [CloudBlob](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.storage.blob.cloudblob) 类型相同。

|properties  |类型  |说明  |
|---------|---------|---------|
|`BlobTrigger`|`string`|触发 Blob 的路径。|
|`Uri`|`System.Uri`|主位置的 blob 的 URI。|
|`Properties` |[BlobProperties](https://docs.azure.cn/zh-cn/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blob 的系统属性。 |
|`Metadata` |`IDictionary<string,string>`|Blob 的用户定义元数据。|

例如，以下 C# 脚本和 JavaScript 示例会记录触发 blob 的路径，包括容器：

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

