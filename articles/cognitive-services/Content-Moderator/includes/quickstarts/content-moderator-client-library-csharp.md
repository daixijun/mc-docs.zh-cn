---
title: 内容审查器 .NET 客户端库快速入门
titleSuffix: Azure Cognitive Services
description: 通过本快速入门开始使用适用于 .NET 的内容审查器客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 12/11/2020
origin.date: 01/27/2020
ms.author: v-tawe
ms.openlocfilehash: 7a80f9380f4b4d1c3e0e70a489ad6e388e303c67
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97105708"
---
适用于 .NET 的内容审查器客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 内容审查器是一项认知服务，用于检查文本、图像和视频中是否存在可能的冒犯性内容、有风险内容或其他令人不适的内容。 找到此类内容时，此服务会将相应的标签（标记）应用到该内容。 然后，应用会处理标记的内容，使之符合法规的要求，或者为用户维持一个理想的环境。

使用适用于 .NET 的内容审查器客户端库可以：

* [审查文本](#moderate-text)
* [审查图像](#moderate-images)
* [创建评审](#create-a-review)

[参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [示例](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。

## <a name="setting-up"></a>设置

### <a name="create-a-content-moderator-azure-resource"></a>创建内容审查器 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.azure.cn/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.azure.cn/cognitive-services/cognitive-services-apis-create-account-cli) 创建内容审查器的资源。 你还可以：

<!-- not available -->
* 在 [Azure 门户](https://portal.azure.cn/)上查看资源

获取试用订阅或资源后，请为该密钥和终结点 URL [创建环境变量](https://docs.azure.cn/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别名为 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 和 `CONTENT_MODERATOR_ENDPOINT`。

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在首选文本编辑器或 IDE 中创建新的 .NET Core 应用程序。 

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `content-moderator-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*Program.cs*。

```console
dotnet new console -n content-moderator-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

在首选的编辑器或 IDE 中，从项目目录打开 *Program.cs* 文件。 添加以下 `using` 语句：

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
```

在 Program 类中，为资源的终结点位置创建变量，并将密钥创建为环境变量。

```csharp
// Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page. Add to your environment variables.
private static readonly string SubscriptionKey = Environment.GetEnvironmentVariable("CONTENT_MODERATOR_SUBSCRIPTION_KEY");
// Base endpoint URL. Add this to your environment variables. Found on 'Overview' page in Azure resource. For example: https://chinaeast2.api.cognitive.azure.cn
private static readonly string Endpoint = Environment.GetEnvironmentVariable("CONTENT_MODERATOR_ENDPOINT");
```

> [!NOTE]
> 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，通过以下命令安装适用于 .NET 的内容审查器客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

## <a name="object-model"></a>对象模型

以下类将处理内容审查器 .NET 客户端库的某些主要功能。

|名称|说明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|所有内容审查器功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|此类提供用于分析成人内容、个人信息或人脸的功能。|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|此类提供用于在文本中分析语言、猥亵内容、错误和个人信息的功能。|
|[评审](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|此类提供评审 API 的功能，包括用于创建作业、自定义工作流和人工评审的方法。|

## <a name="code-examples"></a>代码示例


这些代码片段演示如何使用适用于 .NET 的内容审查器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [审查文本](#moderate-text)
* [审查图像](#moderate-images)
* [创建评审](#create-a-review)

## <a name="authenticate-the-client"></a>验证客户端

在新方法中，使用终结点和密钥实例化客户端对象。 无需为每种方案都使用不同的客户端，但这有助于使代码保持条理性。

```csharp
// Create an image review client
ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
// Create a text review client
ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
// Create a human reviews client
ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
```

## <a name="moderate-text"></a>审查文本

以下代码使用内容审查器客户端分析文本的正文，并将结果输出到控制台。 在 Program 类的根中，定义输入和输出文件：

```csharp
// TEXT MODERATION
// Name of the file that contains text
private static readonly string TextFile = "TextFile.txt";
// The name of the file to contain the output from the evaluation.
private static string TextOutputFile = "TextModerationOutput.txt";
```

然后，在项目的根下添加一个 TextFile.txt 文件。 将你自己的文本添加到此文件中，或使用以下示例文本：

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

将以下方法调用添加到 `Main` 方法：

```csharp
// Moderate text from text in a file
ModerateText(clientText, TextFile, TextOutputFile);
```

然后在 Program 类中的某个位置定义文本审查方法：

```csharp
/*
 * TEXT MODERATION
 * This example moderates text from file.
 */
public static void ModerateText(ContentModeratorClient client, string inputFile, string outputFile)
{
    Console.WriteLine("--------------------------------------------------------------");
    Console.WriteLine();
    Console.WriteLine("TEXT MODERATION");
    Console.WriteLine();
    // Load the input text.
    string text = File.ReadAllText(inputFile);

    // Remove carriage returns
    text = text.Replace(Environment.NewLine, " ");
    // Convert string to a byte[], then into a stream (for parameter in ScreenText()).
    byte[] textBytes = Encoding.UTF8.GetBytes(text);
    MemoryStream stream = new MemoryStream(textBytes);

    Console.WriteLine("Screening {0}...", inputFile);
    // Format text

    // Save the moderation results to a file.
    using (StreamWriter outputWriter = new StreamWriter(outputFile, false))
    {
        using (client)
        {
            // Screen the input text: check for profanity, classify the text into three categories,
            // do autocorrect text, and check for personally identifying information (PII)
            outputWriter.WriteLine("Autocorrect typos, check for matching terms, PII, and classify.");

            // Moderate the text
            var screenResult = client.TextModeration.ScreenText("text/plain", stream, "eng", true, true, null, true);
            outputWriter.WriteLine(JsonConvert.SerializeObject(screenResult, Formatting.Indented));
        }

        outputWriter.Flush();
        outputWriter.Close();
    }

    Console.WriteLine("Results written to {0}", outputFile);
    Console.WriteLine();
}
```

## <a name="moderate-images"></a>审查图像

以下代码使用内容审查器客户端和 [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) 对象，分析远程图像中的成人内容和猥亵内容。

> [!NOTE]
> 还可以分析本地图像的内容。 有关使用本地图像的方法和操作，请参阅[参考文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)。

### <a name="get-sample-images"></a>获取示例图像

定义输入和输出文件：

```csharp
// IMAGE MODERATION
//The name of the file that contains the image URLs to evaluate.
private static readonly string ImageUrlFile = "ImageFiles.txt";
// The name of the file to contain the output from the evaluation.
private static string ImageOutputFile = "ImageModerationOutput.json";
```

然后，在项目的根下创建输入文件 ImageFiles.txt。 在此文件中添加要分析的图像的 URL &mdash; 在每行添加一个 URL。 可使用以下示例图像：

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

将输入和输出文件传递到 `Main` 方法的以下方法调用中。 你将在以后的步骤中定义此方法。

```csharp
// Moderate text from text in a file
ModerateText(clientText, TextFile, TextOutputFile);
```

### <a name="define-helper-class"></a>使用帮助器类

在 Program 类中添加以下类定义。 此内部类将处理图像审查结果。

```csharp
// Contains the image moderation results for an image, 
// including text and face detection results.
public class EvaluationData
{
    // The URL of the evaluated image.
    public string ImageUrl;

    // The image moderation results.
    public Evaluate ImageModeration;

    // The text detection results.
    public OCR TextDetection;

    // The face detection results;
    public FoundFaces FaceDetection;
}
```

### <a name="define-the-image-moderation-method"></a>定义图像审查方法

以下方法将循环访问文本文件中的图像 URL，创建 EvaluationData 实例，并分析图像中的成人/猥亵内容、文本和人脸。 然后，它将最终的 EvaluationData 实例添加到列表中，并将返回数据的完整列表写入控制台。

#### <a name="iterate-through-image-urls"></a>循环访问图像 URL

```csharp
/*
 * IMAGE MODERATION
 * This example moderates images from URLs.
 */
public static void ModerateImages(ContentModeratorClient client, string urlFile, string outputFile)
{
    Console.WriteLine("--------------------------------------------------------------");
    Console.WriteLine();
    Console.WriteLine("IMAGE MODERATION");
    Console.WriteLine();
    // Create an object to store the image moderation results.
    List<EvaluationData> evaluationData = new List<EvaluationData>();

    using (client)
    {
        // Read image URLs from the input file and evaluate each one.
        using (StreamReader inputReader = new StreamReader(urlFile))
        {
            while (!inputReader.EndOfStream)
            {
                string line = inputReader.ReadLine().Trim();
                if (line != String.Empty)
                {
                    Console.WriteLine("Evaluating {0}...", Path.GetFileName(line));
                    var imageUrl = new BodyModel("URL", line.Trim());
```

#### <a name="analyze-content"></a>分析内容

有关内容审查器屏幕的图像属性的详细信息，请参阅[图像审查概念](../../image-moderation-api.md)指南。

```csharp
var imageData = new EvaluationData
            {
                ImageUrl = imageUrl.Value,

                // Evaluate for adult and racy content.
                ImageModeration =
                client.ImageModeration.EvaluateUrlInput("application/json", imageUrl, true)
            };
            Thread.Sleep(1000);

            // Detect and extract text.
            imageData.TextDetection =
                client.ImageModeration.OCRUrlInput("eng", "application/json", imageUrl, true);
            Thread.Sleep(1000);

            // Detect faces.
            imageData.FaceDetection =
                client.ImageModeration.FindFacesUrlInput("application/json", imageUrl, true);
            Thread.Sleep(1000);

            // Add results to Evaluation object
            evaluationData.Add(imageData);
        }
    }
}
```

#### <a name="write-moderation-results-to-file"></a>将审查结果写入文件

```csharp
// Save the moderation results to a file.
        using (StreamWriter outputWriter = new StreamWriter(outputFile, false))
        {
            outputWriter.WriteLine(JsonConvert.SerializeObject(
                evaluationData, Formatting.Indented));

            outputWriter.Flush();
            outputWriter.Close();
        }
        Console.WriteLine();
        Console.WriteLine("Image moderation results written to output file: " + outputFile);
        Console.WriteLine();
    }
}
```

<!--Not available in MC: Review Tool-->

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 .NET 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
> [图像审查的概念](https://docs.azure.cn/cognitive-services/content-moderator/image-moderation-api)

* [什么是 Azure 内容审查器？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs) 上找到此示例的源代码。
