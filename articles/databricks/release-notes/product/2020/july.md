---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 07/31/2020
title: 2020 年 7 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2020 年 7 月发行说明。
ms.openlocfilehash: 12d24264b1ba3f35f7de4b64be9b984c410a0632
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329160"
---
# <a name="july-2020"></a>2020 年 7 月

这些功能和 Azure Databricks 平台的改进已于 2020 年 7 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="web-terminal-public-preview"></a>Web 终端（公共预览版）

2020 年 7 月 29 日 - 8 月 4 日：版本 3.25

Web 终端为具有群集上的“可附加到”权限的用户提供一种便捷且高度交互的方式来运行 shell 命令，包括 Vim 或 Emacs 等编辑器。 使用 Web 终端的示例包括监视资源使用情况和安装 Linux 包。

有关详细信息，请参阅 [Web 终端](../../../clusters/web-terminal.md)。

## <a name="new-more-secure-global-init-script-framework-public-preview"></a>新的更安全的全局初始化脚本框架（公共预览版）

2020 年 7 月 29 日 - 8 月 4 日：版本 3.25

新的全局 init 脚本框架相对于旧的全局 init 脚本进行了重大改进：

* Init 脚本更安全，需要管理员权限才能执行创建、查看和删除操作。
* 记录与脚本相关的启动失败。
* 可以设置多个 init 脚本的执行顺序。
* Init 脚本可以引用与群集相关的环境变量。
* 可以使用管理控制台或新的全局 Init 脚本 REST API 创建和管理 Init 脚本。

Databricks 建议[将现有的旧版全局 init 脚本迁移到新框架](../../../clusters/init-scripts.md#migrate-legacy-scripts)，以利用这些改进。

有关详细信息，请参阅[全局 init 脚本（新）](../../../clusters/init-scripts.md#global-init-script)。

## <a name="ip-access-lists-now-ga"></a>IP 访问列表现已正式发布

2020 年 7 月 29 日 - 8 月 4 日：版本 3.25

IP 访问列表 API 现以正式版提供。

正式版包含一项更改，即重命名 `list_type` 值：

* `WHITELIST` 至 `ALLOW`
* `BLACKLIST` 至 `BLOCK`

使用 IP 访问列表 API 配置 Azure Databricks 工作区，以便用户只通过具有安全外围的现有企业网络连接到服务。 Azure Databricks 管理员可以使用 IP 访问列表 API 来定义一组已获批准的 IP 地址，包括允许列表和阻止列表。 对 Web 应用程序和 REST API 的所有传入访问均要求用户从经授权的 IP 地址进行连接，从而确保用户只有使用 VPN 才能从咖啡店或机场等公共网络访问工作区。

该功能需要 [Azure Databricks Premium 计划](https://databricks.com/product/azure-pricing)。

有关详细信息，请参阅 [IP 访问列表](../../../security/network/ip-access-list.md)。

## <a name="new-file-upload-dialog"></a>新的“文件上传”对话框

2020 年 7 月 29 日 - 8 月 4 日：版本 3.25

现在，可以上传小型表格数据文件（例如 CSV），并通过笔记本访问它们，方法是从笔记本“文件”菜单选择“上传数据” 。 生成的代码演示如何将数据加载到 Pandas 或 DataFrames 中。 管理员可以在管理控制台的“高级”选项卡上禁用此功能。

有关详细信息，请参阅[文件上传接口](../../../data/databricks-file-system.md#user-interface)。

## <a name="scim-api-filter-and-sort-improvements"></a>对 SCIM API 筛选器和排序的改进

2020 年 7 月 29 日 - 8 月 4 日：版本 3.25

SCIM API 现在包含以下筛选和排序改进：

* 管理员用户可根据 `active` 属性筛选用户。
* 所有用户均可使用 `sortBy` 和 `sortOrder` [查询参数](https://tools.ietf.org/html/rfc7644#section-3.4.2.3)对结果进行排序。 默认选择“按 ID 排序”。

## <a name="azure-government-regions-added"></a>添加了 Azure 政府区域

2020 年 7 月 25 日

最近，我们在 US Gov 亚利桑那州和 US Gov 弗吉尼亚州地区为美国政府实体及其合作伙伴提供了 Azure Databricks。

## <a name="databricks-runtime-71-ga"></a>Databricks Runtime 7.1 正式版

**2020 年 7 月 21 日**

在 Databricks Runtime 7.0 的基础上，Databricks Runtime 7.1 引入了许多额外的功能和改进，包括：

* Google BigQuery 连接器
* `%pip` 命令用于管理在笔记本会话中安装的 Python 库
* 已安装 Koalas
* 很多 Delta Lake 改进，包括：
  * 设置用户定义的提交元数据
  * 获取由当前 `SparkSession` 编写的最后一个提交的版本
  * 使用 `_spark_metadata` 事务日志转换结构化流式处理创建的 Parquet 表
  * `MERGE INTO` 性能改进

有关详细信息，请参阅完整的 [Databricks Runtime 7.1](../../runtime/7.1.md) 发行说明。

## <a name="databricks-runtime-71-ml-ga"></a>Databricks Runtime 7.1 ML 正式版

**2020 年 7 月 21 日**

用于机器学习的 Databricks Runtime 7.1 基于 Databricks Runtime 7.1 构建，并引入了以下新功能和库更改：

* 默认启用 pip 和 conda magic 命令
* spark-tensorflow-distributor：0.1.0
* pillow 7.0.0 -> 7.1.0
* pytorch 1.5.0 -> 1.5.1
* torchvision 0.6.0 -> 0.6.1
* horovod 0.19.1 -> 0.19.5
* mlflow 1.8.0 -> 1.9.1

有关详细信息，请参阅完整的 [Databricks Runtime 7.1 ML](../../runtime/7.1ml.md) 发行说明。

## <a name="databricks-runtime-71-genomics-ga"></a>Databricks Runtime 7.1 基因组学正式版

**2020 年 7 月 21 日**

用于基因组学的 Databricks Runtime 7.1 基于 Databricks Runtime 7.1 构建，并引入了以下新功能：

* LOCO 转换
* GloWGR 输出重塑函数
* RNASeq 输出不成对对齐

有关详细信息，请参阅完整的[用于基因组学的 Databricks Runtime 7.1](../../runtime/7.1genomics.md) 发行说明。

## <a name="databricks-connect-71-public-preview"></a>Databricks Connect 7.1（公共预览版）

**2020 年 7 月 17 日**

[Databricks Connect](../../../dev-tools/databricks-connect.md) 7.1 现以公共预览版提供。

## <a name="ip-access-list-api-updates"></a>IP 访问列表 API 更新

2020 年 7 月 15 日 - 7 月 21 日：版本 3.24

以下 [IP 访问列表 API](../../../security/network/ip-access-list.md) 属性已更改：

* `updator_user_id` 至 `updated_by`
* `creator_user_id` 至 `created_by`

## <a name="python-notebooks-now-support-multiple-outputs-per-cell"></a>Python 笔记本现在支持每个单元格多个输出

2020 年 7 月 15 日 - 7 月 21 日：版本 3.24

Python 笔记本现在支持每个单元格有多个输出。 这意味着一个单元格中可以有任意数量的 display、displayHTML 或 print 语句。 利用该功能，可以在同一单元格中查看原始数据和绘图，或查看在遇到错误之前成功打印的所有输出。

> [!div class="mx-imgBorder"]
> ![一个单元格中有多个输出](../../../_static/images/notebooks/multiple-cell-outputs.gif)

此功能需要 Databricks Runtime 7.1 或更高版本，并且在 Databricks Runtime 7.1 中默认处于禁用状态。 可以通过设置 `spark.databricks.workspace.multipleResults.enabled true` 来启用它。

## <a name="view-notebook-code-and-results-cells-side-by-side"></a>并排查看笔记本代码和结果单元格

2020 年 7 月 15 日 - 7 月 21 日：版本 3.24

使用新的“并排”笔记本显示选项，可以查看彼此相邻的代码和结果。 此显示选项将“标准”选项（以前称为“代码”）和“仅结果”选项结合在一起。

> [!div class="mx-imgBorder"]
> ![“并排显示”视图](../../../_static/images/notebooks/side-by-side.gif)

## <a name="pause-job-schedules"></a>暂停作业计划

2020 年 7 月 15 日 - 7 月 21 日：版本 3.24

现在，作业计划具有“暂停”和“取消暂停”按钮，使用这些按钮可以轻松地暂停和恢复作业 。 现在，可以对作业计划进行更改，进行更改时不会启动额外的作业运行。 当前运行及由“立即运行”触发的运行不受影响。 有关详细信息，请参阅[暂停和恢复作业计划](../../../jobs.md#pause-and-resume-a-job-schedule)。

## <a name="jobs-api-endpoints-validate-run-id"></a>作业 API 终结点验证运行 ID

2020 年 7 月 15 日 - 7 月 21 日：版本 3.24

`jobs/runs/cancel` 和 `jobs/runs/output` API 终结点现在会验证相应 `run_id` 参数是否有效。 对于无效参数，这些 API 终结点现在返回 HTTP 状态代码 400，而不是代码 500。

## <a name="azure-active-directory-tokens-to-authorize-to-the-databricks-rest-api-ga"></a>使用 Azure Active Directory 令牌向 Databricks REST API 正式版授权

2020 年 7 月 15 日 - 7 月 21 日：版本 3.24

现已正式推出使用 Azure Active Directory 令牌向 [REST API 2.0](../../../dev-tools/api/latest/index.md) 进行身份验证的功能。 使用 Azure Active Directory 令牌，可以对新工作区的创建和设置进行自动化。 服务主体是 Azure Active Directory 中的应用程序对象。 还可以使用 Azure Databricks 工作区中的服务主体自动执行工作流。 有关详细信息，请参阅[使用 Azure Active Directory 令牌进行身份验证](../../../dev-tools/api/latest/aad/index.md)。

## <a name="format-sql-in-notebooks-automatically"></a>自动设置笔记本中的 SQL 格式

2020 年 7 月 15 日 - 7 月 21 日：版本 3.24

现在可以通过键盘快捷方式、命令上下文菜单和笔记本“编辑”菜单（选择“编辑”>“格式化 SQL 单元格”）来[格式化 SQL 笔记本单元格](../../../notebooks/notebooks-use.md#sql-format)。 通过 SQL 格式设置，可以轻松读取和维护代码。 它适用于 SQL 笔记本以及 `%sql` 单元格。

> [!div class="mx-imgBorder"]
> ![格式化笔记本 SQL 单元格](../../../_static/images/notebooks/format-sql.gif)

## <a name="reproducible-order-of-installation-for-maven-and-cran-libraries"></a>关于 Maven 和 CRAN 库安装的可重现顺序

2020 年 7 月 1 日 - 9 日：版本 3.23

Azure Databricks 按照在群集上安装 Maven 和 CRAN 库的顺序对其进行处理。

## <a name="take-control-of-your-users-personal-access-tokens-with-the-token-management-api-public-preview"></a>使用令牌管理 API（公共预览版）控制用户的个人访问令牌

2020 年 7 月 1 日 - 9 日：版本 3.23

现在，Azure Databricks 管理员可以使用令牌管理 API 来管理其用户的 Azure Databricks 个人访问令牌：

* 监视和撤销用户的个人访问令牌。
* 控制工作区中未来令牌的生存期。
* 控制哪些用户可以创建和使用令牌。

请参阅[管理个人访问令牌](../../../administration-guide/access-control/tokens.md)。

## <a name="restore-cut-notebook-cells"></a>还原剪切的笔记本单元

2020 年 7 月 1 日 - 9 日：版本 3.23

现在，可以使用 (`Z`) 键盘快捷方式或选择“编辑”>“撤消已剪切的单元格”来还原已剪切的笔记本单元格。 此功能类似于撤消已删除单元格的功能。

## <a name="assign-jobs-can-manage-permission-to-non-admin-users"></a>分配作业 **可以管理非管理员用户的** 权限

2020 年 7 月 1 日 - 9 日：版本 3.23

现在可以将非管理员用户和组分配给作业的“可管理”权限。 此权限级别允许用户管理作业上的所有设置，包括分配权限、更改所有者和更改群集配置（例如，添加库和修改群集规范）。 请参阅[作业访问控制](../../../security/access-control/jobs-acl.md)。

## <a name="non-admin-azure-databricks-users-can-view-and-filter-by-username-using-the-scim-api"></a>非管理员 Azure Databricks 用户可以使用 SCIM API 按用户名查看和筛选

2020 年 7 月 1 日 - 9 日：版本 3.23

现在，非管理员用户可以使用 [SCIM/用户终结点](../../../dev-tools/api/latest/scim/scim-users.md)查看用户名并按用户名筛选用户。

## <a name="link-to-view-cluster-specification-when-you-view-job-run-details"></a>用于在查看作业运行详细信息时查看群集规范的链接

2020 年 7 月 1 日 - 9 日：版本 3.23

现在，查看作业运行的详细信息时，可以单击指向群集配置页面的链接，以查看群集规范。 以前，必须从 URL 复制作业 ID，然后转到群集列表中进行搜索。

> [!div class="mx-imgBorder"]
> ![作业运行时的群集链接](../../../_static/images/release-notes/job-run-cluster-link.png)