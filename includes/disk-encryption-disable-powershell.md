---
title: include 文件
description: include 文件
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 06/19/2020
ms.date: 08/03/2020
ms.testscope: yes
ms.testdate: 08/03/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 833b485ad3f15ea4ddd87194fc3d511ae5c3c3fd
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301138"
---
可以使用 Azure PowerShell、Azure CLI 或资源管理器模板禁用加密。 当 OS 和数据磁盘都已加密时，无法按预期在 Windows VM 上禁用数据磁盘加密。 请改为在所有磁盘上禁用加密。

- **使用 Azure PowerShell 禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzVMDiskEncryption](https://docs.microsoft.com/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 
     ```powershell
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **使用 Azure CLI 禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](https://docs.azure.cn/cli/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
     
<!--Not Available on - **Disable encryption with a Resource Manager template:**-->
<!--Not Available on https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-decrypt-running-windows-vm-without-aad/azuredeploy.json-->
<!--Block by China Great Wall-->


<!-- Update_Description: new article about disk encryption disable powershell -->
<!--NEW.date: 08/03/2020-->