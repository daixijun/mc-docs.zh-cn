---
title: 对 Azure 数据工厂中的管道业务流程和触发器进行故障排除
description: 使用不同方法排查 Azure 数据工厂中的管道触发器问题。
author: WenJason
ms.service: data-factory
origin.date: 12/15/2020
ms.date: 01/04/2021
ms.topic: troubleshooting
ms.author: v-jay
ms.reviewer: susabat
ms.openlocfilehash: e832f56617334e632537a33f4943ed431a13ce10
ms.sourcegitcommit: cf3d8d87096ae96388fe273551216b1cb7bf92c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830474"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>对 Azure 数据工厂中的管道业务流程和触发器进行故障排除

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的“管道运行”用于定义管道执行实例。 例如，你有一个管道，分别在上午 8:00、9:00 和 10:00 点执行。 在这种情况下，将分三次单独运行管道，也即有三次管道运行。 每次管道运行都有唯一的管道运行 ID。 运行 ID 是标识该特定管道运行的 GUID（全局唯一标识符）。

管道运行通常通过将自变量传递给管道中定义的参数进行实例化。 执行管道时，可以手动，也可以使用触发器。 有关详细信息，请参阅 [Azure 数据工厂中的管道执行和触发器](concepts-pipeline-execution-triggers.md)。

## <a name="common-issues-causes-and-solutions"></a>常见问题、原因和解决方案

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>管道运行已终止，但监视器仍显示进度状态

#### <a name="issue"></a>问题
很多情况下，终止管道运行后，管道监视仍然显示进度状态。 出现这种情况是因为浏览器中存在缓存问题，并且你没有使用恰当的筛选器进行监视。

#### <a name="resolution"></a>解决方法
刷新浏览器，并应用恰当的筛选器进行监视。
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>复制管道故障 - 找到的列多于预期的列计数 (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>问题  
如果特定文件夹下你要复制的文件包含具有不同架构（例如可变列数、不同分隔符、引号字符设置或某个数据问题）的文件，则数据工厂管道最终会遇到以下错误：

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>解决方法
创建“复制数据”活动时，选择“二进制复制”选项。 这样，如果要进行大容量复制或者将数据从一个 Data Lake 迁移到另一个，则当使用“二进制”选项时，数据工厂不会打开文件来读取架构，而只是将每个文件视为二进制文件并将其复制到另一个位置。

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>当达到集成运行时的容量限制时，管道运行失败

#### <a name="issue"></a>问题
错误消息：

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

此错误指示了每个集成运行时的限制，目前为 50。 有关详细信息，请参阅[限制](/azure-resource-manager/management/azure-subscription-service-limits#version-2)。

#### <a name="resolution"></a>解决方法 
- 将这些管道分开，以便执行不同的触发器时间。
- 创建一个新的集成运行时，并将这些管道拆分到多个集成运行时。

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>如何按固定时间间隔监视管道故障

#### <a name="issue"></a>问题
经常需要按特定时间间隔（例如 5 分钟）监视数据工厂管道。 可以使用终结点从数据工厂查询和筛选管道运行。 

#### <a name="recommendation"></a>建议
1. 将 Azure 逻辑应用设置为每隔 5 分钟查询一次所有故障管道。
2. 然后，你可以根据 [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory) 将事件报告给我们的票证系统。

#### <a name="reference"></a>参考
- [外部材料 - 从数据工厂发送通知](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>如何处理管道中的活动级错误和失败

#### <a name="issue"></a>问题
Azure 数据工厂业务流程允许条件逻辑，使用户能够根据上一活动的结果采用不同的路径。 它允许四个条件路径：“成功时（默认通过）”、“失败时”、“完成时”和“跳过时”。 允许使用不同的路径。

Azure 数据工厂会定义管道运行的成功和失败，如下所述：

- 对所有叶级活动的结果进行评估。 如果跳过了某个叶活动，则会改为评估其父活动。
- 当且仅当所有叶都成功时，管道结果才是成功的。

#### <a name="recommendation"></a>建议
- 按照[如何处理管道故障和错误](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)一文的说明实施活动级检查。
- 按照[通过 DataFactory 进行查询](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)一文的说明使用 Azure 逻辑应用来定期监视管道。

