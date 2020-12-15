---
title: 扩展资源类型
description: 列出用于扩展其他资源类型的功能的 Azure 资源类型。
ms.topic: conceptual
origin.date: 11/14/2020
author: rockboyfor
ms.date: 12/14/2020
ms.testscope: yes
ms.testdate: 08/24/2020
ms.author: v-yeche
ms.openlocfilehash: cff0c0c2c73beee1c3f0be3a11ef8636d13b909e
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004210"
---
<!--Verified successfully/Redirect file-->
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>用于扩展其他资源的功能的资源类型

扩展资源是扩展另一资源的功能的资源。 例如，资源锁是扩展资源。 对另一资源应用资源锁可以防止该资源被删除或修改。 单独创建资源锁没有意义。 扩展资源始终会应用到其他资源。

## <a name="microsoftadvisor"></a>Microsoft.Advisor

- Microsoft.Advisor/configurations
- Microsoft.Advisor/recommendations
- Microsoft.Advisor/suppressions

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

- Microsoft.AlertsManagement/alerts

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft.Authorization/denyAssignments
- Microsoft.Authorization/locks
- Microsoft.Authorization/policyAssignments
- Microsoft.Authorization/policyDefinitions
- Microsoft.Authorization/policyExemptions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Microsoft.Authorization/roleDefinitions

<!--Not Available on ## Microsoft.Automanage-->
<!--Not Available on ## Microsoft.Billing-->

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints

<!--Not Available on ## Microsoft.Consumption-->

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

- Microsoft.ContainerInstance/serviceAssociationLinks

<!--Not Available on ## Microsoft.CostManagement-->
<!--Not Available on ## Microsoft.CustomProviders-->
    
## <a name="microsofteventgrid"></a>Microsoft.EventGrid

- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

- Microsoft.GuestConfiguration/configurationProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software

## <a name="microsoftinsights"></a>microsoft.insights

- microsoft.insights/baseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricbaselines
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topology
- microsoft.insights/transactions

    
<!--Not Available on ## Microsoft.KubernetesConfiguration-->
    
## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

- Microsoft.Maintenance/applyUpdates
- Microsoft.Maintenance/configurationAssignments
- Microsoft.Maintenance/updates

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

- Microsoft.ManagedIdentity/Identities

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

- Microsoft.OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

- Microsoft.OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

- Microsoft.PolicyInsights/attestations
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/events
- Microsoft.ResourceHealth/impactedResources
- Microsoft.ResourceHealth/notifications

## <a name="microsoftresources"></a>Microsoft.Resources

- Microsoft.Resources/links
- Microsoft.Resources/tags

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments
- Microsoft.Security/Compliances
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/devices
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/iotSensors
- Microsoft.Security/jitPolicies
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.Security/sqlVulnerabilityAssessments 

<!--Not Available on ## Microsoft.SecurityInsights-->
<!--Not Available on ## Microsoft.SerialConsole.PPE-->
<!--Not Available on ## Microsoft.SoftwarePlan-->
<!--Not Available on ## microsoft.support-->
<!--Not Available on ## Microsoft.WorkloadMonitor-->

## <a name="next-steps"></a>后续步骤

- 若要在 Azure 资源管理器模板中获取扩展资源的资源 ID，请使用 [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid)。

<!--Not Available on [Event Grid Event Subscriptions](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)-->

<!-- Update_Description: update meta properties, wording update, update link -->