---
title: 在 Azure Spring Cloud 中设置过渡环境 | Microsoft Docs
description: 了解如何在 Azure Spring Cloud 中使用蓝绿部署
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: v-junlch
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 4de4e16ef15857e699351ac3302923db33ff3cac
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94328684"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>在 Azure Spring Cloud 中设置过渡环境

本文介绍如何使用 Azure Spring Cloud 中的蓝绿色部署模式来设置过渡部署。 蓝/绿部署是一种 Azure DevOps 持续交付模式，它依赖于在部署新（绿色）版本时保持现有（蓝色）版本的活动性。 本文还会介绍如何在不直接更改生产部署的情况下，将此过渡部署投入生产。

## <a name="prerequisites"></a>先决条件

本文假设你已根据 [Azure Spring Cloud 应用程序的启动教程](./spring-cloud-quickstart.md)部署 PiggyMetrics 应用程序。 PiggyMetrics 包括三个应用程序：“gateway”、“account-service”和“auth-service”。  

如果要对本示例使用其他应用程序，需要在该应用程序面向公众的部分进行一项简单的更改。  此项更改会将过渡部署与生产部署区分开来。


若要在 Azure Spring Cloud 中设置过渡环境，请按照后续部分中的说明进行操作。

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展：

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>查看所有部署

在 Azure 门户中转到你的服务实例，然后选择“部署管理”以查看所有部署。 若要查看更多详细信息，可选择每个部署。

## <a name="create-a-staging-deployment"></a>创建分阶段部署

1. 在本地开发环境中，对 PiggyMetrics 网关应用程序进行少许修改。 例如，更改 gateway/src/main/resources/static/css/launch.css 文件中的颜色。 这样便可轻松区分这两个部署。 若要生成 jar 包，请运行以下命令： 

    ```console
    mvn clean package
    ```

1. 在 Azure CLI 中创建新的部署，并指定过渡部署名称“绿色”。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 成功部署后，从应用程序仪表板访问网关页面，然后在左侧的“应用实例”选项卡中查看所有实例 。
  
> [!NOTE]
> 发现状态为“OUT_OF_SERVICE”，因此在验证完成之前，流量不会路由到此部署。

## <a name="verify-the-staging-deployment"></a>验证过渡部署

1. 返回“部署管理”页面并选择你的新部署。 部署状态应显示为“正在运行”。 “分配/取消分配域”按钮应显示为灰色，因为该环境是一个过渡环境。

1. 在“概述”页面中，应会看到“测试终结点” 。 将其复制粘贴到新的浏览器窗口，此时应会显示新的 PiggyMetrics 页面。

>[!TIP]
> * 确认测试终结点以斜线 (/) 结尾，从而确保正确加载 CSS 文件。  
> * 如果浏览器要求在查看该页之前输入登录凭据，请使用 [URL decode](https://www.urldecoder.org/) 来解码测试终结点。 URL 解码会返回格式为“https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green”的 URL。  请使用此格式来访问终结点。

>[!NOTE]    
> 配置服务器设置既应用于过渡环境，也应用于生产环境。 例如，如果在配置服务器中将应用网关的上下文路径 (`server.servlet.context-path`) 设置为 somepath，则绿色部署的路径将更改为“https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/...”。
 
 如果此时访问面向公众的应用网关，应会看到没有新更改的旧页面。
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>将绿色部署设置为生产环境

1. 在过渡环境中验证更改后，可将其推送到生产环境。 返回“部署管理”，选中“网关”应用程序复选框。 

2. 选择“设置部署”。
3. 在“生产部署”列表中选择“绿色”，然后选择“应用”  。
4. 转到网关应用程序的“概述”页。 如果已为网关应用程序分配了一个域，URL 将在“概述”窗格中显示。 若要查看修改后的 PiggyMetrics 页面，请选择该 URL，然后前往该站点。

>[!NOTE]
> 将绿色部署设置为生产环境后，以前的部署将变成过渡部署。

## <a name="modify-the-staging-deployment"></a>修改过渡部署

如果你对更改不满意，可修改应用程序代码，生成新的 jar 包，然后使用 Azure CLI 将其上传到绿色部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>删除过渡部署

若要在 Azure 门户中删除过渡部署，请转到过渡部署页面，然后选择“删除”按钮。

或者，在 Azure CLI 中运行以下命令删除过渡部署：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>后续步骤

* [快速入门：部署第一个 Azure Spring Cloud 应用程序](spring-cloud-quickstart.md)

