---
title: 在 Azure 上开发安全的应用程序
description: 本文讨论在 Web 应用程序项目的实现和验证阶段要考虑的最佳做法。
author: Johnnytechn
manager: barbkess
ms.author: v-johya
ms.date: 12/03/2020
origin.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6e8d138cd5f5853a14315ffadf916e33a917ce68
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97104301"
---
# <a name="develop-secure-applications-on-azure"></a>在 Azure 上开发安全的应用程序
本文介绍了在为云开发应用程序时要考虑的安全活动和控制措施。 涵盖在 Microsoft [安全开发生命周期 (SDL)](https://docs.microsoft.com/previous-versions/windows/desktop/cc307891(v=msdn.10)) 的实现和验证阶段要考虑的安全问题和概念。 目标是帮助你定义可用于开发更安全的应用程序的活动和 Azure 服务。

本文涵盖以下 SDL 阶段：

- 实现
- 验证

## <a name="implementation"></a>实现
实现阶段的重点是建立早期预防的最佳做法，检测并删除代码中的安全性问题。
假设应用程序将以你不打算使用的方式使用。 这有助于防止意外或故意滥用应用程序。

### <a name="perform-code-reviews"></a>执行代码评审

在签入代码之前，请执行代码评审以提高总体代码质量并降低生成错误的风险。 可以使用 Visual Studio 来管理代码评审过程。

### <a name="perform-static-code-analysis"></a>执行静态代码分析

[静态代码分析](https://owasp.org/www-community/controls/Static_Code_Analysis)（也称为源代码分析）通常作为代码评审的一部分执行。 静态代码分析通常是指运行静态代码分析工具，通过使用排斥检查和数据流分析等技术查找非运行代码中的潜在漏洞。

<!-- code review tool not available-->

### <a name="validate-and-sanitize-every-input-for-your-application"></a>验证并清理应用程序的每个输入

将所有输入视为不受信任，以保护应用程序免受最常见的 Web 应用程序漏洞的攻击。 不受信任的数据是进行注入攻击的载体。 应用程序的输入包括 URL 中的参数、来自用户的输入、来自数据库或 API 的数据以及用户可能操作的任何传入内容。 在应用程序以任何方式使用数据（包括将其显示回用户）之前，应用程序应[验证](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs)数据在语法和语义上是否有效。

在数据流的早期验证输入内容，确保只有格式正确的数据才能进入工作流。 你不希望格式错误的数据保存在数据库中或触发下游组件中的故障。

阻止列表和允许列表是执行输入语法验证的两种常用方法：

  - 阻止列表试图检查给定的用户输入不包含“已知为恶意”内容。

  - 允许列表试图检查给定的用户输入是否与一组“已知良好”输入匹配。 基于字符的允许列表是一种允许列表形式，应用程序检查用户输入是否只包含“已知良好”字符或输入是否匹配已知格式。
    例如，这可能需要检查用户名是否只包含字母数字字符，或者用户名是否正好包含两个数字。

允许列表是构建安全软件的首选方法。
阻止列表很容易出错，因为不可能考虑到一个完整的潜在错误输入列表。

在服务器上执行此操作，而不是在客户端（或在服务器和客户端）执行。

### <a name="verify-your-applications-outputs"></a>验证应用程序的输出

任何输出，无论是在视觉上还是在文档中呈现，都应始终进行编码和转义。 [转义](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29)（也称为输出编码），用于帮助确保不受信任的数据不是注入攻击的载体。 转义与数据验证相结合，提供了分层防御，以提高整个系统的安全性。

通过转义，可确保所有内容都显示为“输出”。 转义还可以让解释器知道数据不打算执行，这可以防止攻击的发生。 这是另一种常见的攻击技术，称为跨站点脚本攻击 (XSS)。

如果使用来自第三方的 Web 框架，则可以使用 [OWASP XSS 保护备忘单](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)验证你在网站上输出编码的选项。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>请在联系数据库时使用参数化查询

不要在代码中动态创建内联数据库查询并将其直接发送到数据库。 插入到应用程序中的恶意代码可能会导致数据库被盗、被擦除或被修改。 应用程序还可以用于在托管数据库的操作系统上运行恶意操作系统命令。

相反，请使用参数化查询或存储过程。 使用参数化查询时，可以从代码中安全地调用过程并向其传递字符串，而无需担心会将其视为查询语句的一部分。

### <a name="remove-standard-server-headers"></a>删除标准服务器标头

Server、X-Powered-By 和 X-AspNet-Version 等标头会透露有关服务器和底层技术的信息。 建议你取消这些标头以避免对应用程序进行指纹识别。
请参阅[删除 Azure 网站上的标准服务器标头](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

### <a name="segregate-your-production-data"></a>分离生产数据

生产数据或“真实”数据不应用于开发、测试或业务预期之外的任何其他目的。 所有开发和测试都应使用一个屏蔽（匿名）数据集。

这意味着可以访问你的真实数据的人员会更少，从而减少了你的攻击面。 这也意味着可以查看个人数据的员工会更少，从而消除了潜在的保密漏洞。

### <a name="implement-a-strong-password-policy"></a>实现强密码策略

为了防范暴力破解和基于字典的猜测，必须实现强密码策略，确保用户创建复杂密码（例如，最小长度为 12 个字符，必须包含字母数字和特殊字符）。

你可以使用标识框架来创建和实施密码策略。 Azure AD B2C 通过提供[内置策略](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow)、[自助式密码重置](../../active-directory-b2c/user-flow-self-service-password-reset.md)等来帮助你进行密码管理。

为了防范针对默认帐户的攻击，请验证所有密钥和密码是否可替换，并且是否是在安装后生成或替换的。

如果应用程序必须自动生成密码，请确保生成的密码是随机的并具有高熵。

### <a name="validate-file-uploads"></a>验证文件上传

如果你的应用程序允许[文件上传](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)，请考虑针对此风险活动可以采取的预防措施。 许多攻击的第一步是将一些恶意代码导入受到攻击的系统。 使用文件上传可以帮助攻击者实现此目的。 OWASP 提供了验证文件的解决方案，以确保要上传的文件是安全的。

反恶意软件保护有助于识别和删除病毒、间谍软件和其他恶意软件。 可安装 [Microsoft 反恶意软件](../fundamentals/antimalware.md)或 Microsoft 合作伙伴的终结点保护解决方案（[Trend Micro](https://www.trendmicro.com/azure/)、[Broadcom](https://www.broadcom.com/products)、[McAfee](https://www.mcafee.com/us/products.aspx)、[Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) 和 [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)）。

[Microsoft 反恶意软件](../fundamentals/antimalware.md)包括实时保护、计划扫描、恶意软件修正、签名更新、引擎更新、示例报告和排除事件收集等功能。 可将 Microsoft 反恶意软件和合作伙伴解决方案与 [Azure 安全中心](../../security-center/security-center-partner-integration.md)集成，以方便部署和内置检测（警报和事件）。

### <a name="dont-cache-sensitive-content"></a>不缓存敏感内容

不要在浏览器中缓存敏感内容。 浏览器可能会出于缓存和历史记录的存储信息。 这些缓存的文件存储在某个文件夹中，例如，如果使用的是 Internet Explorer，该文件夹为“Internet 临时文件”。 再次浏览这些页面时，浏览器会从缓存显示这些页面。 如果向用户显示了敏感信息（地址、信用卡详细信息、身份证号、用户名），则这些信息可能存储在浏览器的缓存中，通过查看浏览器的缓存或只需按下浏览器的“后退”按钮即可检索。

## <a name="verification"></a>验证
验证阶段包括一项全面的工作，以确保代码符合在前几个阶段建立的安全和隐私原则。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>查找并修复应用程序依赖项中的漏洞

扫描应用程序及其依赖库以识别任何已知的易受攻击组件。 可用于执行此扫描的产品包括 [OWASP 依赖项检查](https://www.owasp.org/index.php/OWASP_Dependency_Check)、[Snyk](https://snyk.io/) 和 [Black Duck](https://www.blackducksoftware.com/)。

由 [Tinfoil Security](https://www.tinfoilsecurity.com/) 提供支持的漏洞扫描可用于 Azure 应用服务 Web 应用。 [Tinfoil Security 通过应用程序服务进行 Tinfoil Security 扫描](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)为开发人员和管理员提供了一种快速、集成且经济的方法，可以在恶意参与者利用漏洞之前发现并解决漏洞。

<!-- not available-->

### <a name="test-your-application-in-an-operating-state"></a>在运行状态中测试应用程序

动态应用程序安全测试 (DAST) 是在运行状态下测试应用程序以查找安全漏洞的过程。 DAST 工具在程序执行时分析程序，以发现安全漏洞，如内存损坏、服务器配置不安全、跨站点脚本攻击、用户权限问题、SQL 注入和其他重要的安全问题。

DAST 不同于静态应用程序安全测试 (SAST)。 SAST 工具在代码不执行时分析源代码或已编译的代码版本，以发现安全漏洞。

执行 DAST，最好在安全专业人员（[渗透测试人员](../fundamentals/pen-testing.md)或漏洞评估人员）的协助下进行。 如果没有安全专业人员，则可以使用 Web 代理扫描程序并通过一定的培训来执行 DAST。 尽早插入 DAST 扫描程序，以确保不会在代码中引入明显的安全问题。 有关 Web 应用程序漏洞扫描程序的列表，请参阅 [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) 网站。

### <a name="perform-fuzz-testing"></a>执行模糊测试

在[模糊测试](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)中，通过故意向应用程序引入不良格式或随机数据诱发程序故障。 诱发程序故障有助于在应用程序发布之前揭示潜在的安全问题。

安全风险检测是 Microsoft 独有的模糊测试服务，用于查找软件中的关键安全 Bug。

### <a name="conduct-attack-surface-review"></a>执行攻击面评审

在代码完成后评审攻击面，这有助于确保考虑了对应用程序或系统的任何设计或实现更改。 也有助于确保由于更改而创建的任何新的攻击途径（包括威胁模型）都得到了评审和缓解。

通过扫描应用程序，可以生成攻击面的图片。 Microsoft 提供了一种名为 [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487) 的攻击面分析工具。 可以从许多商业动态测试和漏洞扫描工具或服务中进行选择，包括 [OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)、[Arachni](http://arachni-scanner.com/) 和 [w3af](http://w3af.sourceforge.net/)。 这些扫描工具对应用进行爬网，并映射可通过 Web 访问的应用程序部分。 还可以在 Azure 市场中搜索类似的开发人员工具。

### <a name="perform-security-penetration-testing"></a>执行安全渗透测试

确保应用程序的安全与测试其他任何功能一样重要。 将[渗透测试](../fundamentals/pen-testing.md)规定为生成和部署过程的标准组成部分。 针对已部署应用程序对定期安全测试和漏洞扫描进行计划，并监视打开的端口、终结点和攻击活动。

<DevOps not available -->

## <a name="next-steps"></a>后续步骤
下面的文章中推荐了一些安全控制措施和活动，有助于设计和部署安全的应用程序。

- [设计安全的应用程序](secure-design.md)
- [部署安全的应用程序](secure-deploy.md)

