---
title: 使用 .NET 为容器或 Blob 创建用户委托 SAS
titleSuffix: Azure Storage
description: 了解如何使用适用于 Azure 存储的 .NET 客户端库通过 Azure Active Directory 凭据创建用户委托 SAS。
services: storage
author: WenJason
ms.service: storage
ms.topic: how-to
origin.date: 12/11/2020
ms.date: 01/18/2021
ms.author: v-jay
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 13391e36f08c5527670b39573812c5067ffc6191
ms.sourcegitcommit: f086abe8bd2770ed10a4842fa0c78b68dbcdf771
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163140"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>使用 .NET 为容器或 blob 创建用户委托 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文介绍如何使用适用于 12 版 .NET 的 Azure 存储客户端库通过 Azure Active Directory (Azure AD) 凭据为容器或 Blob 创建用户委托 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>分配可访问数据的 Azure 角色

当 Azure AD 安全主体尝试访问 Blob 数据时，该安全主体必须具有对资源的访问权限。 不管安全主体是 Azure 中的托管标识还是在开发环境中运行代码的 Azure AD 用户帐户，都必须为安全主体分配一个 Azure 角色，由该角色授权访问 Azure 存储中的 Blob 数据。 若要了解如何通过 Azure RBAC 分配权限，请参阅[使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)中的“分配 Azure 角色以授予访问权限”部分。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

若要详细了解如何使用 Azure 存储中的 Azure 标识客户端库进行身份验证，请参阅 [使用 Azure Active Directory 和 Azure 资源的托管标识授权访问 Blob 和队列](../common/storage-auth-aad-msi.md?toc=%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)中标题为 **使用 Azure 标识库进行身份验证** 的部分。

## <a name="get-an-authenticated-token-credential"></a>获取经过身份验证的令牌凭据

若要获取令牌凭据，以便代码用它来授权对 Azure 存储的请求，请创建 [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential) 类的实例。

以下代码片段演示了如何获取经身份验证的令牌凭据并使用它来为 Blob 存储创建服务客户端：

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.chinacloudapi.cn", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>获取用户委托密钥

每个 SAS 均使用密钥进行签名。 若要创建用户委托 SAS，必须首先请求用户委托密钥，然后使用该密钥对 SAS 进行签名。 用户委托密钥与用于对服务 SAS 或帐户 SAS 签名的帐户密钥类似，只不过它依赖于 Azure AD 凭据。 当客户端使用 OAuth 2.0 令牌请求用户委托密钥时，Azure 存储将代表该用户返回用户委托密钥。

拥有用户委托密钥后，你就可以在该密钥的生存期内使用该密钥创建任意数量的用户委托共享访问签名。 用户委托密钥独立于用于获取该密钥的 OAuth 2.0 令牌，因此只要密钥仍有效，就不必续订令牌。 你可以指定密钥最长在 7 天内有效。

使用以下方法之一请求用户委托密钥：

- [GetUserDelegationKey](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

下面的代码片段将获取用户委托密钥并写出其属性：

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>为 blob 获取用户委托 SAS

下面的代码示例展示了用于对安全主体进行身份验证和为 blob 创建用户委托 SAS 的完整代码：

```csharp
async static Task<Uri> GetUserDelegationSasBlob(BlobClient blobClient)
{
    BlobServiceClient blobServiceClient =
        blobClient.GetParentBlobContainerClient().GetParentBlobServiceClient();

    // Get a user delegation key for the Blob service that's valid for 7 days.
    // You can use the key to generate any number of shared access signatures 
    // over the lifetime of the key.
    UserDelegationKey userDelegationKey =
        await blobServiceClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                          DateTimeOffset.UtcNow.AddDays(7));

    // Create a SAS token that's valid for 7 days.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = blobClient.BlobContainerName,
        BlobName = blobClient.Name,
        Resource = "b",
        ExpiresOn = DateTimeOffset.UtcNow.AddDays(7)
    };

    // Specify read and write permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read |
                              BlobSasPermissions.Write);

    // Add the SAS token to the blob URI.
    BlobUriBuilder blobUriBuilder = new BlobUriBuilder(blobClient.Uri)
    {
        // Specify the user delegation key.
        Sas = sasBuilder.ToSasQueryParameters(userDelegationKey, 
                                              blobServiceClient.AccountName)
    };

    Console.WriteLine("Blob user delegation SAS URI: {0}", blobUriBuilder);
    Console.WriteLine();
    return blobUriBuilder.ToUri();
}
```

下面的示例通过模拟的客户端应用程序测试在上一个示例中创建的用户委托 SAS。 如果 SAS 有效，客户端应用程序将能够读取 Blob 的内容。 如果 SAS 无效（例如已过期），则 Azure 存储将返回错误代码 403（禁止访问）。

```csharp
static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing a read operation using the blob SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        Console.WriteLine("Blob contents:");

        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

## <a name="get-a-user-delegation-sas-for-a-container"></a>为容器获取用户委托 SAS

下面的代码示例演示如何为容器生成用户委派 SAS：

```csharp
async static Task<Uri> GetUserDelegationSasContainer(BlobContainerClient blobContainerClient)
{
    BlobServiceClient blobServiceClient = blobContainerClient.GetParentBlobServiceClient();

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures 
    // over the lifetime of the key.
    UserDelegationKey userDelegationKey =
        await blobServiceClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                          DateTimeOffset.UtcNow.AddDays(7));

    // Create a SAS token that's valid for seven days.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = blobContainerClient.Name,
        Resource = "c",
        ExpiresOn = DateTimeOffset.UtcNow.AddDays(7)
    };

    // Specify racwl permissions for the SAS.
    sasBuilder.SetPermissions(
        BlobContainerSasPermissions.Read |
        BlobContainerSasPermissions.Add |
        BlobContainerSasPermissions.Create |
        BlobContainerSasPermissions.Write |
        BlobContainerSasPermissions.List
        );

    // Add the SAS token to the container URI.
    BlobUriBuilder blobUriBuilder = new BlobUriBuilder(blobContainerClient.Uri)
    {
        // Specify the user delegation key.
        Sas = sasBuilder.ToSasQueryParameters(userDelegationKey,
                                              blobServiceClient.AccountName)
    };

    Console.WriteLine("Container user delegation SAS URI: {0}", blobUriBuilder);
    Console.WriteLine();
    return blobUriBuilder.ToUri();
}
```

下面的示例通过模拟的客户端应用程序测试在上一个示例中创建的用户委托 SAS。 如果 SAS 有效，客户端应用程序将能够读取 Blob 的内容。 如果 SAS 无效（例如已过期），则 Azure 存储将返回错误代码 403（禁止访问）。

```csharp
private static async Task ListBlobsWithSasAsync(Uri sasUri)
{
    // Try performing a listing operation using the container SAS provided.

    // Create a container client object for blob operations.
    BlobContainerClient blobContainerClient = new BlobContainerClient(sasUri, null);

    // List blobs in the container.
    try
    {
        // Call the listing operation and return pages of the specified size.
        var resultSegment = blobContainerClient.GetBlobsAsync().AsPages();

        // Enumerate the blobs returned for each page.
        await foreach (Azure.Page<BlobItem> blobPage in resultSegment)
        {
            foreach (BlobItem blobItem in blobPage.Values)
            {
                Console.WriteLine("Blob name: {0}", blobItem.Name);
            }
            Console.WriteLine();
        }

        Console.WriteLine();
        Console.WriteLine("Blob listing operation succeeded for SAS {0}", sasUri);
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Blob listing operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另请参阅

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)
- [获取用户委托密钥操作](https://docs.microsoft.com/rest/api/storageservices/get-user-delegation-key)
- [创建用户委托 SAS (REST API)](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)
