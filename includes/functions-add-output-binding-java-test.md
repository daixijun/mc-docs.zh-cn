---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/04/2021
ms.author: v-junlch
ms.openlocfilehash: 5ead07c338526df4960fd34aad0fd6f5a9d1edc2
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022934"
---
由于原型还创建一组测试，因此需更新这些测试，以便处理 `run` 方法签名中的新 `msg` 参数。  

浏览到测试代码在 _src/test/java_ 下的位置，打开 *Function.java* 项目文件，将 `//Invoke` 下的代码行替换为以下代码。

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);
final HttpResponseMessage ret = new Function().run(req, msg, context);
```

