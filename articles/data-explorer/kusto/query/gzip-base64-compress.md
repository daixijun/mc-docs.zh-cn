---
title: gzip_compress_to_base64_string - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 gzip_compress_to_base64_string() 命令。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
origin.date: 11/01/2020
ms.date: 01/22/2021
ms.openlocfilehash: 794180ea81700bcd8572840ffc190c5377be9642
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614898"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string()

执行 gzip 压缩，并将结果编码为 base64。


## <a name="syntax"></a>语法

`gzip_compress_to_base64_string("`input_string"`)`

## <a name="arguments"></a>参数

input_string：输入 `string`（要压缩并进行 base64 编码的字符串）。 此函数接受一个字符串参数。

## <a name="returns"></a>返回

* 返回 `string`，它表示已进行 gzip 压缩和 base64 编码的原始字符串。 
* 如果压缩或编码失败，则返回空结果。

## <a name="example"></a>示例
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**输出：** 

|H4sIAAAAAAAA/wEUAOv/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA==|

## <a name="next-steps"></a>后续步骤

* 使用 [gzip_decompress_from_base64_string()](gzip-base64-decompress.md) 检索未压缩的原始字符串。
* 另请参阅 [zlib_compress_to_base64_string()](zlib-base64-compress.md)
