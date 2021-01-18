---
author: WenJason
ms.author: v-jay
ms.service: azure-stack
ms.topic: include
origin.date: 12/9/2020
ms.date: 01/11/2021
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: bc158cce823de72fb11e0b8717adad1d610ae6aa
ms.sourcegitcommit: 3f54ab515b784c9973eb00a5c9b4afbf28a930a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97894390"
---
### <a name="when-installing-az-module-falsely-throws-admin-rights-required-error"></a>安装 Az 模块时，错误地引发“需要管理员权限”错误

- 适用于：此问题适用于 2002 及更高版本
- 原因：在提升的提示符下安装模块时，会引发错误。 错误指出“`Administrator rights required`”。
- 补救措施：关闭会话并启动权限提升的新 PowerShell 会话。 请确保会话中没有加载现有的 Az. Accounts 模块。
- 发生次数：通用