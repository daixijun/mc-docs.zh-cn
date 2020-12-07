---
title: 使用 Azure CLI 创建和加密 Linux VM
description: 本快速入门介绍如何使用 Azure CLI 创建和加密 Linux 虚拟机
author: Johnnytechn
ms.author: v-johya
ms.service: virtual-machines-linux
ms.subservice: security
origin.date: 05/17/2019
ms.topic: quickstart
ms.date: 12/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd5760aec1c89ed6a01512ac618ce9bf795e420d
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431683"
---
<!--Verfied successfully-->
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>快速入门：使用 Azure CLI 创建和加密 Linux VM

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门演示如何使用 Azure CLI 创建和加密 Linux 虚拟机 (VM)。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

如果选择在本地安装并使用 Azure CLI，本快速入门要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“chinanorth2”位置创建名为“myResourceGroup”的资源组：

```azurecli
az group create --name "myResourceGroup" --location "chinanorth2"
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 以下示例创建一个名为 myVM 的 VM。

```azurecli
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

创建 VM 和支持资源需要几分钟时间。 以下示例输出表明 VM 创建操作已成功。

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "chinanorth2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>创建为加密密钥配置的密钥保管库

Azure 磁盘加密将其加密密钥存储在 Azure 密钥保管库中。 使用 [az keyvault create](https://docs.azure.cn/cli/keyvault?view=azure-cli-latest#az-keyvault-create) 创建密钥保管库。 要使密钥保管库能够存储加密密钥，请使用 --enabled-for-disk-encryption 参数。

> [!Important]
> 每个密钥保管库必须有一个在 Azure 中唯一的名称。 在下面的示例中，将 <your-unique-keyvault-name> 替换为你选择的名称。

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "chinanorth2" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>加密虚拟机

使用 [az vm encryption](https://docs.azure.cn/cli/vm/encryption?view=azure-cli-latest#az-vm-encryption) 加密 VM，为 --disk-encryption-keyvault 参数提供唯一的密钥保管库名称。

```azurecli
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

稍后，进程将返回“加密请求已被接受。 请使用 'show' 命令监视进度”。 "show" 命令是 [az vm show](https://docs.azure.cn/cli/vm/encryption?view=azure-cli-latest#az-vm-encryption-show)。

```azurecli
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

启用加密后，你将在返回的输出中看到以下内容：

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和密钥保管库，可以使用 [az group delete](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-delete) 命令将其删除。 

```azurecli
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个虚拟机，创建了一个启用加密密钥的密钥保管库，并对 VM 进行了加密。  请继续学习下一篇文章，详细了解 Linux VM 的 Azure 磁盘加密。

> [!div class="nextstepaction"]
> [Azure 磁盘加密概述](disk-encryption-overview.md)

<!-- Update_Description: new article about disk encryption cli quickstart  -->
<!--NEW.date: 11/11/2019-->