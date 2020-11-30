---
author: rockboyfor
ms.service: virtual-machines-linux
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 768a6a5559201acaea21820acaa7074c4802fdfb
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301076"
---
## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](https://docs.azure.cn/zh-cn/cli/group#az-group-create)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“chinaeast”  位置创建名为“myResourceGroup”  的资源组。

```azurecli 
az group create --name myResourceGroup --location chinaeast
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](https://docs.azure.cn/zh-cn/cli/vm#az-vm-create) 命令创建 VM。 

下面的示例创建一个名为 *myVM* 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。 该命令还会将 *azureuser* 设置为管理员用户名。 稍后要使用此名称连接到 VM。 

```azurecli 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 后，Azure CLI 会显示类似于以下示例的信息。 记下 `publicIpAddress`。 在后续步骤中，将使用此地址访问 VM。

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "chinaeast",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80 

默认情况下，仅允许通过 SSH 连接登录到 Azure 中部署的 Linux VM。 由于此 VM 将用作 Web 服务器，因此需要从 Internet 打开端口 80。 使用 [az vm open-port](https://docs.azure.cn/zh-cn/cli/vm#az-vm-open-port) 命令打开所需端口。  

```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM

如果还不知道 VM 的公共 IP 地址，请运行 [az network public-ip list](https://docs.azure.cn/zh-cn/cli/network/public-ip#list) 命令。 在多个后续步骤中都需要用到此 IP 地址。

```azurecli
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

使用以下命令创建与虚拟机的 SSH 会话。 使用虚拟机的正确的公共 IP 地址进行替换。 在此示例中，IP 地址为 *40.68.254.142*。 *azureuser* 是创建 VM 时设置的管理员用户名。

```bash
ssh azureuser@40.68.254.142
```

<!-- Update_Description: update meta properties, wording update, update link -->