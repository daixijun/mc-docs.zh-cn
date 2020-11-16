---
title: 使用 Azure CLI 创建 Apache Hadoop 群集 - Azure HDInsight
description: 了解如何使用跨平台 Azure CLI 创建 Azure HDInsight 群集。
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 02/03/2020
ms.date: 03/02/2019
ms.author: v-yiso
ms.openlocfilehash: d676d8095c9d2b520ce24356ac201abd9413f881
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552327"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>使用 Azure CLI 创建 HDInsight 群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

本文介绍了使用 Azure CLI 创建 HDInsight 3.6 群集的相关步骤。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="prerequisites"></a>先决条件

Azure CLI。 如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](/cli/install-azure-cli) 来了解步骤。

## <a name="create-a-cluster"></a>创建群集

1. 登录到 Azure 订阅。 

    ```azurecli
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 设置环境变量。 本文中的变量用法基于 Bash。 在其他环境中需要进行细微的更改。 有关用于群集创建的可能参数的完整列表，请参见 [az-hdinsight-create](/cli/hdinsight#az-hdinsight-create)。

    |参数 | 说明 |
    |---|---|
    |`--workernode-count`| 群集中的工作器节点数。 本文使用变量 `clusterSizeInNodes` 作为传递给 `--workernode-count` 的值。 |
    |`--version`| HDInsight 群集版本。 本文使用变量 `clusterVersion` 作为传递给 `--version` 的值。 另请参阅：[支持的 HDInsight 版本](./hdinsight-component-versioning.md#supported-hdinsight-versions)。|
    |`--type`| HDInsight 群集的类型，如：hadoop、interactivehive、hbase、Kafka、storm、spark、rserver、mlservices。  本文使用变量 `clusterType` 作为传递给 `--type` 的值。 另请参阅：[群集类型和配置](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type)。|
    |`--component-version`|各种 Hadoop 组件的版本，采用“component=version”格式的空格分隔版本。 本文使用变量 `componentVersion` 作为传递给 `--component-version` 的值。 另请参阅：[Hadoop 组件](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)。|

    将 `RESOURCEGROUPNAME`、`LOCATION`、`CLUSTERNAME`、`STORAGEACCOUNTNAME` 和 `PASSWORD` 替换为所需的值。 根据需要更改其他变量的值。 然后输入 CLI 命令。

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. 输入以下命令来[创建资源组](/cli/group#az-group-create)：

    ```azurecli
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    有关有效位置的列表，请使用 `az account list-locations` 命令，并使用 `name` 值中的位置之一。

4. 输入以下命令来[创建 Azure 存储帐户](/cli/storage/account#az-storage-account-create)：

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. 通过输入以下命令[从 Azure 存储帐户中提取主密钥](/cli/storage/account/keys#az-storage-account-keys-list)，然后将其存储在一个变量中：

    ```azurecli
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. 输入以下命令来[创建 Azure 存储容器](/cli/storage/container#az-storage-container-create)：

    ```azurecli
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. 输入以下命令来[创建 HDInsight 群集](/cli/hdinsight#az-hdinsight-create)：

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

     > [!IMPORTANT]
     > HDInsight 群集具有各种不同的类型，与该群集进行优化的工作负荷或技术相对应。 不支持在一个群集上创建合并了多个类型（如 Storm 和 HBase）的群集。

    可能需要几分钟时间才能完成群集创建过程。 通常大约为 15 分钟。

## <a name="clean-up-resources"></a>清理资源

完成本文后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要支付 HDInsight 群集费用，即使未使用。 由于群集费用高于存储空间费用数倍，因此在不使用群集时将其删除可以节省费用。

输入以下命令中的全部或部分来删除资源：

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](./hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>后续步骤

使用 Azure CLI 成功创建 HDInsight 群集后，请参考以下主题来了解如何使用群集：

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 群集

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 群集

* [HDInsight 中的 Apache HBase 入门](hbase/apache-hbase-tutorial-get-started-linux.md)
* [为 Apache HBase on HDInsight 开发 Java 应用程序](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 群集

* [为 Apache Storm on HDInsight 开发 Java 拓扑](storm/apache-storm-develop-java-topology.md)
* [在 Apache Storm on HDInsight 中使用 Python 组件](storm/apache-storm-develop-python-topology.md)
* [使用 Apache Storm on HDInsight 部署和监视拓扑](storm/apache-storm-deploy-monitor-topology-linux.md)
