---
title: include 文件
description: include 文件
services: storage
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 11/05/2019
ms.date: 11/16/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 71a0d00c7d9f61133a30c49dccf0e01f4de440a2
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552037"
---
Azure Blob 存储是 Azure 的适用于云的对象存储解决方案。 Blob 存储最适合存储巨量的非结构化数据。 非结构化数据是不遵循特定数据模型或定义（如文本或二进制数据）的数据。

## <a name="about-blob-storage"></a>关于 Blob 存储

Blob 存储用于：

* 直接向浏览器提供图像或文档。
* 存储文件以供分布式访问。
* 对视频和音频进行流式处理。
* 向日志文件进行写入。
* 存储用于备份和还原、灾难恢复及存档的数据。
* 存储数据以供本地或 Azure 托管服务执行分析。

用户或客户端应用程序通过 HTTP/HTTPS 可以从世界任何地方访问 Blob 存储中的对象。 Blob 存储中的对象可以通过 [Azure 存储 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage)、[Azure CLI](/cli/storage) 或 Azure 存储客户端库访问。 提供了不同语言的客户端库，包括：

* [.NET](https://docs.azure.cn/dotnet/api/overview/storage/)
* [Java](https://docs.azure.cn/java/api/overview/storage/managementapi)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>关于 Azure Data Lake Storage Gen2

Blob 存储支持 Azure Data Lake storage Gen2，即 Azure 适用于云的企业大数据分析解决方案。 Azure Data Lake Storage Gen2 提供了分层文件系统以及 Blob 存储的优势，包括：

* 低成本分层存储
* 高可用性
* 强一致性
* 灾难恢复功能

有关 Data Lake Storage Gen2 的详细信息，请参阅 [Azure Data Lake Storage Gen2 简介](../articles/storage/data-lake-storage/introduction.md)。