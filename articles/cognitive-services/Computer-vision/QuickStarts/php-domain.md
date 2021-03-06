---
title: 快速入门：特定于域的内容 - REST、PHP
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将通过计算机视觉 API 和 PHP 使用域模型识别图像中的地标。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
origin.date: 04/17/2019
ms.date: 12/01/2020
ms.author: v-junlch
ms.custom: seodec18
ms.openlocfilehash: 20ea03d16b9e9ff9951650f870822224be470366
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432402"
---
# <a name="quickstart-recognize-domain-specific-content-using-the-rest-api-and-php-with-computer-vision"></a>快速入门：在计算机视觉中使用 REST API 和 PHP 识别特定于域的内容

在本快速入门中，你将通过计算机视觉的 REST API 使用域模型识别远程存储图像中的地标或者名人。 使用[识别域特定内容](https://dev.cognitive.azure.cn/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200)方法，可以应用一个特定于域的模型来识别图像中的内容。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。

## <a name="prerequisites"></a>先决条件

- 必须安装有 [PHP](https://secure.php.net/downloads.php)。
- 必须安装有 [Pear](https://pear.php.net)。
- 必须具有计算机视觉的订阅密钥。 你可以按照[创建认知服务帐户](/cognitive-services/cognitive-services-apis-create-account)中的说明订阅计算机视觉并获取密钥。

## <a name="create-and-run-the-sample"></a>创建并运行示例

要创建和运行示例，请执行以下步骤：

1. 安装 PHP5 [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) 包。
   1. 以管理员身份打开“命令提示符”窗口。
   1. 运行以下命令：

      ```console
      pear install HTTP_Request2
      ```

   1. 包安装成功后，关闭命令提示符窗口。

1. 将以下代码复制到文本编辑器中。
1. 必要时在代码中进行如下更改：
    1. 将 `subscriptionKey` 的值替换为你的订阅密钥。
    1. 如有必要，请将 `uriBase` 的值替换为获取的订阅密钥所在的 Azure 区域中的[识别域特定内容](https://dev.cognitive.azure.cn/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200)方法的终结点 URL。
    1. （可选）将 `imageUrl` 的值替换为要分析的其他图像的 URL。
    1. （可选）如果要使用 `celebrities` 域模型而不是 `landmarks` 域模型，请将 `domain` 请求参数的值替换为 `celebrites`。
1. 将代码保存为以 `.php` 为扩展名的文件。 例如，`use-domain-model.php`。
1. 打开具有 PHP 支持的浏览器窗口。
1. 将文件拖放到浏览器窗口中。

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

$uriBase = 'https://api.cognitive.azure.cn/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>检查响应

成功响应将以 JSON 格式返回。 示例网站会在浏览器窗口中分析和显示成功响应，如下例所示：

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="clean-up-resources"></a>清理资源

不再需要文件时，请将其删除，然后卸载 PHP5 `HTTP_Request2` 包。 要卸载包，请执行以下步骤：

1. 以管理员身份打开“命令提示符”窗口。
2. 运行以下命令：

   ```console
   pear uninstall HTTP_Request2
   ```

3. 成功卸载包后，关闭命令提示符窗口。

## <a name="next-steps"></a>后续步骤

了解计算机视觉 API，它用于分析图像、检测名人和地标、创建缩略图，并提取印刷体文本和手写文本。 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://dev.cognitive.azure.cn/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://dev.cognitive.azure.cn/docs/services/5adf991815e1060e6355ad44)

