---
title: 快速入门 - 适用于 .NET 的 Azure Key Vault 客户端库 (SDK v3)
description: 了解如何使用 .NET 客户端库 (v3) 在 Azure 密钥保管库中创建、检索和删除机密
author: msmbaldwin
ms.author: v-tawe
origin.date: 11/05/2019
ms.date: 09/15/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 2c0fdb403f27719ffddc66fafaaed8408cf91f6a
ms.sourcegitcommit: 87b6bb293f39c5cfc2db6f38547220a13816d78f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96431168"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>快速入门：适用于 .NET 的 Azure Key Vault 客户端库 (SDK v3)

适用于 .NET 的 Azure Key Vault 客户端库入门。 请遵循以下步骤安装包并试用基本任务的示例代码。

> [!NOTE]
> 此快速入门使用 v3.0.4 版本的 Microsoft.Azure.KeyVault 客户端库。 若要使用 Key Vault 客户端库的最新版本，请参阅[适用于 .NET 的 Azure Key Vault 客户端库 (SDK v4)](quick-create-net.md)。 

Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 使用适用于 .NET 的 Key Vault 客户端库可以：

- 提高安全性以及控制密钥和密码。
- 在几分钟内创建并导入加密密钥。
- 通过云扩展和全局冗余减少延迟。
- 简化和自动化与 TLS/SSL 证书相关的任务。

<!-- - Use FIPS 140-2 Level 2 validated HSMs. -->

[API 参考文档](https://docs.microsoft.com/dotnet/api/overview/key-vault) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> 每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。


## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [试用版订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。
* [.NET Core 3.1 SDK 或更高版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Azure CLI](/cli/install-azure-cli) 或 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)

本快速入门假定你正在 Windows 终端（例如 [PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6)、[Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6)）中运行 `dotnet`、[Azure CLI](/cli/install-azure-cli) 和 Windows 命令。

## <a name="setting-up"></a>设置

### <a name="create-new-net-console-app"></a>创建新的 .NET 控制台应用

在控制台窗口中，使用 `dotnet new` 命令新建名为 `akv-dotnet` 的 .NET 控制台应用。


```console
dotnet new console -n akvdotnet
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>安装包

在控制台窗口中，安装适用于 .NET 的 Azure Key Vault 客户端库：

```console
dotnet add package Microsoft.Azure.KeyVault
```

本快速入门还需要安装以下包：

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault

本快速入门使用预先创建的 Azure Key Vault。 可以遵循 [Azure CLI 快速入门](quick-create-cli.md)、[Azure PowerShell 快速入门](quick-create-powershell.md)或 [Azure 门户快速入门](quick-create-portal.md)中的步骤创建 Key Vault。 或者，只需运行以下 Azure CLI 命令。

> [!Important]
> 每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。

```azurecli
az group create --name "myResourceGroup" -l "ChinaEast"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>创建服务主体

对基于云的 .NET 应用程序进行身份验证的最简单方法是使用托管标识；有关详细信息，请参阅[使用应用服务托管标识访问 Azure Key Vault](../general/managed-identity.md)。 

不过，为了简单起见，本快速入门创建了一个需要使用服务主体和访问控制策略的 .NET 控制台应用程序。 服务主体要求使用格式为“http://&lt;my-unique-service-principal-name&gt;”的唯一名称。

使用 Azure CLI [az ad sp create-for-rbac](/cli/ad/sp#az-ad-sp-create-for-rbac) 命令创建服务主体：

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

此操作将返回一系列键/值对。 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.chinacloudapi.cn",
  "resourceManagerEndpointUrl": "https://management.chinacloudapi.cn/",
  "sqlManagementEndpointUrl": "https://management.core.chinacloudapi.cn:8443/",
  "galleryEndpointUrl": "https://gallery.chinacloudapi.cn/",
  "managementEndpointUrl": "https://management.core.chinacloudapi.cn/"
}
```

请记下 clientId 和 clientSecret，因为在下面的[向密钥保管库进行身份验证](#authenticate-to-your-key-vault)步骤中将要用到。

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>为服务主体授予对 Key Vault 的访问权限

通过将 clientId 传递给 [az keyvault set-policy](/cli/keyvault#az-keyvault-set-policy) 命令，为密钥保管库创建授予服务主体权限的访问策略。 授予服务主体对密钥和机密的 get、list 和 set 权限。

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>对象模型

使用适用于 .NET 的 Azure Key Vault 客户端库可以管理密钥和相关的资产（例如证书和机密）。 以下代码示例演示如何设置机密和检索机密。

[https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet ) 中提供了整个控制台应用。

## <a name="code-examples"></a>代码示例

### <a name="add-directives"></a>添加指令

将以下指令添加到代码的顶部：

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Azure.KeyVault;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
```

### <a name="authenticate-to-your-key-vault"></a>对 Key Vault 进行身份验证

本 .NET 快速入门依赖于使用环境变量来存储不应放入代码中的凭据。 

在生成和运行应用之前，请使用 `setx` 命令将 `akvClientId`、`akvClientSecret`、`akvTenantId` 和 `akvSubscriptionId` 环境变量设置为上面记下的值。

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

将这些环境变量分配到代码中的字符串，然后通过将这些字符串传递给 [KeyVaultClient 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient)，来对应用程序进行身份验证：

```csharp
string clientId = Environment.GetEnvironmentVariable("akvClientId");
string clientSecret = Environment.GetEnvironmentVariable("akvClientSecret");

KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
{
    var adCredential = new ClientCredential(clientId, clientSecret);
    var authenticationContext = new AuthenticationContext(authority, null);
    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
});
```

### <a name="save-a-secret"></a>保存机密

对应用程序进行身份验证后，可以使用 [SetSecretAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)将机密放入 Key Vault。这需要使用 Key Vault 的 URL，其格式为 `https://<your-unique-keyvault-name>.vault.azure.cn/secrets/`。 还需要使用机密的名称 - 我们使用了“mySecret”。 

```csharp
await kvClient.SetSecretAsync($"{kvURL}", secretName, secretValue);
```

可以使用 [az keyvault secret show](/cli/keyvault/secret#az-keyvault-secret-show) 命令来验证是否设置了机密：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>检索机密

现在，可以使用 [GetSecretAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)检索以前设置的值

```csharp
var keyvaultSecret = await kvClient.GetSecretAsync($"{kvURL}", secretName).ConfigureAwait(false);
```

机密现已保存为 `keyvaultSecret.Value;`。

## <a name="clean-up-resources"></a>清理资源

可以使用 Azure CLI 或 Azure PowerShell 来删除不再需要的 Key Vault 和相应的资源组。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 Key Vault、存储了一个机密，然后检索了该机密。 请查看 [GitHub 中的整个控制台应用](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet)。

若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 实现[使用 .NET 对 Azure Key Vault 进行服务到服务的身份验证](../general/service-to-service-authentication.md)
- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 查看 [Azure Key Vault 最佳做法](../general/best-practices.md)
