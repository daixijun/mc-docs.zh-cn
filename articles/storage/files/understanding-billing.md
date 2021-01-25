---
title: 了解 Azure 文件存储计费 | Microsoft Docs
description: 了解如何解释针对 Azure 文件共享的预配和即用即付的计费模型。
author: WenJason
ms.service: storage
ms.topic: how-to
origin.date: 12/1/2020
ms.date: 01/18/2021
ms.author: v-jay
ms.subservice: files
ms.openlocfilehash: 91455cbab49443ad44a75bdfbd3b37d4330396a6
ms.sourcegitcommit: f086abe8bd2770ed10a4842fa0c78b68dbcdf771
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163233"
---
# <a name="understanding-azure-files-billing"></a>了解 Azure 文件存储计费

## <a name="provisioned-billing"></a>预配计费
Azure 文件存储将预配模型用于高级文件共享。 在预配业务模型中，可以主动向 Azure 文件存储服务指定存储需求，而不是根据使用的资源进行计费。 这类似于在本地购买硬件，因为当你预配具有一定存储量的 Azure 文件共享时，无论是否使用该存储，都需要为其付费，就像在开始使用空间时，你不会支付本地物理媒体的费用一样。 与在本地购买物理媒体不同，可以根据存储和 IO 性能特征来灵活地纵向扩展或纵向缩放预配的文件共享。

预配高级文件共享时，可以指定工作负载所需的 GiB。 预配的每个 GiB 均可提供固定比率的附加 IOPS 和吞吐量。 除得到保证的基线 IOPS 之外，每个高级文件共享还支持最大程度的突发限制。 IOPS 和吞吐量的公式如下所示：

- 基线 IOPS = 400 + 1 * 预配的 GiB。 （最大可为 100,000 IOPS）。
- 突发限制 = MAX（4,000，3 * 基线 IOPS）。
- 出口速率 = 60 MiB/秒 + 0.06 * 预配的 GiB。
- 入口速率 = 40 MiB/秒 + 0.04 * 预配的 GiB。

文件共享的预配大小可以随时增加，但只能在自上次增加后的 24 小时之后减小。 等待 24 小时且不要提高配额，然后，可将共享配额降低任意次数，直到再次提高配额为止。 IOPS/吞吐量规模更改将在预配大小更改后的数分钟内生效。

可将预配共享的大小减至所用 GiB 以下。 这样做不会丢失数据，但仍会根据所用大小计费，并且性能（基线 IOPS、吞吐量和突发 IOPS）与预配的共享（而不是所用大小）相符。

下表演示了这些预配共享大小公式的几个示例：

|容量 (GiB) | 基线 IOPS | 突发 IOPS | 出口速率（MiB/秒） | 入口速率（MiB/秒） |
|---------|---------|---------|---------|---------|
|100         | 500     | 最大 4,000     | 66   | 44   |
|500         | 900     | 最大 4,000  | 90   | 60   |
|1,024       | 1,424   | 最大 4,000   | 122   | 81   |
|5,120       | 5,520   | 最大 15,360  | 368   | 245   |
|10,240      | 10,640  | 最大 30,720  | 675   | 450   |
|33,792      | 34,192  | 最大 100,000 | 2,088 | 1,392   |
|51,200      | 51,600  | 最大 100,000 | 3,132 | 2,088   |
|102,400     | 100,000 | 最大 100,000 | 6,204 | 4,136   |

有效的文件共享性能与计算机网络限制、可用网络带宽、IO 大小、并行度和其他许多因素相关。

### <a name="bursting"></a>突发
如果工作负荷需要额外的性能来满足峰值需求，则你的共享可以使用突发积分，从而超越共享基线 IOPS 限制来提供满足需求所需的共享性能。 高级文件共享可将其 IOPS 突发到 4000 或其基线 IOPS 的 3 倍（以二者中较大的值为准）。 突发是自动进行的，根据额度系统运行。 突发采用“尽力而为”的原则，并且突发限制不是一个保证，文件共享可以在 60 分钟的最大持续时间内最多突发到该限制。

每当文件共享的流量低于基线 IOPS 时，额度将累积在突发桶中。 例如，一个 100 GiB 的共享有 500 个基线 IOPS。 如果共享中的实际流量在特定的 1 秒间隔内为 400 个 IOPS，则会将 400 个未使用的 IOPS 积分计入到突发桶。 类似地，一个空闲的 1 TiB 共享会在 1424 个 IOPS 上累积突发积分。 以后在操作要超过基线 IOPS 时，将使用这些积分。

每当共享超过基线 IOPS 并且在突发桶中有积分时，就会以允许的最大峰值突发率进行突发。 只要还有积分，共享就可以继续突发，最多可达 60 分钟的持续时间，但这取决于所累积的突发积分的数量。 超出基线 IOPS 的每个 IO 会消耗一个积分，一旦耗尽所有积分，共享就会返回到基线 IOPS。

共享额度具有三种状态：

- 应计：文件共享使用的 IOPS 小于基线 IOPS。
- 下降（在文件共享使用的 IOPS 超过基线 IOPS 并且文件共享处于突发模式时）。
- 恒定（在文件共享只使用基线 IOPS 时，不会累积或使用任何额度）。

新文件共享最初在其突发桶中包含所有额度。 如果由于服务器的限制，导致共享 IOPS 低于基线 IOPS，则不会对突发额度进行应计。

### <a name="what-are-transactions"></a>什么是事务？
事务是针对 Azure 文件存储的操作或请求，用于上传、下载或以其他方式处理文件共享的内容。 对文件共享执行的每项操作均转化为一个或多个事务，而对标准共享执行的每项操作均转化为事务成本。

共有五种基本的事务类别：写入、列出、读取、其他和删除。 通过 REST API 或 SMB 完成的所有操作都归入以下四个类别之一：

| 操作类型 | 写入事务 | 列出事务 | 读取事务 | 其他事务 | 删除事务 |
|-|-|-|-|-|-|
| 管理操作 | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| 数据操作 | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

## <a name="see-also"></a>另请参阅
- [Azure 文件存储定价页](https://azure.cn/pricing/details/storage/files/)。
- [规划 Azure 文件存储部署](./storage-files-planning.md)。
- [创建文件共享](./storage-how-to-create-file-share.md)。