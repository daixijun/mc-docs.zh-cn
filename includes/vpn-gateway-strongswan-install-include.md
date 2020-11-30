---
title: include 文件
description: include 文件
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 08/14/2019
ms.date: 09/07/2020
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: ea86f1ce184dcfa466a204d952d9461aaa9c60c5
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301117"
---
以下配置用于执行下面的步骤：

- 计算机：Ubuntu Server 18.04
- 依赖项：strongSwan


使用以下命令安装所需的 strongSwan 配置：

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

使用以下命令安装 Azure 命令行接口：

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[有关如何安装 Azure CLI 的其他说明](/cli/install-azure-cli-apt)
