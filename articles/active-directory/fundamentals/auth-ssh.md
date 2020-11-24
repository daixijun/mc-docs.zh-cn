---
title: 使用 Azure Active Directory 进行 SSH 身份验证
description: 有关如何实现与 Azure Active Directory 的 SSH 集成的体系结构指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: v-junlch
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b91671520858c8a9f0c350be74dcfbfe6b350f6d
ms.sourcegitcommit: 59810f8eba5e430d85a595e346d3b7fb6e4a0102
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502234"
---
# <a name="ssh"></a>SSH  

安全外壳 (SSH) 是一种网络协议，它为在不安全的网络上安全地运行网络服务提供加密。 SSH 还提供命令行登录、执行远程命令以及安全传输文件等功能。 它通常用于基于 UNIX 的系统，如 Linux®。 SSH 取代了 Telnet 协议，后者在不安全的网络中不提供加密。 

Azure Active Directory (Azure AD) 为在 Azure 上运行的基于 Linux® 的系统提供了虚拟机 (VM) 扩展。 

## <a name="use-when"></a>何时使用 

* 使用需要远程登录的基于 Linux® 的 VM

* 在基于 Linux® 的系统中执行远程命令

* 在不安全的网络中安全传输文件

![Azure AD 与 SSH 协议的关系图](./media/authentication-patterns/ssh-auth.png)

SSH 与 Azure AD

## <a name="components-of-system"></a>系统组件 

* **用户**：启动 SSH 客户端以与 Linux® VM 建立连接，并提供用于身份验证的凭据。

* **Web 浏览器**：用户与之交互的组件。 它与标识提供者 (Azure AD) 通信，以安全地对用户进行身份验证和授权。

* **SSH 客户端**：驱动连接设置过程。

* **Azure AD**：使用设备流验证用户的身份，并向 Linux VM 颁发令牌。

* **Linux VM**：接受令牌并提供成功连接。

## <a name="implement-ssh-with-azure-ad"></a>通过 Azure AD 实现 SSH 

* [OAuth 2.0 设备代码流 - Microsoft 标识平台](/active-directory/develop/v2-oauth2-device-code)

* [与 Azure Active Directory (akamai.com) 集成](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

 

