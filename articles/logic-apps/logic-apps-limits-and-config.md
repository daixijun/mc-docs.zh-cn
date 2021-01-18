---
title: 限制和配置
description: Azure 逻辑应用的服务限制（例如持续时间、吞吐量和容量）以及配置值（例如允许的 IP 地址）
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
origin.date: 10/09/2020
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: no
ms.testdate: 06/15/2020
ms.author: v-yeche
ms.openlocfilehash: b75e407b373239325286874824b3bf10e0ec9b15
ms.sourcegitcommit: 40db5a4b9ab8b5877e307ff7a567fd930ca81c72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97894306"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Azure 逻辑应用的限制和配置信息

本文介绍使用 Azure 逻辑应用创建和运行自动化工作流的限制和配置的详细信息。 对于 Power Automate，请参阅 [Power Automate 中的限制和配置](https://docs.microsoft.com/flow/limits-and-config)。

<a name="definition-limits"></a>

## <a name="definition-limits"></a>定义限制

下面是针对单个逻辑应用定义的限制：

| 名称 | 限制 | 注释 |
| ---- | ----- | ----- |
| 每个工作流的操作数 | 500 | 要对此限制进行扩展，可根据需要添加嵌套工作流。 |
| 操作的允许嵌套深度 | 8 | 要对此限制进行扩展，可根据需要添加嵌套工作流。 |
| 每个订阅每个区域的工作流数 | 1,000 | |
| 每个工作流的触发数 | 10 个 | 在代码视图中工作时，而不是在设计器中工作时 |
| Switch 作用域事例限制 | 25 | |
| 每个工作流的变量数 | 250 | |
| `action` 或 `trigger` 的名称 | 80 个字符 | |
| 每个表达式的字符数 | 8,192 | |
| `description` 的长度 | 256 个字符 | |
| 最大 `parameters` 数 | 50 | |
| 最大 `outputs` 数 | 10 | |
| `trackedProperties` 的最大大小 | 16,000 个字符 |
| 内联代码操作 - 最大代码字符数 | 1,024 个字符 <p>对于 100,000 字符限制，请使用 Visual Studio Code 创建逻辑应用。 |

<!--Not Available on and the [preview **Azure Logic Apps** extension](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md)-->

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>运行持续时间和保留期历史记录限制

下面是针对单个逻辑应用运行的限制：

<!--Not Available on Integration service environment limit-->

| 名称 | 多租户限制 |  注释 |
|------|--------------------|-------|
| 运行持续时间 | 90 天 | 运行持续时间是使用运行开始时间以及在开始时工作流设置“[运行历史记录保留期(天)](#change-duration)”中指定的限制计算的。 <p><p>若要更改默认限制，请参阅[更改存储中的运行持续时间和历史记录保留期](#change-duration)。 |
| 存储中的运行历史记录保留期 | 90 天 | 当运行的持续时间超过当前运行历史记录保留期限制时，将从存储的运行历史记录中删除该运行。 无论运行是完成还是超时，都会始终使用运行开始时间和工作流设置“[运行历史记录保留期(天)](#change-retention)”中指定的当前限制来计算运行历史记录保留期。 无论先前的限制如何，将始终使用当前限制来计算保留期。 <p><p>若要更改默认限制以及了解详细信息，请参阅存储[在存储中更改持续时间和运行历史记录保留期](#change-retention)。 若要提高最大限制，[请联系逻辑应用团队](mailto://logicappsemail@microsoft.com)，就你的要求获取帮助。 |
| 最小重复间隔 | 1 秒 | |
| 最大重复间隔 | 500 天 | |
||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>更改存储中的运行持续时间和历史记录保留期

同一设置控制工作流可以运行的最大天数，并控制在存储中保留运行历史记录的最大天数。 若要更改这些属性的默认值或当前限制，请执行以下步骤。

* 对于多租户 Azure 中的逻辑应用，90 天默认限制与最大限制相同。 只能减小此值。

* 对于集成服务环境中的逻辑应用，可以降低或提高 90 天默认限制。

例如，假设你将保留期限制从 90 天减少到 30 天。 将从运行历史记录中删除 60 天的运行。 如果将保持期从 30 天增至 60 天，则已经保留了 20 天的运行将在运行历史记录中继续保留 40 天。

> [!IMPORTANT]
> 当运行的持续时间超过当前运行历史记录保留期限制时，将从存储的运行历史记录中删除该运行。 若要避免丢失运行历史记录，请确保保留期限制始终大于运行的最长持续时间。

1. 在 [Azure 门户](https://portal.azure.cn)搜索框中，找到并选择“逻辑应用”。

1. 找到并选择选择逻辑应用。 在逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的菜单中选择“工作流设置”。

1. 在“运行时选项”下，从“运行历史记录保留天数”列表中选择“自定义”  。

1. 拖动滑块更改所需的天数。

1. 完成后，在“工作流设置”工具栏上选择“保存”。 

如果为逻辑应用生成了 Azure 资源管理器模板，则此设置将显示为工作流资源定义中的属性，如 [Microsoft.Logic 工作流模板参考](https://docs.microsoft.com/azure/templates/microsoft.logic/workflows)中所述：

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>并发、循环和拆分限制

下面是针对单个逻辑应用运行的限制：

| 名称 | 限制 | 说明 |
| ---- | ----- | ----- |
| 触发器并发 | - 若并发控制已关闭，则无限制 <p><p>- 在启用并发控制时，默认限制为 25（启用并发后无法撤消）。 可以将默认值更改为介于 1 与 50（含）之间的值。 | 此限制描述可以在同一时间或并行运行的逻辑应用实例的最大数。 <p><p>**注意**：启用并发后，[解除数组批处理](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)的 SplitOn 限制会降低到 100 个项。 <p><p>若要将默认限制更改为介于 1 到 50 之间（含）的值，请参阅[更改触发器并发限制](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)或[按顺序触发实例](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger)。 |
| 最大等待运行数 | - 未启用并发时，最小等待运行数是 1，最大数目是 50。 <p><p>- 启用并发时，最小等待运行数是 10 加上并发运行（触发器并发）数。 可以将最大数更改为多达 100 个（含）。 | 此限制描述当逻辑应用已在运行最大数量并发实例时，可等待运行的最大逻辑应用实例数。 <p><p>若要更改此默认限制，请参阅[更改等待的运行限制](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)。 |
| Foreach 数组项 | 100,000 | 此限制描述“for each”循环可以处理的最大数组项数。 <p><p>可以使用[查询操作](logic-apps-perform-data-operations.md#filter-array-action)筛选更大数组。 |
| Foreach 并发 | 20 是并发控制关闭时的默认限制。 可以将默认值更改为介于 1 与 50（含）之间的值。 | 此限制是可同时或并行运行的最大“for each”循环迭代数。 <p><p>若要将默认限制更改为介于 1 到 50 之间（含）的值，请参阅[更改“for each”并发限制](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)或[按顺序运行“for each”循环](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)。 |
| SplitOn 项 | - 未启用触发器并发时为 100,000 <p><p>- 启用触发器并发时为 100 | 对于返回数组的触发器，可指定一个表达式，它使用[将数组项拆分或解除批处理到多个工作流实例](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)进行处理的“SplitOn”属性，而不是使用“Foreach”循环。 此表达式引用要用于为每个数组项创建和运行工作流实例的数组。 <p><p>**注意**：启用并发后，SplitOn 限制会降低到 100 个项。 |
| Until 迭代 | - 默认值：60 <p><p>- 最大值：5,000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>吞吐量限制

下面是针对单个逻辑应用定义的限制：

### <a name="multi-tenant-logic-apps-service"></a>多租户逻辑应用服务

| 名称 | 限制 | 说明 |
| ---- | ----- | ----- |
| 操作：每 5 分钟执行的次数 | 默认限制为 100,000，最大限制为 300,000。 | 若要更改此默认限制，请参阅处于预览阶段的[在“高吞吐量”模式下运行逻辑应用](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)。 或者，你可根据需要在多个逻辑应用之间分配工作负荷。 |
| 操作：并发出站调用 | ~2,500 | 你可减少并发请求数，或根据需要减少持续时间。 |
| 运行时终结点：并发入站调用 | ~1,000 | 你可减少并发请求数，或根据需要减少持续时间。 |
| 运行时终结点：每 5 分钟读取调用  | 60,000 | 此限制适用于从逻辑应用的运行历史记录获取原始输入和输出的调用。 可根据需要在多个应用中分发工作负荷。 |
| 运行时终结点：每 5 分钟调用调用 | 45,000 | 可根据需要在多个应用中分发工作负荷。 |
| 每 5 分钟的内容吞吐量 | 600 MB | 可根据需要在多个应用中分发工作负荷。 |
||||

<!--Not Available on ### Integration service environment (ISE)-->

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>网关限制

Azure 逻辑应用支持通过网关执行写入操作（包括插入和更新）。 但是，这些操作存在[有效负载大小限制](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)。

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP 限制

下面是单个传出或传入 HTTP 调用的限制：

#### <a name="timeout"></a>超时

某些连接器操作会进行异步调用或侦听 Webhook 请求，因此，这些操作的超时时间可能会长于以下限制。 有关详细信息，请参阅特定连接器的技术详细信息以及[工作流触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)。

<!--Not Available on Integration service environment limit-->

| 名称 | 多租户限制 | 注释 |
|------|--------------------|-------|
| 出站请求 | 120 秒 <br />（2 分钟） | 出站请求的示例包括 HTTP 触发器进行的调用。 <p><p>**提示**：对于运行时间较长的操作，请使用 [异步轮询模式](../logic-apps/logic-apps-create-api-app.md#async-pattern)或 [until 循环](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)。 在调用其他具有[可调用终结点](logic-apps-http-endpoint.md)的逻辑应用时，若要绕过超时限制，可改用内置的 Azure 逻辑应用操作（可在“内置”下的连接器连接器中找到）。 |
| 入站请求 | 120 秒 <br />（2 分钟） | 入站请求的示例包括请求触发器和 webhook 触发器收到的请求。 <p><p>**注意**：要使原始调用方能够获得响应，则除非以嵌套工作流的形式调用其他逻辑应用，否则必须在限制内完成响应的所有步骤。 有关详细信息，请参阅[调用、触发器或嵌套逻辑应用](../logic-apps/logic-apps-http-endpoint.md)。 |
|||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>消息大小

<!--Not Available on ISE-labeled connectors use the ISE limit, not their non-ISE connector limits.-->

| 名称 | 多租户限制 | 注释 |
|------|--------------------|-------|
| 消息大小 | 100 MB  | 若要解决此限制问题，请参阅[使用分块处理大型消息](../logic-apps/logic-apps-handle-large-messages.md)。 但是，某些连接器和 API 可能不支持分块，甚至不支持默认限制。 <p><p>- 连接器（如 AS2、X12 和 EDIFACT）具有自己的 [B2B 消息限制](#b2b-protocol-limits)。 |
| 使用分块的消息大小 | 1 GB | 此限制适用于本机支持分块或可以在其运行时配置中启用分块的操作。 <p><p>有关分块的详细信息，请参阅[使用分块处理大型消息](../logic-apps/logic-apps-handle-large-messages.md)。 |
|||||   

#### <a name="character-limits"></a>字符限制

| 名称 | 说明 |
|------|-------|
| 表达式计算限制 | 131,072 个字符 | `@concat()`、`@base64()`、`@string()` 表达式的长度不能超过此限制。 |
| 请求 URL 字符限制 | 16,384 个字符 |
|||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>重试策略

| 名称 | 限制 | 说明 |
| ---- | ----- | ----- |
| 重试次数 | 90 | 默认值为 4。 若要更改默认值，请使用[重试策略参数](../logic-apps/logic-apps-workflow-actions-triggers.md)。 |
| 重试最大延迟 | 1 天 | 若要更改默认值，请使用[重试策略参数](../logic-apps/logic-apps-workflow-actions-triggers.md)。 |
| 重试最小延迟 | 5 秒 | 若要更改默认值，请使用[重试策略参数](../logic-apps/logic-apps-workflow-actions-triggers.md)。 |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>身份验证限制

如果逻辑应用从使用请求触发器开始，并启用 [Azure Active Directory 开放式身份验证](../active-directory/develop/index.yml) (Azure AD OAuth) 来授权对请求触发器的入站调用，则应遵循以下限制：

| 名称 | 限制 | 说明 |
| ---- | ----- | ----- |
| Azure AD 授权策略 | 5 | |
| 每个授权策略的声明 | 10 | |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>自定义连接器限制

下面介绍对可通过 Web API 创建的自定义连接器的限制。

<!--Not Available on Integration service environment limit-->

| 名称 | 多租户限制 | 注释 |
|------|--------------------|-------|
| 自定义连接器数 | 每个 Azure 订阅 1,000 | |
| 自定义连接器的每分钟请求数 | 每分钟每个连接 500 个请求 | |
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>托管标识

<!--Not Available on user-assigned identity-->

| 名称 | 限制 |
|------|-------|
| 每个逻辑应用的托管标识 | 系统分配的标识或 1 个用户分配的标识 |
| 每个区域的每个 Azure 订阅中具有托管标识的逻辑应用数量 | 1,000 |
|||

<a name="integration-account-limits"></a>

<!--Pending Upgrade on Production Team ## Integration account limits-->
<!--Not Available on [Free tier] for Azure China Cloud-->


<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>每个集成帐户的项目限制

下面介绍对每个集成帐户层的项目数量限制。
有关定价费率，请参阅[逻辑应用定价](https://www.azure.cn/pricing/details/logic-apps/)。 若要了解集成帐户的定价和计费工作原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#integration-accounts)。

> [!NOTE]
> 免费层仅用于探索场景，不用于生产场景。 此层限制吞吐量和使用情况，并且不具有服务级别协议 (SLA)。

| 项目 | 免费 | 基本 | Standard |
|----------|------|-------|----------|
| EDI 贸易协议 | 10 | 1 | 1,000 |
| EDI 参与方 | 25 | 2 | 1,000 |
| 地图 | 25 | 500 | 1,000 |
| 架构 | 25 | 500 | 1,000 |
| 程序集 | 10 | 25 | 1,000 |
| 证书 | 25 | 2 | 1,000 |
| 批处理配置 | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>项目容量限制

| 项目 | 限制 | 说明 |
| -------- | ----- | ----- |
| Assembly | 8 MB | 若要上传大于 2 MB 的文件，请使用 [Azure 存储帐户和 blob 容器](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 |
| 映射（XSLT 文件） | 8 MB | 若要上传大于 2 MB 的文件，请使用 [Azure 逻辑应用 REST API - 映射](https://docs.microsoft.com/rest/api/logic/maps/createorupdate)。 <p><p>**注意**：映射可以成功处理的数据或记录量取决于 Azure 逻辑应用中的消息大小和操作超时限制。 例如，如果使用 HTTP 操作，则根据 [HTTP 消息大小和超时限制](#request-limits)，在操作能够在 HTTP 超时限制内完成的情况下，映射最多可以处理达到 HTTP 消息大小限制的数据量。 |
| 架构 | 8 MB | 若要上传大于 2 MB 的文件，请使用 [Azure 存储帐户和 blob 容器](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>吞吐量限制

<!--Not Available on FREE INTEGRATION ACCOUNT-->

| 运行时终结点 | 基本 | Standard | 说明 |
|------------------|-------|----------|-------|
| 每 5 分钟读取调用 | 30,000 | 60,000 |此限制适用于从逻辑应用的运行历史记录获取原始输入和输出的调用。 你可根据需要在多个帐户之间分配工作负荷。 |
| 每 5 分钟调用调用 | 30,000 | 45,000 | 你可根据需要在多个帐户之间分配工作负荷。 |
| 每 5 分钟跟踪调用 | 30,000 | 45,000 | 你可根据需要在多个帐户之间分配工作负荷。 |
| 阻止并发调用 | ~1,000 | ~1,000 | 对于所有 SKU 均相同。 你可减少并发请求数，或根据需要减少持续时间。 |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B 协议（AS2、X12、EDIFACT）消息大小

以下消息大小限制适用于 B2B 协议：

<!--Not Available on Integration service environment limit-->

| 名称 | 多租户限制 | 注释 |
|------|--------------------|-------|
| AS2 | v2 - 100 MB<br />v1 - 25 MB | 适用于解码和编码 |
| X12 | 50 MB | 适用于解码和编码 |
| EDIFACT | 50 MB | 适用于解码和编码 |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>禁用或删除逻辑应用

禁用逻辑应用后，任何新运行都不会实例化。 所有正在进行的和挂起的运行将继续进行，直到完成，这可能要花费一些时间才能完成。

删除逻辑应用后，任何新运行都不会实例化。 所有正在进行和挂起的运行都将取消。 如果有成千上万个运行，取消操作可能需要很长时间才能完成。

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>防火墙配置：IP 地址和服务标记

Azure 逻辑应用用于传入和传出调用的 IP 地址由逻辑应用所在的区域决定。 同一区域中的所有逻辑应用都使用相同的 IP 地址范围。 某些 [Power Automate](https://docs.microsoft.com/power-automate/getting-started) 调用（例如 HTTP 和 HTTP + OpenAPI 请求）直接通过 Azure 逻辑应用服务执行并来自此处列出的 IP 地址。 要详细了解 Power Automate 使用的 IP 地址，请参阅 [Power Automate 中的限制和配置](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration)。

> [!TIP]
> 为帮助你更简单地创建安全规则，可选择性地使用[服务标记](../virtual-network/service-tags-overview.md)，而不是为每个区域指定逻辑应用 IP 地址，如此部分中稍后所述。 这些标记适用于可使用逻辑应用服务的区域：
>
> * **LogicAppsManagement**：表示逻辑应用服务的入站 IP 地址前缀。
> * **LogicApps**：表示逻辑应用服务的出站 IP 地址前缀。

* 对于 [Azure 中国世纪互联](https://docs.microsoft.com/azure/china/)，固定或保留 IP 地址不可用于[自定义连接器](../logic-apps/custom-connector-overview.md)和[托管连接器](../connectors/apis-list.md#managed-api-connectors)（例如，Azure 存储、SQL Server、Office 365 Outlook 等）。

* 若要支持逻辑应用通过 [HTTP](../connectors/connectors-native-http.md)、[HTTP + Swagger](../connectors/connectors-native-http-swagger.md) 和其他 HTTP 请求直接发出的调用，请根据逻辑应用所在的区域，使用逻辑应用服务所用的所有[入站](#inbound)和[出站](#outbound) IP 地址对防火墙进行设置。 这些地址显示在本部分的“入站”和“出站”标题下，并按区域进行排序。

* 若要支持[托管连接器](../connectors/apis-list.md#managed-api-connectors)发出的调用，请根据逻辑应用所在的区域，使用这些连接器所用的全部[出站](#outbound) IP 地址对防火墙进行设置。 这些地址显示在本部分的“出站”标题下，并按区域进行排序。

    <!--Not Available on [open these ports](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)-->

* 如果逻辑应用无法访问使用[防火墙和防火墙规则](../storage/common/storage-network-security.md)的 Azure 存储帐户，则可通过[各种选项来启用访问权限](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)。

    例如，逻辑应用不能直接访问使用防火墙规则的存储帐户，因此存在于同一区域中。 但是，如果允许[区域中托管连接器的出站 IP 地址](../logic-apps/logic-apps-limits-and-config.md#outbound)，则逻辑应用可以访问其他区域中的存储帐户，除非你使用 Azure 表存储或 Azure 队列存储连接器。 若要访问表存储或队列存储，则可改用 HTTP 触发器和操作。 有关其他选项，请参阅[访问防火墙后的存储帐户](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)。

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>入站 IP 地址
> [!TIP]
> 为帮助你更简单地创建安全规则，可选择性地使用[服务标记](../virtual-network/service-tags-overview.md)和 `LogicAppsManagement`，而不是为每个区域指定入站逻辑应用 IP 地址前缀。
> 此标记可使用逻辑应用服务的区域。

> [!NOTE]
>  有关最新的服务标记信息，可下载并查看 [Azure IP 范围和服务标记 - 中国云](https://www.microsoft.com/download/confirmation.aspx?id=57062)。
>

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>多租户 Azure - 入站 IP 地址

| 多租户区域 | IP |
|---------------------|----|
| 中国 | 2404:7940:1:402::4f0, 2404:7940:101:402::cf0, 2404:7940:201:402::4f0, 2404:7940:301:402::4f0 |

<a name="outbound"></a>

<a name="multi-tenant-azure---outbound-ip-addresses"></a>
### <a name="outbound-ip-addresses"></a>出站 IP 地址

该部分列出了 Azure 逻辑应用服务和托管连接器的出站 IP 地址。

<!--Not Available on [Azure Government - Outbound IP addresses](#azure-government-outbound)-->

> [!TIP]
> 为帮助你更简单地创建安全规则，可选择使用[服务标记](../virtual-network/service-tags-overview.md) `LogicApps`，而不为每个区域指定出站逻辑应用 IP 地址前缀。
> 对于托管连接器，可以选择使用 `AzureConnectors` 服务标记，而不是为每个区域指定出站托管连接器 IP 地址前缀。 这些标记适用于可使用逻辑应用服务的区域。 

> [!NOTE]
>  有关最新的服务标记信息，可下载并查看 [Azure IP 范围和服务标记 - 中国云](https://www.microsoft.com/download/confirmation.aspx?id=57062)。
>

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>多租户 Azure - 出站 IP 地址

<!--CUSTOMIZE ON Alex Shyronosov REQUEST ON 01/05/2021-->

| 多租户区域 | 逻辑应用 IP | 托管连接器 IP |
|---------------------|---------------|-----------------------|
| 中国东部          | 52.130.112.64 | 139.219.192.103、139.219.195.254、139.219.187.126、139.219.191.183、139.219.186.24、139.219.188.127 |
| 中国东部 2        | 40.73.172.224 | 52.130.89.228、40.72.96.40 |
| 中国北部         | 139.217.52.224 | 40.125.171.226、139.217.8.104、139.217.10.71、139.217.15.113、139.217.15.67、139.217.14.71 |
| 中国北部 2       | 40.73.141.160 |  139.217.103.102、139.217.114.96 |

<!--CUSTOMIZE ON Alex Shyronosov REQUEST ON 01/05/2021-->

## <a name="next-steps"></a>后续步骤

* 了解如何[创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* 了解[常见示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- Update_Description: update meta properties, wording update, update link -->