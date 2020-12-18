---
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 08/10/2020
ms.date: 12/14/2020
ms.author: v-jay
ms.openlocfilehash: 24ac2a14bccde998f9146dad404492a08ecc4819
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850345"
---
#### <a name="additional-premium-file-share-level-limits"></a>其他高级文件共享级别限制

|区域  |目标  |
|---------|---------|
|最小大小增加/减少    |1 GiB      |
|基线 IOPS    |每个 GiB 1 IOPS，最高 100,000|
|IOPS 突发    |每个 GiB 3倍 IOPS，最高 100,000|
|出口速率         |60 MiB/秒 + 0.06 * 预配 GiB        |
|入口速率| 40 MiB/秒 + 0.04 * 预配 GiB |

#### <a name="file-level-limits"></a>文件级别限制

|区域  |标准文件  |高级文件  |
|---------|---------|---------|
|大小     |1 TiB         |4 TiB         |
|每个文件的最大 IOPS      |1,000         |最大 8,000*         |
|并发句柄数     |2,000         |2,000         |
|流出量     |查看标准文件吞吐量值         |300 MiB/秒         |
|流入量     |查看标准文件吞吐量值         |200 MiB/秒         |
|吞吐量     |最多 60 MiB/秒         |查看高级文件流入量/流出量值         |

\* <sup> 适用于读取和写入 IO（通常较小的 IO 大小 <=64K）。除读取和写入之外的元数据操作数可能更低。</sup>
