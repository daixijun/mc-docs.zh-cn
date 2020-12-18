---
title: 使用 GitHub Actions 生成和测试容器并将其部署到 Azure Kubernetes 服务
description: 了解如何使用 GitHub Actions 将容器部署到 Kubernetes
services: container-service
ms.topic: article
origin.date: 11/06/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: no
ms.testdate: 05/06/2020
ms.author: v-yeche
ms.custom: github-actions-azure
ms.openlocfilehash: ab29bbc18c64cfbcba8dee1764a46abd7b93d0b4
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004204"
---
<!--CONFIRME WITH DONG LIU SUCCESSFULLY-->
<!--NEW FEATURES HAS BEEN RELEASED ON AZURE CHINA CLOUD-->
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>用于将容器部署到 Kubernetes 服务的 GitHub Actions

可以通过 [GitHub Actions](https://help.github.com/en/articles/about-github-actions) 灵活地生成自动化软件开发生命周期工作流。 可以使用多个 Kubernetes 操作通过 GitHub Actions 将 Azure 容器注册表中的容器部署到 Azure Kubernetes 服务。 

## <a name="prerequisites"></a>先决条件 

- 具有活动订阅的 Azure 帐户。 [创建试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。
- 一个 GitHub 帐户。 如果没有该帐户，请注册[免费版](https://github.com/join)。  
- 正常工作的 Kubernetes 群集
    - [教程：准备用于 Azure Kubernetes 服务的应用程序](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>工作流文件概述

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

对于以 AKS 为目标的工作流，该文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 登录到专用容器注册表 (ACR) |
|**生成** | 生成和推送容器映像  |
|**部署** | 1.设置目标 AKS 群集 |
| |2.在 Kubernetes 群集中创建通用/docker 注册表机密  |
||3.部署到 Kubernetes 群集|

## <a name="create-a-service-principal"></a>创建服务主体

可以在 [Azure CLI](https://docs.azure.cn/cli/) 中使用 [az ad sp create-for-rbac](https://docs.azure.cn/cli/ad/sp#az_ad_sp_create_for_rbac) 命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。

<!--Not Avaialble on [Azure Cloud Shell](https://shell.azure.com/)-->

```azurecli
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

在上述命令中，将占位符替换为你的订阅 ID 和资源组。 输出是用于访问资源的角色分配凭据。 此命令应输出下面这样的 JSON 对象。

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
请复制此 JSON 对象，它可以用来从 GitHub 进行身份验证。

## <a name="configure-the-github-secrets"></a>配置 GitHub 机密

按照以下步骤配置机密：

1. 在 [GitHub](https://github.com/) 中浏览到存储库，选择“设置”>“机密”>“添加新机密”。 

    :::image type="content" source="media/kubernetes-action/secrets.png" alt-text="屏幕截图显示了存储库的“添加新机密”链接。":::

2. 将上述 `az cli` 命令的内容作为机密变量的值粘贴。 例如，`AZURE_CREDENTIALS`。

3. 同样，为容器注册表凭据定义以下附加机密，并在 Docker 登录操作中设置它们。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 在定义后，会看到如下所示的机密。

    :::image type="content" source="media/kubernetes-action/kubernetes-secrets.png" alt-text="屏幕截图显示了存储库的现有机密。":::

## <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>生成容器映像并将其部署到 Azure Kubernetes 服务群集

容器映像的生成和推送使用 `Azure/docker-login@v1` 操作完成。 

```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master

    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.cn
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}

    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.cn/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.cn/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>部署到 Azure Kubernetes 服务群集

若要将容器映像部署到 AKS，需使用 `Azure/k8s-deploy@v1` 操作。 该操作有五个参数：

| **参数** | **解释** |
|---------|---------|
| **namespace** | （可选）选择目标 Kubernetes 命名空间。 如果未提供命名空间，则命令会在默认命名空间中运行 | 
| **manifests** |  （必需）将要用于部署的清单文件的路径 |
| **images** | （可选）将要用于在清单文件上进行替换的映像的完全限定资源 URL |
| **imagepullsecrets** | （可选）已在群集中设置的 docker 注册表机密的名称。 这些机密名称的每一个都在输入清单文件中的工作负载的 imagePullSecrets 字段下添加 |
| **kubectl-version** | （可选）安装 kubectl 二进制文件的特定版本 |

在部署到 AKS 之前，需要设置目标 Kubernetes 命名空间并创建映像拉取机密。 请参阅[将映像从 Azure 容器注册表拉取到 Kubernetes 群集](../container-registry/container-registry-auth-kubernetes.md)，以详细了解拉取映像的工作原理。 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -

  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.cn
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```

使用 `k8s-deploy` 操作完成部署。 将环境变量替换为应用程序的值。 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master

    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.cn
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}

    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.cn/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.cn/${{ env.APP_NAME }}:${{ github.sha }}

    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}

    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -

    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.cn
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true

    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.cn/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>清理资源

不再需要 Kubernetes 群集、容器注册表和存储库时，请通过删除资源组和 GitHub 存储库来清理部署的资源。 

<!--Not Available on ## Next steps-->
<!--Not Available on > [Learn about Azure Kubernetes Service](https://docs.microsoft.com/azure/architecture/reference-architectures/containers/aks-start-here)-->

### <a name="more-kubernetes-github-actions"></a>更多 Kubernetes GitHub Actions

* [Kubectl 工具安装程序](https://github.com/Azure/setup-kubectl) (`azure/setup-kubectl`)：在运行器上安装 kubectl 的特定版本。
* [Kubernetes 设置上下文](https://github.com/Azure/k8s-set-context) (`azure/k8s-set-context`)：设置将由其他操作使用的目标 Kubernetes 群集上下文，或运行任何 kubectl 命令。
* [AKS 设置上下文](https://github.com/Azure/aks-set-context) (`azure/aks-set-context`)：设置目标 Azure Kubernetes 服务群集上下文。
* [Kubernetes 创建机密](https://github.com/Azure/k8s-create-secret) (`azure/k8s-create-secret`)：在 Kubernetes 群集中创建通用机密或 docker 注册表机密。
* [Kubernetes 部署](https://github.com/Azure/k8s-deploy) (`azure/k8s-deploy`)：烘焙清单并将清单部署到 Kubernetes 群集。
* [设置 Helm](https://github.com/Azure/setup-helm) (`azure/setup-helm`)：在运行器上安装 Helm 二进制文件的特定版本。
* [Kubernetes 烘培](https://github.com/Azure/k8s-bake) (`azure/k8s-bake`)：烘焙清单文件，用于通过 helm2、kustomize 或 kompose 进行部署。
* [Kubernetes lint](https://github.com/azure/k8s-lint) (`azure/k8s-lint`)：验证/lint 清单文件。

<!-- Update_Description: update meta properties, wording update, update link -->