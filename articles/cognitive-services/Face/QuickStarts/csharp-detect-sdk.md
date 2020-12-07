---
title: 快速入门：使用 Azure 人脸 .NET SDK 检测图像中的人脸
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，请使用 Azure 人脸 SDK 和 C# 检测图像中的人脸。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
origin.date: 07/03/2019
ms.date: 12/01/2020
ms.author: v-junlch
ms.openlocfilehash: f26542818ae4e0363d78c7f50deccfe7e64b420e
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432654"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>快速入门：使用人脸 .NET SDK 检测图像中的人脸

在本快速入门中，请使用人脸服务 SDK 和 C# 检测图像中的人脸。 如需本快速入门中代码的工作示例，请查看 GitHub 上的[认知服务视觉 csharp 快速入门](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face)存储库中的人脸项目。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.microsoft.com/china/azure/index.html?fromtype=cn)。 

## <a name="prerequisites"></a>必备条件

- 人脸 API 订阅密钥。 可以按照[创建认知服务帐户](/cognitive-services/cognitive-services-apis-create-account)中的说明订阅人脸 API 服务并获取密钥。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 中创建新的 **控制台应用 (.NET Framework)** 项目并将其命名为 **FaceDetection**。 
1. 如果解决方案中有其他项目，请将此项目选为单一启动项目。
1. 获取所需的 NuGet 包。 在解决方案资源管理器中，右键单击项目并选择“管理 NuGet 包”  。 单击“浏览”选项卡，选择“包括预发行版”，然后找到并安装以下包：  
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)
1. 确保已为项目安装所有 NuGet 包的最新版本。 在解决方案资源管理器中，右键单击项目并选择“管理 NuGet 包”  。 单击“更新”选项卡，安装显示的任何包的最新版本。 

## <a name="add-face-detection-code"></a>添加人脸检测代码

打开新项目的 *Program.cs* 文件。 在这里，请添加加载图像和检测人脸所需的代码。

### <a name="include-namespaces"></a>包括命名空间

将以下 `using` 语句添加到 *Program.cs* 文件顶部。

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
```
### <a name="add-essential-fields"></a>添加必要的字段

为 **Program** 类添加以下字段。 该数据指定如何连接到人脸服务，以及在何处获取输入数据。 需使用订阅密钥的值更新 `subscriptionKey` 字段，并且可能需要更改 `faceEndpoint` 字符串，使之包含正确的区域标识符。 还需将 `localImagePath` 和/或 `remoteImageUrl` 的值设置为路径，使之指向实际的图像文件。

`faceAttributes` 字段只是一个数组，包含特定类型的属性。 它将指定要检索的有关已检测人脸的信息。

```csharp
namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        private const string faceEndpoint = "https://api.cognitive.azure.cn";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

```

> [!NOTE]
> 如果创建的人脸 API 的 `Location` 为 `China East 2`，则需要将 URL `https://api.cognitive.azure.cn` 替换为 `https://chinaeast2.api.cognitive.azure.cn`。

### <a name="create-and-use-the-face-client"></a>创建并使用人脸客户端

接下来，请将以下代码添加到 **Program** 类的 **Main** 方法。 这样会设置人脸 API 客户端。

```csharp
static void Main(string[] args)
{
    FaceClient faceClient = new FaceClient(
        new ApiKeyServiceClientCredentials(subscriptionKey),
        new System.Net.Http.DelegatingHandler[] { });
    faceClient.Endpoint = faceEndpoint;

```
另请在 **Main** 方法中添加以下代码，以便使用新创建的人脸客户端来检测远程图像和本地图像中的人脸。 检测方法将随后定义。 

```csharp
    Console.WriteLine("Faces being detected ...");
    var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
    var t2 = DetectLocalAsync(faceClient, localImagePath);

    Task.WhenAll(t1, t2).Wait(5000);
    Console.WriteLine("Press any key to exit");
    Console.ReadLine();
}
```
### <a name="detect-faces"></a>检测人脸

将以下方法添加到 **Program** 类。 它使用人脸服务客户端检测通过 URL 引用的远程图像中的人脸。 它使用 `faceAttributes` 字段&mdash;添加到 `faceList` 的 **DetectedFace** 对象将具有指定的属性（在此示例中为年龄和性别）。

```csharp
// Detect faces in a remote image
private static async Task DetectRemoteAsync(
    FaceClient faceClient, string imageUrl)
{
    if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
    {
        Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
        return;
    }

    try
    {
        IList<DetectedFace> faceList =
            await faceClient.Face.DetectWithUrlAsync(
                imageUrl, true, false, faceAttributes);

        DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
    }
    catch (APIErrorException e)
    {
        Console.WriteLine(imageUrl + ": " + e.Message);
    }
}
```
以类似方式添加 **DetectLocalAsync** 方法。 它使用人脸服务客户端检测通过文件路径引用的本地图像中的人脸。

```csharp
// Detect faces in a local image
private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
{
    if (!File.Exists(imagePath))
    {
        Console.WriteLine(
            "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
        return;
    }

    try
    {
        using (Stream imageStream = File.OpenRead(imagePath))
        {
            IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithStreamAsync(
                        imageStream, true, false, faceAttributes);
            DisplayAttributes(
                GetFaceAttributes(faceList, imagePath), imagePath);
        }
    }
    catch (APIErrorException e)
    {
        Console.WriteLine(imagePath + ": " + e.Message);
    }
}
```
### <a name="retrieve-and-display-face-attributes"></a>检索和显示人脸属性

接下来，定义 **GetFaceAttributes** 方法。 它返回一个字符串，其中包含相关的属性信息。

```csharp
private static string GetFaceAttributes(
    IList<DetectedFace> faceList, string imagePath)
{
    string attributes = string.Empty;

    foreach (DetectedFace face in faceList)
    {
        double? age = face.FaceAttributes.Age;
        string gender = face.FaceAttributes.Gender.ToString();
        attributes += gender + " " + age + "   ";
    }

    return attributes;
}
```
最后，定义 **DisplayAttributes** 方法，以便将人脸属性数据写入到控制台输出。 然后即可关闭类和命名空间。

```csharp
// Display the face attributes
private static void DisplayAttributes(string attributes, string imageUri)
{
    Console.WriteLine(imageUri);
    Console.WriteLine(attributes + "\n");
}
```

## <a name="run-the-app"></a>运行应用

成功的响应会显示图像中每张人脸的性别和年龄。 例如：

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个简单的 .NET 控制台应用程序，该应用程序可以使用人脸 API 服务检测本地图像和远程图像中的人脸。 接下来，请阅读更深入的教程，了解如何以直观的方式将人脸信息显示给用户。

> [!div class="nextstepaction"]
> [教程：创建一个用于检测和分析图像中人脸的 WPF 应用](../Tutorials/FaceAPIinCSharpTutorial.md)

<!-- Update_Description: wording update -->
