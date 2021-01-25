---
title: 使用 AzCopy v10 在 Azure 存储帐户之间复制 Blob | Microsoft Docs
description: 本文包含 AzCopy 示例命令的集合，可帮助你在存储帐户之间复制 Blob。
author: WenJason
ms.service: storage
ms.topic: how-to
origin.date: 12/08/2020
ms.date: 01/18/2021
ms.author: v-jay
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: a891c8920e900048a89bc47ff93497206299335f
ms.sourcegitcommit: f086abe8bd2770ed10a4842fa0c78b68dbcdf771
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163231"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>使用 AzCopy v10 在 Azure 存储帐户之间复制 Blob

可以使用 AzCopy v10 命令行实用程序在存储帐户之间复制 Blob、目录和容器。 

若要查看其他类型任务（如上传文件、下载 Blob 以及与 Blob 存储同步）的示例，请参阅本文的[后续步骤](#next-steps)部分中提供的链接。

AzCopy 使用[服务器到服务器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)，因此，数据会直接在存储服务器之间复制。 这些复制操作不会占用计算机的网络带宽。

若要下载 AzCopy 并了解如何向存储服务提供授权凭据，请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)。 

## <a name="guidelines"></a>指南

将以下指南应用于 AzCopy 命令。 

- 客户端必须具有对源和目标存储帐户的网络访问权限。 若要了解如何配置每个存储帐户的网络设置，请参阅[配置 Azure 存储防火墙和虚拟网络](storage-network-security.md?toc=/storage/blobs/toc.json)。

- 将 SAS 令牌追加到每个源 URL。 

  如果使用 Azure Active Directory (Azure AD) 提供授权凭据，则只能从目标 URL 中省略 SAS 令牌。 请确保已在目标帐户中设置了适当的角色。 请参阅[选项 1：使用 Azure Active Directory](storage-use-azcopy-v10.md?toc=/storage/blobs/toc.json#option-1-use-azure-active-directory)。 

  本文中的示例假设你已使用 Azure AD 验证了身份，因此这些示例省略了目标 URL 中的 SAS 令牌。

-  如果复制到高级块 Blob 存储帐户，请通过将 `s2s-preserve-access-tier` 设置为 `false`（例如：`--s2s-preserve-access-tier=false`），在复制操作中省略 Blob 的访问层。 高级块 Blob 存储帐户不支持访问层。 

- 如果复制到具有分层命名空间的帐户或从其中复制，请在 URL 语法中使用 `blob.core.chinacloudapi.cn` 而不是 `dfs.core.chinacloudapi.cn`。 [Data Lake Storage 上的多协议访问](../blobs/data-lake-storage-multi-protocol-access.md)使你可以使用 `blob.core.chinacloudapi.cn`，这是帐户间复制方案的唯一受支持的语法。 

- 可以通过设置 `AZCOPY_CONCURRENCY_VALUE` 环境变量的值来提高复制操作的吞吐量。 有关详细信息，请参阅[优化吞吐量](storage-use-azcopy-configure.md#optimize-throughput)。 

## <a name="copy-a-blob"></a>复制 Blob

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将 Blob 复制到另一个存储帐户。 

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.chinacloudapi.cn/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.chinacloudapi.cn/<container-name>/<blob-path>'` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.chinacloudapi.cn/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.chinacloudapi.cn/mycontainer/myTextFile.txt'` |

复制操作是同步的，因此，当命令返回时，表示已复制所有文件。 

## <a name="copy-a-directory"></a>复制目录

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将目录复制到另一个存储帐户。 

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.chinacloudapi.cn/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.chinacloudapi.cn/<container-name>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.chinacloudapi.cn/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.chinacloudapi.cn/mycontainer' --recursive` |

复制操作是同步的，因此，当命令返回时，表示已复制所有文件。

## <a name="copy-a-container"></a>复制容器

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将容器复制到另一个存储帐户。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.chinacloudapi.cn/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.chinacloudapi.cn/<container-name>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.chinacloudapi.cn/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.chinacloudapi.cn/mycontainer' --recursive` |

复制操作是同步的，因此，当命令返回时，表示已复制所有文件。

## <a name="copy-containers-directories-and-blobs"></a>复制容器、目录和 Blob

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将所有容器、目录和 Blob 复制到另一个存储帐户。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.chinacloudapi.cn/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.chinacloudapi.cn/' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.chinacloudapi.cn/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.chinacloudapi.cn' --recursive` |

复制操作是同步的，因此，当命令返回时，表示已复制所有文件。

## <a name="copy-with-optional-flags"></a>使用可选标志复制

可以通过使用可选标志来调整复制操作。 下面是几个示例。

|方案|标志|
|---|---|
|将 Blob 复制为块 Blob、页 Blob 或追加 Blob。|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|复制到特定访问层（如存档层）。|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
|自动解压缩文件。|**--decompress**=\[gzip\|deflate\]|

有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。 

## <a name="next-steps"></a>后续步骤

如需了解更多示例，请参阅以下文章：

- [示例：上载](storage-use-azcopy-blobs-upload.md)
- [示例：下载](storage-use-azcopy-blobs-download.md)
- [示例：同步](storage-use-azcopy-blobs-synchronize.md)
- [示例：Amazon S3 存储桶](storage-use-azcopy-s3.md)
- [示例：Azure 文件存储](storage-use-azcopy-files.md)
- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)