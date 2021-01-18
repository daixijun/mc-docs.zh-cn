---
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: include
origin.date: 12/9/2020
ms.date: 01/11/2021
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: dc3530876c64a32466d3fe170c28a3064ac59cd5
ms.sourcegitcommit: 3f54ab515b784c9973eb00a5c9b4afbf28a930a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97894389"
---
### <a name="error-thrown-when-installing-the-az-modules"></a>安装 Az 模块时引发错误

- 适用于：此问题适用于 2002 及更高版本
- 原因：安装模块时引发错误。 错误消息的开头为：`Register-PacakgeSource : A parameter cannot be found that matches parameter name. 'PackageManagementProvider'.`，错误消息也可能包含以下文本：`PackageManagement\Install-Package : Cannot convert value "2.0.1-preview" to type "System.Version". Error: "Input string was not in a correct format."`
- 补救措施：请在同一会话中运行以下 cmdlet：  
    `Install-Module PowershellGet -MinimumumVersion 2.3.0 -Force`  
关闭会话并启动权限提升的新 PowerShell 会话。
- 发生次数：通用