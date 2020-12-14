---
author: WenJason
ms.service: storage
ms.topic: include
origin.date: 10/26/2018
ms.date: 12/14/2020
ms.author: v-jay
ms.openlocfilehash: cdad77aedcfea22b268c76eba4b73bf5bf682203
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850354"
---
[适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 提供用于分析配置文件中连接字符串的类。 [CloudConfigurationManager](https://docs.microsoft.com/previous-versions/azure/reference/mt634650(v=azure.100)) 类会分析配置设置。 它分析在桌面、移动设备、Azure 虚拟机或 Azure 云服务中运行的客户端应用程序的设置。

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
