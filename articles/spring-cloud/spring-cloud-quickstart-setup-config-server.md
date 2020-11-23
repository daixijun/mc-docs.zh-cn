---
title: 快速入门 - 设置 Azure Spring Cloud Config Server
description: 描述用于应用部署的 Azure Spring Cloud Config Server 的设置。
author: MikeDodaro
ms.author: v-junlch
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 07aa6489859d9dec5d6df1554d4144be0c5f137b
ms.sourcegitcommit: b072689d006cbf9795612acf68e2c4fee0eccfbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849358"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>快速入门：设置 Azure Spring Cloud Config Server

Azure Spring Cloud 配置服务器是分布式系统的集中式配置服务。 它使用当前支持本地存储、Git 和 Subversion 的可插入存储库层。 在此快速入门中，你将设置配置服务器以从 Git 存储库获取数据。

Azure Spring Cloud Config Server 是分布式系统的集中式配置服务。 它使用当前支持本地存储、Git 和 Subversion 的可插入存储库层。  设置 Config Server，将微服务应用部署到 Azure Spring Cloud。

## <a name="prerequisites"></a>先决条件

* [安装 JDK 8](https://docs.microsoft.com/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [注册 Azure 订阅](https://www.azure.cn/pricing/1rmb-trial/)
* （可选）[安装 Azure CLI 版本 2.0.67 或更高版本](/cli/install-azure-cli?preserve-view=true&view=azure-cli-latest)，并使用以下命令安装 Azure Spring Cloud 扩展：`az extension add --name spring-cloud`
* （可选）[安装 Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) 并[登录](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud Config Server 过程

#### <a name="portal"></a>[门户](#tab/Azure-portal)

以下过程使用 Azure 门户设置 Config Server，以部署 [Piggymetrics 示例](spring-cloud-quickstart-sample-app-introduction.md)。

1. 转到服务的“概览”页，选择“配置服务器”。 

2. 在“默认存储库”部分，将“URI”设置为“https://github.com/Azure-Samples/piggymetrics-config” 。

3. 选择“应用”以保存所做的更改。

    ![ASC 门户的屏幕截图](./media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

以下过程使用 Azure CLI 设置 Config Server，以部署 [Piggymetrics 示例](spring-cloud-quickstart-sample-app-introduction.md)。

使用项目的 Git 存储库的位置设置 Config Server：

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---

## <a name="troubleshooting-of-azure-spring-cloud-config-server"></a>Azure Spring Cloud Config Server 的故障排除

以下过程说明如何对 Config Server 设置进行故障排除。

1. 在 Azure 门户中，转到服务“概览”页，然后选择“日志” 。 
1. 选择“查询”和“显示包含‘错误’或‘异常’术语的应用程序日志” 。 
1. 单击 **“运行”** 。 
1. 如果在日志中发现错误“java.lang.illegalStateException”，则表明 Spring Cloud Service 无法从 Config Server 中找到属性。

    [ ![ASC 门户运行查询](./media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png) ](./media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. 转到服务“概述”页。
1. 选择“诊断并解决问题”。 
1. 选择“Config Server”检测程序。

    [ ![ASC 门户诊断问题](./media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) ](./media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. 单击“Config Server 运行状况检查”。

    [ ![ASC 门户精灵](./media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png) ](./media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. 单击“Config Server 状态”以查看来自检测程序的详细信息。

    [ ![ASC 门户运行状况状态](./media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png) ](./media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## <a name="next-steps"></a>后续步骤

在此快速入门中，你创建了 Azure 资源，如果这些资源保留在订阅中，将继续产生费用。 如果不打算继续学习下一个快速入门，请参阅[清理资源](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources)。 否则，请继续学习下一个快速入门：

> [!div class="nextstepaction"]
> [构建和部署应用](spring-cloud-quickstart-deploy-apps.md)

