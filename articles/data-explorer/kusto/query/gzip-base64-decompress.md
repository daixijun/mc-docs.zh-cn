---
title: gzip_decompress_from_base64_string() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 gzip_decompress_from_base64_string() 命令。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
origin.date: 11/01/2020
ms.date: 01/22/2021
ms.openlocfilehash: 41b93d288134d5cba452618db750e42824f0607f
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614897"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string()

从 base64 对输入字符串进行解码，并执行 gzip 解压缩。

## <a name="syntax"></a>语法

`gzip_decompress_from_base64_string("`input_string`")`

## <a name="arguments"></a>参数

input_string：输入 `string`，该项已用 gzip 压缩并进行了 base64 编码。 此函数接受一个字符串参数。

> [!NOTE]
> 此函数检查必需的 gzip 标头字段（ID1、ID2 和 CM），如果其中任何字段的值不正确，则返回空输出。
> 不支持可选的标头字段。 FLG 和 XFL 都应为零。


## <a name="returns"></a>返回

* 返回一个表示原始字符串的 `string`。 
* 如果解压缩或解码失败，则返回空结果。 
    * 例如，无效的经过 gzip 压缩和 base64 编码的字符串会返回空输出。

## <a name="examples"></a>示例

```kusto
print res=gzip_decompress_from_base64_string("H4sIAAAAAAAA/wEUAOv/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA==")
```

**输出：**

|"1234567890qwertyuiop"|

无效输入的示例：

```kusto
print res=gzip_decompress_from_base64_string("x0x0x0")
```

**输出：**
>||

## <a name="next-steps"></a>后续步骤

* 使用 [gzip_compress_to_base64_string()](gzip-base64-compress.md) 创建压缩的输入字符串。
* 另请参阅 [zlib_decompress_from_base64_string](zlib-base64-decompress.md)。
