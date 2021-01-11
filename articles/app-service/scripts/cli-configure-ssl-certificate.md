---
title: CLI：将 TLS/SSL 证书上传并绑定到应用
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例展示了如何将自定义 TLS/SSL 证书绑定到应用。
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
origin.date: 12/11/2017
ms.date: 12/21/2020
ms.author: v-tawe
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 5f88aa54f69f618b560817e1f4ef87c6ac27d2f7
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022944"
---
# <a name="bind-a-custom-tlsssl-certificate-to-an-app-service-app-using-cli"></a>使用 CLI 将自定义 TLS/SSL 证书绑定到应用服务应用

此示例脚本在应用服务中创建一个应用及其相关资源，然后将自定义域名的 TLS/SSL 证书绑定到该应用。 在此示例中，需要以下项：

* 访问域注册机构的 DNS 配置页的权限。
* 要上传和绑定的 TLS/SSL 证书的有效 .PFX 文件及其密码。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

如果选择在本地安装并使用 CLI，则需使用 Azure CLI 2.0 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

fqdn=<replace-with-www.{yourdomain}>
pfxPath=<replace-with-path-to-your-.PFX-file>
pfxPassword=<replace-with-your=.PFX-password>
resourceGroup=myResourceGroup
webappname=mywebapp$RANDOM

# Create a resource group.
az group create --location chinanorth --name $resourceGroup

# Create an App Service plan in Basic tier (minimum required by custom domains).
az appservice plan create --name $webappname --resource-group $resourceGroup --sku B1

# Create a web app.
az webapp create --name $webappname --resource-group $resourceGroup \
--plan $webappname

echo "Configure a CNAME record that maps $fqdn to $webappname.chinacloudsites.cn"
read -p "Press [Enter] key when ready ..."

# Before continuing, go to your DNS configuration UI for your custom domain and follow the 
# instructions at https://docs.azure.cn/app-service/app-service-web-tutorial-custom-domain#step-2-create-the-dns-records to configure a CNAME record for the 
# hostname "www" and point it your web app's default domain name.

# Map your prepared custom domain name to the web app.
az webapp config hostname add --webapp-name $webappname --resource-group $resourceGroup \
--hostname $fqdn

# Upload the SSL certificate and get the thumbprint.
thumbprint=$(az webapp config ssl upload --certificate-file $pfxPath \
--certificate-password $pfxPassword --name $webappname --resource-group $resourceGroup \
--query thumbprint --output tsv)

# Binds the uploaded SSL certificate to the web app.
az webapp config ssl bind --certificate-thumbprint $thumbprint --ssl-type SNI \
--name $webappname --resource-group $resourceGroup

echo "You can now browse to https://$fqdn"
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [`az group create`](https://docs.azure.cn/cli/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](https://docs.azure.cn/cli/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](https://docs.azure.cn/cli/webapp#az_webapp_create) | 创建应用服务应用。 |
| [`az webapp config hostname add`](https://docs.azure.cn/cli/webapp/config/hostname#az_webapp_config_hostname_add) | 将自定义域映射到应用服务应用。 |
| [`az webapp config ssl upload`](https://docs.azure.cn/cli/webapp/config/ssl#az_webapp_config_ssl_upload) | 将 TLS/SSL 证书上传到应用服务应用。 |
| [`az webapp config ssl bind`](https://docs.azure.cn/cli/webapp/config/ssl#az_webapp_config_ssl_bind) | 将上传的 TLS/SSL 证书绑定到应用服务应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/cli)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
