---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/29/2020
title: 2018 年 9 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2018 年 9 月发行说明。
ms.openlocfilehash: 41e8d9586e2e2caadffd96bdc47daacaf0e43c6d
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329441"
---
# <a name="september-2018"></a>2018 年 9 月

这些功能和 Azure Databricks 平台的改进已于 2018 年 9 月发布。

## <a name="scim-provisioning-using-azure-ad-preview"></a>使用 Azure AD 进行 SCIM 预配（预览版）

**2018 年 9 月 26 日**

现在可以使用 Azure Active Directory 为用户和组预配 Azure Databricks，并在其离职或不再需要访问 Azure Databricks 时取消预配。

此功能以预览版提供。 请参阅[使用 SCIM 预配用户和组](../../../administration-guide/users-groups/scim/index.md)。

## <a name="cluster-tags-on-azure-bills"></a>Azure 帐单上的群集标记

**2018 年 9 月 25 日：版本 2.81**

可以使用创建群集时指定的[群集标签](../../../clusters/configure.md#cluster-tags)监视组织中各种组所使用的云资源的成本。 Azure 账单上现显示自定义群集标签，这方便更轻松地创建 DBU 使用的退款。

## <a name="support-for-fsv2-series-vms"></a>对 Fsv2 系列 VM 的支持

**2018 年 9 月 25 日：版本 2.81**

Azure Databricks 现支持 Fsv2 系列虚拟机。 若要了解 Fsv2 系列，请参阅[计算优化虚拟机大小](/virtual-machines/windows/sizes-compute)。

## <a name="scim-api-for-provisioning-users-and-groups-preview"></a>用于预配用户和组的 SCIM API（预览版）

**2018 年 9 月 13 日：版本 2.80**

Azure Databricks 现支持 SCIM（跨域身份管理系统，一种可用于使用 REST API 和 JSON 将用户预配过程自动化的开放标准）。 借助 SCIM，你可以使用标识提供者在 Databricks 中创建用户并为他们提供适当的访问级别；当他们离开你的组织或不再需要访问 Databricks 时，你还可以删除他们的访问权限（对他们进行取消预配）。

此功能以预览版提供。 请参阅 [SCIM API](../../../dev-tools/api/latest/scim/index.md)。