---
title: 使用 Azure CLI 在 Linux VM 上安装 MongoDB
description: 了解如何使用 Azure CLI 在 Linux 虚拟机上安装和配置 MongoDB
author: Johnnytechn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/05/2021
ms.author: v-johya
ms.custom: devx-track-azurecli
origin.date: 12/15/2017
ms.openlocfilehash: 733a55ea85c763f0daf05adf91fc4e906b5a2d2b
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023195"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>如何在 Linux VM 上安装和配置 MongoDB

[MongoDB](https://www.mongodb.org) 是一个流行的开源、高性能 NoSQL 数据库。 本文介绍如何使用 Azure CLI 在 Linux VM 上安装和配置 MongoDB。 文中提供了一些示例，详细说明如何执行以下操作：

* [手动安装和配置基本 MongoDB 实例](#manually-install-and-configure-mongodb-on-a-vm)
* [使用 Resource Manager 模板创建基本 MongoDB 实例](#create-basic-mongodb-instance-on-centos-using-a-template)
* [使用 Resource Manager 模板创建包含副本集的复杂 MongoDB 分片群集](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>在 VM 上手动安装和配置 MongoDB
MongoDB 为 CentOS、SUSE、Ubuntu 和 Debian 等 Linux 发行版[提供安装说明](https://docs.mongodb.com/manual/administration/install-on-linux/)。 以下示例创建 CentOS VM。 若要创建此环境，需要安装最新的 [Azure CLI](https://docs.azure.cn/cli/install-az-cli2?view=azure-cli-latest)，并使用 [az login](https://docs.azure.cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户。

<!-- Not Avaiable on Red Hat -->

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) 创建资源组。 以下示例在“chinaeast”  位置创建名为“myResourceGroup”  的资源组：

```azurecli
az group create --name myResourceGroup --location chinaeast
```

使用 [az vm create](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 以下示例通过名为 *azureuser* 的用户使用 SSH 公钥身份验证创建名为 *myVM* 的 VM

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用你自己的用户名和上一步骤中的输出中列出的 `publicIpAddress` 以 SSH 方式登录到 VM：

```bash
ssh azureuser@<publicIpAddress>
```

若要为 MongoDB 添加安装源，请按如下所示创建一个 yum 存储库文件：

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

打开该 MongoDB 存储库文件进行编辑，例如使用 `vi` 或 `nano`。 添加以下行：

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

按如下所示使用 yum 安装 MongoDB：

```bash
sudo yum install -y mongodb-org
```

默认情况下，在阻止访问 MongoDB 的 CentOS 映像中强制实施 SELinux。 安装策略管理工具并配置 SELinux，让 MongoDB 在其默认的 TCP 端口 27017 上运行，如下所示：

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

按如下所示启动 MongoDB 服务：

```bash
sudo service mongod start
```

通过使用本地 `mongo` 客户端进行连接来验证 MongoDB 安装：

```bash
mongo
```

现在，通过添加一些数据并执行搜索来测试 MongoDB 实例：

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

如果需要，可将 MongoDB 配置为在系统重新引导期间自动启动：

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>使用模板在 CentOS 上创建基本 MongoDB 实例
可以使用 GitHub 中的以下 Azure 快速入门模板，在单个 CentOS VM 上创建基本的 MongoDB 实例。 此模板使用适用于 Linux 的自定义脚本扩展将 yum 存储库添加到新建的 CentOS VM，然后安装 MongoDB。

* [CentOS 上的基本 MongoDB 实例](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

若要创建此环境，需要安装最新的 [Azure CLI](https://docs.azure.cn/cli/install-az-cli2?view=azure-cli-latest)，并使用 [az login](https://docs.azure.cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户。 首先，使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) 创建资源组。 以下示例在“chinaeast”  位置创建名为“myResourceGroup”  的资源组：

```azurecli
az group create --name myResourceGroup --location chinaeast
```

接下来，使用 [az deployment group create](/cli/deployment/group) 部署 MongoDB 模板。 出现提示时，为 newStorageAccountName、dnsNameForPublicIP 以及管理员用户名和密码提供自己的唯一值：

```azurecli
az deployment group create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

使用 VM 的公共 DNS 地址登录到 VM。 可以使用 [az vm show](/cli/vm) 查看公共 DNS 地址：

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

使用自己的用户名和公共 DNS 地址 SSH 连接到 VM：

<!--MOONCAKE: CORRECT ON cloudapp.azure.com to cloudapp.chinacloudapi.cn -->

```bash
ssh azureuser@mypublicdns.chinaeast.cloudapp.chinacloudapi.cn
```

<!--MOONCAKE: CORRECT ON cloudapp.azure.com to cloudapp.chinacloudapi.cn -->

如下所示，通过使用本地 `mongo` 客户端进行连接来验证 MongoDB 安装：

```bash
mongo
```

现在，按如下所示，通过添加一些数据并执行搜索来测试实例：

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>使用模板在 CentOS 上创建复杂的 MongoDB 分片群集
可以使用 Github 中的以下 Azure 快速入门模板创建复杂的 MongoDB 分片群集。 此模板遵循 [MongoDB 分片群集最佳实践](https://docs.mongodb.com/manual/core/sharded-cluster-components/)来提供冗余和高可用性。 该模板将创建两个分片，其中每个副本集包含三个节点。 另外，它还会创建一个配置服务器副本集和两个 mongos 路由器服务器，前者包含 3 个节点的，后者用于确保各分片中的应用程序保持一致。

* [CentOS 上的 MongoDB 分片群集](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> 部署这种复杂 MongoDB 分片群集需要 20 个以上的核心（每个区域中一个订阅的默认核心计数通常为 20 个）。 请提出 Azure 支持请求，以增加核心计数。

若要创建此环境，需要安装最新的 [Azure CLI](https://docs.azure.cn/cli/install-az-cli2?view=azure-cli-latest)，并使用 [az login](https://docs.azure.cn/cli/reference-index?view=azure-cli-latest#az-login) 登录到 Azure 帐户。 首先，使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) 创建资源组。 以下示例在“chinaeast”  位置创建名为“myResourceGroup”  的资源组：

```azurecli
az group create --name myResourceGroup --location chinaeast
```

接下来，使用 [az deployment group create](/cli/deployment/group) 部署 MongoDB 模板。 根据需要定义自己的资源名称和大小，例如针对 mongoAdminUsername、sizeOfDataDiskInGB 和 configNodeVmSize：

```azurecli
az deployment group create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureChinaCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

此部署可能需要一个多小时来部署和配置所有 VM 实例。 上述命令末尾使用了 `--no-wait` 标志，在 Azure 平台接受模板部署后将控制权返回给命令提示符。 然后，可以使用 [az deployment group show](/cli/deployment/group) 查看部署状态。 以下示例查看 myResourceGroup 资源组中 myMongoDBCluster 部署的状态：

```azurecli
az deployment group show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>后续步骤
在上述示例中，已在本地从 VM 连接到 MongoDB 实例。 如果想要从另一个 VM 或网络连接到 MongoDB 实例，请确保[创建相应的网络安全组规则](nsg-quickstart.md)。

这些示例部署用于开发的核心 MongoDB 环境。 请为你的环境应用必需的安全配置选项。 有关详细信息，请参阅 [MongoDB 安全文档](https://docs.mongodb.com/manual/security/)。

有关使用模板创建这些规则的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/management/overview.md)。

Azure 资源管理器模板使用自定义脚本扩展在 VM 上下载并执行脚本。 有关详细信息，请参阅[在 Linux 虚拟机上使用 Azure 自定义脚本扩展](../extensions/custom-script-linux.md)。

<!-- Update_Description: update meta properties, wording update, update link -->