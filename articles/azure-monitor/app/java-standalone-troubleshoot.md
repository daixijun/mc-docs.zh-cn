---
title: 对适用于 Java 的 Azure Monitor Application Insights 进行故障排除
description: 了解如何对 Azure Monitor Application Insights 的 Java 代理进行故障排除
ms.topic: conceptual
ms.author: v-johya
ms.date: 01/12/2021
ms.custom: devx-track-java
ms.openlocfilehash: 79977e2ea4734432b5acacfa528289acae299288
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231159"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>故障排除指南：适用于 Java 的 Azure Monitor Application Insights

本文介绍了使用 Application Insights 的 Java 代理来检测 Java 应用程序时可能会遇到的一些常见问题。 本文还介绍了解决这些问题的步骤。 Application Insights 是 Azure Monitor 平台服务的一项功能。

## <a name="check-the-self-diagnostic-log-file"></a>检查自诊断日志文件

默认情况下，Application Insights 的 Java 3.0 代理在 `applicationinsights-agent-3.0.0.jar` 文件所在的目录中生成名为 `applicationinsights.log` 的日志文件。

针对你可能遇到的问题查找线索时，此日志文件是要检查的第一个位置。

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>从 Application Insights Java 2.x SDK 进行升级

如果你已在应用程序中使用 Application Insights Java 2.x SDK，则可以继续使用它。 Java 3.0 代理会检测到它。 有关详细信息，请参阅[从 Java 2.x SDK 进行升级](./java-standalone-upgrade-from-2x.md)。

## <a name="upgrade-from-application-insights-java-30-preview"></a>从 Application Insights Java 3.0 预览版进行升级

如果要从 Java 3.0 预览版代理进行升级，请仔细检查所有[配置选项](./java-standalone-config.md)。 JSON 结构在 3.0 正式发布版 (GA) 中已完全更改。

这些更改包括：

-  配置文件名称已从 `ApplicationInsights.json` 更改为 `applicationinsights.json`。
-  `instrumentationSettings` 节点不再存在。 `instrumentationSettings` 中的所有内容已移动到根级别。 
-  `sampling`、`jmxMetrics`、`instrumentation` 和 `heartbeat` 等配置节点已从 `preview` 移出到根级别。

## <a name="some-logging-is-not-auto-collected"></a>某些日志记录不是自动收集的

仅当日志记录首先满足日志记录框架的配置阈值，并且还满足 Application Insights 的配置阈值时，才会捕获日志记录。

若要了解某个特定的日志记录语句是否满足日志记录框架的已配置阈值，最好的方法是确认它是否显示在正常的应用程序日志（例如文件或控制台）中。

有关更多详细信息，请参阅[自动收集的日志记录配置](./java-standalone-config.md#auto-collected-logging)。

## <a name="import-ssl-certificates"></a>导入 SSL 证书

如果使用的是默认的 Java 密钥存储，则它已具有所有 CA 根证书。 你应当不需要导入更多的 SSL 证书。

如果使用的是自定义 Java 密钥存储，则你可能需要将 Application Insights 终结点 SSL 证书导入其中。

### <a name="key-terminology"></a>关键术语
“密钥存储”是证书、公钥和私钥的存储库。 通常，Java 开发工具包发行版具有用于管理它们的可执行文件：`keytool`。

下面的示例是一个简单的命令，用于将 SSL 证书导入到密钥存储：

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>下载和添加 SSL 证书的步骤

1.  打开你习惯使用的浏览器，然后转到用于检测应用程序的连接字符串中存在的 `IngestionEndpoint` URL。

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="屏幕截图显示了 Application Insights 连接字符串。":::

2.  选择浏览器中的“查看站点信息”（锁）图标，然后选择“证书”选项。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="屏幕截图显示了站点信息中的“证书”选项。":::

3.  转到“详细信息”选项卡，然后选择“复制到文件”。
4.  选择“下一步”按钮，选择“Base-64 编码的 X.509 (.CER)”格式，然后再次选择“下一步”。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="证书导出向导的屏幕截图，其中选择了一种格式。":::

5.  指定要在其中保存 SSL 证书的文件。 然后，选择“下一步” > “完成”。  应该会看到“导出成功”消息。
6.  获得证书后，即可将证书导入到 Java 密钥存储中。 使用[前面的命令](#key-terminology)来导入证书。

> [!WARNING]
> 在当前证书到期之前，你需要重复这些步骤以获取新证书。 可以在“证书”对话框的“详细信息”选项卡上找到到期信息。
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="屏幕截图显示了 SSL 证书详细信息。":::

