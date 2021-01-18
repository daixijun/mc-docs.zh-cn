---
title: 在模板中使用条件
description: 了解如何根据条件部署 Azure 资源。 演示如何部署新资源或使用现有资源。
origin.date: 04/23/2020
author: rockboyfor
ms.date: 01/11/2021
ms.testscope: yes
ms.testdate: 08/24/2020
ms.topic: tutorial
ms.author: v-yeche
ms.openlocfilehash: 3303800367731e27258ebbfab721055b96410019
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022257"
---
<!--Verify sucessfully-->
# <a name="tutorial-use-condition-in-arm-templates"></a>教程：在 ARM 模板中使用条件

了解如何根据 Azure 资源管理器模板（ARM 模板）中的条件部署 Azure 资源。

[设置资源部署顺序](./template-tutorial-create-templates-with-dependent-resources.md)教程介绍如何创建虚拟机、虚拟网络以及其他一些依赖资源（包括存储帐户）。 无需每次都创建新的存储帐户，可让用户选择是创建新的存储帐户还是使用现有的存储帐户。 为实现此目的，需定义附加的参数。 如果参数值为“new”，则创建新存储帐户。 否则，将使用具有所提供名称的现有存储帐户。

:::image type="content" source="./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png" alt-text="资源管理器模板使用条件关系图":::

本教程涵盖以下任务：

> [!div class="checklist"]
> * 打开快速入门模板
> * 修改模板
> * 部署模板
> * 清理资源

本教程仅介绍使用条件的基本方案。 有关详细信息，请参阅：

* [模板文件结构：条件](conditional-resource-deployment.md)。
    
    <!--Not Available on * [Conditionally deploy a resource in an ARM template](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy)-->
    
* [模板函数：If](./template-functions-logical.md#if)。
* [ARM 模板的比较函数](./template-functions-comparison.md)

<!--Not Available on [Manage complex cloud deployments by using advanced ARM template features](https://docs.microsoft.com/learn/modules/manage-deployments-advanced-arm-template-features/)-->

如果没有 Azure 订阅，请在开始前[创建试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含资源管理器工具扩展的 Visual Studio Code。 请参阅[快速入门：使用 Visual Studio Code 创建 ARM 模板](quickstart-create-templates-use-visual-studio-code.md)。
* 若要提高安全性，请使用为虚拟机管理员帐户生成的密码。 以下是密码生成示例：

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault 旨在保护加密密钥和其他机密。 有关详细信息，请参阅[教程：在 ARM 模板部署中集成 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我们还建议你每三个月更新一次密码。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

Azure 快速入门模板是 ARM 模板的存储库。 无需从头开始创建模板，只需找到一个示例模板并对其自定义即可。 本教程中使用的模板称为[部署简单的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，选择“文件” > “打开文件”。 
1. 在“文件名”中粘贴以下 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. 选择“打开”以打开该文件。
1. 有六个通过此模板定义的资源：

    * `Microsoft.Storage/storageAccounts`.
    * `Microsoft.Network/publicIPAddresses`.
    * `Microsoft.Network/networkSecurityGroups`.
    * `Microsoft.Network/virtualNetworks`.
    * `Microsoft.Network/networkInterfaces`.
    * `Microsoft.Compute/virtualMachines`.
    
        <!-- Not Available on  [template reference](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)-->
        <!-- Not Available on  [template reference](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)-->
        <!-- Not Available on  [template reference](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)-->
        <!-- Not Available on  [template reference](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)-->
        <!-- Not Available on  [template reference](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)-->
        <!-- Not Available on  [template reference](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)-->
    
    在自定义模板之前查看模板参考会很有帮助。

1. 选择“文件” > “另存为”，将该文件的副本保存到名为 _azuredeploy.json_ 的本地计算机。 

## <a name="modify-the-template"></a>修改模板

对现有模板进行两项更改：

* 添加存储帐户名称参数。 用户可以指定新的存储帐户名称或现有的存储帐户名称。
* 添加名为 `newOrExisting` 的新参数。 部署使用此参数来确定是要创建新存储帐户还是使用现有的存储帐户。

下面是进行更改的过程：

1. 在 Visual Studio Code 中打开 _azuredeploy.json_。
1. 在整个模板中，将三个 `variables('storageAccountName')` 替换为 `parameters('storageAccountName')` 。
1. 删除以下变量定义：

    :::image type="content" source="./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png" alt-text="突出显示需要删除的变量定义的屏幕截图。":::

1. 将以下两个参数添加到 parameters 节的开头：

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    在 Visual Studio Code 中按“`Alt+Shift+F`”，设置模板格式。

    更新的参数定义如下所示：

    :::image type="content" source="./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png" alt-text="在资源管理器中使用条件":::

1. 将以下行添加到存储帐户定义的开头。

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    条件会检查参数 `newOrExisting` 的值。 如果参数值为 **new**，则部署将创建存储帐户。

    更新的存储帐户定义如下所示：

    :::image type="content" source="./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png" alt-text="显示更新的存储帐户定义的屏幕截图。":::
1. 使用以下值更新虚拟机资源定义的 `storageUri` 属性：

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.chinacloudapi.cn')]"
    ```

    如果使用另一资源组中的现有存储帐户，则此更改是必需的。

1. 保存更改。

## <a name="deploy-the-template"></a>部署模板

<!--Not Available on https://shell.azure.com-->

1. 运行以下 PowerShell 脚本以部署我们在上一步中保存的模板。

    > [!IMPORTANT]
    > 存储帐户名称在 Azure 中必须是唯一的。 该名称只能包含小写字母或数字。 其长度不能超过 24 个字符。 存储帐户名称是追加了“store”的项目名称。 请确保项目名称和生成的存储帐户名称符合存储帐户名称要求。

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. chinaeast)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > 如果 `newOrExisting` 为 **new**，但具有指定存储帐户名称的存储帐户已存在，则部署将会失败。

通过将 `newOrExisting` 设置为“existing”并指定现有存储帐户来尝试进行另一个部署。 若要提前创建存储帐户，请参阅[创建存储帐户](../../storage/common/storage-account-create.md)。

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。 

<!--Not Available on Cloud Shell-->
<!--Not Available on To delete the resource group, select **Try it** to open the local Shell. To paste the PowerShell script, right-click the shell pane, and then select **Paste**.-->

```powershell
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

在本教程中，我们开发了一个允许用户选择创建新存储帐户或使用现有存储帐户的模板。 若要了解如何从 Azure Key Vault 检索机密并在模板部署中使用这些机密作为密码，请参阅：

> [!div class="nextstepaction"]
> [在模板部署中集成 Key Vault](./template-tutorial-use-key-vault.md)

<!-- Update_Description: update meta properties, wording update, update link -->
