---
title: CLI：从备份中还原应用
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何从备份还原应用。
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
origin.date: 12/07/2017
ms.date: 12/21/2020
ms.author: v-tawe
ms.reviewer: cephalin
ms.custom: mvc, seodec18
ms.openlocfilehash: bfd85029bca80303b93cce31f3afbb7d242da9ef
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022947"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>使用 CLI 从备份中还原 Web 应用

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后为其创建一次性备份。 

若要运行此脚本，需要 Web 应用的现有备份。 若要创建备份，请参阅[备份 Web 应用](cli-backup-onetime.md)或[创建 Web 应用的计划备份](cli-backup-scheduled.md)。



如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli?view=azure-cli-lastest)。 

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

groupname="myResourceGroup"
webappname="<replace-with-your-app-name>"

# List statuses of all backups that are complete or currently executing.
az webapp config backup list --resource-group $groupname --webapp-name $webappname

# Note the backupItemName and storageAccountUrl properties of the backup you want to restore

# Restore the app by overwriting it with the backup data
# Be sure to replace <backupItemName> and <storageAccountUrl>
az webapp config backup restore --resource-group $groupname --webapp-name $webappname \
--backup-name <backupItemName> --container-url <storageAccountUrl> --overwrite
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [`az webapp config backup list`](https://docs.azure.cn/cli/webapp/config/backup#az_webapp_config_backup_list) | 获取 Web 应用的备份列表。 |
| [`az webapp config backup restore`](https://docs.azure.cn/cli/webapp/config/backup#az_webapp_config_backup_restore) | 从备份中恢复 web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
