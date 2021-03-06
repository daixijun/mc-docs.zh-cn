---
title: Azure Spring Cloud CI/CD 与 GitHub Actions
description: 如何通过 GitHub Actions 来增进 Azure Spring Cloud 的 CI/CD 工作流
author: MikeDodaro
ms.author: v-junlch
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/28/2020
ms.custom: devx-track-java
ms.openlocfilehash: d10d0a2d3d2067afcc96f4e85ee41c7485727d1f
ms.sourcegitcommit: a37f80e7abcf3e42859d6ff73abf566efed783da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829449"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD 与 GitHub Actions

GitHub Actions 支持自动化的软件开发生命周期工作流。 通过适用于 Azure Spring Cloud 的 GitHub Actions，可以在存储库中创建工作流来生成、测试、打包、发布并部署到 Azure。 

## <a name="prerequisites"></a>先决条件
此示例需要 [Azure CLI](/cli/install-azure-cli?preserve-view=true&view=azure-cli-latest)。

## <a name="set-up-github-repository-and-authenticate"></a>设置 GitHub 存储库并进行身份验证
需要使用 Azure 服务主体凭据来为 Azure 登录操作授权。 若要获取 Azure 凭据，请在本地计算机上执行以下命令：
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
若要访问某个特定的资源组，可以缩小范围：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
该命令应该会输出一个 JSON 对象：
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

此示例使用 GitHub 上的 [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) 示例。  对于该示例，请打开 GitHub 存储库页，然后单击“设置”选项卡。打开“机密”菜单，然后单击“添加新机密” ：

 ![添加新机密](./media/github-actions/actions1.png)

将机密名称设置为 `AZURE_CREDENTIALS`，并将其值设置为在标题“设置 GitHub 存储库并进行身份验证”下找到的 JSON 字符串。

 ![设置机密数据](./media/github-actions/actions2.png)

也可以在 GitHub Actions 中从 Key Vault 获取 Azure 登录凭据，如[在 GitHub Actions 中向 Key Vault 进行 Azure Spring 身份验证](./spring-cloud-github-actions-key-vault.md)中所述。

## <a name="provision-service-instance"></a>预配服务实例
若要预配 Azure Spring Cloud 服务实例，请使用 Azure CLI 运行以下命令。
```
az extension add --name spring-cloud
az group create --location chinaeast2 --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>生成工作流
工作流是使用以下选项定义的。

### <a name="prepare-for-deployment-with-azure-cli"></a>使用 Azure CLI 准备部署
`az spring-cloud app create` 命令目前不是幂等的。  建议将此工作流用于现有的 Azure Spring Cloud 应用和实例。

请使用以下 Azure CLI 命令来进行准备：
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>直接使用 Azure CLI 进行部署
请在存储库中创建 `.github/workflow/main.yml` 文件：

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>使用 Azure CLI action 进行部署
Az `run` 命令将使用最新版本的 Azure CLI。 如果有重大更改，也可以将特定版本的 Azure CLI 与 azure/CLI `action` 配合使用。 

> [!Note] 
> 此命令将会在一个新容器中运行，所以 `env` 将不起作用，并且跨操作文件访问可能会有额外限制。

在存储库中创建 .github/workflow/main.yml 文件：
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>使用 Maven 插件进行部署
另一种选择是使用 [Maven 插件](./spring-cloud-quickstart.md)来部署 Jar 并更新应用设置。 `mvn azure-spring-cloud:deploy` 命令是幂等的，将会在需要时自动创建应用。 你无需提前创建相应的应用。

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>运行工作流

在将 `.github/workflow/main.yml` 推送到 GitHub 后，GitHub Actions 应该会自动启用。 在推送新提交时，将会触发该操作。 如果你在浏览器中创建此文件，你的操作应该已经运行了。

若要验证是否已启用该操作，请单击 GitHub 存储库页上的“操作”选项卡：

![验证操作是否已启用](./media/github-actions/actions3.png)

如果在存在错误的情况下（例如，如果尚未设置 Azure 凭据）运行操作，则可以在修复该错误后重新运行检查。 在 GitHub 存储库页上，单击“操作”，选择特定的工作流任务，然后单击“重新运行检查”按钮来重新运行检查 ：

![重新运行检查](./media/github-actions/actions4.png)

## <a name="next-steps"></a>后续步骤

* [用于 Spring Cloud GitHub Actions 的 Key Vault](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory 服务主体](/cli/ad/sp?preserve-view=true&view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [适用于 Azure 的 GitHub Actions](https://github.com/Azure/actions/)

