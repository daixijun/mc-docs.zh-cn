---
title: 从本地 Git 存储库进行部署
description: 了解如何实现从本地 Git 部署到 Azure 应用服务。 从本地计算机部署代码的最简单方法之一。
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
origin.date: 06/18/2019
ms.date: 01/18/2021
ms.author: v-tawe
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: bf34d70a80b561d5c518689eefea3bc37ce2641c
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230751"
---
# <a name="local-git-deployment-to-azure-app-service"></a>从本地 Git 部署到 Azure 应用服务

本操作方法指南介绍如何将应用从本地计算机上的 Git 存储库部署到 [Azure 应用服务](overview.md)。

## <a name="prerequisites"></a>先决条件

按照本操作方法指南中的步骤操作：

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [安装 Git](https://www.git-scm.com/downloads)。
  
- 创建包含想要部署的代码的本地 Git 存储库。 若要下载示例存储库，请在本地终端窗口运行以下命令：
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-with-kudu-build-server"></a>使用 Kudu 生成服务器进行部署

使用 Kudu 应用服务生成服务器为应用启用本地 Git 部署的最简单方法是使用 Azure CLI。 

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>获取部署 URL

若要获取所需的 URL 来为现有应用启用本地 Git 部署，请在 Azure CLI 中运行 [`az webapp deployment source config-local-git`](https://docs.azure.cn/cli/webapp/deployment/source#az_webapp_deployment_source_config_local_git)。 将 \<app-name> 和 \<group-name> 分别替换为应用及其 Azure 资源组的名称。

```azurecli
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> 如果使用的是 Linux 应用服务计划，则需要添加此参数：--runtime python|3.7


或者，若要创建启用 Git 的新应用，请在 Azure CLI 中结合 `--deployment-local-git` 参数运行 [`az webapp create`](https://docs.azure.cn/cli/webapp#az_webapp_create)。 请将 \<app-name>、\<group-name> 和 \<plan-name> 替换为新 Git 应用、其 Azure 资源组及其 Azure 应用服务计划的名称。

```azurecli
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

上述任一命令都会返回类似于 `https://<deployment-username>@<app-name>.scm.chinacloudsites.cn/<app-name>.git` 的 URL。 在下一步骤中，将使用此 URL 部署应用。

如果不使用此帐户级 URL，也可以使用应用级凭据启用本地 Git。 Azure 应用服务会自动为每个应用生成这些凭据。 

在 Azure CLI 中运行以下命令以获取应用凭据。 将 \<app-name> 和 \<group-name> 替换为应用的名称和 Azure 资源组名称。

```azurecli
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

在下一步骤中，将使用返回的 URL 部署应用。

### <a name="deploy-the-web-app"></a>部署 Web 应用

1. 打开本地终端窗口并转到本地 Git 存储库，然后添加一个 Azure 远程实例。 在以下命令中，请将 \<url> 替换为你在上一步骤中获取的特定于部署用户的 URL 或特定于应用的 URL。
   
   ```bash
   git remote add azure <url>
   ```
   
1. 使用 `git push azure main` 推送到 Azure 远程实例。 
   
1. 在[Git 凭据管理器](#configure-a-deployment-user)窗口中，输入 部署用户密码 而不是 Azure 登录密码。 
   
1. 查看输出。 你可能会看到特定于运行时的自动化，例如 MSBuild for ASP.NET、`npm install` for Node.js 和 `pip install` for Python。 
   
1. 在 Azure 门户中浏览到你的应用以检查内容是否已部署。

<!-- ## Deploy with Azure Pipelines builds -->
<!--Last Check Date: 2020/01/11-->
## <a name="troubleshoot-deployment"></a>排查部署问题

使用 Git 发布到 Azure 中的应用服务应用时，你可能会看到以下常见错误消息：

|Message|原因|解决方法
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|应用未正常运行。|在 Azure 门户中启动应用。 如果 Web 应用已停止，Git 部署将不可用。|
|`Couldn't resolve host 'hostname'`|“azure”远程实例的地址信息不正确。|使用 `git remote -v` 命令列出所有远程网站以及关联的 URL。 确认“azure”远程网站的 URL 正确。 如果需要，请删除此远程网站并使用正确的 URL 重新创建它。|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|在运行 `git push` 期间未指定分支，或者未在 `.gitconfig` 中设置 `push.default` 值。|再次运行 `git push`，并指定主分支：`git push azure main`。|
|`src refspec [branchname] does not match any.`|你已尝试推送到“azure”远程实例上除主节点以外的分支。|再次运行 `git push`，并指定主分支：`git push azure main`。|
|`RPC failed; result=22, HTTP code = 5xx.`|如果尝试通过 HTTPS 推送大型 Git 存储库，则可能出现此错误。|在本地计算机上更改 Git 配置，以增大 `postBuffer`。 例如：`git config --global http.postBuffer 524288000`。|
|`Error - Changes committed to remote repository but your web app not updated.`|你已使用一个指定了其他所需模块的 _package.json_ 文件部署了 Node.js 应用。|检查发生此错误之前出现的 `npm ERR!` 错误消息，以了解有关失败的更多上下文。 下面是此错误的已知原因，以及相应的 `npm ERR!` 消息：<br /><br />**package.json 文件格式不当**：`npm ERR! Couldn't read dependencies.`<br /><br />**本机模块没有适用于 Windows 的二进制分发版**：<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />或 <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>其他资源

- [项目 Kudu 文档](https://github.com/projectkudu/kudu/wiki)

    <!--Not Available on - [Continuous deployment to Azure App Service](deploy-continuous-deployment.md)-->

- [示例：从本地 Git 存储库创建 Web 应用和部署代码 (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [示例：从本地 Git 存储库创建 Web 应用和部署代码 (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
