---
title: Azure 数据资源管理器中的“引入”错误代码
description: 本主题列出了 Azure 数据资源管理器中的“引入”错误代码
author: orspod
ms.author: v-tawe
ms.reviewer: vladikbr
ms.service: data-explorer
ms.topic: reference
origin.date: 11/11/2020
ms.date: 01/20/2021
ms.openlocfilehash: 6e7c6bdc6678c2df33f9213741255a097e3419af
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614909"
---
# <a name="ingestion-error-codes-in-azure-data-explorer"></a>Azure 数据资源管理器中的“引入”错误代码

以下列表包含在[引入](ingest-data-overview.md)过程中可能会遇到的错误代码。 如果在群集上启用“失败引入”[诊断日志](using-diagnostic-logs.md#ingestion-logs-schema)，则可在“失败引入”操作日志中查看错误代码。 还可以监视“引入结果”[指标](using-metrics.md#ingestion-metrics)来查看引入错误的“类别”（而不是特定的错误代码）。 下面的错误按这些类别进行组织。 

> [!NOTE]
> 如果错误是暂时性的，则重试引入可能会成功。

## <a name="category-badformat"></a>类别：BadFormat

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|Stream_WrongNumberOfFields                        |输入记录中的字段数不一致。 HRESULT：0x80DA0008      |永久性           |
|Stream_ClosingQuoteMissing                        |CSV 格式无效。 缺少右引号。 HRESULT：0x80DA000b            |永久性           |
|BadRequest_InvalidBlob                            |Blob 无效。                                                              |永久性           |
|BadRequest_EmptyArchive                           |存档为空。                                                             |永久性           |
|BadRequest_InvalidArchive                         |存档无效。                                                           |永久性           |
|BadRequest_InvalidMapping                         |未能分析引入映射。<br>若要详细了解如何编写引入映射，请参阅[数据映射](./kusto/management/mappings.md)。   |永久性           |
|BadRequest_InvalidMappingReference                |映射引用无效。            |永久性           |
|BadRequest_FormatNotSupported                     |格式不受支持。 这可能是因为你使用的是特定数据连接不支持的格式。 <br>若要详细了解 Azure 数据资源管理器支持的用于引入的数据格式，请参阅[支持的数据格式](ingestion-supported-formats.md)。 |永久性          |
|BadRequest_InconsistentMapping                    |支持的引入映射与现有的表架构不一致。 |永久性           |
|BadRequest_UnexpectedCharacterInInputStream       |输入流中出现意外的字符。                                     |永久性           |

## <a name="category-badrequest"></a>类别：BadRequest

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|BadRequest_EmptyBlob                              |Blob 为空。                                                               |永久性           |
|BadRequest_EmptyBlobUri                           |Blob URI 为空。                                                           |永久性           |
|BadRequest_DuplicateMapping                       |引入属性同时包含 ingestionMapping 和 ingestionMappingReference，这是无效的。              |永久性          |
|BadRequest_InvalidOrEmptyTableName                |表名为空或无效。<br>有关 Azure 数据资源管理器命名约定的详细信息，请参阅[实体名称](./kusto/query/schema-entities/entity-names.md)。    |永久性          |
|BadRequest_EmptyDatabaseName                      |数据库名称为空。             |永久性          |
|BadRequest_EmptyMappingReference                  |某些格式应使引入映射可被引入，但映射引用为空。<br>有关映射的详细信息，请参阅[数据映射](./kusto/management/mappings.md)。        |永久性           |
|Download_BadRequest                               |由于请求不正确，无法从 Azure 存储下载源。    |永久性           |
|BadRequest_MissingMappingtFailure                 |Avro 和 Json 格式必须通过 ingestionMapping 或 ingestionMappingReference 参数引入。         |永久性           |
|Stream_NoDataToIngest                             |找不到要引入的数据。<br>对于 JSON 格式的数据，此错误可能指示 JSON 格式无效。        |永久性          |
|Stream_DynamicPropertyBagTooLarge                 |数据在动态列中包含的值太大。 HRESULT：0x80DA000E         |永久性          |
|General_BadRequest                                |请求错误。            |永久性           |
|BadRequest_CorruptedMessage                       |消息已损坏。    |永久性           |
|BadRequest_SyntaxError                            |请求语法错误。     |永久性           |
|BadRequest_ZeroRetentionPolicyWithNoUpdatePolicy  |表没有任何保留策略，不是任何更新策略的源表。    |永久性           |
|BadRequest_CreationTimeEarlierThanSoftDeletePeriod|为引入指定的创建时间不在 `SoftDeletePeriod` 内。<br>有关 `SoftDeletePeriod` 的详细信息，请参阅[策略对象](./kusto/management/retentionpolicy.md#the-policy-object)。  |永久性   |
|BadRequest_NotSupported                           |请求不受支持。    |永久性           |
|Download_SourceNotFound                           |从 Azure 存储下载源失败。 找不到源。       |永久性       |
|BadRequest_EntityNameIsNotValid                   |实体名称无效。<br>有关 Azure 数据资源管理器命名约定的详细信息，请参阅[实体名称](./kusto/query/schema-entities/entity-names.md)。    |永久性           |
|BadRequest_MalformedIngestionProperty              |引入属性的格式不正确。    |永久性           |
| BadRequest_IngestionPropertyNotSupportedInThisContext | 引入属性在此上下文中不受支持| 永久性

## <a name="category-dataaccessnotauthorized"></a>类别：DataAccessNotAuthorized

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|Download_AccessConditionNotSatisfied              |从 Azure 存储下载源失败。 不满足访问条件。     |永久性           |
|Download_Forbidden                                |从 Azure 存储下载源失败。 访问被禁止。    |永久性           |
|Download_AccountNotFound                          |从 Azure 存储下载源失败。 找不到帐户。    |永久性           |
|BadRequest_TableAccessDenied                      |对表进行访问已被拒绝。<br>有关详细信息，请参阅 [Kusto 中基于角色的授权](./kusto/management/access-control/role-based-authorization.md)。     |永久性           |
|BadRequest_DatabaseAccessDenied                   |对数据库进行访问已被拒绝。<br>有关详细信息，请参阅 [Kusto 中基于角色的授权](./kusto/management/access-control/role-based-authorization.md)。                        |永久性           |

## <a name="category-downloadfailed"></a>类别：DownloadFailed

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|Download_NotTransient                             |从 Azure 存储下载源失败。 发生了非暂时性错误                   |永久性           |
|Download_UnknownError                             |从 Azure 存储下载源失败。 发生未知错误              |暂时性           |


## <a name="category-entitynotfound"></a>类别：EntityNotFound

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|BadRequest_MappingReferenceWasNotFound            |找不到映射引用。   |永久性           |
|BadRequest_DatabaseNotExist                       |数据库不存在。          |永久性           |
|BadRequest_TableNotExist                          |表不存在。          |永久性           |
|BadRequest_EntityNotFound                         |找不到 Azure 数据资源管理器实体（如映射、数据库或表）。           |永久性           |

## <a name="category-filetoolarge"></a>类别：FileTooLarge

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|Stream_InputStreamTooLarge                        |输入数据的总大小或数据中的单个字段太大。 HRESULT：0x80DA0009                 |永久性          |
|BadRequest_FileTooLarge                           |Blob 大小已超出允许用于引入的大小限制。<br>若要详细了解引入的大小限制，请参阅 [Azure 数据资源管理器数据引入概述](ingest-data-overview.md#comparing-ingestion-methods-and-tools)。 |永久性           |

## <a name="category-internalserviceerror"></a>类别：InternalServiceError

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|General_InternalServerError                       |发生了内部服务器错误。                     |暂时性          |
|General_TransientSchemaMismatch                   |启动引入时目标表的架构与提交引入时的架构不匹配。         |暂时性           |
|超时                                            |操作已因超时而中止。     |暂时性           |
|BadRequest_MessageExhausted                       |未能引入数据，因为引入已达到最大重试尝试次数或最大重试周期。<br>重试引入可能会成功。   |暂时性          |

## <a name="category-updatepolicyfailure"></a>类别：UpdatePolicyFailure

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema   |调用更新策略失败。 查询架构与表架构不匹配。     |永久性           |
|UpdatePolicy_FailedDescendantTransaction          |调用更新策略失败。 后代事务性更新策略失败。    |暂时性           |
|UpdatePolicy_IngestionError                       |调用更新策略失败。 发生引入错误。<br>针对更新策略的源表报告了此错误。     |暂时性          |
|UpdatePolicy_UnknownError                         |调用更新策略失败。 发生未知错误。<br>针对更新策略的目标表报告了此错误。    |暂时性           |
|UpdatePolicy_Cyclic_Update_Not_Allowed         |调用更新策略失败。 不允许循环更新。      |永久性           |

## <a name="category-useraccessnotauthorized"></a>类别：UserAccessNotAuthorized

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|BadRequest_InvalidKustoIdentityToken              |Kusto 标识令牌无效。                           |永久性           |
|禁止                                          |没有足够的安全权限来执行请求。 | 永久性|

## <a name="category-unknown"></a>类别：未知

|错误消息                                 |说明                                           |永久/暂时|
|---|---|---|
|Unknown                                            |发生未知错误。                             |暂时性          |
