---
title: include 文件
description: include 文件
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 10/28/2020
ms.date: 11/23/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 17046ad3546b49927e6b773e22495bd6208155dd
ms.sourcegitcommit: 054636c134cc0f53c194a6b76668644e18d1c4fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95970655"
---
获取根证书的 .cer 文件。 你可以使用通过企业解决方案生成的根证书（推荐），或者生成自签名证书。 创建根证书后，将公共证书数据（不是私钥）作为 Base64 编码的 X.509 .cer 文件导出。 稍后，请将此文件上传到 Azure。

* **企业证书：** 如果使用的是企业级解决方案，可以使用现有的证书链。 获取要使用的根证书的 .cer 文件。
* **自签名根证书：** 如果使用的不是企业证书解决方案，请创建自签名根证书。 否则，创建的证书将不兼容 P2S 连接，客户端在尝试连接时会收到连接错误。 可以使用 Azure PowerShell、MakeCert 或 OpenSSL。 以下文章中的步骤介绍了如何生成兼容的自签名根证书：

  * [Windows 10 PowerShell 指令](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md)：这些指令需要 Windows 10 和 PowerShell 才能生成证书。 从根证书生成的客户端证书可以安装在任何受支持的 P2S 客户端上。
  * [MakeCert 指令](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)：使用 MakeCert 的前提是，无法接触用于生成证书的 Windows 10 计算机。 虽然 MakeCert 已弃用，但仍可使用它来生成证书。 从根证书生成的客户端证书可以安装在任何受支持的 P2S 客户端上。
  * [Linux 说明](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)。
