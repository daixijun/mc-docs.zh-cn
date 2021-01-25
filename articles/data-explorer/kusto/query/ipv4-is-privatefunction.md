---
title: ipv4_is_private() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 ipv4_is_private() 函数。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 11/20/2020
ms.date: 01/22/2021
ms.openlocfilehash: 44fee844e44d2c40bf884c42a769b5c171cc43c5
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614907"
---
# <a name="ipv4_is_private"></a>ipv4_is_private()

检查 IPv4 字符串地址是否属于一组专用网络 IP。

专用网络地址最初是为帮助延迟 IPv4 地址的耗尽而定义的。 源于或寻址到某个专用 IP 地址的 IP 数据包无法通过公共 Internet 进行路由。

## <a name="private-ipv4-addresses"></a>专用 IPv4 地址

Internet 工程任务组 (IETF) 已指示 Internet 号码分配机构 (IANA) 为专用网络保留以下 IPv4 地址范围：

| IP 地址范围|地址数|最大 CIDR 块（子网掩码）|
|-----------------|-------------------|--------------------------------|
|10.0.0.0 – 10.255.255.255|16777216|10.0.0.0/8 (255.0.0.0)|
|172.16.0.0 – 172.31.255.255|1048576|172.16.0.0/12 (255.240.0.0)|
|192.168.0.0 – 192.168.255.255|65536|192.168.0.0/16 (255.255.0.0)|


```kusto
ipv4_is_private('192.168.1.1/24') == true
ipv4_is_private('10.1.2.3/24') == true
ipv4_is_private('202.1.2.3') == false
ipv4_is_private("127.0.0.1") == false
```

## <a name="syntax"></a>语法

`ipv4_is_private(`Expr`)`

## <a name="arguments"></a>参数

Expr：表示 IPv4 地址的字符串表达式。 可以使用 [IP 前缀表示法](#ip-prefix-notation)对 IPv4 字符串进行掩码操作。

### <a name="ip-prefix-notation"></a>IP 前缀表示法

IP 地址可通过使用斜杠 (`/`) 字符的 `IP-prefix notation` 进行定义。 斜杠 (`/`) 左边的 IP 地址是基本 IP 地址。 斜杠 (`/`) 右边的数字（1 到 32）是网络掩码中连续位的数目。 

例如，192.168.2.0/24 将具有关联的网络/子网掩码，其中包含 24 个连续位或点分十进制格式的 255.255.255.0。

## <a name="returns"></a>返回

* `true`：如果该 IPv4 地址属于任何专用网络范围。
* `false`：其他情况。
* `null`：如果两个 IPv4 字符串之一转换不成功。

## <a name="example-check-if-ipv4-belongs-to-a-private-network"></a>示例：检查 IPv4 是否属于专用网络

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
datatable(ip_string:string)
[
 '10.1.2.3',
 '192.168.1.1/24',
 '127.0.0.1',
]
| extend result = ipv4_is_private(ip_string)
```

|ip_string|result|
|---|---|
|10.1.2.3|1|
|192.168.1.1/24|1|
|127.0.0.1|0|
