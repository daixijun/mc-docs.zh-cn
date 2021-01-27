---
author: mmacy
ms.author: v-junlch
ms.date: 01/05/2021
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: fa27efe87cc908532b201d776864865920f4a8aa
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98793970"
---
## <a name="retrying-after-errors-and-exceptions"></a>出现错误和异常后重试

在调用 MSAL 时，应实现自己的重试策略。 MSAL 会对 Azure AD 服务进行 HTTP 调用，并且有时会发生失败。 例如，网络可能出现故障，或者服务器发生过载。  

### <a name="http-429"></a>HTTP 429

如果过多的请求导致服务令牌服务器 (STS) 过载，`Retry-After` 响应字段中将返回 HTTP 错误 429，并提示可在多长时间后重试。

