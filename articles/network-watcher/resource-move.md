---
title: 移动 Azure 网络观察程序资源 | Azure
description: 跨区域移动 Azure 网络观察程序资源
services: network-watcher
documentationcenter: na
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 01/07/2021
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: bd8622871fe3fe7cd866d5de9edf5e1a44d1aa07
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98229841"
---
<!--Verified successfully-->
<!--Charactor contents only-->

# <a name="moving-azure-network-watcher-resources-across-regions"></a>跨区域移动 Azure 网络观察程序资源

## <a name="moving-the-network-watcher-resource"></a>移动网络观察程序资源
网络观察程序资源代表网络观察程序的后端服务，由 Azure 完全托管。 客户无需管理它。 此资源不支持移动操作。

## <a name="moving-child-resources-of-network-watcher"></a>移动网络观察程序的子资源
对于 `*networkWatcher*` 资源类型的任何子资源，当前都不支持跨区域移动资源。

## <a name="next-steps"></a>后续步骤
* 阅读[网络观察程序概述](./network-watcher-monitoring-overview.md)
* 查看[网络观察程序常见问题解答](./frequently-asked-questions.md)

<!-- Update_Description: update meta properties, wording update, update link -->