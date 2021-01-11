---
title: 教程 - 监视 Azure 中的 Linux 虚拟机
description: 在本教程中，你将学习如何监视 Linux 虚拟机上运行的性能和发现的应用程序组件。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: Johnnytechn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/05/2021
ms.author: v-johya
origin.date: 09/30/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7d5ac62e8abacfd96481e83c86c3de881bbfded0
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022537"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>教程：监视 Azure 中的 Linux 虚拟机

Azure 监视使用代理从 Azure VM 收集启动和性能数据，将此数据存储在 Azure 存储中，并使其可供通过门户、Azure PowerShell 模块和 Azure CLI 进行访问。 用于 VM 的 Azure Monitor 通过收集性能指标、发现 VM 上安装的应用程序组件来提供高级监视，并包括性能图表和依赖关系图。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 VM 上启用启动诊断
> * 查看启动诊断
> * 查看 VM 主机指标
> * 创建警报

<!--Not Available on > * Enable Azure Monitor for VMs-->
<!--Not Available on > * View VM performance metrics-->

## <a name="launch-azure-local-shell"></a>启动 Azure 本地 Shell

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli?view=azure-cli-latest)。

## <a name="create-vm"></a>创建 VM

若要查看诊断和指标的状态，需要创建一个 VM。 首先，使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) 创建资源组。 以下示例在“chinaeast”位置创建名为“myResourceGroupMonitor”的资源组。

```azurecli
az group create --name myResourceGroupMonitor --location chinaeast
```

现使用 [az vm create](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 以下示例将创建名为 myVM 的 VM，并生成 SSH 密钥（如果它们尚不存在于 *~/.ssh/* 中）：

```azurecli
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>启用启动诊断

Linux VM 启动时，启动诊断扩展将捕获启动输出并将其存储在 Azure 存储中。 此数据可以用于排查 VM 启动问题。 使用 Azure CLI 创建 Linux VM 时，不会自动启用启动诊断。

在启用启动诊断之前，需要创建一个存储帐户来存储启动日志。 存储帐户的名称必须全局唯一，介于 3 和 24 个字符之间，并且只能包含数字和小写字母。 使用 [az storage account create](https://docs.azure.cn/cli/storage/account?view=azure-cli-latest#az-storage-account-create) 命令创建存储帐户。 本示例使用一个随机字符串来创建唯一的存储帐户名称。

```azurecli
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location chinaeast
```

启用引导诊断时，需要 Blob 存储容器的 URI。 以下命令查询存储帐户以返回此 URI。 URI 值存储在名为 *bloburi* 的变量中，会在下一步骤中使用。

```azurecli
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

现在，请使用 [az vm boot-diagnostics enable](https://docs.azure.cn/cli/vm/boot-diagnostics?view=azure-cli-latest#az-vm-boot-diagnostics-enable) 启用启动诊断。 `--storage` 值是在上一步骤中收集的 Blob URI。

```azurecli
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>查看启动诊断

启用引导诊断后，每当停止再启动 VM 时，会将有关启动过程的信息写入日志文件。 本示例首先使用 [az vm deallocate](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-deallocate) 命令解除分配 VM，如下所示：

```azurecli
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

现在，请使用 [az vm start](https://docs.azure.cn/cli/vm?view=azure-cli-latest#az-vm-start) 命令启动 VM，如下所示：

```azurecli
az vm start --resource-group myResourceGroupMonitor --name myVM
```

可以使用 [az vm boot-diagnostics get-boot-log](https://docs.azure.cn/cli/vm/boot-diagnostics?view=azure-cli-latest#az-vm-boot-diagnostics-get-boot-log) 命令获取 *myVM* 的启动诊断数据，如下所示：

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>查看主机指标

Linux VM 在 Azure 中有一个与它交互的专用主机。 系统会自动收集该主机的指标，可以在 Azure 门户中查看这些指标，如下所示：

1. 在 Azure 门户中选择“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。
1. 若要查看主机 VM 的性能情况，请在 VM 窗口中选择“指标”，并选择“可用指标”下面的任一“[主机]”指标。

    ![查看主机指标](./media/tutorial-monitoring/monitor-host-metrics.png)

<!--Not Available on ## Enable advanced monitoring-->
<!--MOONCAKE: No Insights(preview) in Monitoring section-->
<!--Not Available on ## View VM performance metrics-->
<!--MOONCAKE: No Insights(preview) in Monitoring section-->
## <a name="create-alerts"></a>创建警报

可以根据特定的性能指标创建警报。 例如，当平均 CPU 使用率超过特定的阈值或者可用磁盘空间低于特定的空间量时，警报可用于发出通知。 警报显示在 Azure 门户中，也可以通过电子邮件发送。 还可以触发 Azure 自动化 Runbook 或 Azure 逻辑应用来响应生成的警报。

以下示例针对平均 CPU 使用率创建警报。

1. 在 Azure 门户中单击“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。

2. 在“警报”边栏选项卡上单击“新建警报规则”。 

3. 为警报提供 **名称**，例如 *myAlertRule*

4. 若要在 CPU 百分比持续 5 分钟超过 1.0 时触发警报，请选中其他所有默认值。

5. （可选）选中“电子邮件所有者、参与者和读者”对应的框，以便向他们发送电子邮件通知。 默认操作是在门户中显示通知。

6. 单击“确定”按钮。

## <a name="next-steps"></a>后续步骤

在本教程中，你配置并查看了 VM 的性能。 你已了解如何：

> [!div class="checklist"]
> * 创建资源组和 VM
> * 在 VM 上启用启动诊断
> * 查看启动诊断
> * 查看主机指标
> * 创建警报

请转到下一教程来了解 Azure 安全中心。

> [!div class="nextstepaction"]
> [管理 VM 安全性](../tutorial-azure-security.md)
<!--NEW.date: 11/11/2019-->