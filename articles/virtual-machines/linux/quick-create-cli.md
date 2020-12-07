---
title: 快速入门：使用 Azure CLI 创建 Linux VM
description: 本文快速入门介绍了如何使用 Azure CLI 创建 Linux 虚拟机
author: Johnnytechn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2020
ms.author: v-johya
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
ms.openlocfilehash: 9b7f93ec556ace76b27a386d67f4acbd89a3aacb
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431632"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>快速入门：使用 Azure CLI 创建 Linux 虚拟机

本快速入门向你展示了如何使用 Azure 命令行接口 (CLI) 在 Azure 中部署 Linux 虚拟机 (VM)。 Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。

在本教程中，我们将安装 Ubuntu 16.04 LTS。 为了显示运转中的 VM，我们将使用 SSH 连接到它并安装 NGINX Web 服务器。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="launch-azure-local-shell"></a>启动 Azure 本地 Shell
如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI version 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli?view=azure-cli-latest)。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“chinaeast”  位置创建名为“myResourceGroup”  的资源组：

```azurecli
az group create --name myResourceGroup --location chinaeast
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-create) 命令创建 VM。

以下示例创建一个名为 *myVM* 的 VM 并添加一个名为 *azureuser* 的用户帐户。 `--generate-ssh-keys` 参数用来自动生成一个 SSH 密钥，并将其放置在默认密钥位置 ( *~/.ssh*) 中。 若要改为使用一组特定的密钥，请使用 `--ssh-key-value` 选项。

```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

创建 VM 和支持资源需要几分钟时间。 以下示例输出表明 VM 创建操作已成功。

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "chinaeast",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

记下 VM 输出中自己的 `publicIpAddress`。 在后续步骤中，将使用此地址访问 VM。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

默认情况下，在 Azure 中创建 Linux VM 时仅打开 SSH 连接。 使用 [az vm open-port](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-open-port) 打开 TCP 端口 80 以供 NGINX Web 服务器使用：

```azurecli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

通过 SSH 照常连接到 VM。 将 publicIpAddress  替换为 VM 的公共 IP 地址（在 VM 的上一输出中记下）：

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>安装 Web 服务器

若要查看运行中的 VM，请安装 NGINX Web 服务器。 更新程序包来源，然后安装最新的 NGINX 程序包。

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

完成后，键入 `exit` 以离开 SSH 会话。

## <a name="view-the-web-server-in-action"></a>查看运行中的 Web 服务器

使用所选的 Web 浏览器查看默认的 NGINX 欢迎页。 使用你的 VM 的公共 IP 地址作为 Web 地址。 以下示例演示了默认 NGINX 网站：

![查看 NGINX 欢迎页](./media/quick-create-cli/view-the-nginx-welcome-page.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [az group delete](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-delete) 命令将其删除。 

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了简单的虚拟机，打开了 Web 流量的网络端口，并安装了一个基本 Web 服务器。 若要详细了解 Azure 虚拟机，请继续学习 Linux VM 的教程。


> [!div class="nextstepaction"]
> [Azure Linux 虚拟机教程](./tutorial-manage-vm.md)

<!--Update_Description: update meta properties, wording update -->