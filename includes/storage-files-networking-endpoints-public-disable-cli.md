---
title: include 文件
description: include 文件
services: storage
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 6/2/2020
ms.date: 11/16/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: ca9eab44824b6596672959279c70fca606db5f4b
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553038"
---
以下 CLI 命令将拒绝发往存储帐户公共终结点的所有流量。 请注意，此命令的 `-bypass` 参数设置为 `AzureServices`。 这将允许受信任的第一方服务通过公共终结点访问存储帐户。

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```