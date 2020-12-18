---
title: 使用 Azure PowerShell 创建服务总线队列
description: 在本快速入门中，你将了解如何使用 Azure PowerShell 创建服务总线命名空间并在其中创建队列。
ms.devlang: dotnet
ms.topic: quickstart
origin.date: 08/12/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: no
ms.testdate: 07/20/2020
ms.author: v-yeche
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ec9c6d14084f5bab72ea21d12091c8739275ecfd
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105353"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>使用 Azure PowerShell 创建服务总线命名空间和队列
本快速入门介绍如何使用 Azure PowerShell 创建服务总线命名空间和队列。 本快速入门还介绍了如何获取客户端应用程序向队列发送消息或从队列接收消息所使用的授权凭据。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保你有一个 Azure 订阅。 如果没有 Azure 订阅，可在开始前创建一个[试用版订阅][trial subscription]。 
<!--Not Available on Cloud Shell-->

可以在本地计算机上[安装](https://docs.microsoft.com/powershell/azure/install-Az-ps)和使用 Azure PowerShell。 

## <a name="provision-resources"></a>预配资源

<!--MOONCAKE CUSTOMIZE ON 08/27/2020-->

1. 首先，如果尚未安装服务总线 PowerShell 模块，请安装：

    ```powershell
    Install-Module Az.ServiceBus
    ```

2. 运行以下命令来登录到 Azure：

    ```powershell
    Connect-AzAccount -Environment AzureChinaCloud
    ```

3. 发出以下命令来设置当前的订阅上下文，或者查看当前活动的订阅：

    ```powershell
    Select-AzSubscription -SubscriptionName "MyAzureSubName" 
    Get-AzContext
    ```
    
    <!--MOONCAKE CUSTOMIZE ON 08/27/2020-->

4. 运行以下命令来创建 Azure 资源组。 如有需要，请更新资源组名称和位置。 

    ```powershell
    New-AzResourceGroup -Name ContosoRG -Location chinaeast
    ```
5. 运行以下命令以创建服务总线消息命名空间。 在此示例中，`ContosoRG` 是在上一步中创建的资源组。 `ContosoSBusNS` 是在该资源组中创建的服务总线命名空间的名称。 

    ```powershell
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location chinaeast
    ```
6. 运行以下命令，在上一步中创建的命名空间中创建一个队列。 

    ```powershell
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. 获取命名空间的主连接字符串。 使用此连接字符串连接到队列并发送和接收消息。 

    ```powershell    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    请记下该连接字符串和队列名称。 使用它们发送和接收消息。 

## <a name="next-steps"></a>后续步骤
在本文中，你创建了服务总线命名空间并在其中创建了队列。 若要了解如何向队列发送消息或从队列接收消息，请参阅“发送和接收消息”部分中的以下某个快速入门。 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[trial subscription]: https://www.microsoft.com/china/azure/index.html?fromtype=cn/

<!-- Update_Description: update meta properties, wording update, update link -->