---
title: 在 Azure 中排查出站 SMTP 连接问题 | Azure
description: 了解建议的电子邮件发送方法，以及排查 Azure 中的出站 SMTP 连接问题的方法。
services: virtual-network
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 01/04/2021
author: rockboyfor
ms.date: 01/18/2021
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: a3d3b3260c9b52c51fbcaaf30ba9fc39c29e08e3
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98229809"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>在 Azure 中排查出站 SMTP 连接问题

从 2017 年 11 月 15 日开始，从虚拟机 (VM) 直接发送到外部域（例如 outlook.com）的出站电子邮件仅适用于 Azure 中的某些订阅类型。 将阻止使用 TCP 端口 25 的出站 SMTP 连接。 （端口 25 主要用于未经身份验证的电子邮件传递。）

<!--Not Available on gmail.com-->

此行为更改仅适用于在 2017 年 11 月 15 日之后创建的订阅和部署。

## <a name="recommended-method-of-sending-email"></a>推荐的电子邮件发送方法

建议使用经过身份验证的 SMTP 中继服务从 Azure VM 或 Azure 应用服务发送电子邮件。 （这些中继服务通常通过 TCP 端口 587 或 443 连接，但它们支持其他端口。）这些服务用于维护 IP 或域信誉，以尽量降低第三方电子邮件提供商拒绝邮件的可能性。 [SendGrid](https://sendgrid.com/partners/azure/) 就是这样一个 SMTP 中继服务，但还有其他服务。 还可具有本地运行并能使用的安全的 SMTP 中继服务。

在 Azure 中使用这些电子邮件传递服务不受限制（无论是哪种订阅类型）。

## <a name="enterprise-agreement"></a>企业协议

企业协议 Azure 用户仍然无需使用经身份验证的中继便可发送电子邮件。 新的和现有的企业协议用户均可以尝试从 Azure VM 直接向外部电子邮件提供商发送出站电子邮件，且没有任何 Azure 平台限制。 不保证电子邮件提供商会接受来自任何给定用户的传入电子邮件。 但是，Azure 平台不会阻止企业协议订阅中针对 VM 的交付尝试。 必须直接与电子邮件提供商接洽，以修复涉及特定提供商的各项邮件发送或垃圾邮件筛选问题。

## <a name="standard-pay-in-advance-offer"></a>标准预付费套餐

如果你已在 2017 年 11 月 15 日前注册标准预付费套餐订阅，那么从技术上来说，你仍然能够尝试发送出站电子邮件。 你仍然能够尝试从这些订阅中的 Azure VM 直接向外部电子邮件提供商发送出站电子邮件，而且不存在任何 Azure 平台限制。 同样地，不保证电子邮件提供商会接受来自任何给定用户的传入电子邮件。 用户必须直接与电子邮件提供商接洽，以修复涉及特定提供商的各项邮件发送或垃圾邮件筛选问题。

对于在 2017 年 11 月 15 日之后创建的标准预付费套餐订阅，存在一些技术限制，它们会阻止从订阅中的 VM 直接发送电子邮件。 如果希望能够将来自 Azure VM 的电子邮件直接发送给外部电子邮件提供商（不使用经过身份验证的 SMTP 中继），并且你的帐户具有良好的付款历史记录，那么你可请求删除限制。 世纪互联会根据自己的判断审批请求，并且只会在执行额外的防欺诈检查后才授予权限。 若要提交请求，请使用以下支持类型开启一个支持案例：**订阅管理** 服务类型：**维护通知相关问题**。 请确保添加详细信息，解释为什么要直接向电子邮件提供商发送邮件而不是使用经身份验证的中继。

<!--MOONCAKE CUSTOMIZE NOT AVIABLLE ON You can do so in the **Connectivity** section of the **Diagnose and Solve** blade for an Azure Virtual Network resource in the Azure portal. -->
<!--MOONCAKE CORRECT ON support type: **Subscription Management** service type: **Maintenance notification related issue**-->

在 Azure 门户中豁免标准预付费套餐订阅并停止再重启 VM 后，该订阅中的所有 VM 在此后都会被豁免。 该豁免仅适用于请求的订阅，并且仅适用于直接路由到 Internet 的 VM 流量。

> [!NOTE]
> Azure 保留在确定发生违反服务条款的情况下撤销这些豁免的权利。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-trial"></a>MSDN、Azure Pass、Azure 开放许可、教育、面向学生的 Azure、Visual Studio 和试用版

如果你在 2017 年 11 月 15 日之后创建了以下订阅类型之一，则将存在技术限制，它们会阻止从订阅中的 VM 直接发送给电子邮件提供商的电子邮件：
- MSDN
- Azure Pass
- Azure 开放许可
- 教育
- 面向学生的 Azure
- 试用
- 任何 Visual Studio 订阅  

此限制是为了防止滥用邮件。 不接受去除这些限制的请求。

如果你正在使用这些订阅类型，建议使用 SMTP 中继服务（如本文前面部分所述），或者更改订阅类型。

## <a name="cloud-solution-provider"></a>云解决方案提供商

<!--Not Available on  the **Connectivity** section of the **Diagnose and Solve** pane-->

如果你正在通过云解决方案提供商使用 Azure 资源，则可在 [Azure 支持站点](https://support.azure.cn/support/support-azure/)中请求删除限制。 如果你的请求被接受，你的订阅将被启用，或者你将收到后续步骤的说明。

## <a name="azure-partner-network-bizspark-plus-or-azure-sponsorship"></a>Azure 合作伙伴网络、BizSpark Plus 或 Azure 赞助

对于在 2017 年 11 月 15 日之后创建的以下类型的订阅，将存在一些技术限制，它们会阻止订阅中的 VM 直接发送电子邮件：

- Azure 合作伙伴网络 (MPN)
- BizSpark Plus
- Azure 赞助

<!--CORRECT ON  Network connectivity problems -->

如果希望能够将来自 Azure VM 的电子邮件直接发送给外部电子邮件提供商（不使用经过身份验证的 SMTP 中继），可使用以下问题类型打开支持案例来发出请求：“技术” > “虚拟网络” > “连接”>“网络连接问题”，并在“标题”字段中键入“无法发送电子邮件（SMTP/端口 25）”    。 请务必添加详细信息，解释你的部署为什么需要向邮件直接发送给电子邮件提供商，而不使用经过身份验证的中继。 请求将由 Microsoft 自行决定审查和审批。 只有在完成其他反欺诈检查后，才能授予请求。 

在 Azure 门户中豁免订阅并停止再重启 VM 后，该订阅中的所有 VM 在此后都会被豁免。 该豁免仅适用于请求的订阅，并且仅适用于直接路由到 Internet 的 VM 流量。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

<!--CORRECT ON  Network connectivity problems -->

如果仍需帮助，请[联系支持人员](https://support.azure.cn/support/support-azure/)，以快速解决问题。 使用此问题类型：“技术” > “虚拟网络” > “连接”>“网络连接问题”，并在“标题”字段中键入“无法发送电子邮件（SMTP/端口 25）”    。

<!--MOONCAKE CORRECT ON support type: **Subscription Management** service type: **Maintenance notification related issue**-->

<!-- Update_Description: update meta properties, wording update, update link -->