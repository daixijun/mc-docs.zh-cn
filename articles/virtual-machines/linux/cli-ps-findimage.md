---
title: 使用 Azure CLI 选择 Linux VM 映像
description: 了解如何使用 Azure CLI 确定发布服务器、产品/服务、SKU 和市场 VM 映像的版本。
author: Johnnytechn
ms.service: virtual-machines-linux
origin.date: 01/25/2019
ms.topic: how-to
ms.date: 01/05/2021
ms.author: v-johya
ms.openlocfilehash: 0d9fd862bce384c25e62d968ac5726a2bb8bc4e5
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022544"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>使用 Azure CLI 在 Azure 市场中查找 Linux VM 映像

本主题介绍如何使用 Azure CLI 在 Azure 市场中查找 VM 映像。 使用 CLI、资源管理器模板或其他工具以编程方式创建 VM 时，使用此信息指定市场映像。

还可以使用 [Azure 市场](https://market.azure.cn/marketplace/)店面、[Azure 门户](https://portal.azure.cn)或 [Azure PowerShell](../windows/cli-ps-findimage.md) 浏览可用的映像和产品/服务。 

<!--MOONCAKE: CORRECT FORMAT ON [Azure Marketplace](https://market.azure.cn/marketplace/)-->

请确保已安装最新版的 [Azure CLI](https://docs.azure.cn/cli/install-azure-cli?view=azure-cli-latest) 且已登录到 Azure 帐户 (`az login`)。

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="list-popular-images"></a>列出常用映像

运行 [az vm image list](https://docs.azure.cn/cli/vm/image?view=azure-cli-latest#az-vm-image-list) 命令，无需选择 `--all` 选项即可在 Azure 市场中查看常用 VM 映像的列表。 例如，运行以下命令以表格形式显示缓存的常用映像列表：

```azurecli
az vm image list --output table
```

输出包括映像 URN（Urn 列中的值）  。 使用其中一个常用市场映像创建 VM 时，可选择指定 *UrnAlias*（一种简短格式，如 *UbuntuLTS*）。

<!--AVAILABLE SUCCESSFULLY ON RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest-->

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

<!--AVAILABLE SUCCESSFULLY ON RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest-->

## <a name="find-specific-images"></a>查找特定映像

若要在市场中查找特定 VM 映像，请结合使用 `az vm image list` 命令和 `--all` 选项。 此版本命令完成需要一些时间，且会返回冗长输出，因此通常可按 `--publisher` 或其他参数筛选列表。 

例如，以下命令显示所有 Debian 产品/服务（请记住，不使用 `--all` 开关时，只搜索常见映像的本地缓存）：

```azurecli
az vm image list --offer Debian --all --output table 

```

部分输出： 

```
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

通过 `--location`、`--publisher` 和 `--sku` 选项应用类似的筛选器。 可以在筛选器上执行部分匹配，如搜索 `--offer Deb` 以查找所有 Debian 映像。

如果没有使用 `--location` 选项指定一个特定位置，则将返回默认位置的值。 （通过运行 `az configure --defaults location=<location>` 设置不同默认位置。）

例如，以下命令列出了“中国北部”位置中的所有 Debian 8 SKU：

```azurecli
az vm image list --location chinanorth --offer Deb --publisher credativ --sku 8 --all --output table
```

部分输出：

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>浏览映像

在某个位置查找映像的另一种方法是，运行序列中的 [az vm image list-publishers](https://docs.azure.cn/cli/vm/image?view=azure-cli-latest#az-vm-image-list-publishers)、[az vm image list-offers](https://docs.azure.cn/cli/vm/image?view=azure-cli-latest#az-vm-image-list-offers) 和 [az vm image list-skus](https://docs.azure.cn/cli/vm/image?view=azure-cli-latest#az-vm-image-list-skus) 命令。 可以使用这些命令确定以下值：

1. 列出映像发布者。
2. 对于给定的发布者，列出其产品。
3. 对于给定的产品，列出其 SKU。

然后，对于所选 SKU，可以选择要部署的版本。

例如，以下命令列出了中国北部位置的映像发布者：

```azurecli
az vm image list-publishers --location chinanorth --output table
```

部分输出：


<!--MOONCAKE CUSTOMIZE BELOW-->

```
Location    Name
----------  ----------------------------------------------------
chinanorth  360-cn
chinanorth  A10Networks
chinanorth  a10networks-cn
chinanorth  afajr-cn
chinanorth  aibaby
chinanorth  aigauss
chinanorth  airdoc
chinanorth  airdoc-cn
chinanorth  alauda
chinanorth  AllMobilize
chinanorth  alteonva
chinanorth  Antshares
chinanorth  arcblock-cn
chinanorth  arctron
chinanorth  array_networks
chinanorth  array_networks-cn
chinanorth  AsiaInfo.DeepSecurity
chinanorth  attittu-cn
chinanorth  AzureChinaMarketplace
chinanorth  AzureDatabricks
chinanorth  baison
chinanorth  BespinGlobal
chinanorth  beyondsoft
chinanorth  beyondsoft-cn
...
```

<!--MOONCAKE CUSTOMIZE ABOVE-->

使用此信息可以从特定发布者找到产品/服务。 例如，对于位于中国北部位置的 *Canonical* 发布者，请通过运行 `azure vm image list-offers` 查找产品/服务。 传递位置和发布者，如以下示例中所示：

```azurecli
az vm image list-offers --location chinanorth --publisher Canonical --output table
```

输出：

```
Location    Name
----------  -------------------------
chinanorth      Ubuntu15.04Snappy
chinanorth      Ubuntu15.04SnappyDocker
chinanorth      UbunturollingSnappy
chinanorth      UbuntuServer
chinanorth      Ubuntu_Core
```
可以看到，在中国北部区域，Canonical 在 Azure 上发布了 *UbuntuServer* 产品。 但是，有哪些 SKU 呢？ 要获取这些值，请运行 `azure vm image list-skus`，并对找到的位置、发布者和产品/服务进行设置：

```azurecli
az vm image list-skus --location chinanorth --publisher Canonical --offer UbuntuServer --output table
```

输出：

```
Location    Name
----------  -----------------
chinanorth  12.04.5-LTS
chinanorth  14.04.2-LTS
chinanorth  14.04.3-LTS
chinanorth  14.04.4-LTS
chinanorth  14.04.5-LTS
chinanorth  14.10
chinanorth  15.04
chinanorth  15.10
chinanorth  16.04-DAILY-LTS
chinanorth  16.04-LTS
chinanorth  16.04.0-LTS
chinanorth  16.10
chinanorth  16_04-daily-lts-gen2
chinanorth  16_04-lts-gen2
chinanorth  16_04_0-lts-gen2
chinanorth  17.04
chinanorth  17.10
chinanorth  18.04-DAILY-LTS
chinanorth  18.04-LTS
chinanorth  18.10
chinanorth  18_04-daily-lts-gen2
chinanorth  18_04-lts-gen2
chinanorth  19.04
chinanorth  19.04-DAILY
chinanorth  19.10-DAILY
chinanorth  19_04-daily-gen2
chinanorth  19_04-gen2
chinanorth  19_10-daily-gen2
```

最后，使用 `az vm image list` 命令查找所需的特定版本的 SKU，例如，18.04-LTS  ：

```azurecli
az vm image list --location chinanorth --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

部分输出：

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

现在，可通过记下 URN 值准确地选择想要使用的映像。 通过 [az vm create](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-create) 命令创建 VM 时，可将此值与 `--image` 参数一起传递。 记住，可选择将 URN 中的版本号替换为“latest”。 此版本始终是映像的最新版本。 

如果使用资源管理器模板部署 VM，请在 `imageReference` 属性中单独设置映像参数。

<!--Not Available on [template reference](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)-->

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>查看计划属性

若要查看映像的购买计划信息，请运行 [az vm image show](https://docs.azure.cn/cli/image?view=azure-cli-latest#az-vm-image-show) 命令。 如果输出中的 `plan` 属性不是 `null`，则映像有条款，在以编程方式部署前需要接受该条款。

例如，Canonical Ubuntu Server 18.04 LTS 映像没有附加条款，因为 `plan` 信息为 `null`：

```azurecli
az vm image show --location chinanorth --urn Canonical:UbuntuServer:18.04-LTS:latest
```

输出：

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/chinanorth/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "chinanorth",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

<!-- Not Available on Bitnami image on Mooncake -->
<!-- Not Available on Bitnami ### Accept the terms -->
<!-- Not Available on Bitnami ### Deploy using purchase plan parameters -->
## <a name="next-steps"></a>后续步骤
若要使用映像信息快速创建虚拟机，请参阅[使用 Azure CLI 创建和管理 Linux VM](tutorial-manage-vm.md)。

<!--Update_Description: update meta properties, wording update, update cmdlet -->