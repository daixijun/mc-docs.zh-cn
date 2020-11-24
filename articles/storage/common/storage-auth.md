---
title: 授权数据操作
titleSuffix: Azure Storage
description: 了解授权访问 Azure 存储的各种方式，包括 Azure Active Directory、共享密钥授权或共享访问签名 (SAS)。
services: storage
author: WenJason
ms.service: storage
ms.topic: conceptual
origin.date: 04/15/2019
ms.date: 11/16/2020
ms.author: v-jay
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 564b6bd00244322a1b1a0e8c7b8d0cf0bb9146c6
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94551690"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>授权访问 Azure 存储中的数据

每次访问存储帐户中的数据时，客户端都会通过 HTTP/HTTPS 向 Azure 存储发出请求。 每个对安全资源的请求都必须经过授权，以便服务确保客户端具有访问数据所需的权限。

下表介绍了多个选项，Azure 存储提供这些选项，用于授权对资源的访问：

| Azure 项目 | 共享密钥（存储帐户密钥） | 共享访问签名 (SAS) | Azure Active Directory (Azure AD) | 匿名公共读取访问权限 |
| -------------- | -------------------------------- | ----------------------------- | --------------------------------- | ---------------------------- |
|Azure Blob     |[支持](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/)         |[支持](storage-sas-overview.md)         |[支持](storage-auth-aad.md)         |[支持](../blobs/anonymous-read-access-configure.md)         |
|Azure 文件存储 (SMB)     |[支持](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/)         |不支持         |不支持         |不支持         |
|Azure 文件存储 (REST)     |[支持](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/)         |[支持](storage-sas-overview.md)         |不支持         |不支持         |
|Azure 队列     |[支持](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/)         |[支持](storage-sas-overview.md)         |[支持](storage-auth-aad.md)         | 不支持         |
|Azure 表     |[支持](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/)         |[支持](storage-sas-overview.md)         |不支持         | 不支持         |

下面简要介绍每个授权选项：

- 用于 Blob 和队列的 **Azure Active Directory (Azure AD) 集成**。 Azure 提供 Azure 基于角色的访问控制 (Azure RBAC)，用于控制客户端对存储帐户中资源的访问权限。 有关用于 Blob 和队列的 Azure AD 集成的详细信息，请参阅[使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限](storage-auth-aad.md)。
- 用于 blob、文件、队列和表的 **共享密钥授权**。 使用共享密钥的客户端会随使用存储帐户访问密钥签名的每个请求传递一个标头。 有关详细信息，请参阅[通过共享密钥进行授权](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/)。
- 用于 blob、文件、队列和表的 **共享访问签名**。 共享访问签名 (SAS) 针对存储帐户中的资源提供有限的委托访问权限。 通过对签名的有效时间间隔或对它授予的权限添加约束，可灵活地管理访问权限。 有关详细信息，请参阅[使用共享访问签名 (SAS)](storage-sas-overview.md)。
- 用于容器和 blob 的 **匿名公共读取访问**。 无需授权。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../blobs/anonymous-read-access-configure.md)。  

默认情况下，Azure 存储中的所有资源都受到保护，并且只能由帐户所有者使用。 虽然你可以使用上述任何授权策略来为客户端授予访问存储帐户资源的权限，但 Azure 建议尽可能使用 Azure AD，以便最大限度地提高安全性和易用性。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限](storage-auth-aad.md)
- [通过共享密钥进行授权](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/)
- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)
