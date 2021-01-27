---
author: mmacy
ms.author: v-junlch
ms.date: 01/05/2021
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: bb873ef0606f193ef6ef679ceb9c38d50bdc94fb
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98793969"
---
## <a name="conditional-access-and-claims-challenges"></a>条件访问和声明质询

以无提示方式获取令牌时，如果你尝试访问的 API 需要[条件访问声明质询](../articles/active-directory/develop/v2-conditional-access-dev-guide.md)（例如 MFA 策略），则应用程序可能会收到错误。

处理此错误的模式是使用 MSAL 以交互方式获取令牌。 这会提示用户，并为他们提供机会来满足所需的条件访问策略。

在某些情况下调用需要条件访问的 API 时，API 返回的错误中可能会包含声明质询。 例如，如果条件访问策略要求使用托管设备 (Intune)，则错误将类似于 [AADSTS53000:需要管理你的设备才能访问此资源](../articles/active-directory/develop/reference-aadsts-error-codes.md)。 在这种情况下，可以在令牌获取调用中传递声明，使系统提示用户，以满足相应的策略。

