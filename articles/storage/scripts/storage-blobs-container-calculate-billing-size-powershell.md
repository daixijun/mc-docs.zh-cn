---
title: Azure PowerShell 脚本示例 - 计算要计费的 Blob 容器总大小 | Microsoft Docs
description: 出于计费目的计算 Azure Blob 存储中容器的总大小。
services: storage
author: WenJason
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
origin.date: 12/29/2020
ms.date: 01/18/2021
ms.author: v-jay
ms.openlocfilehash: acaeff428e08363a1c39c47aa1714896d4b6f5e4
ms.sourcegitcommit: f086abe8bd2770ed10a4842fa0c78b68dbcdf771
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163199"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>计算要计费的 Blob 容器总大小

此脚本出于估算计费成本的目的，计算 Azure Blob 存储中的容器大小。 此脚本计算容器中各 blob 的大小总和。

> [!IMPORTANT]
> 本文中提供的示例脚本可能无法准确地计算 blob 快照的账单大小。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> 此 PowerShell 脚本出于计费目的计算容器大小。 如果要出于其他目的计算容器大小，请参阅[计算 Blob 存储容器的总大小](../scripts/storage-blobs-container-calculate-size-powershell.md)，获取进行估算的更简单的脚本。

## <a name="determine-the-size-of-the-blob-container"></a>确定 Blob 容器的大小

Blob 容器的总大小包括容器自身大小，以及容器内所有 blob 的大小。

下述部分介绍 Blob 容器和 blob 的存储容量计算方法。 在下一部分中，Len(X) 表示字符串中的字符数。

### <a name="blob-containers"></a>Blob 容器

下述计算介绍如何估算每个 Blob 容器使用的存储量：

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

以下是明细信息：

* 每个容器的开销（48 字节）包括最后修改时间、权限、公共设置和一些系统元数据。

* 容器名称以 Unicode 形式存储，因此字节数按字符数乘以 2 计算。

* 对于存储的每个 Blob 容器元数据块，我们将存储名称 (ASCII) 长度，再加上字符串值的长度。

* 每个签名标识符（512 字节）包括签名标识符名称、开始时间、到期时间和权限。

### <a name="blobs"></a>Blob

下述计算显示如何估算每个 blob 使用的存储量。

* 块 blob（基本 blob 或快照）：

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* 页 blob（基本 blob 或快照）：

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

以下是明细信息：

* Blob 的开销为 124 字节，其中包括：
    - 上次修改时间
    - 大小
    - Cache-Control
    - Content-Type
    - Content-Language
    - Content-Encoding
    - Content-MD5
    - 权限
    - 快照信息
    - Lease
    - 某些系统元数据

* Blob 名称以 Unicode 形式存储，因此字节数按字符数乘以 2 计算。

* 对于每个存储的元数据块，添加名称长度（以 ASCII 码存储），再加上字符串值的长度。

* 对于块 blob：
  * 块列表为 8 字节。
  * 块数乘以块 ID 大小（按字节计）。
  * 所有已提交和未提交块中数据的大小。

    >[!NOTE]
    >使用快照时，大小仅包括此基本或快照 blob 的唯一数据。 如果未提交块在一周后未被使用，则回收到垃圾桶。 之后不计入账单。

* 对于页 blob：
  * 字节数按具有数据的不连续页面范围数乘以 12 计算。 这是在调用 GetPageRanges API 时看到的唯一页面范围数。

  * 所有存储页面中的数据大小（按字节计）。

    >[!NOTE]
    >使用快照时，大小仅包含要计数的基本 blob 或快照 blob 的唯一页面。

## <a name="sample-script"></a>示例脚本

```powershell

# this script will show how to get the total size of the blobs in a container
# before running this, you need to create a storage account, create a container,
#    and upload some blobs into the container
# note: this retrieves all of the blobs in the container in one command.
#       connect Azure with Login-AzAccount -EnvironmentName AzureChinaCloud before you run the script.
#       requests sent as part of this tool will incur transactional costs.
# command line usage: script.ps1 -ResourceGroup {YourResourceGroupName} -StorageAccountName {YourAccountName} -ContainerName {YourContainerName}
#


param(
    [Parameter(Mandatory=$true)]
    [string]$ResourceGroup,

    [Parameter(Mandatory=$true)]
    [string]$StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]$ContainerName
)

#Set-StrictMode will cause Get-AzStorageBlob returns result in different data types when there is only one blob
#Set-StrictMode -Version 2

$VerbosePreference = "Continue"

if((Get-Module -ListAvailable Az.Storage) -eq $null)
{
    throw "Azure Powershell not found! Please install from https://docs.microsoft.com/en-us/powershell/azure/install-Az-ps"
}

# function Retry-OnRequest
function Retry-OnRequest
{
    param(
        [Parameter(Mandatory=$true)]
        $Action)
    
    # It could encounter various of temporary errors, like network errors, or storage server busy errors.
    # Should retry the request on transient errors

    # Retry on storage server timeout errors
    $clientTimeOut = New-TimeSpan -Minutes 15
    $retryPolicy = New-Object -TypeName Microsoft.Azure.Storage.RetryPolicies.ExponentialRetry -ArgumentList @($clientTimeOut, 10)
    $requestOption = @{}
    $requestOption.RetryPolicy = $retryPolicy

    # Retry on temporary network errors
    $shouldRetryOnException = $false
    $maxRetryCountOnException = 3

    do
    {
        try
        {
            return $Action.Invoke($requestOption)
        }
        catch
        {
            if ($_.Exception.InnerException -ne $null -And $_.Exception.InnerException.GetType() -Eq [System.TimeoutException] -And $maxRetryCountOnException -gt 0)
            {
                $shouldRetryOnException = $true
                $maxRetryCountOnException--
            }
            else
            {
                $shouldRetryOnException = $false
                throw
            }
        }
    }
    while ($shouldRetryOnException)

}

# function Get-BlobBytes

function Get-BlobBytes
{
    param(
        [Parameter(Mandatory=$true)]
        $Blob,
        [Parameter(Mandatory=$false)]
        [bool]$IsPremiumAccount = $false)

    # Base + blobname
    $blobSizeInBytes = 124 + $Blob.Name.Length * 2

    # Get size of metadata
    $metadataEnumerator=$Blob.ICloudBlob.Metadata.GetEnumerator()
    while($metadataEnumerator.MoveNext())
    {
        $blobSizeInBytes += 3 + $metadataEnumerator.Current.Key.Length + $metadataEnumerator.Current.Value.Length
    }

    if (!$IsPremiumAccount)
    {
        if($Blob.BlobType -eq [Microsoft.Azure.Storage.Blob.BlobType]::BlockBlob)
        {
            $blobSizeInBytes += 8
            # Default is Microsoft.Azure.Storage.Blob.BlockListingFilter.Committed. Need All
            $action = { param($requestOption) return $Blob.ICloudBlob.DownloadBlockList([Microsoft.Azure.Storage.Blob.BlockListingFilter]::All, $null, $requestOption) }

            $blocks=Retry-OnRequest $action

            if ($null -eq $blocks)
            {
                $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
            }
            else
            {
                $blocks | ForEach-Object { $blobSizeInBytes += $_.Length + $_.Name.Length }
            }
        }
        elseif($Blob.BlobType -eq [Microsoft.Azure.Storage.Blob.BlobType]::PageBlob)
        {
            # It could cause server time out issue when trying to get page ranges of highly fragmented page blob
            # Get page ranges in segment can mitigate chance of meeting such kind of server time out issue
            # See https://blogs.msdn.microsoft.com/windowsazurestorage/2012/03/26/getting-the-page-ranges-of-a-large-page-blob-in-segments/ for details.
            $pageRangesSegSize = 148 * 1024 * 1024L
            $totalSize = $Blob.ICloudBlob.Properties.Length
            $pageRangeSegOffset = 0

            $pageRangesTemp = New-Object System.Collections.ArrayList

            while ($pageRangeSegOffset -lt $totalSize)
            {
                $action = {param($requestOption) return $Blob.ICloudBlob.GetPageRanges($pageRangeSegOffset, $pageRangesSegSize, $null, $requestOption) }

                Retry-OnRequest $action | ForEach-Object { $pageRangesTemp.Add($_) }  | Out-Null
                $pageRangeSegOffset += $pageRangesSegSize
            }

            $pageRanges = New-Object System.Collections.ArrayList

            foreach ($pageRange in $pageRangesTemp)
            {
                if($lastRange -eq $Null)
                {
                    $lastRange = New-Object PageRange
                    $lastRange.StartOffset = $pageRange.StartOffset
                    $lastRange.EndOffset =  $pageRange.EndOffset
                }
                else
                {
                    if (($lastRange.EndOffset + 1) -eq $pageRange.StartOffset)
                    {
                        $lastRange.EndOffset = $pageRange.EndOffset
                    }
                    else
                    {
                        $pageRanges.Add($lastRange)  | Out-Null
                        $lastRange = New-Object PageRange
                        $lastRange.StartOffset = $pageRange.StartOffset
                        $lastRange.EndOffset =  $pageRange.EndOffset
                    }
                }
            }

            $pageRanges.Add($lastRange) | Out-Null
            $pageRanges |  ForEach-Object {
                    $blobSizeInBytes += 12 + $_.EndOffset - $_.StartOffset
                }
        }
        else
        {
            $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
        }
        return $blobSizeInBytes
    }
    else
    {
        $blobSizeInBytes += $Blob.ICloudBlob.Properties.Length
    }
    return $blobSizeInBytes
}

# function Get-ContainerBytes

function Get-ContainerBytes
{
    param(
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Storage.Blob.CloudBlobContainer]$Container,
        [Parameter(Mandatory=$false)]
        [bool]$IsPremiumAccount = $false)

    # Base + name of container
    $containerSizeInBytes = 48 + $Container.Name.Length*2

    # Get size of metadata
    $metadataEnumerator = $Container.Metadata.GetEnumerator()
    while($metadataEnumerator.MoveNext())
    {
        $containerSizeInBytes += 3 + $metadataEnumerator.Current.Key.Length + $metadataEnumerator.Current.Value.Length
    }

    # Get size for SharedAccessPolicies
    $containerSizeInBytes += $Container.GetPermissions().SharedAccessPolicies.Count * 512

    # Calculate size of all blobs.
    $blobCount = 0
    $Token = $Null
    $MaxReturn = 5000

    do {
        $Blobs = Get-AzStorageBlob -Context $storageContext -Container $Container.Name -MaxCount $MaxReturn -ContinuationToken $Token
        if($Blobs -eq $Null) { break }

        #Set-StrictMode will cause Get-AzStorageBlob returns result in different data types when there is only one blob
        if($Blobs.GetType().Name -eq "AzureStorageBlob")
        {
            $Token = $Null
        }
        else
        {
            $Token = $Blobs[$Blobs.Count - 1].ContinuationToken;
        }

        $Blobs | ForEach-Object {
                $blobSize = Get-BlobBytes $_ $IsPremiumAccount
                $containerSizeInBytes += $blobSize
                $blobCount++

                if(($blobCount % 1000) -eq 0)
                {
                    Write-Verbose("Counting {0} Sizing {1} " -f $blobCount, $containerSizeInBytes)
                }
            }
    }
    While ($Token -ne $Null)

    return @{ "containerSize" = $containerSizeInBytes; "blobCount" = $blobCount }
}

#Login-AzAccount -EnvironmentName AzureChinaCloud

$storageAccount = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -ErrorAction SilentlyContinue
if($storageAccount -eq $null)
{
    throw "The storage account specified does not exist in this subscription."
}

$storageContext = $storageAccount.Context

if (-not ([System.Management.Automation.PSTypeName]'PageRange').Type)
{
    $Source = "
        public class PageRange
        {
            public long StartOffset;
            public long EndOffset;
        }"
    Add-Type -TypeDefinition $Source
}

$containers = New-Object System.Collections.ArrayList
if($ContainerName.Length -ne 0)
{
    $container = Get-AzStorageContainer -Context $storageContext -Name $ContainerName -ErrorAction SilentlyContinue |
        ForEach-Object { $containers.Add($_) } | Out-Null
}
else
{
    Get-AzStorageContainer -Context $storageContext | ForEach-Object { $containers.Add($_) } | Out-Null
}

$sizeInBytes = 0
$IsPremiumAccount = ($storageAccount.Sku.Tier -eq "Premium")

if($containers.Count -gt 0)
{
    $containers | ForEach-Object {
        Write-Output("Calculating container {0} ..." -f $_.CloudBlobContainer.Name)
        $result = Get-ContainerBytes $_.CloudBlobContainer $IsPremiumAccount
        $sizeInBytes += $result.containerSize

        Write-Output("Container '{0}' with {1} blobs has a sizeof {2:F2} MB." -f $_.CloudBlobContainer.Name,$result.blobCount,($result.containerSize/1MB))
    }
}
else
{
    Write-Warning "No containers found to process in storage account '$StorageAccountName'."
}
```

## <a name="next-steps"></a>后续步骤

- 请参阅[计算 Blob 存储容器的总大小](../scripts/storage-blobs-container-calculate-size-powershell.md)，获取估算容器大小的简单脚本。

- 有关 Azure 存储计费的详细信息，请参阅[了解 Windows Azure 存储计费](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)。

- 有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。

- 有关其他存储 PowerShell 脚本示例，可查看[适用于 Azure 存储的 PowerShell 示例](../blobs/storage-samples-blobs-powershell.md)。
