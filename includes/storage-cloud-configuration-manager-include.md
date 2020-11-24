---
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/16/2020
ms.author: v-jay
ms.openlocfilehash: 667286ff8da44832114ce3511edab1327877b433
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552552"
---
[适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 提供用于分析配置文件中连接字符串的类。 [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) 类会分析配置设置。 它分析在桌面、移动设备、Azure 虚拟机或 Azure 云服务中运行的客户端应用程序的设置。

若要引用 `CloudConfigurationManager` 包，请添加以下 `using` 指令：

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

下面的示例演示了如何检索配置文件中的连接字符串：

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

可以选择使用 Azure Configuration Manager。 还可以使用 API，例如 .NET Framework 的 [ConfigurationManager 类](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager)。
