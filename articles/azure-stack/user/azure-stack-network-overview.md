---
title: Azure Stack Hub 网络简介
description: 了解 Azure Stack Hub 网络
author: WenJason
ms.topic: conceptual
origin.date: 11/16/2020
ms.date: 12/07/2020
ms.author: v-jay
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 3b81e6565d50c32116dd01cd9349a63f4b593b38
ms.sourcegitcommit: a1f565fd202c1b9fd8c74f814baa499bbb4ed4a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96508084"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Azure Stack Hub 网络简介

Azure Stack Hub 提供可结合使用或单独使用的各种网络功能：

- **Azure Stack Hub 资源之间的连接**  
    在云中的安全专用虚拟网络内将 Azure 资源连接在一起。
- **Internet 连接**  
    通过 Internet 与 Azure Stack Hub 资源进行通信。
- **本地连接**  
    在 Internet 上通过虚拟专用网络 (VPN) 或者通过与 Azure Stack Hub 建立的专用连接将本地网络连接到 Azure Stack Hub 资源。 
    > [!IMPORTANT]
    > 必须创建 VPN 或公共 IP 连接才能访问本地资源。
- 负载均衡和流量方向   
    对发往同一位置中的服务器的流量进行负载均衡，并将流量定向到不同位置中的服务器。
- **安全性**  
    筛选网络子网或单个 VM 之间的网络流量。
- **路由**  
    使用默认路由，或者完全控制 Azure Stack Hub 与本地资源之间的路由。
- **可管理性**  
    监视和管理 Azure Stack Hub 网络资源。
- **部署和配置工具**  
    使用基于 Web 的门户或跨平台命令行工具来部署和配置网络资源。


## <a name="next-steps"></a>后续步骤

* [Azure Stack Hub 网络注意事项](azure-stack-network-differences.md)
