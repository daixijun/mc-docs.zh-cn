---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/04/2021
ms.author: v-junlch
ms.openlocfilehash: abc23543e15ab1abb0c8afe603e3e8a04158814b
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024254"
---
## <a name="run-the-function-in-azure"></a>在 Azure 中运行函数

1. 返回到“Azure：函数”函数”区域，在你的订阅下展开新的函数应用。 展开“函数”，在“HttpExample”中右键单击“(Windows)”或者在按住 <kbd>Ctrl</kbd> 的同时单击“(macOS)”，然后选择“复制函数 URL”  。

    ![复制新的 HTTP 触发器的函数 URL](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. 将 HTTP 请求的此 URL 粘贴到浏览器的地址栏中，将 `name` 查询字符串以 `?name=Functions` 形式添加到此 URL 的末尾，然后执行请求。 调用 HTTP 触发的函数的 URL 应采用以下格式：

    ```http
    http://<FUNCTION_APP_NAME>.chinacloudsites.cn/api/HttpExample?name=Functions
    ```

    以下示例演示浏览器中函数返回的对远程 GET 请求的响应：

    ![浏览器中的函数响应](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)

