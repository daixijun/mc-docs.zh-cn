---
title: include 文件
description: include 文件
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: include
origin.date: 09/03/2018
ms.date: 12/14/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 1a0fb95de19a7881203a78383e2bd47b953798cd
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105661"
---
有关物理服务器复制的配置/进程服务器要求 

组件  | **要求** 
--- | ---
硬件设置  | 
CPU 核心数 | 8 
RAM | 16 GB
磁盘数目 | 3，包括操作系统磁盘、进程服务器缓存磁盘和用于故障回复保留驱动器 
可用磁盘空间（进程服务器缓存） | 600 GB
可用磁盘空间（保留磁盘） | 600 GB
 | 
软件设置  | 
操作系统 | Windows Server 2012 R2 <br> Windows Server 2016
操作系统区域设置 | 美国英语
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V 
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10)
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - 启用 [FastCGI](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 设置。
IP 地址类型 | 静态 
| 
访问设置  | 
MYSQL | MySQL 应安装在配置服务器上。 可以手动安装，或者让 Site Recovery 在部署期间进行安装。 为安装 Site Recovery，请检查计算机是否可以访问 http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi 。
URL | 配置服务器需要访问这些 URL（直接或通过代理）：<br/><br/> Azure AD：`login.chinacloudapi.cn`；`*.accesscontrol.chinacloudapi.cn`<br/><br/> 复制数据传输：`*.backup.windowsazure.cn`<br/><br/> 复制管理：`*.hypervrecoverymanager.windowsazure.cn`；`https://management.chinacloudapi.cn`；`*.services.visualstudio.com`<br/><br/> 存储访问：`*.blob.core.chinacloudapi.cn`<br/><br/> 时间同步：`time.nist.gov`；`time.windows.com`<br/><br/> 遥测（可选）：`dc.services.visualstudio.com`
防火墙 | 基于 IP 地址的防火墙规则应允许与 Azure URL 通信。 为了简化和限制 IP 范围，建议使用 URL 筛选。<br/><br/>**对于 Azure 中国 IP：**<br/><br/>- 允许 [Azure IP 范围和服务标记 - 中国云](https://www.microsoft.com/en-us/download/confirmation.aspx?id=57062)和 HTTPS (443) 端口。<br/><br/> - 允许订阅的 Azure 区域的 IP 地址范围以支持 Azure Active Directory、备份、复制和存储所需的 URL。<br/><br/>

<!--Not Available on  login.microsoftonline.us-->
<!--Not Available on *.backup.windowsazure.us-->
<!--Not Available on *.hypervrecoverymanager.windowsazure.us-->
<!--Not Available on *.blob.core.usgovcloudapi.net-->
<!--Not Available on Virginia, Texas, Arizona, and Iowa-->

配置/进程服务器大小要求 

CPU  | **内存** | 缓存磁盘  | **数据更改率** | 复制的计算机 
--- | --- | --- | --- | ---
8 个 vCPU<br/><br/> 2 个插槽 * 4 个核心 \@ 2.5 GHz | 16GB | 300 GB | 500 GB 或更少 | < 100 台计算机
12 个 vCPU<br/><br/> 2 个插槽 * 6 个核心 \@ 2.5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 到 150 台计算机
16 个 vCPU<br/><br/> 2 个插槽 * 8 个核心 \@ 2.5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 台计算机

<!-- Update_Description: update meta properties, update link -->