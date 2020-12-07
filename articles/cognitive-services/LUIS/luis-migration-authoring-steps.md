---
title: 迁移到 Azure 创作资源
titleSuffix: Azure Cognitive Services
description: 迁移到 Azure 创作资源。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/01/2020
origin.date: 05/17/2020
ms.author: v-tawe
ms.openlocfilehash: 0c0a83fb7df57abd53ed0d690d187c30b5f9232b
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432717"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>迁移到 Azure 创作资源的步骤

从语言理解 (LUIS) 门户中，迁移你拥有的所有应用以使用 Azure 创作资源。

## <a name="prerequisites"></a>先决条件

* 可选：通过导出每个应用或使用导出 [API](https://dev.cognitive.azure.cn/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)，对 LUIS 门户的应用列表中的应用进行备份。
* 可选：保存每个应用的协作者列表。 在迁移过程中，可向所有协作者发送电子邮件。
* 必需：需要具备 [Azure 订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。 订阅过程中需要帐单信息。 但是，如果使用 LUIS，可以使用免费 (F0) 定价层。 随着使用量的增加，你最终可能会发现需要付费层。

如果没有 Azure 订阅，请进行[注册](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="access-the-migration-process"></a>访问迁移过程

每周一次，系统都会提示你迁移应用。 无需迁移即可取消此窗口。 如果要在下一个计划的时间段之前迁移，可以从 LUIS 门户顶部工具栏上的“Azure”图标开始迁移过程。

> [!div class="mx-imgBorder"]
> ![“迁移”图标](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>应用所有者开始迁移过程

如果你是任何 LUIS 应用的所有者，则可以使用该迁移过程。

1. 登录 [LUIS 门户](https://luis.azure.cn)并同意使用条款。
1. 使用“迁移”弹出窗口可以继续迁移或稍后进行迁移。 选择“立即迁移”。 如果选择稍后迁移，那么你有 9 个月的时间可用于迁移到 Azure 中新的创作密钥。

    ![迁移过程中的第一个弹出窗口，选择“立即迁移”。](./media/migrate-authoring-key/migrate-now.png)

1. （可选）如果你的任何应用有协作者，则系统会提示你 **向他们发送一封电子邮件**，让他们了解迁移情况。 这是可选步骤。

    一旦你将帐户迁移到 Azure，你的应用将不再可供协作者使用。

    对于每个协作者和应用，会打开默认的电子邮件应用程序，显示一封格式简单的电子邮件。 可以在发送电子邮件之前对其进行编辑。

    电子邮件模板包含确切的应用 ID 和应用名称。

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. 选择创建 LUIS 创作资源，具体方法是选择使用现有的创作资源或创建新的创作资源。

    > [!div class="mx-imgBorder"]
    > ![创建创作资源](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. 在下一个窗口中，输入资源密钥信息。 输入信息后，选择“创建资源”。 每个区域的每个订阅可以有 10 个免费创作资源。

    ![创建创作资源](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **创建新的创作资源** 时，请提供以下信息：

    * **资源名称** - 你选择的自定义名称，用作创作和预测终结点查询的 URL 的一部分。
    * **租户** - 与 Azure 订阅关联的租户。
    * **订阅名称** - 将对资源计费的订阅。
    * **资源组** - 你选择或创建的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。
    * **位置** - 位置选项基于 **资源组** 选择。
    * **定价层** - 定价层确定每秒和每月的最大事务数。

1. 验证创作资源并 **立即迁移**。

    ![创建创作资源](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. 创建了创作资源后，会显示成功消息。 选择“关闭”以关闭弹出窗口。

    ![创作资源已成功创建。](./media/migrate-authoring-key/migration-success.png)

    “我的应用”列表显示了已迁移到新创作资源的应用。

    无需知道创作资源的密钥即可继续在 LUIS 门户中编辑应用。 如果你计划以编程方式编辑应用，那么你需要这些创作密钥值。 这些值显示在 LUIS 门户中的“管理”->“Azure 资源”页上，也可以在 Azure 门户中资源的“密钥”页上找到。

1. 在访问应用之前，请选择订阅和 LUIS 创作资源，以查看可以创作的应用。

    > [!div class="mx-imgBorder"]
    > ![选择订阅和 LUIS 创作资源，以查看可以创作的应用。](./media/create-app-in-portal-select-subscription-luis-resource.png)

## <a name="app-contributor-begins-the-migration-process"></a>应用参与者开始迁移过程

按照与应用所有者相同的步骤进行迁移。 此过程将创建一个 `LUIS.Authoring` 类型的新创作资源。

你需要迁移你的帐户，以便作为参与者添加到其他人拥有的已迁移应用。

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>迁移过程完成后，将参与者添加到创作资源

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

了解如何[添加参与者](luis-how-to-collaborate.md)。

## <a name="troubleshooting-errors-with-the-migration-process"></a>排查迁移过程中的错误

如果在迁移过程中从 LUIS 门户中显示的红色通知栏收到 `MissingSubscriptionRegistration` 错误，请在 [Azure 门户](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)或 [Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli) 中创建认知服务资源。 详细了解[造成此错误的原因](../../azure-resource-manager/templates/error-register-resource-provider.md#cause)。

## <a name="next-steps"></a>后续步骤


* 查看有关创作和运行时密钥的[概念](luis-concept-keys.md)
* 查看[如何分配密钥](luis-how-to-azure-subscription.md)并添加[参与者](luis-how-to-collaborate.md)
