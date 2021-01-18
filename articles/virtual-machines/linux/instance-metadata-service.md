---
title: 适用于 Linux 的 Azure 实例元数据服务
description: 了解 Azure 实例元数据服务及其如何提供有关当前正在 Linux 中运行的虚拟机实例的信息。
services: virtual-machines
author: Johnnytechn
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/05/2021
ms.author: v-johya
ms.reviewer: azmetadatadev
ms.openlocfilehash: a79e83f4e6f7b525aea695aaac5aa20350aa16f6
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022538"
---
# <a name="azure-instance-metadata-service-imds"></a>Azure 实例元数据服务 (IMDS)

Azure 实例元数据服务 (IMDS) 提供有关当前正在运行的虚拟机实例的信息。 可以使用它来管理和配置虚拟机。
这些信息包括 SKU、存储、网络配置和即将发生的维护事件。 有关可用的数据的完整列表，请参阅[元数据 API](#metadata-apis)。

IMDS 适用于运行虚拟机 (VM) 的实例和虚拟机规模集实例。 所有 API 均支持使用 [Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/)创建和管理的 VM。 只有经过证明的终结点和网络终结点支持通过经典部署模型创建的 VM。 经过证明的终结点仅在有限范围内提供这样的支持。

IMDS 是一个 REST 终结点，可在已知的不可路由的 IP 地址 (`169.254.169.254`) 中可用。 只能从 VM 中访问该终结点。 VM 与 IMDS 之间的通信绝不会离开主机。
让 HTTP 客户端在查询 IMDS 时绕过 VM 中的 Web 代理并同等对待 `169.254.169.254` 和 [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md)。

## <a name="security"></a>安全性

IMDS 终结点只能从不可路由的 IP 地址上正在运行的虚拟机实例中访问。 此外，任何包含 `X-Forwarded-For` 标头的请求都会被服务拒绝。
请求还必须包含 `Metadata: true` 标头，以确保实际请求是本来的意图，而不是属于无意重定向的。

> [!IMPORTANT]
> IMDS 不是敏感数据的通道。 该终结点面向 VM 上的所有进程开放。 应将通过此服务公开的信息视为与 VM 内运行的所有应用程序共享的信息。

## <a name="usage"></a>使用情况

### <a name="access-azure-instance-metadata-service"></a>访问 Azure 实例元数据服务

若要访问 IMDS，请从 [Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/)或 [Azure 门户](https://portal.azure.cn)创建一个 VM，并使用以下示例。
如需更多示例，请参阅 [Azure 实例元数据示例](https://github.com/microsoft/azureimds)。

下面是用于检索实例的所有元数据的示例代码。 若要访问特定的数据源，请参阅[元数据 API](#metadata-apis) 部分。 

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
    "compute": {
        "azEnvironment": "AzureChinaCloud",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "chinanorth",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```
<!-- Notice: Zone is not Available on Moocake, so the value is "" -->

### <a name="data-output"></a>数据输出

默认情况下，IMDS 以 JSON 格式返回数据 (`Content-Type: application/json`)。 但是，某些 API 可根据请求返回不同格式的数据。
下表列出了 API 可能支持的其他数据格式。

API | 默认数据格式 | 其他格式
--------|---------------------|--------------
/attested | json | 无
/identity | json | 无
/instance | json | text
/scheduledevents | json | 无

若要访问非默认响应格式，请在请求中将所请求的格式指定为查询字符串参数。 例如：

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> 对于 `/metadata/instance` 中的叶节点，`format=json` 不起作用。 对于这些查询，需要显式指定 `format=text`，因为默认格式为 JSON。

### <a name="version"></a>版本

IMDS 进行了版本控制，必须在 HTTP 请求中指定 API 版本。

支持的 API 版本有： 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> 版本 2020-10-01 不一定在每个区域中都可用。

在添加更新的版本时，你仍可访问早期版本以保持兼容性（如果脚本依赖于特定的数据格式）。

如果不指定版本，则会收到错误消息，其中会列出受支持的最新版本。

> [!NOTE]
> 此响应是 JSON 字符串。 下面的示例展示未指定版本时的错误情况。 响应显示清晰，可供阅读。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
```

**响应**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>元数据 API

IMDS 包含多个 API（表示不同数据源）。

API | 说明 | 引入的版本
----|-------------|-----------------------
/attested | 请参阅[证明数据](#attested-data) | 2018-10-01
/identity | 请参阅[获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | 请参阅[实例 API](#instance-api) | 2017-04-02
/scheduledevents | 请参阅[计划事件](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>实例 API

实例 API 公开 VM 实例的重要元数据，其中包括 VM、网络和存储。 可以通过 `instance/compute` 访问以下类别：

数据 | 说明 | 引入的版本
-----|-------------|-----------------------
azEnvironment | 正在运行 VM 的 Azure 环境。 | 2018-10-01
customData | 此功能目前已禁用。 | 2019-02-01
isHostCompatibilityLayerVm | 标识 VM 是否在主机兼容性层上运行。 | 2020-06-01
LicenseType | [Azure 混合权益](https://www.azure.cn/pricing/hybrid-benefit)许可证的类型。 请注意，这仅适用于启用了 AHB 的 VM。 | 2020-09-01
location | 正在运行 VM 的 Azure 区域。 | 2017-04-02
name | VM 的名称。 | 2017-04-02
offer | 为 VM 映像提供信息。 这仅适用于从 Azure 映像库中部署的映像。 | 2017-04-02
osProfile.adminUsername | 指定管理员帐户的名称。 | 2020-07-15
osProfile.computerName | 指定计算机的名称。 | 2020-07-15
osProfile.disablePasswordAuthentication | 指定是否禁用密码身份验证。 请注意，这仅适用于 Linux VM。 | 2020-10-01
osType | Linux 或 Windows。 | 2017-04-02
placementGroupId | 虚拟机规模集的[放置组](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)。 | 2017-08-01
计划 | 包含 VM 的名称、产品和发布者（如果是 Azure 市场映像）的[计划](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan)。 | 2018-04-02
platformUpdateDomain |  正在运行 VM 的[更新域](../manage-availability.md)。 | 2017-04-02
platformFaultDomain | 正在运行 VM 的[容错域](../manage-availability.md)。 | 2017-04-02
provider | VM 的提供商。 | 2018-10-01
publicKeys | 分配给 VM 的[公钥的集合](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey)和路径。 | 2018-04-02
publisher | VM 映像的发布者。 | 2017-04-02
resourceGroupName | VM 的[资源组](../../azure-resource-manager/management/overview.md)。 | 2017-08-01
ResourceId | 资源的[完全限定](https://docs.microsoft.com/rest/api/resources/resources/getbyid) ID。 | 2019-03-11
sku | VM 映像的特定 SKU。 | 2017-04-02
securityProfile.secureBootEnabled | 标识是否在 VM 上启用了 UEFI 安全启动。 | 2020-06-01
securityProfile.virtualTpmEnabled | 标识是否在 VM 上启用了虚拟的受信任的平台模块 (TPM)。 | 2020-06-01
storageProfile | 请参阅[存储配置文件](#storage-metadata)。 | 2019-06-01
subscriptionId | VM 的 Azure 订阅。 | 2017-08-01
标记 | VM 的[标记](../../azure-resource-manager/management/tag-resources.md)。  | 2017-08-01
tagsList | 格式化为 JSON 数组以方便编程分析的标记。  | 2019-06-04
版本 | VM 映像的版本。 | 2017-04-02
vmId | VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)。 | 2017-04-02
vmScaleSetName | 虚拟机规模集的[虚拟机规模集名称](../../virtual-machine-scale-sets/overview.md)。 | 2017-12-01
vmSize | 请参阅 [VM 大小](../sizes.md)。 | 2017-04-02

### <a name="sample-1-track-a-vm-running-on-azure"></a>示例 1：跟踪在 Azure 上运行的 VM

作为服务提供商，你可能需要跟踪运行软件的 VM 数目，或者你的代理需要跟踪 VM 的唯一性。 为了能够获取 VM 的唯一 ID，请使用 IMDS 中的 `vmId` 字段。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**响应**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>示例 2：不同数据副本的放置

对于某些方案，不同数据副本的放置至关重要。 例如，对于 [HDFS 副本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)或者对于通过[业务流程协调程序](https://kubernetes.io/docs/user-guide/node-selection/)进行的容器放置，可能需要知道正在运行 VM 的 `platformFaultDomain` 和 `platformUpdateDomain`。
可以通过 IMDS 直接查询此数据。
<!-- Not Available on  [Availability Zones](../../availability-zones/az-overview.md)-->

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**响应**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>示例 3：在支持案例期间获取有关 VM 的详细信息

作为服务提供商，你可能会接到支持电话，了解有关 VM 的详细信息。 在这种情况下，一个有用做法是要求客户共享计算元数据。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
    "azEnvironment": "AzureChinaCloud",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "chinanorth",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>示例 4：获取运行 VM 的 Azure 环境

Azure 具有各种主权云，如 Azure 中国云。 有时你需要使用 Azure 环境来做出一些运行时决策。 以下示例显示了如何实现此行为。

<!--MOONCAKE: Correct on Azure China Cloud-->
**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**响应**

```text
AzureChinaCloud
```

此处列出了 Azure 环境的云和值。

 云   | Azure 环境
---------|-----------------
[全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure 美国政府版](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure 中国世纪互联](https://www.azure.cn/)         | AzureChinaCloud
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud
<!--Correct in MC: Azure US Government, Azure China 21Vianet-->

## <a name="network-metadata"></a>网络元数据 

网络元数据是实例 API 的一部分。 可以通过 `instance/network` 终结点使用以下网络类别。

数据 | 说明 | 引入的版本
-----|-------------|-----------------------
ipv4/privateIpAddress | VM 的本地 IPv4 地址。 | 2017-04-02
ipv4/publicIpAddress | VM 的公共 IPv4 地址。 | 2017-04-02
subnet/address | VM 的子网地址。 | 2017-04-02
subnet/prefix | 子网前缀。 示例：24 | 2017-04-02
ipv6/ipAddress | VM 的本地 IPv6 地址。 | 2017-04-02
macAddress | VM Mac 地址。 | 2017-04-02

> [!NOTE]
> 所有 API 响应都是 JSON 字符串。 以下所有示例响应显示清晰，可供阅读。

#### <a name="sample-1-retrieve-network-information"></a>示例 1：检索网络信息

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**响应**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieve-public-ip-address"></a>示例 2：检索公共 IP 地址

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>存储元数据

存储元数据是实例 API 的一部分，位于 `instance/compute/storageProfile` 终结点下。
它提供与 VM 关联的存储磁盘的详细信息。 

VM 的存储配置文件分为三个类别：映像引用、操作系统磁盘和数据磁盘。

映像引用对象包含有关操作系统映像的以下信息：

数据    | 说明
--------|-----------------
id      | 资源 ID
offer   | 平台或映像的套餐
publisher | 映像发布者
sku     | 映像 SKU
版本 | 平台或映像的版本

操作系统磁盘对象包含有关 VM 所用操作系统磁盘的以下信息：

数据    | 说明
--------|-----------------
caching | 缓存要求
createOption | 有关 VM 创建方式的信息
diffDiskSettings | 临时磁盘设置
diskSizeGB | 磁盘大小 (GB)
image   | 源用户映像虚拟硬盘
lun     | 磁盘的逻辑单元号
managedDisk | 托管磁盘参数
name    | 磁盘名称
vhd     | 虚拟硬盘
writeAcceleratorEnabled | 磁盘上是否启用了 `writeAccelerator`

数据磁盘阵列包含附加到 VM 的数据磁盘列表。 每个数据磁盘对象包含以下信息：

数据    | 说明
--------|-----------------
caching | 缓存要求
createOption | 有关 VM 创建方式的信息
diffDiskSettings | 临时磁盘设置
diskSizeGB | 磁盘大小 (GB)
encryptionSettings | 磁盘的加密设置
image   | 源用户映像虚拟硬盘
managedDisk | 托管磁盘参数
name    | 磁盘名称
osType  | 磁盘中包含的操作系统类型
vhd     | 虚拟硬盘
writeAcceleratorEnabled | 磁盘上是否启用了 `writeAccelerator`

以下示例演示如何查询 VM 的存储信息。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>VM 标记

VM 标记包含在实例 API 中，位于 `instance/compute/tags` 终结点下。
系统可能已将标记应用到 Azure VM，以便按逻辑方式将其组织成分类。 可以使用以下请求来检索分配给 VM 的标记。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**响应**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` 字段是带有用分号分隔的标记的字符串。 如果标记本身使用了分号，则此输出可能会出现问题。 如果编写分析程序以编程方式提取标记，则应该依赖于 `tagsList` 字段。 `tagsList` 字段是不带分隔符的 JSON 数组，因此其更易于分析。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
```

**响应**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>证明数据

IMDS 可帮助确保提供的数据来自 Azure。 Microsoft 会对此信息的一部分进行签名，以便你可以确认 Azure 市场中的映像是你正在 Azure 上运行的映像。

### <a name="sample-1-get-attested-data"></a>示例 1：获取经过证明的数据

> [!NOTE]
> 所有 API 响应都是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

`Api-version` 是必填字段。 有关支持的 API 版本的信息，请参阅[使用部分](#usage)。
`Nonce` 是一个可选的 10 位字符串。 如果未提供，IMDS 会返回其所在位置当前的协调世界时时间戳。

> [!NOTE]
> 由于 IMDS 的缓存机制，可能会返回以前缓存的 `nonce` 值。

**响应**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

签名 Blob 是文档的 [pkcs7](https://aka.ms/pkcs7) 签名版本。 它包含用于签名的证书以及某些特定于 VM 的详细信息。 

对于使用 Azure 资源管理器创建的 VM，这包括 `vmId`、`sku`、`nonce`、`subscriptionId`、文档创建和到期的 `timeStamp` 以及关于映像的计划信息。 该计划信息只针对 Azure 市场映像进行填充。 

对于使用经典部署模型创建的 VM，仅保证填充 `vmId`。 可以从响应中提取证书，并使用它来确认响应是否有效并来自 Azure。

该文档包含以下字段：

数据 | 说明 | 引入的版本
-----|-------------|-----------------------
LicenseType | [Azure 混合权益](https://www.azure.cn/pricing/hybrid-benefit)许可证的类型。 请注意，这仅适用于启用了 AHB 的 VM。 | 2020-09-01
nonce | 可以随请求提供的可选字符串。 如果未提供 `nonce`，则会使用当前的协调世界时时间戳。 | 2018-10-01
plan | [Azure 市场映像计划](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan)。 包含计划 ID（名称）、产品映像或产品/服务（产品）和发布者 ID（发布者）。 | 2018-10-01
timestamp/createdOn | 创建签名文档时的协调世界时时间戳。 | 2018-20-01
timestamp/expiresOn | 签名文档过期时的协调世界时时间戳。 | 2018-10-01
vmId |  VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)。 | 2018-10-01
subscriptionId | VM 的 Azure 订阅。 | 2019-04-30
sku | VM 映像的特定 SKU。 | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>示例 2：验证 VM 是否在 Azure 中运行

Azure 市场供应商希望确保其软件仅获许在 Azure 中运行。 如果有人将 VHD 复制到本地环境，则供应商需要能够检测到此操作。 通过 IMDS，这些供应商可以获取能确保只从 Azure 响应的签名数据。

> [!NOTE]
> 此示例需要安装 jq 实用工具。

**请求**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**响应**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

验证签名是否来自 Azure 并检查证书链中是否存在错误。

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

> [!NOTE]
> 由于 IMDS 的缓存机制，可能会返回以前缓存的 `nonce` 值。

如果在初始请求中提供了 `nonce` 参数，则可以比较签名文档中的 `nonce`。

> [!NOTE]
> 公有云和每个主权云的证书将有所不同。

云 | 证书
------|------------
[全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure 美国政府版](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure 中国世纪互联](https://www.azure.cn/)     | *.metadata.azure.cn
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> 对于公有云，证书可能不完全匹配 `metadata.azure.com`。 出于此原因，证书验证应允许任何 `.metadata.azure.com` 子域中的公用名称。

如果由于验证期间出现网络限制，导致中间证书无法下载，可以固定中间证书。 请注意，Azure 会滚动更新证书，这是标准 PKI 做法。 发生滚动更新时，需要更新固定证书。 每当规划某项更改来更新中间证书时，就会更新 Azure 博客并向 Azure 客户发出通知。 

可在 [PKI 存储库](https://www.microsoft.com/pki/mscorp/cps/default.htm)中找到中间证书。 每个区域的中间证书可能不同。

> [!NOTE]
> Azure 中国世纪互联的中间证书将来自 DigiCert 全局根 CA（而不是 Baltimore）。
如果在更改根证书链颁发机构的过程中已固定 Azure 中国的中间证书，则必须更新中间证书。

## <a name="managed-identity"></a>托管标识

可以在 VM 上启用由系统分配的托管标识。 还可以将一个或多个用户分配的托管标识分配给 VM。
然后，可以从 IMDS 请求托管标识的令牌。 使用这些令牌来通过其他 Azure 服务（如 Azure Key Vault）进行身份验证。

有关启用此功能的详细步骤，请参阅[获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="scheduled-events"></a>计划事件
可以使用 IMDS 获取计划事件的状态。 然后，用户可以指定一组在发生这些事件时要运行的操作。 有关详细信息，请参阅[计划事件](scheduled-events.md)。

## <a name="regional-availability"></a>区域可用性

此服务在所有 Azure 云中正式发布。

## <a name="sample-code-in-different-languages"></a>不同语言的示例代码

下表列出了在 VM 中使用不同语言调用 IMDS 的相关示例：

语言      | 示例
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>错误和调试

如果找不到某个数据元素，或者请求的格式不正确，则实例元数据服务返回标准 HTTP 错误。 例如：

HTTP 状态代码 | 原因
-----------------|-------
200 正常 |
400 错误的请求 | 查询叶节点时缺少 `Metadata: true` 标头或缺少参数 `format=json`。
404 未找到  | 请求的元素不存在。
不允许 405 方法 | 仅支持 `GET` 请求。
410 不存在 | 稍后重试，最多等待 70 秒。
429 请求次数过多 | 该 API 当前支持每秒最多 5 个查询。
500 服务错误     | 请稍后重试。

### <a name="frequently-asked-questions"></a>常见问题

我收到错误 `400 Bad Request, Required metadata header not specified`。这是什么意思呢？

IMDS 需要在请求中传递标头 `Metadata: true`。 将该标头传入 REST 调用将允许访问 IMDS。

**为什么我无法获取我的 VM 的计算信息？**

当前 IMDS 仅支持 Azure 资源管理器创建的实例。

前段时间，我通过 Azure 资源管理器创建了 VM。为什么我看不到计算元数据信息？

如果在 2016 年 9 月之后创建了 VM，请添加[标记](../../azure-resource-manager/management/tag-resources.md)以开始查看计算元数据。 如果在 2016 年 9 月之前创建了 VM，请在 VM 实例中添加或删除扩展或数据磁盘以刷新元数据。

为什么我看不到为新版本填充的任何数据？

如果在 2016 年 9 月之后创建了 VM，请添加[标记](../../azure-resource-manager/management/tag-resources.md)以开始查看计算元数据。 如果在 2016 年 9 月之前创建了 VM，请在 VM 实例中添加或删除扩展或数据磁盘以刷新元数据。

我为什么会收到错误 `500 Internal Server Error` 或 `410 Resource Gone`？

请重试请求。 有关详细信息，请参阅[临时故障处理](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults)。 如果问题持续存在，请在 Azure 门户中为 VM 创建支持问题。

这是否适用于虚拟机规模集实例？

是的，IMDS 适用于虚拟机规模集实例。

我在虚拟机规模集中更新了我的标记（与单实例 VM 不同），这些标记未出现在实例中。这是怎么回事？

目前，虚拟机规模集的标记仅在重启、重置映像或更改实例的磁盘时向 VM 显示。

为什么调用服务时请求超时？

必须从分配给 VM 的主要网卡的主 IP 地址进行元数据调用。 此外，如果你更改了路由，则 VM 的本地路由表中必须存在 169.254.169.254/32 地址的路由。

   * <details>
        <summary>验证路由表</summary>

        1. 转储本地路由表并查找 IMDS 条目。 例如：
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. 验证是否存在 `169.254.169.254` 的路由，并记下相应的网络接口（例如 `172.16.69.7`）。
        1. 转储接口配置并查找与路由表中引用的接口相对应的接口，注明 MAC（物理）地址。
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.chinacloudapp.cn
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. 确认该接口对应于 VM 的主 NIC 和主 IP。 可以通过在 Azure 门户中查看网络配置，或通过 Azure CLI 查找它来找到主 NIC 和 IP。 记下公共和专用 IP（如果使用 CLI，还要记下 MAC 地址）。 下面是一个 PowerShell CLI 示例：
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. 如果它们不匹配，请更新路由表，以使主 NIC 和 IP 成为目标。
    </details>
    <!-- Not Available on New Support Request on Mooncake ![Instance Metadata Support](./media/instance-metadata-service/InstanceMetadata-support.png)-->

## <a name="next-steps"></a>后续步骤

[获取 VM 的访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[计划事件](scheduled-events.md)

<!--Update_Description: update meta properties, wording update -->
