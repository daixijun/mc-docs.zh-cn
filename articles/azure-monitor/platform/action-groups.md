---
title: 在 Azure 门户中创建和管理器操作组
description: 了解如何在 Azure 门户中创建和管理操作组。
author: Johnnytechn
ms.topic: conceptual
origin.date: 2/18/2020
ms.date: 01/12/2021
ms.author: v-johya
ms.subservice: alerts
ms.openlocfilehash: 684d0ef3a370270c8b033c64fbd1d8dd5712cd3c
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98230749"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 门户中创建和管理器操作组
操作组是由 Azure 订阅的所有者定义的通知首选项的集合。 Azure Monitor 和服务运行状况警报使用操作组来通知用户某个警报已触发。 各种警报可以使用相同的操作组或不同的操作组，具体取决于用户的要求。 

本文演示如何在 Azure 门户中创建和管理操作组。

<!--Correct in MC: Action type-->
每个操作包含以下属性：

* **操作类型**：执行的通知或操作。 示例包括发送语音呼叫、短信、电子邮件，或者触发各种类型的自动化操作。 请参阅本文下文中的“类型”。
* **Name**：操作组中的唯一标识符。
* **详细信息**：因“类型”而异的相应详细信息。

有关如何使用 Azure 资源管理器模板以配置操作组的信息，请参阅[操作组资源管理器模板](./action-groups-create-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 门户创建操作组

1. 在 [Azure 门户](https://portal.azure.cn)中，搜索并选择“监视”。 “监视”窗格将所有监视设置和数据合并到一个视图中。

1. 依次选择“警报”、“管理操作” 。

    ![“管理操作”按钮](./media/action-groups/manage-action-groups.png)
    
1. 选择“添加操作组”，并在向导体验中填写相关字段。

    ![“添加操作组”命令](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>配置基本操作组设置

在“项目详细信息”下：

选择在其中保存操作组的“订阅”和“资源组” 。

在“实例详细信息”下：

1. 输入“操作组名称”。

1. 输入“显示名称”。 使用此组发送通知时，显示名称被用来代替完整的操作组名称。

      ![“添加操作组”对话框](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>配置通知

1. 单击“下一步:通知 >”按钮以移动到“通知”选项卡，或选择屏幕顶部的“通知”选项卡 。

1. 定义触发警报时要发送的通知的列表。 为每个通知提供以下信息：

    a. **通知类型**：选择要发送的通知的类型。 可用选项是：
      * 向 Azure 资源管理器角色发送电子邮件 - 将电子邮件发送给分配有某些订阅级别 ARM 角色的用户。
      * 电子邮件/短信/推送/语音 - 将这些通知类型发送给特定收件人。
    
    b. **名称**：输入通知的唯一名称。

    c. **详细信息**：根据所选的通知类型，输入电子邮件地址、电话号码等。
    
    d. **常见警报架构**：可以选择启用 [常见警报架构](./alerts-common-schema.md)，这可获得在 Azure Monitor 中的所有警报服务中具有单个可扩展和统一的警报有效负载的优势。

    ![“通知”选项卡](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>配置操作

1. 单击“下一步:操作 >”按钮以移动到“操作”选项卡，或选择屏幕顶部的“操作”选项卡 。

1. 定义触发警报时要触发的操作的列表。 为每个操作提供以下内容：

    a. **操作类型**：选择自动化 Runbook、Azure Function、逻辑应用、安全 Webhook、Webhook。
    
    b. **名称**：输入操作的唯一名称。

    c. **详细信息**：根据操作类型，输入 Webhook URI、Azure 应用或自动化 Runbook。
    
    d. **常见警报架构**：可以选择启用 [常见警报架构](./alerts-common-schema.md)，这可获得在 Azure Monitor 中的所有警报服务中具有单个可扩展和统一的警报有效负载的优势。
    
    ![“操作”选项卡](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>创建操作组

1. 如果你愿意，可以浏览“选项卡”设置。 这使你可将键/值对关联到操作组以进行分类，并且该功能可用于任何 Azure 资源。

    ![“标记”选项卡](./media/action-groups/action-group-4-tags.png)
    
1. 单击“查看 + 创建”以查看设置。 这将快速验证输入，确保已选择所有必填字段。 如果有问题，将在此处报告。 查看设置后，单击“创建”预配操作组。
    
    ![“查看 + 创建”选项卡](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> 当配置操作来通过电子邮件或短信通知某个人员时，该人员将收到确认，指出其已被添加到操作组。

## <a name="manage-your-action-groups"></a>管理操作组

创建操作组后，可以通过从“监视”窗格中的“警报”登陆页面中选择“管理操作”来查看“操作组”   。 选择要管理的操作组：

* 添加、编辑或删除操作。
* 删除操作组。

## <a name="action-specific-information"></a>特定于操作的信息

> [!NOTE]
> 请参阅[针对监视的订阅服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits)，了解下面每个项的数值限制。  

### <a name="automation-runbook"></a>自动化 Runbook
有关针对 Runbook 有效负载的限制，请参阅 [Azure 订阅服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

操作组中的 Runbook 操作数可能有限。 

### <a name="azure-app-push-notifications"></a>Azure 应用推送通知
操作组中的 Azure 应用操作数可能有限。

### <a name="email"></a>电子邮件
将从以下电子邮件地址发送电子邮件。 确保电子邮件筛选正确配置
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.cn

操作组中的电子邮件操作数可能有限。 请参阅[速率限制信息](./alerts-rate-limiting.md)一文。

### <a name="email-azure-resource-manager-role"></a>通过电子邮件发送 Azure 资源管理器角色
向订阅角色的成员发送电子邮件。 电子邮件将仅发送给该角色的“Azure AD 用户”成员。 不会将电子邮件发送到 Azure AD 组或服务主体。

通知电子邮件将只发送到主电子邮件地址。

如果你没有在“主电子邮件”中收到通知，则可以尝试以下步骤：

1. 在 Azure 门户中，转到“Active Directory”。
2. 单击“所有用户”（在左窗格中），你将看到用户列表（在右窗格中）。
3. 选择你要查看其“主电子邮件”信息的用户。

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="有关如何查看用户配置文件的示例。"border="true":::

4. 在“联系人信息”下的“用户配置文件”中，如果“电子邮件”选项卡为空白，请单击顶部的“编辑”按钮，然后添加你的“主电子邮件”，然后单击顶部的“保存”按钮。

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="有关如何添加主电子邮件的示例。"border="true":::

操作组中的电子邮件操作数可能有限。 请参阅[速率限制信息](./alerts-rate-limiting.md)一文。

### <a name="function"></a>函数
调用 [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app) 中的现有 HTTP 触发器终结点。

一个操作组中可以存在有限数量的 Function 操作。

<!--Not available in MC: ### ITSM-->
### <a name="logic-app"></a>逻辑应用
操作组中的逻辑应用操作数可能有限。

### <a name="secure-webhook"></a>安全 Webhook
操作组 Webhook 操作使你能够利用 Azure Active Directory 来保护操作组和受保护的 Web API（Webhook 终结点）之间的连接。 下面介绍了利用此功能的整个工作流。 有关 Azure AD 应用程序和服务主体的概述，请参阅 [Microsoft 标识平台 (v2.0) 概述](../../active-directory/develop/v2-overview.md)。

1. 针对受保护的 Web API 创建 Azure AD 应用程序。 请参阅[受保护的 Web API：应用注册](../../active-directory/develop/scenario-protected-web-api-app-registration.md)中的说明进行操作。
    - 将受保护的 API 配置为[通过守护程序应用进行调用](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)。
    
2. 启用操作组以使用 Azure AD 应用程序。

    > [!NOTE]
    > 你必须是 [Azure AD 应用程序管理员角色](../../active-directory/roles/permissions-reference.md#available-roles)的成员才能执行此脚本。
    
    - 修改 PowerShell 脚本的 Connect-AzureAD 调用以使用 Azure AD 租户 ID。
    - 修改 PowerShell 脚本的变量 $myAzureADApplicationObjectId，以便使用 Azure AD 应用程序的对象 ID。
    - 运行修改的脚本。
    
3. 配置操作组安全 Webhook 操作。
    - 从脚本中复制值 $myApp.ObjectId，并将其输入到 Webhook 操作定义中的“应用程序对象 ID”字段。
    
    ![保护 Webhook 操作](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>安全 Webhook PowerShell 脚本

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
有关其他重要信息，请参阅[速率限制信息](./alerts-rate-limiting.md)和[短信警报行为](./alerts-sms-behavior.md)。 

操作组中的短信操作数可能有限。

> [!NOTE]
> 如果在 Azure 门户操作组用户界面无法选择你的国家/地区代码，则表示你所在的国家/地区不支持短信。  如果你的国家/地区代码不可用，则可以在[用户之声](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)投票以请求添加你的国家/地区。 此时，一个解决办法是使操作组向你所在国家/地区支持的第三方短信提供商调用 Webhook。  

受支持国家/地区的定价在 [Azure Monitor 定价页](https://www.azure.cn/pricing/details/monitor/)中列出。

**支持短信通知的国家/地区列表**

| 国家/地区代码 | 国家/地区名称 |
|:---|:---|
| 61 | 澳大利亚 |
| 43 | 奥地利 |
| 32 | 比利时 |
| 55 | 巴西 |
| 1 |加拿大 |
| 56 | 智利 |
| 86 | 中国 |
| 420 | 捷克共和国 |
| 45 | 丹麦 |
| 372 | 爱沙尼亚 |
| 358 | 芬兰 |
| 33 | 法国 |
| 49 | 德国 |
| 852 | 香港特别行政区 |
| 91 | 印度 |
| 353 | 爱尔兰 |
| 972 | 以色列 |
| 39 | 意大利 |
| 81 | 日本 |
| 352 | 卢森堡 |
| 60 | 马来西亚 |
| 52 | 墨西哥 |
| 31 | 荷兰 |
| 64 | 新西兰 |
| 47 | 挪威 |
| 351 | 葡萄牙 |
| 1 | 波多黎各 |
| 40 | 罗马尼亚 |
| 65 | 新加坡 |
| 27 | 南非 |
| 82 | 韩国 |
| 34 | 西班牙 |
| 41 | 瑞士 |
| 886 | 台湾 |
| 44 | 英国 |
| 1 | 美国 |

### <a name="voice"></a>语音
有关其他重要行为，请参阅[速率限制信息](./alerts-rate-limiting.md)一文。

操作组中的语音操作数可能有限。

> [!NOTE]
> 如果在 Azure 门户操作组用户界面无法选择你的国家/地区代码，则表示你所在的国家/地区不支持语音呼叫。 如果你的国家/地区代码不可用，则可以在[用户之声](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)投票以请求添加你的国家/地区。  此时，一个解决办法是使操作组向你所在国家/地区支持的第三方语音呼叫提供商调用 Webhook。  
> 目前在 Azure 门户操作组中支持语音通知的国家/地区代码只有“+1(美国)”。 

受支持国家/地区的定价在 [Azure Monitor 定价页](https://www.azure.cn/pricing/details/monitor/)中列出。

### <a name="webhook"></a>Webhook
Webhook 使用以下规则进行处理
- 最多尝试三次 Webhook 调用。
- 如果在超时期限内未收到响应，或者返回以下 HTTP 状态代码之一，将重试此调用：408、429、503 或 504。
- 第一次调用将等待响应 10 秒。
- 第二次和第三次尝试将等待响应 30 秒。
- 三次尝试调用 Webhook 失败后，任何操作组在 15 分钟内都不会再调用该终结点。

源 IP 地址范围：

 - 13.66.60.119/32
 - 13.66.143.220/30
 - 13.66.202.14/32
 - 13.66.248.225/32
 - 13.66.249.211/32
 - 13.67.10.124/30
 - 13.69.109.132/30
 - 13.71.199.112/30
 - 13.77.53.216/30
 - 13.77.172.102/32
 - 13.77.183.209/32
 - 13.78.109.156/30
 - 13.84.49.247/32
 - 13.84.51.172/32
 - 13.84.52.58/32
 - 13.86.221.220/30
 - 13.106.38.142/32
 - 13.106.38.148/32
 - 13.106.54.3/32
 - 13.106.54.19/32
 - 13.106.57.181/32
 - 13.106.57.196/31
 - 20.38.149.132/30
 - 20.42.64.36/30
 - 20.43.121.124/30
 - 20.44.17.220/30
 - 20.45.123.236/30
 - 20.72.27.152/30
 - 20.150.172.228/30
 - 20.192.238.124/30
 - 20.193.202.4/30
 - 40.68.195.137/32
 - 40.68.201.58/32
 - 40.68.201.65/32
 - 40.68.201.206/32
 - 40.68.201.211/32
 - 40.68.204.18/32
 - 40.115.37.106/32
 - 40.121.219.215/32
 - 40.121.221.62/32
 - 40.121.222.201/32
 - 40.121.223.186/32
 - 51.104.9.100/30
 - 52.183.20.244/32
 - 52.183.31.0/32
 - 52.183.94.59/32
 - 52.184.145.166/32
 - 191.233.50.4/30
 - 191.233.207.64/26
 - 2603:1000:4:402::178/125
 - 2603:1000:104:402::178/125
 - 2603:1010:6:402::178/125
 - 2603:1010:101:402::178/125
 - 2603:1010:304:402::178/125
 - 2603:1010:404:402::178/125
 - 2603:1020:5:402::178/125
 - 2603:1020:206:402::178/125
 - 2603:1020:305:402::178/125
 - 2603:1020:405:402::178/125
 - 2603:1020:605:402::178/125
 - 2603:1020:705:402::178/125
 - 2603:1020:805:402::178/125
 - 2603:1020:905:402::178/125
 - 2603:1020:a04:402::178/125
 - 2603:1020:b04:402::178/125
 - 2603:1020:c04:402::178/125
 - 2603:1020:d04:402::178/125
 - 2603:1020:e04:402::178/125
 - 2603:1020:f04:402::178/125
 - 2603:1020:1004:800::f8/125
 - 2603:1020:1104:400::178/125
 - 2603:1030:f:400::978/125
 - 2603:1030:10:402::178/125
 - 2603:1030:104:402::178/125
 - 2603:1030:107:400::f0/125
 - 2603:1030:210:402::178/125
 - 2603:1030:40b:400::978/125
 - 2603:1030:40c:402::178/125
 - 2603:1030:504:802::f8/125
 - 2603:1030:608:402::178/125
 - 2603:1030:807:402::178/125
 - 2603:1030:a07:402::8f8/125
 - 2603:1030:b04:402::178/125
 - 2603:1030:c06:400::978/125
 - 2603:1030:f05:402::178/125
 - 2603:1030:1005:402::178/125
 - 2603:1040:5:402::178/125
 - 2603:1040:207:402::178/125
 - 2603:1040:407:402::178/125
 - 2603:1040:606:402::178/125
 - 2603:1040:806:402::178/125
 - 2603:1040:904:402::178/125
 - 2603:1040:a06:402::178/125
 - 2603:1040:b04:402::178/125
 - 2603:1040:c06:402::178/125
 - 2603:1040:d04:800::f8/125
 - 2603:1040:f05:402::178/125
 - 2603:1040:1104:400::178/125
 - 2603:1050:6:402::178/125
 - 2603:1050:403:400::1f8/125

若要接收有关这些 IP 地址更改的更新，建议配置监视有关操作组服务的信息通知的服务运行状况警报。

操作组中的 Webhook 操作数可能有限。

频繁更新源 IP 地址可能会在 Webhook 中花费很长时间。 对 ActionGroup 使用“服务标记”有助于最大程度地手动降低频繁更新 IP 地址的复杂性。 上面分享的源 IP 地址范围前缀由 Microsoft 自动管理，包含在“服务标记”内。

#### <a name="service-tag"></a>服务标记
服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而最大限度地降低频繁更新操作组的网络安全规则的复杂性。

1. 在 Azure 门户中的“Azure 服务”下搜索“网络安全组”。
2. 单击“添加”，创建一个网络安全组。

   1. 添加资源组名称，然后输入“实例详细信息”。
   1. 依次单击“查看 + 创建”、“创建”。
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="有关如何创建网络安全组的示例。"border="true":::

3. 转到“资源组”，然后单击你创建的网络安全组。

    1. 选择“入站安全规则”。
    1. 单击“添加”。
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="有关如何添加服务标记的示例。"border="true":::

4. 此时将在右侧窗格中打开一个新窗口。
    1.  选择“源”：**服务标记**
    1.  源服务标记：**ActionGroup**
    1.  单击 **添加**。
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="有关如何添加服务标记的示例。"border="true":::

## <a name="next-steps"></a>后续步骤
* 详细了解[短信警报行为](./alerts-sms-behavior.md)。  
* 获取[对活动日志警报 webhook 架构的了解](./activity-log-alerts-webhook.md)。  
* 详细了解有关警报的[速率限制](./alerts-rate-limiting.md)。
* 获取[活动日志警报概述](./alerts-overview.md)，了解如何接收警报。  
* 了解如何[配置每次发布服务运行状况通知时的警报](../../service-health/alerts-activity-log-service-notifications-portal.md)。

