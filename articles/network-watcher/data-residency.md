---
title: Azure 网络观察程序的数据驻留 | Azure Docs
description: 本文介绍 Azure 网络观察程序服务的数据驻留。
services: network-watcher
documentationcenter: na
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 07/20/2020
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: 36a350420e97b779989012957faeab8d90b00a91
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230451"
---
<!--Verified successfully for charactors only-->
# <a name="data-residency-for-azure-network-watcher"></a>Azure 网络观察程序的数据驻留
Azure 网络观察程序不会存储客户数据，连接监视器（预览版）服务除外。

## <a name="connection-monitor-preview-data-residency"></a>连接监视器（预览版）数据驻留
连接监视器（预览版）服务会存储客户数据。 这些数据由网络观察程序自动存储在单个区域中。 因此，连接监视器（预览版）会自动满足区域内数据驻留要求，包括在[信任中心](https://azuredatacentermap.azurewebsites.net/)指定的要求。

<!--Not Available on ## Singapore data residency-->


## <a name="next-steps"></a>后续步骤

* 阅读[网络观察程序](./network-watcher-monitoring-overview.md)的概述。

<!-- Update_Description: update meta properties, wording update, update link -->