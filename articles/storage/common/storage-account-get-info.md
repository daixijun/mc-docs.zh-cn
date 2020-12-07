---
title: 使用 .NET 获取存储帐户类型和 SKU 名称
titleSuffix: Azure Storage
description: 了解如何使用 .NET 客户端库获取 Azure 存储帐户类型和 SKU 名称。
services: storage
author: WenJason
ms.author: v-jay
origin.date: 11/12/2020
ms.date: 11/30/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: d39bcaa74d8f059a5a0d19d74d76b3431018f8a9
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96153032"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>使用 .NET 获取存储帐户类型和 SKU 名称

本文介绍如何使用[用于 .NET 的 Azure 存储客户端库](https://docs.azure.cn/zh-cn/dotnet/api/overview/storage?view=azure-dotnet)获取 Blob 的 Azure 存储帐户类型和 SKU 名称。

## <a name="about-account-type-and-sku-name"></a>关于帐户类型和 SKU 名称

**帐户类型**：有效的帐户类型包括 `BlobStorage`、`BlockBlobStorage`、`FileStorage`、`Storage` 和 `StorageV2`。 [Azure 存储帐户概述](storage-account-overview.md)提供详细信息，包括对各种存储帐户的说明。

**SKU 名称**：有效的 SKU 名称包括 `Premium_LRS`、`Standard_GRS`、`Standard_LRS` 和 `Standard_RAGRS`。

## <a name="retrieve-account-information"></a>检索帐户信息

以下代码示例检索并显示只读帐户属性。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

若要获取与 Blob 关联的存储帐户类型和 SKU 名称，请调用 [GetAccountInfo](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) 或 [GetAccountInfoAsync](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync) 方法。

```csharp
private static async Task GetAccountInfoAsync(string connectStr)
{
    try
    {
        BlobServiceClient blobServiceClient = new BlobServiceClient(connectStr);

        // Get the blob's storage account properties.
        AccountInfo acctInfo = await blobServiceClient.GetAccountInfoAsync();

        // Display the properties.
        Console.WriteLine("Account info");
        Console.WriteLine($" AccountKind: {acctInfo.AccountKind}");
        Console.WriteLine($"     SkuName: {acctInfo.SkuName}");
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine($"HTTP error code {ex.Status}: {ex.ErrorCode}");
        Console.WriteLine(ex.Message);
        Console.ReadLine();
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

若要获取与 Blob 关联的存储帐户类型和 SKU 名称，请调用 [GetAccountProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) 或 [GetAccountPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) 方法。

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>后续步骤

了解可以通过 [Azure 门户](https://portal.azure.cn)和 Azure REST API 在存储帐户上执行的其他操作。

- [“获取帐户信息”操作 (REST)](https://docs.microsoft.com/rest/api/storageservices/get-account-information)
