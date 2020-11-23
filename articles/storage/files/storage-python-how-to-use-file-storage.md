---
title: 使用 Python 针对 Azure 文件进行开发 | Microsoft Docs
description: 了解如何开发使用 Azure 文件存储文件数据的 Python 应用程序和服务。
author: WenJason
ms.service: storage
ms.topic: how-to
origin.date: 10/08/2020
ms.date: 11/16/2020
ms.author: v-jay
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 3fa27adab05e17113d85ea1fe2eff04c4e24d467
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94551811"
---
# <a name="develop-for-azure-files-with-python"></a>使用 Python 针对 Azure 文件进行开发

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

了解有关如何使用 Python 开发应用或服务的基础知识，这些应用或服务会使用 Azure 文件存储来存储文件数据。 创建一个简单的控制台应用，并了解如何使用 Python 和 Azure 文件存储执行基本操作：

- 创建 Azure 文件共享
- 创建目录
- 枚举 Azure 文件共享中的文件和目录
- 上传、下载和删除文件
- 使用快照创建文件共享备份

> [!NOTE]
> 由于 Azure 文件可通过 SMB 进行访问，因此可编写简单的应用程序，通过标准 Python I/O 类和函数访问 Azure 文件共享。 本文将介绍如何编写使用 Azure 文件存储 Python SDK 的应用，该 SDK 使用 [Azure 文件存储 REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) 与 Azure 文件存储通信。

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下载和安装适用于 Python 的 Azure 存储 SDK

> [!NOTE]
> 如果要从适用于 Python 的 Azure 存储 SDK 版本 0.36 或更早版本升级，请在安装最新软件包之前使用 `pip uninstall azure-storage` 卸载旧版 SDK。

# <a name="python-v12"></a>[Python v12](#tab/python)

[用于 Python 的 Azure 文件存储客户端库 v12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) 需要 Python 2.7 或 3.5。

# <a name="python-v2"></a>[Python v2](#tab/python2)

[适用于 Python 的 Azure 存储 SDK](https://github.com/azure/azure-storage-python) 需要使用 Python 2.7、3.3、3.4、3.5 或 3.6。

---

## <a name="install-via-pypi"></a>通过 PyPI 安装

要通过 Python 包索引 (PyPI) 安装，请键入：

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>查看示例应用程序

若要查看和运行示例应用程序，了解如何将 Python 与 Azure 文件存储配合使用，请参阅 [Azure Storage:Getting Started with Azure Files in Python](https://github.com/Azure-Samples/storage-file-python-getting-started)（Azure 存储：开始在 Python 中使用 Azure 文件存储）。

若要运行示例应用程序，请确保已安装 `azure-storage-file` 和 `azure-storage-common` 包。

---

## <a name="set-up-your-application-to-use-azure-files"></a>设置应用程序以使用 Azure 文件

将以下代码添加到 Python 源文件顶部附近，以使用本文中的代码片段。

# <a name="python-v12"></a>[Python v12](#tab/python)

```csharp
from azure.core.exceptions import (
    ResourceExistsError,
    ResourceNotFoundError
)

from azure.storage.fileshare import (
    ShareServiceClient,
    ShareClient,
    ShareDirectoryClient,
    ShareFileClient
)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>设置与 Azure 文件的连接

# <a name="python-v12"></a>[Python v12](#tab/python)

通过 [ShareServiceClient](https://docs.microsoft.com/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient)，可以使用共享、目录和文件。 以下代码使用存储帐户连接字符串创建 `ShareServiceClient` 对象。

```csharp
# Create a ShareServiceClient from a connection string
service_client = ShareServiceClient.from_connection_string(connection_string)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

通过 [FileService](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) 对象，可以使用共享、目录和文件。 以下代码使用存储帐户名称和帐户密钥创建一个 `FileService` 对象。 将 `<myaccount>` 和 `<mykey>` 替换为自己的帐户名和密钥。

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

# <a name="python-v12"></a>[Python v12](#tab/python)

以下代码示例使用 [ShareClient](https://docs.microsoft.com/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) 对象来创建共享（如果该共享不存在）。

```csharp
def create_file_share(self, connection_string, share_name):
    try:
        # Create a ShareClient from a connection string
        share_client = ShareClient.from_connection_string(
            connection_string, share_name)

        print("Creating share:", share_name)
        share_client.create_share()

    except ResourceExistsError as ex:
        print("ResourceExistsError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

以下代码示例使用 [FileService](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) 对象来创建共享（如果该共享不存在）。

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>创建目录

可以将文件置于子目录中，不必将其全部置于根目录中，以便对存储进行有效的组织。

# <a name="python-v12"></a>[Python v12](#tab/python)

下面的方法通过使用 [ShareDirectoryClient](https://docs.microsoft.com/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) 对象在指定文件共享的根目录中创建一个目录。

```csharp
def create_directory(self, connection_string, share_name, dir_name):
    try:
        # Create a ShareDirectoryClient from a connection string
        dir_client = ShareDirectoryClient.from_connection_string(
            connection_string, share_name, dir_name)

        print("Creating directory:", share_name + "/" + dir_name)
        dir_client.create_directory()

    except ResourceExistsError as ex:
        print("ResourceExistsError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

以下代码会在根目录下创建名为 *sampledir* 的子目录。

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>上传文件

本部分会介绍如何将文件从本地存储上传到 Azure 文件存储。

# <a name="python-v12"></a>[Python v12](#tab/python)

下面的方法将指定文件的内容上传到指定的 Azure 文件共享中的指定目录。

```csharp
def upload_local_file(self, connection_string, local_file_path, share_name, dest_file_path):
    try:
        source_file = open(local_file_path, "rb")
        data = source_file.read()

        # Create a ShareFileClient from a connection string
        file_client = ShareFileClient.from_connection_string(
            connection_string, share_name, dest_file_path)

        print("Uploading to:", share_name + "/" + dest_file_path)
        file_client.upload_file(data)

    except ResourceExistsError as ex:
        print("ResourceExistsError:", ex.message)

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

Azure 文件共享至少包含文件可以驻留的根目录。 若要创建文件并上传数据，请使用 [create_file_from_path](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-)、[create_file_from_stream](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)、[create_file_from_bytes](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) 或 [create_file_from_text](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) 方法。 这些方法是高级方法，它们在数据大小超过 64 MB 时执行必要的分块操作。

`create_file_from_path` 从指定位置上传文件内容，`create_file_from_stream` 从已经打开的文件/流上传内容。 `create_file_from_bytes` 上传字节数组，`create_file_from_text` 使用指定的编码（默认为 UTF-8）上传指定的文本值。

下面的示例将 *sunset.png* 文件的内容上传到 **myfile** 文件中。

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>枚举 Azure 文件共享中的文件和目录

# <a name="python-v12"></a>[Python v12](#tab/python)

若要列出子目录中的文件和目录，请使用 [list_directories_and_files](https://docs.microsoft.com/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) 方法。 此方法返回自动分页迭代器。 以下代码将指定目录中每个文件和子目录的名称输出到控制台。

```csharp
def list_files_and_dirs(self, connection_string, share_name, dir_name):
    try:
        # Create a ShareClient from a connection string
        share_client = ShareClient.from_connection_string(
            connection_string, share_name)

        for item in list(share_client.list_directories_and_files(dir_name)):
            if item["is_directory"]:
                print("Directory:", item["name"])
            else:
                print("File:", dir_name + "/" + item["name"])

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要列出共享中的文件和目录，请使用 [list_directories_and_files](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) 方法。 此方法会返回一个生成器。 以下代码将共享中每个文件和目录的 **名称** 输出到控制台。

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>下载文件

# <a name="python-v12"></a>[Python v12](#tab/python)

若要从文件中下载数据，请使用 [download_file](https://docs.microsoft.com/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-)。

下面的示例演示如何使用 `download_file` 获取指定文件的内容并将其存储在本地（“DOWNLOADED-”会预置到文件名之前）。

```csharp
def download_azure_file(self, connection_string, share_name, dir_name, file_name):
    try:
        # Build the remote path
        source_file_path = dir_name + "/" + file_name

        # Add a prefix to the filename to 
        # distinguish it from the uploaded file
        dest_file_name = "DOWNLOADED-" + file_name

        # Create a ShareFileClient from a connection string
        file_client = ShareFileClient.from_connection_string(
            connection_string, share_name, source_file_path)

        print("Downloading to:", dest_file_name)

        # Open a file for writing bytes on the local system
        with open(dest_file_name, "wb") as data:
            # Download the file from Azure into a stream
            stream = file_client.download_file()
            # Write the stream to the local file
            data.write(stream.readall())

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要从文件下载数据，请使用 [get_file_to_path](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)、[get_file_to_stream](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)、[get_file_to_bytes](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-) 或 [get_file_to_text](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)。 这些方法是高级方法，它们在数据大小超过 64 MB 时执行必要的分块操作。

以下示例演示如何使用 `get_file_to_path` 下载 **myfile** 文件的内容，并将其存储到 *out-sunset.png* 文件。

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>创建共享快照

可以创建整个文件共享的时点副本。

# <a name="python-v12"></a>[Python v12](#tab/python)

```csharp
def create_snapshot(self, connection_string, share_name):
    try:
        # Create a ShareClient from a connection string
        share_client = ShareClient.from_connection_string(
            connection_string, share_name)

        # Create a snapshot
        snapshot = share_client.create_snapshot()
        print("Created snapshot:", snapshot["snapshot"])

        # Return the snapshot time so 
        # it can be accessed later
        return snapshot["snapshot"]

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**使用元数据创建共享快照**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>列出共享和快照

可以为特定的共享列出所有快照。

# <a name="python-v12"></a>[Python v12](#tab/python)

```csharp
def list_shares_snapshots(self, connection_string):
    try:
        # Create a ShareServiceClient from a connection string
        service_client = ShareServiceClient.from_connection_string(connection_string)

        # List the shares in the file service
        shares = list(service_client.list_shares(include_snapshots=True))

        for share in shares:
            if (share["snapshot"]):
                print("Share:", share["name"], "Snapshot:", share["snapshot"])
            else:
                print("Share:", share["name"])

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>浏览共享快照

可以浏览每个共享快照来检索相应时间点的文件和目录。

# <a name="python-v12"></a>[Python v12](#tab/python)

```csharp
def browse_snapshot_dir(self, connection_string, share_name, snapshot_time, dir_name):
    try:
        # Create a ShareClient from a connection string
        snapshot = ShareClient.from_connection_string(
            conn_str=connection_string, share_name=share_name, snapshot=snapshot_time)

        print("Snapshot:", snapshot_time)

        for item in list(snapshot.list_directories_and_files(dir_name)):
            if item["is_directory"]:
                print("Directory:", item["name"])
            else:
                print("File:", dir_name + "/" + item["name"])

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>从共享快照获取文件

可以从共享快照下载文件。 这使你可以还原文件的早期版本。

# <a name="python-v12"></a>[Python v12](#tab/python)

```csharp
def download_snapshot_file(self, connection_string, share_name, snapshot_time, dir_name, file_name):
    try:
        # Build the remote path
        source_file_path = dir_name + "/" + file_name

        # Add a prefix to the local filename to 
        # indicate it's a file from a snapshot
        dest_file_name = "SNAPSHOT-" + file_name

        # Create a ShareFileClient from a connection string
        snapshot_file_client = ShareFileClient.from_connection_string(
            conn_str=connection_string, share_name=share_name, 
            file_path=source_file_path, snapshot=snapshot_time)

        print("Downloading to:", dest_file_name)

        # Open a file for writing bytes on the local system
        with open(dest_file_name, "wb") as data:
            # Download the file from Azure into a stream
            stream = snapshot_file_client.download_file()
            # Write the stream to the local file
            data.write(stream.readall())

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>删除单个共享快照
可以删除单个共享快照。

# <a name="python-v12"></a>[Python v12](#tab/python)

```csharp
def delete_snapshot(self, connection_string, share_name, snapshot_time):
    try:
        # Create a ShareClient for a snapshot
        snapshot_client = ShareClient.from_connection_string(conn_str=connection_string, share_name=share_name, snapshot=snapshot_time)

        print("Deleting snapshot:", snapshot_time)

        # Delete the snapshot
        snapshot_client.delete_share()

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>删除文件

# <a name="python-v12"></a>[Python v12](#tab/python)

若要删除文件，请调用 [delete_file](https://docs.microsoft.com/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-)。

```csharp
def delete_azure_file(self, connection_string, share_name, file_path):
    try:
        # Create a ShareFileClient from a connection string
        file_client = ShareFileClient.from_connection_string(
            connection_string, share_name, file_path)

        print("Deleting file:", share_name + "/" + file_path)

        # Delete the file
        file_client.delete_file()

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要删除文件，请调用 [delete_file](https://docs.microsoft.com/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-)。

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>共享快照存在时，删除共享

# <a name="python-v12"></a>[Python v12](#tab/python)

若要删除包含快照的共享，请调用带有 `delete_snapshots=True` 的 [delete_share](https://docs.microsoft.com/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-)。

```csharp
def delete_share(self, connection_string, share_name):
    try:
        # Create a ShareClient from a connection string
        share_client = ShareClient.from_connection_string(
            connection_string, share_name)

        print("Deleting share:", share_name)

        # Delete the share and snapshots
        share_client.delete_share(delete_snapshots=True)

    except ResourceNotFoundError as ex:
        print("ResourceNotFoundError:", ex.message)
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

无法删除包含快照的共享，除非先删除所有快照。

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>后续步骤

了解如何使用 Python 操作 Azure 文件后，请单击以下链接了解更多信息。

- [Python 开发人员中心](/develop/python/)
- [Azure 存储服务 REST API](https://docs.microsoft.com/rest/api/azure/)
- [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
