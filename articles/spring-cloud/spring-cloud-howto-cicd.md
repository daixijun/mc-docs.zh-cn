---
title: 适用于 Azure Spring Cloud 的 CI/CD
description: 适用于 Azure Spring Cloud 的 CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: v-junlch
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c5abb57ace2e292c1965609ee7393f26b2958b4c
ms.sourcegitcommit: b9d0091aebc506af49b7cfcd44593711df0d04a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373968"
---
# <a name="cicd-for-azure-spring-cloud"></a>适用于 Azure Spring Cloud 的 CI/CD

通过持续集成和持续交付工具，你可以快速地将更新部署到现有应用程序，工作量较少且风险较低。 Azure DevOps 可帮助你组织和控制这些关键作业。 目前，Azure Spring Cloud 不提供特定的 Azure DevOps 插件。  但是，可以使用 [Azure CLI 任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?preserve-view=true&view=azure-devops)将 Spring Cloud 应用程序与 DevOps 集成。

本文介绍如何将 Azure CLI 任务与 Azure Spring Cloud 结合使用以便与 Azure DevOps 集成。

## <a name="create-an-azure-resource-manager-service-connection"></a>创建 Azure 资源管理器服务连接

阅读[本文](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?preserve-view=true&view=azure-devops)，了解如何将 Azure 资源管理器服务连接创建到 Azure DevOps 项目。 请确保选择用于 Azure Spring Cloud 服务实例的同一订阅。

## <a name="azure-cli-task-templates"></a>Azure CLI 任务模板

### <a name="deploy-artifacts"></a>部署项目

你可以使用一系列 `tasks` 来构建和部署项目。 此代码片段首先定义生成应用程序的 Maven 任务，接下来定义使用 Azure Spring Cloud Azure CLI 扩展部署 JAR 文件的第二个任务。

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>从源进行部署

无需单独的生成步骤便可直接部署到 Azure。

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
## <a name="next-steps"></a>后续步骤

* [快速入门：部署第一个 Azure Spring Cloud 应用程序](spring-cloud-quickstart.md)

