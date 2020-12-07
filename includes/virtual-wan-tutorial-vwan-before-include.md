---
title: include 文件
description: include 文件
services: virtual-wan
author: rockboyfor
ms.service: virtual-wan
ms.topic: include
origin.date: 09/12/2018
ms.date: 03/30/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 2cac701ff13a750cf90b85d52a51b08eef89d8af
ms.sourcegitcommit: 87b6bb293f39c5cfc2db6f38547220a13816d78f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96477189"
---
在开始配置之前，请验证是否符合以下条件：

* 如果已有要连接的虚拟网络，请验证本地网络的任何子网都没有与之重叠。 虚拟网络不需要网关子网，并且不能包含任何虚拟网络网关。 如果没有虚拟网络，可以使用本文中的步骤创建一个。
* 获取中心区域的 IP 地址范围。 中心是一个虚拟网络，为中心区域指定的地址范围不能与连接到的现有虚拟网络重叠。 此外，它也不能与连接到本地的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，请咨询能够提供此类详细信息的人员。
* 如果没有 Azure 订阅，请在开始前创建一个[试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

<!-- Update_Description: update meta properties, wording update, update link -->