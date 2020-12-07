---
title: Azure 应用服务访问限制
description: 了解如何通过指定访问限制在 Azure 应用服务中保护应用。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
origin.date: 06/06/2019
ms.date: 11/30/2020
ms.author: v-tawe
ms.custom: seodec18
ms.openlocfilehash: 13944c546c110523bdf4883a9045c64d81f92a3a
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306328"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>设置 Azure 应用服务访问限制

通过设置访问限制可以定义一个按优先级排序的允许/拒绝列表，用于控制在网络中对应用的访问。 该列表可以包含 IP 地址或 Azure 虚拟网络子网。 如果存在一个或多个条目，则列表末尾会存在一个隐式的“全部拒绝”。

访问限制功能适用于所有 Azure 应用服务托管的工作负载。 工作负载可包括 Web 应用、API 应用、Linux 应用、Linux 容器应用和函数。

向应用发出请求时，将会根据访问限制列表中的 IP 地址规则评估 FROM 地址。 如果 FROM 地址位于配置为使用 Microsoft.Web 服务终结点的子网中，则会根据访问限制列表中的虚拟网络规则比较源子网。 如果列表中的规则不允许访问该地址，则服务会以“HTTP 403”状态代码进行答复。

<!--https://en.wikipedia.org/wiki/HTTP_403-->

访问限制功能是在应用服务前端角色（即代码运行所在的辅助角色主机中的上游）中实现的。 因此，访问限制是有效的网络访问控制列表 (ACL)。

限制从 Azure 虚拟网络访问 Web 应用的功能通过[服务终结点][serviceendpoints]启用。 使用服务终结点，可以限制从选定的子网对多租户服务进行访问。 对托管在应用服务环境中的应用的流量进行限制不起作用。 如果处于应用服务环境中，可应用 IP 地址规则控制对应用的访问。

> [!NOTE]
> 必须在网络端以及要对其启用服务终结点的 Azure 服务上同时启用服务终结点。 有关支持服务终结点的 Azure 服务列表，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
>

![访问限制流程图。](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>在门户中添加或编辑访问限制规则

若要向应用添加访问限制规则，请执行以下操作：

1. 登录到 Azure 门户。

1. 在左窗格中，选择“网络”。

1. 在“网络”窗格的“访问限制”下，选择“配置访问限制”  。

   ![Azure 门户中的“应用服务网络选项”窗格的屏幕截图。](media/app-service-ip-restrictions/access-restrictions.png)  

1. 在“访问限制”页上查看为应用定义的访问限制规则列表。

   ![Azure 门户中“访问限制”页面的屏幕截图，显示为所选应用定义的访问限制规则列表。](media/app-service-ip-restrictions/access-restrictions-browse.png)

   列表显示当前应用于该应用的所有限制。 如果应用中存在虚拟网络限制，该表将显示是否为 Microsoft.Web 启用了服务终结点。 如果未对应用定义任何限制，则可从任何位置访问该应用。  

### <a name="add-an-access-restriction-rule"></a>添加访问限制规则

若要向应用添加访问限制规则，请在“访问限制”窗格中选择“添加规则” 。 规则在添加后会立即生效。 

规则会从“优先级”列中最小的数字开始，按优先级顺序强制执行。 即使只添加了一条规则，一个隐式的“全部拒绝”也会立即生效。

<!--On the **Add Access Restriction** pane,-->

在“添加访问限制”窗格上创建规则时，请执行以下操作：

1. 在“操作”下，选择“允许”或“拒绝”  。  

   ![“添加访问策略”窗格的屏幕截图。](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. （可选）输入规则名称和说明。  
1. 在“类型”下拉列表中选择规则类型。  
1. 在“优先级”框中，输入一个优先级值。  
1. 在“订阅”、“虚拟网络”和“子网”下拉列表中，选择要限制访问的内容  。  

### <a name="set-an-ip-address-based-rule"></a>设置基于 IP 地址的规则

按照上一部分所述的过程操作，但有以下差异：
* 在步骤 3 的“类型”下拉列表中，选择“IPv4”或“IPv6”  。 

以无类别域际路由选择 (CIDR) 表示法为 IPv4 和 IPv6 地址指定 IP 地址。 若要指定地址，可以使用类似 1.2.3.4/32 的格式，其中前四个八位字节代表自己的 IP 地址，/32 为掩码 。 所有地址的 IPv4 CIDR 表示法都为 0.0.0.0/0。 若要详细了解 CIDR 表示法，请查看“无类别域际路由选择”。 

<!--https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing-->

<!--## Use service endpoints-->
<!--Add IP Restriction-->

## <a name="manage-access-restriction-rules"></a>管理访问限制规则

可编辑或删除现有的访问限制规则。

### <a name="edit-a-rule"></a>编辑规则

1. 若要开始编辑现有的访问限制规则，请在“访问限制”页上双击要编辑的规则。

    <!--On the **Edit access Restriction** pane-->

1. 在“编辑访问限制”窗格中进行更改，然后选择“更新规则” 。 编辑的内容会立即生效，包括在优先级排序方面的更改。

   ![Azure 门户中“编辑访问限制”窗格的屏幕截图，显示现有访问限制规则的字段。](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)


### <a name="delete-a-rule"></a>删除规则

若要删除规则，请在“访问限制”页上选择要删除的规则旁边的省略号 (...)，然后选择“删除”  。

![“访问限制”页的屏幕截图，显示要删除的访问限制规则旁边的“删除”省略号。](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>阻止单个 IP 地址

<!--When you add your first Access restriction rule,-->

添加第一个访问限制规则时，服务将添加优先级为 2147483647 的显式“全部拒绝”规则。 实际上，显式“全部拒绝”规则将是最后执行的规则，并将阻止访问未被“允许”规则明确允许的任何 IP 地址 。

如果你希望显式阻止单个 IP 地址或 IP 地址块，但允许所有其他访问，请添加一个显式的“全部允许”规则。

![Azure 门户中的“访问限制”页的屏幕截图，只显示一个受阻止的 IP 地址。](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>限制对 SCM 站点的访问 

除了能够控制对应用的访问以外，还可以限制对应用所用的 SCM 站点的访问。 SCM 站点既是 Web 部署终结点，也是 Kudu 控制台。 对于 SCM 站点，可以分配不同于应用的访问限制；也可以对应用和 SCM 站点使用相同的限制设置。 如果选择“与 \<app name> 相同的限制”复选框，则所有内容都将作废。如果清除该复选框，则会重新应用 SCM 站点设置。 

![Azure 门户中“访问限制”页的屏幕截图，显示没有为 SCM 站点或应用设置访问限制。](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>以编程方式管理访问限制规则

可通过以下任一方法以编程方式添加访问限制： 

* 使用 [Azure CLI](https://docs.azure.cn/cli/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true)。 例如：

```azurecli
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```

* 使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true)。 例如：


```powershell
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

还可通过以下任一方法手动设置值：

* 在 Azure 资源管理器中对应用配置使用 [Azure REST API](https://docs.microsoft.com/rest/api/azure/) PUT 操作。 此信息在 Azure 资源管理器中的位置为：

  management.chinacloudapi.cn/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

* 使用 ARM 模板。 例如，可以使用 resources.azure.com 并编辑 ipSecurityRestrictions 块以添加所需的 JSON。

前面的示例的 JSON 语法为：

```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="set-up-azure-functions-access-restrictions"></a>设置 Azure Functions 访问限制

访问限制也适用于与应用服务计划具有相同功能的函数应用。 启用访问限制时，还可针对任何不允许的 IP 禁用 Azure 门户代码编辑器。

## <a name="next-steps"></a>后续步骤
[Azure Functions 的访问限制](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[应用程序网关与服务终结点的集成](networking/app-gateway-with-service-endpoints.md)

<!--Links-->

[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md