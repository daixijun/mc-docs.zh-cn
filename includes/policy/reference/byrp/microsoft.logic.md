---
author: rockboyfor
ms.service: azure-policy
ms.topic: include
origin.date: 10/20/2020
ms.date: 11/30/2020
ms.testscope: no
ms.testdate: 06/08/2020
ms.author: v-yeche
ms.custom: generated
ms.openlocfilehash: 9ceb7c48a7651036a168660d307bccd3a1579f85
ms.sourcegitcommit: abc264824023c7cb7abf74898fc1b375e730545f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231344"
---
<!--Verified successfully from rename articles-->

<!--UPDATE TO MEET SCORECARD REQUEIRMENT OF SENVENTY-->
<!--CORRECT ON this diagnostic setting-->
<!--CORRECT ON Logic Apps that-->

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[将逻辑应用的诊断设置部署到事件中心](https://portal.azure.cn/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1dae6c7-13f3-48ea-a149-ff8442661f60) |在创建或更新缺少此诊断设置的任何逻辑应用时，为逻辑应用部署诊断设置以将其流式传输到区域事件中心。 |DeployIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogicApps_DeployDiagnosticLog_Deploy_EventHub.json) |
|[将逻辑应用的诊断设置部署到 Log Analytics 工作区](https://portal.azure.cn/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb889a06c-ec72-4b03-910a-cb169ee18721) |在创建或更新缺少此诊断设置的任何逻辑应用时，为逻辑应用部署诊断设置以将其流式传输到区域 Log Analytics 工作区。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogicApps_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[应启用逻辑应用的诊断日志](https://portal.azure.cn/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

<!-- Update_Description: update meta properties, wording update, update link -->
