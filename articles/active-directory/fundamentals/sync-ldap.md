---
title: 使用 Azure Active Directory 进行 LDAP 同步
description: 有关如何实现与 Azure Active Directory 的 LDAP 同步的体系结构指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: v-junlch
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 754eed6ad253fed960265159d93e3eca82b3e710
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97003685"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>使用 Azure Active Directory 进行 LDAP 同步

轻型目录访问协议 (LDAP) 是在 TCP/IP 堆栈上运行的目录服务协议。 它提供了用于连接到、搜索和修改 Internet 目录的机制。 LDAP 目录服务基于客户端-服务器模型，其功能是启用对现有目录的访问。 许多公司依赖于本地 LDAP 服务器来存储其关键业务应用的用户和组。 

Azure Active Directory (Azure AD) 可使用 Azure AD Connect 来替换 LDAP 同步。 Azure AD Connect 同步服务会执行与在本地环境和 Azure AD 之间同步标识数据相关的所有操作。 

## <a name="use-when"></a>何时使用

需要在本地 LDAP v3 目录和 Azure AD 之间同步标识数据。 

![体系结构图](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>系统组件

* **用户**：访问一个依赖于使用 LDAP v3 目录对用户和密码进行排序的应用程序。

* Web 浏览器：用户与之交互以访问应用程序外部 URL 的组件

* **Web 应用**：依赖于 LDAP v3 目录的应用程序。

* **Azure AD**：Azure AD 通过 Azure AD Connect 从组织的本地 LDAP 目录中同步标识信息（用户、组、密码）。 

* Azure AD Connect：用于将本地标识基础结构连接到 Azure AD 的工具。 向导和引导式体验可帮助部署和配置进行连接所需要的必备项和组件。 

* 自定义连接器：一个通用 LDAP 连接器，用于将 Azure AD Connect 同步服务与 LDAP v3 服务器集成。 该连接器位于 Azure AD Connect 上。

* **Active Directory**：Active Directory 是大多数 Windows Server 操作系统中包含的一项目录服务。 运行 Active Directory 目录服务的服务器被称为域控制器，它们对 Windows 域中的所有用户和计算机执行身份验证和授权。

* LDAP v3 服务器：兼容 LDAP 协议的目录，其中存储用于目录服务身份验证的企业用户和密码。

## <a name="implement-ldap-synchronization-with-azure-ad"></a>使用 Azure AD 实现 LDAP 同步

* [Azure AD Connect 安装路线图](../hybrid/how-to-connect-install-roadmap.md) 

* [LDAP 连接器概述及 LDAP 连接器创建](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > LDAP 连接器是一种高级配置，它要求对 Forefront Identity Manager 和/或 Microsoft Identity Manager 有一定的了解。 如果在生产环境中使用时有与此配置相关的问题，建议通过[顶级支持](https://support.microsoft.com/premier)或 Microsoft 合作伙伴网络获得答案。


