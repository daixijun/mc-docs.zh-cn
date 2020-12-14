---
author: rockboyfor
ms.service: container-instances
ms.topic: include
origin.date: 07/22/2020
ms.date: 08/10/2020
ms.testscope: no
ms.testdate: 03/02/2020
ms.author: v-yeche
ms.openlocfilehash: fb340bce53a8ed28249adad93540fd9912616edd
ms.sourcegitcommit: 87b6bb293f39c5cfc2db6f38547220a13816d78f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96476881"
---
| 资源 | 限制 |
| --- | :--- |
| 每个区域每个订阅的标准 sku 容器组数 | 100<sup>1</sup> |
| 每个区域每个订阅的专用 sku 容器组数 | 0<sup>1</sup> |
| 每个容器组的容器数 | 60 |
| 每个容器组的卷数 | 20 |
| 每个订阅每个区域的标准 sku 核心数（CPU 数） | 10<sup>1,2</sup> | 
| 每个订阅每个区域的 V100 GPU 的标准 sku 核心数（CPU 数） | 0<sup>1,2</sup> |
| 每个 IP 的端口数 | 5 |
| 容器实例日志大小 - 正在运行的实例 | 4 MB |
| 容器实例日志大小 - 已停止的实例 | 16 KB 或 1,000 行 |
| 每小时创建容器次数 |300<sup>1</sup> |
| 每 5 分钟创建容器次数 | 100<sup>1</sup> |
| 每小时删除容器次数 | 300<sup>1</sup> |
| 每 5 分钟删除容器次数 | 100<sup>1</sup> |

<!--ONLY AVAILABLE ON V100 TO MATCH NCv3-->

<sup>1</sup>要请求提高上限，请创建一个 [Azure 支持请求][azure-support]。 从包括[试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)的免费订阅到“标准预付费套餐”订阅。<br />
<sup>2</sup> [标准预付费套餐](https://www.microsoft.com/china/azure/index.html?fromtype=cn)订阅的默认限制。 其他类别类型的上限可能有所不同。<br/>

<!-- LINKS - External -->

[azure-support]: https://support.azure.cn/support/support-azure/

<!-- Update_Description: update meta properties, wording update, update link -->