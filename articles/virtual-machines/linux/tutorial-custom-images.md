---
title: 教程 - 使用 Azure CLI 创建自定义 VM 映像 | Azure
description: 本教程介绍如何使用 Azure CLI 在 Azure 中创建自定义虚拟机映像
author: Johnnytechn
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/01/2020
ms.author: v-johya
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 9fda4937466fb6c588353c8729dfc6ccc994e6aa
ms.sourcegitcommit: ac1cb9a6531f2c843002914023757ab3f306dc3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747117"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>教程：使用 Azure CLI 创建 Azure VM 的自定义映像

自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 在本教程中，你将创建自己的 Azure 虚拟机自定义映像。 学习如何：

> [!div class="checklist"]
> * 创建共享映像库
> * 创建映像定义
> * 创建映像版本
> * 从映像创建 VM 
> * 共享映像库


如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.4.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="overview"></a>概述

[共享映像库](shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 

共享映像库可让你与他人共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 

共享映像库功能具有多种资源类型：

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>开始之前

下列步骤详细说明如何将现有 VM 转换为可重用自定义映像，以便将其用于创建新 VM 实例。

若要完成本教程中的示例，必须现有一个虚拟机。 如果需要，可以参阅 [CLI 快速入门](quick-create-cli.md)来创建本教程所用的 VM。 在学习本教程期间，请根据需要替换资源名称。


## <a name="create-an-image-gallery"></a>创建映像库 

映像库是用于启用映像共享的主要资源。 

允许用于库名称的字符为大写或小写字母、数字、点和句点。 库名称不能包含短划线。   库名称在你的订阅中必须唯一。 

使用 [az sig create](https://docs.microsoft.com/cli/sig#az-sig-create) 创建一个映像库。 以下示例在“中国北部”创建一个名为“myGalleryRG”的资源组命名库，以及一个名为“myGallery”的库  。

```azurecli
az group create --name myGalleryRG --location chinanorth
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>获取有关 VM 的信息

可以使用 [az vm list](/cli/vm#az-vm-list) 查看可用 VM 的列表。 

```azurecli
az vm list --output table
```

知道 VM 的名称及其所在的资源组后，使用 [az vm get-instance-view](/cli/vm#az-vm-get-instance-view) 获取 VM 的 ID。 

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

复制 VM 的 ID 供稍后使用。

## <a name="create-an-image-definition"></a>创建映像定义

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像版本的信息，这些版本是在其中创建的。 

映像定义名称可以由大写或小写字母、数字、点、短划线和句点构成。 

若要详细了解可为映像定义指定的值，请参阅[映像定义](./shared-image-galleries.md#image-definitions)。

使用 [az sig image-definition create](https://docs.microsoft.com/cli/sig/image-definition#az-sig-image-definition-create) 在库中创建一个映像定义。 

在此示例中，映像定义名为 myImageDefinition，适用于[专用化](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS 映像。 

```azurecli 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

复制输出中的映像定义 ID 供稍后使用。

## <a name="create-the-image-version"></a>创建映像版本

使用 [az image gallery create-image-version](https://docs.microsoft.com/cli/sig/image-version#az-sig-image-version-create) 从 VM 创建映像版本。  

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此示例中，映像的版本为 1.0.0，并且我们打算使用区域冗余存储在“中国北部”区域创建 2 个副本，在“中国东部区域”创建 1 个副本，在“中国东部 2”区域创建 1 个副本   。 复制区域必须包含源 VM 所在的区域。

请将此示例中的 `--managed-image` 值替换为上一步的 VM ID。

```azurecli 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "chinanorth" "chinaeast=1" "chinaeast2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 创建映像版本时，还可以通过添加 `--storage-account-type  premium_lrs` 在高级存储中存储映像。
>
<!--Not available in MC: /storage/common/storage-redundancy-zrs -->

 
## <a name="create-the-vm"></a>创建 VM

结合 --specialized 参数使用 [az vm create](/cli/vm#az-vm-create) 创建 VM 可以指明该映像是专用化映像。 

使用 `--image` 的映像定义 ID 从可用的最新映像版本创建 VM。 还可以通过为 `--image` 提供映像版本 ID 从特定版本创建 VM。 

在此示例中，我们将从 myImageDefinition 映像的最新版本创建 VM。

```azurecli
az group create --name myResourceGroup --location chinanorth
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>共享库

可以使用 Azure 基于角色的访问控制 (Azure RBAC) 在订阅之间共享映像。 可以在库、映像定义或映像版本级别共享映像。 任何对映像版本具有读取权限的用户，即使跨订阅，也能够使用映像版本部署 VM。

建议在库级别与其他用户进行共享。 若要获取库的对象 ID，请使用 [az sig show](https://docs.microsoft.com/cli/sig#az-sig-show)。

```azurecli
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

使用对象 ID 作为范围，并使用电子邮件地址和 [az role assignment create](/cli/role/assignment#az-role-assignment-create) 为用户授予对共享映像库的访问权限。 请将 `<email-address>` 和 `<gallery iD>` 替换为自己的信息。

```azurecli
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

有关如何使用 Azure RBAC 共享资源的详细信息，请参阅[使用 Azure CLI 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-cli.md)。

<!--Not available in MC currently-->
## <a name="next-steps"></a>后续步骤

在本教程中，已创建自定义 VM 映像。 你已了解如何：

> [!div class="checklist"]
> * 创建共享映像库
> * 创建映像定义
> * 创建映像版本
> * 从映像创建 VM 
> * 共享映像库

请转到下一教程，了解高度可用的虚拟机。

> [!div class="nextstepaction"]
> [创建高度可用的 VM](tutorial-availability-sets.md)

