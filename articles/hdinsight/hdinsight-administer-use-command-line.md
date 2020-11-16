---
title: 使用 Azure CLI 管理 Azure HDInsight 群集
description: 了解如何使用 Azure CLI 管理 Azure HDInsight 群集。 群集类型包括 ApacheHadoop、Spark、HBase、Storm、Kafka、交互式查询和 ML Services。
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
origin.date: 02/26/2020
ms.date: 03/23/2020
ms.author: v-yiso
ms.openlocfilehash: 1d9b715f424e21b077f2e9ce2fa2106f0aa45fbc
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94551894"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>使用 Azure CLI 管理 Azure HDInsight 群集

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

了解如何使用 [Azure CLI](/cli/?view=azure-cli-latest) 管理 Azure HDInsight 群集。 Azure 命令行接口 (CLI) 是用于管理 Azure 资源的 Microsoft 跨平台命令行体验。

如果没有 Azure 订阅，可在开始前创建一个 [试用帐户](https://www.azure.cn/pricing/1rmb-trial) 。

## <a name="prerequisites"></a>必备条件

* Azure CLI。 如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](/cli/install-azure-cli) 来了解步骤。

* HDInsight 中的 Apache Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

## <a name="connect-to-azure"></a>连接到 Azure

登录到 Azure 订阅。 输入以下命令：

```azurecli
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>列出群集

使用 [az hdinsight list](/cli/hdinsight#az-hdinsight-list) 列出群集。 编辑以下命令，将 `RESOURCE_GROUP_NAME` 替换为资源组的名称，然后输入命令：

```azurecli
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>显示群集

使用 [az hdinsight show](/cli/hdinsight#az-hdinsight-show) 显示指定群集的信息。 编辑以下命令，将 `RESOURCE_GROUP_NAME` 和 `CLUSTER_NAME` 替换为相关信息，然后输入命令：

```azurecli
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>删除群集

使用 [az hdinsight delete](/cli/hdinsight#az-hdinsight-delete) 删除指定的群集。 编辑以下命令，将 `RESOURCE_GROUP_NAME` 和 `CLUSTER_NAME` 替换为相关信息，然后输入命令：

```azurecli
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

还可通过删除包含该群集的资源组来删除群集。 请注意，这会删除包括默认存储帐户的组中的所有资源。

```azurecli
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>缩放群集

使用 [az hdinsight resize](/cli/hdinsight#az-hdinsight-resize) 将指定的 HDInsight 群集调整为指定大小。 编辑以下命令，将 `RESOURCE_GROUP_NAME` 和 `CLUSTER_NAME` 替换为相关信息。 将 `WORKERNODE_COUNT` 替换为群集所需的工作器节点数。 有关缩放群集的详细信息，请参阅[缩放 HDInsight 群集](./hdinsight-scaling-best-practices.md)。 输入以下命令：

```azurecli
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>后续步骤
在本文中，已了解如何执行不同的 HDInsight 群集管理任务。 若要了解详细信息，请参阅以下文章：

* [使用 Azure 门户管理 HDInsight 中的 Apache Hadoop 群集](hdinsight-administer-use-portal-linux.md)
* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure CLI 入门](/cli/get-started-with-azure-cli)
