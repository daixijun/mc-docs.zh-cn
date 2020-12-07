---
title: 创建并上传 OpenBSD 映像
description: 了解如何创建和上传包含 OpenBSD 操作系统的虚拟硬盘 (VHD)，以便通过 Azure CLI 创建 Azure 虚拟机
author: Johnnytechn
ms.service: virtual-machines-linux
origin.date: 05/24/2017
ms.topic: how-to
ms.date: 12/01/2020
ms.author: v-johya
ms.openlocfilehash: e37565a4f5cbfba7647389c59503c9d9901d6b73
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432447"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>创建 OpenBSD 磁盘映像并将其上传到 Azure
本文介绍如何创建和上传包含 OpenBSD 操作系统的虚拟硬盘 (VHD)。 上传后，可将其用作自己的映像，通过 Azure CLI 在 Azure 中创建虚拟机 (VM)。


## <a name="prerequisites"></a>先决条件
本文假定你拥有以下项目：

* Azure 订阅  - 如果没有帐户，只需几分钟即可创建一个。 如果有 MSDN 订阅，请参阅 [Visual Studio 订户的每月 Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 否则，请了解如何[创建试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。  
* Azure CLI - 确保已安装了最新的 [Azure CLI](https://docs.azure.cn/cli/install-azure-cli?view=azure-cli-latest) 并已使用 [az login](https://docs.azure.cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户  。
* **安装在 .vhd 文件中的 OpenBSD 操作系统** - 必须将受支持的 OpenBSD 操作系统（[6.6 版 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)）安装到虚拟硬盘中。 可使用多种工具创建 .vhd 文件。 例如，可使用 Hyper-V 等虚拟化解决方案创建 .vhd 文件并安装操作系统。 有关如何安装和使用 Hyper-V 的说明，请参阅[安装 Hyper-V 并创建虚拟机](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))。


## <a name="prepare-openbsd-image-for-azure"></a>为 Azure 准备 OpenBSD 映像
在安装了 OpenBSD 操作系统 6.1（已添加 Hyper-V 支持）的 VM上，完成以下步骤：

1. 如果安装期间未启用 DHCP，请启用该服务，如下所示：

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. 设置串行控制台，如下所示：

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. 配置包安装，如下所示：

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. 默认情况下，禁止在 Azure 中的虚拟机上使用 `root` 用户。 用户可以对 OpenBSD VM 使用 `doas` 命令，通过提升的权限运行命令。 默认启用 Doas。 有关详细信息，请参阅 [doas.conf](https://man.openbsd.org/doas.conf.5)。 

5. 安装并配置 Azure 代理的先决条件，如下所示：

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. 始终可以在 [GitHub](https://github.com/Azure/WALinuxAgent/releases) 上找到 Azure 代理的最新版本。 安装代理，如下所示：

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > 安装 Azure 代理后，最好验证它是否正在运行，如下所示：
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. 取消设置系统可清除系统并使其适用于重新设置。 以下命令还会删除上次预配的用户帐户和关联数据：

    ```sh
    waagent -deprovision+user -force
    ```

现在可以关闭 VM。


## <a name="prepare-the-vhd"></a>准备 VHD
Azure 不支持 VHDX 格式，仅支持 **固定大小的 VHD**。 可使用 Hyper-V 管理器或 Powershell [convert-vhd](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd?view=win10-ps) cmdlet 将磁盘转换为固定 VHD 格式。 示例如下。

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>创建存储资源并上传
首先，使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) 创建资源组。 以下示例在“chinaeast”  位置创建名为“myResourceGroup”  的资源组：

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

```azurecli
az group create --name myResourceGroup --location chinaeast
```

若要上传 VHD，请使用 [az storage account create](https://docs.azure.cn/cli/storage/account?view=azure-cli-latest#az-storage-account-create) 创建存储帐户。 存储帐户名称必须唯一，因此，请提供自己的名称。 以下示例创建一个名为 mystorageaccount  的存储帐户：

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location chinaeast \
    --sku Premium_LRS
```

若要控制对存储帐户的访问，请按如下所示，使用 [az storage account key list](https://docs.azure.cn/cli/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) 获取存储密钥：

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

若要从逻辑上划分上传的 VHD，请使用 [az storage container create](https://docs.azure.cn/cli/storage/container?view=azure-cli-latest#az-storage-container-create) 在存储帐户内创建容器：

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

最后，请使用 [az storage blob upload](https://docs.azure.cn/cli/storage/blob?view=azure-cli-latest#az-storage-blob-upload) 上传 VHD，如下所示：

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>从 VHD 创建 VM
可使用[示例脚本](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md)或直接使用 [az vm create](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 若要指定上传的 OpenBSD VHD，请使用 `--image` 参数，如下所示：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.chinacloudapi.cn/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

请使用 [az vm list-ip-addresses](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-list-ip-addresses) 获取 OpenBSD VM 的 IP 地址，如下所示：

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

现在，可如常使用 SSH 连接到 OpenBSD VM：
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>后续步骤
若要深入了解 OpenBSD6.1 上的 Hyper-V 支持，请阅读 [OpenBSD 6.1](https://www.openbsd.org/61.html) 和 [hyperv.4](https://man.openbsd.org/hyperv.4)。

若要从托管磁盘创建 VM，请阅读 [az disk](https://docs.azure.cn/cli/disk?view=azure-cli-latest#az-disk)。

<!--Update_Description: update meta properties -->
