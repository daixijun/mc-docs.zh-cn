---
title: 使用 Azure 门户部署资源
description: 使用 Azure 门户和 Azure 资源管理器将资源部署到订阅中的资源组。
ms.topic: conceptual
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: yes
ms.testdate: 07/13/2020
ms.author: v-yeche
ms.openlocfilehash: 67461ae72fbe886db4e5345c15a1850f902cbcce
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978165"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>使用 ARM 模板和 Azure 门户部署资源

了解如何将 [Azure 门户](https://portal.azure.cn)与 [Azure 资源管理器模板（ARM 模板）](overview.md)配合使用来部署 Azure 资源。 若要了解有关管理资源的信息，请参阅[通过 Azure 门户管理 Azure 资源](../management/manage-resources-portal.md)。

使用 Azure 门户部署 Azure 资源通常涉及两个步骤：

- 创建资源组。
- 将资源部署到资源组。

此外，还可以创建自定义 ARM 模板来部署 Azure 资源。

本文介绍了这两种方法。

## <a name="create-a-resource-group"></a>创建资源组

1. 若要创建新资源组，请从 [Azure 门户](https://portal.azure.cn)中选择“资源组”。

    :::image type="content" source="./media/deploy-portal/select-resource-groups.png" alt-text="选择资源组":::

1. 在“资源组”下，选择“添加”。

    :::image type="content" source="./media/deploy-portal/add-resource-group.png" alt-text="添加资源组":::

1. 选择或输入以下属性值：

    - **订阅**：选择 Azure 订阅。
    - **资源组**：为资源组指定名称。
    - **区域**：指定 Azure 位置。 资源组在该位置存储有关资源的元数据。 出于合规性原因，你可能会想要指定该元数据的存储位置。 一般情况下，建议指定大部分资源所在的位置。 使用相同位置可简化模板。

        :::image type="content" source="./media/deploy-portal/set-group-properties.png" alt-text="设置组的值":::

1. 选择“查看 + 创建”。
1. 查看值，然后选择“创建”。
1. 选择“刷新”，然后才能在列表中看到新资源组。

## <a name="deploy-resources-to-a-resource-group"></a>将资源部署到资源组

创建资源组后，可以从市场将资源部署到该组。 市场为常见方案提供预定义的解决方案。

1. 若要开始部署，请从 [Azure 门户](https://portal.azure.cn)中选择“创建资源”。

    :::image type="content" source="./media/deploy-portal/new-resources.png" alt-text="新建资源":::

1. 查找希望部署的资源类型。 资源将按类别进行组织。 如果看不到想要部署的特定解决方案，可以在市场中搜索。 以下屏幕截图显示已选择“Ubuntu Server”。

    :::image type="content" source="./media/deploy-portal/select-resource-type.png" alt-text="选择资源类型":::

1. 根据所选资源的类型，需要在部署之前设置相关属性的集合。 对于所有类型，必须选择目标资源组。 下图显示了如何创建 Linux 虚拟机并将其部署到所创建的资源组。

    :::image type="content" source="./media/deploy-portal/select-existing-group.png" alt-text="创建资源组":::

    可以在部署资源时创建资源组。 选择“新建”  并为资源组指定名称。

1. 部署开始。 部署可能需要几分钟的时间。 有些资源比其他资源需要更长的时间。 完成部署后，会看到一条通知。 选择“转到资源”以将其打开

    :::image type="content" source="./media/deploy-portal/view-notification.png" alt-text="查看通知":::

1. 部署资源后，可以通过选择“添加”将更多资源添加到资源组。

    :::image type="content" source="./media/deploy-portal/add-resource.png" alt-text="添加资源":::

虽然你没有看到，但门户使用了一个 ARM 模板来部署所选资源。 可以从部署历史记录中找到该模板。 有关详细信息，请参阅[在部署之后导出模板](export-template-portal.md#export-template-after-deployment)。

## <a name="deploy-resources-from-custom-template"></a>从自定义模板部署资源

如果想要执行部署，但不使用市场中的任何模板，可以创建自定义模板来针对解决方案定义基础结构。 若要了解如何创建模板，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。

<!--MOONCAKE: AZURE CHINA CLOUD HAS FOLLOW GLOBAL SITE -->

> [!NOTE]
> 门户界面不支持引用[来自 Key Vault 的密码](key-vault-parameter.md)。 请改用 [PowerShell](deploy-powershell.md) 或 [Azure CLI](deploy-cli.md)，从本地或从外部 URI 部署模板。

1. 若要通过门户部署自定义模板，请选择“创建资源”，搜索“模板”， 然后选择“模板部署”。

    :::image type="content" source="./media/deploy-portal/search-template.png" alt-text="搜索模板部署":::

1. 选择“创建” 。
1. 此时，会看到用于创建模板的几个选项：

    - 在编辑器中生成自己的模板：在门户模板编辑器中创建自己的模板。
    - **常用模板**：从常见解决方案中进行选择。
    - 加载 GitHub 快速启动模板：从[快速启动模板](https://azure.microsoft.com/resources/templates/)中进行选择。

        :::image type="content" source="./media/deploy-portal/see-options.png" alt-text="视图选项":::

    本教程提供了加载快速入门模板的说明。

1. 在“加载 GitHub 快速入门模板”下，键入或选择 **101-storage-account-create**。

    可以使用两个选项：

    - **选择模板**：部署模板。
    - **编辑模板**：在部署快速入门模板之前对其进行编辑。

1. 选择“编辑模板”，浏览门户模板编辑器。 模板会加载到编辑器中。 请注意，有两个参数：**storageAccountType** 和 **location**。

    :::image type="content" source="./media/deploy-portal/show-json.png" alt-text="创建模板":::

1. 对模板进行小的更改。 例如，将 **storageAccountName** 变量更新为：

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. 选择“保存” 。 现在会看到门户模板部署界面。 请注意在模板中定义的两个参数。
1. 输入或选择属性值：

    - **订阅**：选择 Azure 订阅。
    - **资源组**：选择“新建”并指定名称。
    - **位置**：选择 Azure 位置。
    - **存储帐户类型**：使用默认值。
    - **位置**：使用默认值。
    - **我同意上述条款和条件**：（选中）

1. 选择“购买”。

<!--MOONCAKE: AZURE CHINA CLOUD HAS FOLLOW GLOBAL SITE -->

## <a name="next-steps"></a>后续步骤

- 若要查看审核日志，请参阅[使用资源管理器审核操作](../management/view-activity-logs.md)。
- 若要排查部署错误，请参阅[查看部署操作](deployment-history.md)。
- 若要从部署或资源组中导出模板，请参阅[导出 ARM 模板](export-template-portal.md)。

<!-- Not Available on [Azure Deployment Manager](deployment-manager-overview.md)-->
<!-- Update_Description: update meta properties, wording update, update link -->
