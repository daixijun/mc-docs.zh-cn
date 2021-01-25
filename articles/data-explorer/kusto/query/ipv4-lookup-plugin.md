---
title: ipv4_lookup 插件 - Azure 数据资源管理器
description: 本文介绍了 Azure 数据资源管理器中的 ipv4_lookup 插件。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 11/22/2020
ms.date: 01/22/2021
ms.openlocfilehash: a13d4e6dd4554b8dc4e012d3b94c586d638391d1
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614905"
---
# <a name="ipv4_lookup-plugin"></a>ipv4_lookup 插件

`ipv4_lookup` 插件会在查找表中查找 IPv4 值，并返回具有匹配值的行。

```kusto
T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey)

T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey, return_unmatched = true)
```

## <a name="syntax"></a>语法

*T* `|` `evaluate` `ipv4_lookup(` *LookupTable* `,` *SourceIPv4Key* `,` *LookupKey* [`,` *return_unmatched* ] `)`

## <a name="arguments"></a>参数

* *T*：表格输入，其列 SourceIPv4Key 将用于 IPv4 匹配。
* LookupTable：具有 IPv4 查找数据的表或表格表达式，其列 LookupKey 将用于 IPv4 匹配。 可以使用 [IP 前缀表示法](#ip-prefix-notation)对 IPv4 值进行掩码操作。
* SourceIPv4Key：T 的列，其中包含的 IPv4 字符串需在 LookupTable 中查找。 可以使用 [IP 前缀表示法](#ip-prefix-notation)对 IPv4 值进行掩码操作。
* LookupKey：LookupTable 的列，其中包含的 IPv4 字符串与每个 SourceIPv4Key 值相匹配。
* return_unmatched：一个布尔标志，用于定义结果是应包含所有行还是仅包含匹配的行（默认值：`false` - 仅返回匹配的行）。

### <a name="ip-prefix-notation"></a>IP 前缀表示法
 
IP 地址可通过使用斜杠 (`/`) 字符的 `IP-prefix notation` 进行定义。
斜杠 (`/`) 左边的 IP 地址是基本 IP 地址。 斜杠 (`/`) 右边的数字（1 到 32）是网络掩码中连续位的数目。 

例如，192.168.2.0/24 将具有关联的网络/子网掩码，其中包含 24 个连续位或点分十进制格式的 255.255.255.0。

## <a name="returns"></a>返回

`ipv4_lookup` 插件会返回基于 IPv4 密钥进行联接（查找）的结果。 该表的架构是源表与查找表的并集，类似于 [`lookup` 运算符](lookupoperator.md)的结果。

如果 return_unmatched 参数设置为 `true`，则生成的表会同时包含匹配的行和不匹配的行（使用 null 进行填充）。

如果 return_unmatched 参数设置为 `false` 或已省略（使用默认值 `false`），则生成的表的记录数将与匹配的结果数相同。 与 `return_unmatched=true` 执行相比，此查找变体具有更好的性能。

> [!NOTE]
> * 此插件涵盖基于 IPv4 的联接的方案，假设查找表较小（10-20 万行），输入表可以较大。
> * 插件的性能将取决于查找表和数据源表的大小、列数和匹配记录数。

## <a name="examples"></a>示例

### <a name="ipv4-lookup---matching-rows-only"></a>IPv4 查找 - 仅匹配行

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string, continent_code:string ,continent_name:string, country_iso_code:string, country_name:string)
[
  "111.68.128.0/17","AS","Asia","JP","Japan",
  "5.8.0.0/19","EU","Europe","RU","Russia",
  "223.255.254.0/24","AS","Asia","SG","Singapore",
  "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
  "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
  '2.20.183.12',   // United Kingdom
  '5.8.1.2',       // Russia
  '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network)
```

|ip|network|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|欧洲|GB|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|欧洲|RU|俄罗斯|

### <a name="ipv4-lookup---return-both-matching-and-non-matching-rows"></a>IPv4 查找 - 同时返回匹配行和非匹配行

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: 
// https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string,continent_code:string ,continent_name:string ,country_iso_code:string ,country_name:string )
[
    "111.68.128.0/17","AS","Asia","JP","Japan",
    "5.8.0.0/19","EU","Europe","RU","Russia",
    "223.255.254.0/24","AS","Asia","SG","Singapore",
    "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
    "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|network|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|欧洲|GB|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|欧洲|RU|俄罗斯|
|192.165.12.17||||||

### <a name="ipv4-lookup---using-source-in-external_data"></a>IPv4 查找 - 在 external_data() 中使用源

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
let IP_Data = external_data(network:string,geoname_id:long,continent_code:string,continent_name:string ,country_iso_code:string,country_name:string,is_anonymous_proxy:bool,is_satellite_provider:bool)
    ['https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv'];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Sweden
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|network|geoname_id|continent_code|continent_name|country_iso_code|country_name|is_anonymous_proxy|is_satellite_provider|
|---|---|---|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|2635167|EU|欧洲|GB|英国|0|0|
|5.8.1.2|5.8.0.0/19|2017370|EU|欧洲|RU|俄罗斯|0|0|
|192.165.12.17|192.165.8.0/21|2661886|EU|欧洲|SE|瑞典|0|0|
