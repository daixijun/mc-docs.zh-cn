---
title: 使用 AzCopy v10 将文件上传到 Azure Blob 存储 | Microsoft Docs
description: 本文包含一组 AzCopy 示例命令，有助于将文件上传到 Azure Blob 存储。
author: WenJason
ms.service: storage
ms.topic: how-to
origin.date: 12/11/2020
ms.date: 01/18/2021
ms.author: v-jay
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ce1f62ea67225f6d51c595b91c3ca4ddde21ff35
ms.sourcegitcommit: f086abe8bd2770ed10a4842fa0c78b68dbcdf771
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163235"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>使用 AzCopy v10 将文件上传到 Azure Blob 存储

可以使用 AzCopy v10 命令行实用程序将文件和目录上传到 Blob 存储。 

若要查看其他类型任务（如下载 Blob、与 Blob 存储同步或在帐户之间复制 Blob）的示例，请参阅本文的[后续步骤](#next-steps)部分中提供的链接。

## <a name="get-started"></a>入门

请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)一文下载 AzCopy，并了解如何提供存储服务的授权凭据。

> [!NOTE] 
> 本文中的示例假定你已使用 Azure Active Directory (Azure AD) 提供了授权凭据。
>
> 如果你希望使用 SAS 令牌来授权访问 Blob 数据，可将该令牌追加到每个 AzCopy 命令中的资源 URL。 例如：`'https://<storage-account-name>.blob.core.chinacloudapi.cn/<container-name><SAS-token>'`。

## <a name="create-a-container"></a>创建容器

可以使用 [azcopy make](storage-ref-azcopy-make.md) 命令创建容器。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.chinacloudapi.cn/<container-name>'` |
| **示例** | `azcopy make 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer'` |
| **示例**（分层命名空间） | `azcopy make 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer'` |

有关详细参考文档，请参阅 [azcopy make](storage-ref-azcopy-make.md)。

## <a name="upload-a-file"></a>上载文件

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令上传文件。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.chinacloudapi.cn/<container-name>/<blob-name>'` |
| **示例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/myTextFile.txt'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer/myTextFile.txt'` |

还可以在文件路径或文件名中的任意位置使用通配符 (*) 来上传文件。 例如：`'C:\myDirectory\*.txt'` 或 `C:\my*\*.txt`。

## <a name="upload-a-directory"></a>上传目录

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令上传目录。 

此示例将某个目录（以及该目录中的所有文件）复制到 Blob 容器。 结果是该文件共享中出现一个同名的容器。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.chinacloudapi.cn/<container-name>' --recursive` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer' --recursive` |

若要复制到容器中的某个目录，只需在命令字符串中指定该目录的名称。

|    |     |
|--------|-----------|
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/myBlobDirectory' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer/myBlobDirectory' --recursive` |

如果指定的目录名称在容器中不存在，AzCopy 将以该名称创建一个新目录。

## <a name="upload-directory-contents"></a>上传目录内容

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令上传目录的内容。 可以使用通配符 (*) 上传内容，而无需复制包含的目录本身。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.chinacloudapi.cn/<container-name>/<directory-path>'` |
| **示例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/myBlobDirectory'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer/myBlobDirectory'` |


追加 `--recursive` 标志可以上传所有子目录中的文件。

## <a name="upload-specific-files"></a>上传特定的文件

可以使用完整的文件名、包含通配符 (*) 的部分名称或者日期和时间来上传特定文件。

> [!TIP]
> 这些示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

### <a name="specify-multiple-complete-file-names"></a>指定多个完整文件名

结合 `--include-path` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 使用分号 (`;`) 分隔各个文件名。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.chinacloudapi.cn/<container-name>' --include-path <semicolon-separated-file-list>` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

在此示例中，AzCopy 将传输 `C:\myDirectory\photos` 目录和 `C:\myDirectory\documents\myFile.txt` 文件。 包含 `--recursive` 选项以传输 `C:\myDirectory\photos` 目录中的所有文件。

还可以使用 `--exclude-path` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

### <a name="use-wildcard-characters"></a>使用通配符

结合 `--include-pattern` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 指定包含通配符的部分名称。 使用分号 (`;`) 分隔名称。 

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.chinacloudapi.cn/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

还可以使用 `--exclude-pattern` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于文件名，而不适用于路径。  若要复制目录树中存在的所有文本文件，请使用 `-recursive` 选项获取整个目录树，然后使用 `-include-pattern` 并指定 `*.txt` 来获取所有文本文件。

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>上传在某个日期和时间之前或之后修改的文件 

将 [azcopy copy](storage-ref-azcopy-copy.md) 命令与 `--include-before` 或 `--include-after` 选项结合使用。 以 ISO-8601 格式指定日期和时间（例如 `2020-08-19T15:04:00Z`）。 

以下示例上传在指定日期或之后修改的文件。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.chinacloudapi.cn/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **示例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.chinacloudapi.cn/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.chinacloudapi.cn/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

如需详细的参考，请查看 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

## <a name="upload-with-optional-flags"></a>使用可选标志上传

可以通过使用可选标志来调整上传操作。 下面是几个示例。

|方案|标志|
|---|---|
|将文件作为追加 Blob 或页 Blob 上传。|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|上传到特定访问层（如存档层）。|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|

有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。

## <a name="next-steps"></a>后续步骤

如需了解更多示例，请参阅以下文章：

- [示例：下载](storage-use-azcopy-blobs-download.md)
- [示例：在帐户之间复制](storage-use-azcopy-blobs-copy.md)
- [示例：同步](storage-use-azcopy-blobs-synchronize.md)
- [示例：Amazon S3 存储桶](storage-use-azcopy-s3.md)
- [示例：Azure 文件存储](storage-use-azcopy-files.md)
- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)