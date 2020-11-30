---
title: 通过编写代码提取共享访问签名令牌 | Azure Key Vault
description: 托管存储帐户功能在 Azure Key Vault 与 Azure 存储帐户之间提供无缝集成。 此示例使用 Azure SDK for .NET 管理 SAS 令牌。
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: v-tawe
manager: rkarlin
origin.date: 09/10/2019
ms.date: 11/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3e487fe9057fd9299f24ab7ed9d30b2cecd1531e
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96299999"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>创建 SAS 定义，并通过编写代码提取共享访问签名令牌

你可以使用存储在密钥保险库中的共享访问签名 (SAS) 令牌来管理存储帐户。 有关详细信息，请参阅[使用 SAS 授予对 Azure 存储资源的有限访问权限](../../storage/common/storage-sas-overview.md)。

> [!NOTE]
> 建议使用 [Azure 基于角色的访问控制 (Azure RBAC)](../../storage/common/storage-auth-aad.md) 保护存储帐户，以便获得卓越的安全性，并通过共享密钥授权轻松使用。

本文展示了用于创建 SAS 定义和获取 SAS 令牌的 .NET 代码的示例。 请参阅我们的 [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) 示例，获取完整详细信息，包括为密钥保管库托管的存储帐户生成的客户端的信息。 有关如何创建和存储 SAS 令牌的信息，请参阅[使用密钥保管库和 Azure CLI 管理存储帐户密钥](overview-storage-keys.md)或[使用密钥保管库和 Azure PowerShell 管理存储帐户密钥](overview-storage-keys-powershell.md)。

## <a name="code-samples"></a>代码示例

在以下示例中，将创建 SAS 模板：

```csharp
private static string BuildSasDefinitionTemplate(bool readOnly) =>
    new StringBuilder("sv=2018-03-28")  // service version
        .Append("&spr=https")           // HTTPS only
        .Append("&ss=bf")               // blobs and files only
        .Append("&srt=o")               // applies to objects only
        .Append(readOnly ? "&sp=r" : "&sp=rw")  // read-only or read-write
        .ToString();
```

使用此模板，可以通过使用...创建 SAS 定义 

```csharp
string sasDefinitionName = BuildSasDefinitionName(Tag, readOnly, duration);
SasDefinitionAttributes sasDefinitionAttributes = new SasDefinitionAttributes
{
    Enabled = true,
};

Dictionary<string, string> tags = new Dictionary<string, string>
{
    [Tag] = "1",
};

SasDefinitionBundle createdSasDefinition = await storageClient.SetSasDefinitionAsync(
    storageAccountName,
    sasDefinitionName,
    sasTemplate,
    SasTokenType.Account,
    duration,
    sasDefinitionAttributes,
    tags,
    s_cancellationTokenSource.Token);
```

创建 SAS 定义后，就可以使用 `SecretClient` 检索 SAS 令牌，比如机密。 需要在机密名称前面加上存储帐户名称，后跟短划线：

```csharp
// Build our SAS template, get an existing SAS definition, or create a new one.
string sasTemplate = BuildSasDefinitionTemplate(readOnly);
string sasDefinitionName = await GetOrCreateSasDefinitionAsync(storageClient, storageAccountName, sasTemplate, days, readOnly);

// Now we can create a SecretClient and generate a new SAS token from the storage account and SAS definition names.
SecretClient secretClient = new SecretClient(vaultUri, credential, options);
KeyVaultSecret sasToken = await secretClient.GetSecretAsync($"{storageAccountName}-{sasDefinitionName}", cancellationToken: s_cancellationTokenSource.Token);
```

如果共享访问签名令牌即将过期，可以再次获取相同的机密并生成新的令牌。

要了解如何使用从 Key Vault SAS 中检索的令牌访问 Azure 存储服务，请参阅[使用帐户 SAS 访问 Blob 服务](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client)

> [!NOTE]
> 如果应用从存储收到 403，则应用需要准备好刷新 SAS，使你能够处理密钥被泄露的情况，并且轮替操作需要比正常轮替期更频繁。 

## <a name="next-steps"></a>后续步骤
- 了解如何[使用 SAS 授予对 Azure 存储资源的有限访问权限](../../storage/common/storage-sas-overview.md)。
- 了解如何[使用密钥保管库和 Azure CLI 管理存储帐户密钥](overview-storage-keys.md)或 [Azure PowerShell](overview-storage-keys-powershell.md)。
- 请参阅[托管存储帐户密钥示例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
