---
title: 内容审查器 Java 客户端库快速入门
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何开始使用适用于 Java 的 Azure 认知服务内容审查器客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/01/2020
ms.author: v-tawe
origin.date: 01/27/2020
ms.openlocfilehash: 151a7930dee835550b27e95595ff98c6c432a821
ms.sourcegitcommit: 5df3a4ca29d3cb43b37f89cf03c1aa74d2cd4ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96476667"
---
适用于 Java 的内容审查器客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 内容审查器是一项认知服务，用于检查文本、图像和视频中是否存在可能的冒犯性内容、有风险内容或其他令人不适的内容。 找到此类内容时，此服务会将相应的标签（标记）应用到该内容。 然后，应用会处理标记的内容，使之符合法规的要求，或者为用户维持一个理想的环境。

使用适用于 Java 的内容审查器客户端库可以：

* 审查图像中的成人或猥亵内容、文本或人脸。

[参考文档](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [项目 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [示例](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)
* 最新版的 [Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* Gradle 生成工具<!-- not available-->，或其他依赖项管理器。

## <a name="setting-up"></a>设置

### <a name="create-a-content-moderator-azure-resource"></a>创建内容审查器 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.azure.cn/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.azure.cn/cognitive-services/cognitive-services-apis-create-account-cli) 创建内容审查器的资源。 也可执行以下操作：

<!-- not available-->
* 在 [Azure 门户](https://portal.azure.cn/)上查看资源。

获取试用订阅或资源后，请为该密钥创建名为 `AZURE_CONTENTMODERATOR_KEY` 的[环境变量](/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```
运行 `gradle init`。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。 从工作目录运行以下命令：

```console
gradle init --type basic
```

当系统提示你选择一个生成脚本 DSL 时，请选择“Kotlin”。

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后在该文件中复制以下生成配置。 此配置将项目定义一个 Java 应用程序，其入口点为 **ContentModeratorQuickstart** 类。 它会导入内容审查器客户端库以及用于 JSON 序列化的 Gson SDK。

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

从工作目录运行以下命令，以创建项目源文件夹。

```console
mkdir -p src/main/java
```

然后，在新文件夹中创建名为 *ContentModeratorQuickstart.java* 的文件。 在喜好的编辑器或 IDE 中打开该文件，并在顶部导入以下库：

```java
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
```

## <a name="object-model"></a>对象模型

以下类将处理内容审查器 Java 客户端库的某些主要功能。

|名称|说明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|所有内容审查器功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|此类提供用于分析成人内容、个人信息或人脸的功能。|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|此类提供用于在文本中分析语言、猥亵内容、错误和个人信息的功能。|
|[评审](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|此类提供评审 API 的功能，包括用于创建作业、自定义工作流和人工评审的方法。|


## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的内容审查器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [审查图像](#moderate-images)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 此步骤假设你已为内容审查器密钥创建了名为 `AZURE_CONTENTMODERATOR_KEY` 的[环境变量](https://docs.azure.cn/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

在应用程序的 `main` 方法中，使用订阅终结点值和订阅密钥环境变量创建一个 [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) 对象。 

> [!NOTE]
> 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

```java
/**
 * Authenticate
 */
// Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with your key as its value.
// Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure endpoint.
ContentModeratorClient client = 
    ContentModeratorManager.authenticate(AzureRegionBaseUrl
                           .fromString(System.getenv("CONTENT_MODERATOR_ENDPOINT")), 
                                System.getenv("CONTENT_MODERATOR_SUBSCRIPTION_KEY"));
```

## <a name="moderate-images"></a>审查图像

### <a name="get-images"></a>获取图像

在项目的 **src/main/** 文件夹中，创建一个 **resources** 文件夹并导航到该文件夹。 然后创建新的文本文件 *ImageFiles.txt*。 在此文件中添加要分析的图像的 URL &mdash; 在每行添加一个 URL。 可使用以下示例图像：

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>使用帮助器类

然后，在 *ContentModeratorQuickstart* 文件中，将以下类定义添加到 **ContentModeratorQuickstart** 类中。 稍后在图像审查过程中将使用此内部类。

```java
// Contains the image moderation results for an image, including text and face detection from the image.
public static class EvaluationData {
    // The URL of the evaluated image.
    public String ImageUrl;
    // The image moderation results.
    public Evaluate ImageModeration;
    // The text detection results.
    public OCR TextDetection;
    // The face detection results;
    public FoundFaces FaceDetection;
}
```

### <a name="iterate-through-images"></a>循环访问图像

接下来，在 `main` 方法的底部添加以下代码： 或者，可将此代码添加到从 `main` 调用的独立方法。 此代码逐步运行 _ImageFiles.txt_ 文件的每一行。

```java
// ImageFiles.txt is the file that contains the image URLs to evaluate.
// Relative paths are relative to the execution directory.
try (BufferedReader inputStream = new BufferedReader(new FileReader(imageListFile) )){
    String line;
    while ((line = inputStream.readLine()) != null) {
        if (line.length() > 0) {
            // Evalutate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(line);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData(); 
            imageData.ImageUrl = url.value();
```

### <a name="check-for-adultracy-content"></a>检查成人/猥亵内容
以下代码行在给定 URL 处的图像中检查成人或猥亵内容。 有关这些术语的信息，请参阅《图像审查概念指南》。

```java
// Evaluate for adult and racy content.
imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url, new EvaluateUrlInputOptionalParameter().withCacheImage(true));
Thread.sleep(1000);
```

### <a name="check-for-text"></a>检查文本
以代码行在图像中检查可见文本。

```java
// Detect and extract text from image.
imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url, new OCRUrlInputOptionalParameter().withCacheImage(true));
Thread.sleep(1000);
```

### <a name="check-for-faces"></a>检查人脸
以代码行在图像中检查人脸。

```java
// Detect faces.
imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url, new FindFacesUrlInputOptionalParameter().withCacheImage(true));
Thread.sleep(1000);
```

最后，将返回的信息存储在 `EvaluationData` 列表中。

```java
resultsList.add(imageData);
```

### <a name="print-results"></a>输出结果

在 `while` 循环的后面添加以下代码，用于将结果输出到控制台和输出文件 *src/main/resources/ModerationOutput.json*。

```java
// Save the moderation results to a file.
// ModerationOutput.json contains the output from the evaluation.
// Relative paths are relative to the execution directory (where pom.xml is located).
BufferedWriter writer = new BufferedWriter(new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
// For formatting the printed results
Gson gson = new GsonBuilder().setPrettyPrinting().create();  

writer.write(gson.toJson(resultsList).toString());
System.out.println("Check ImageModerationOutput.json to see printed results.");
writer.close();
```

结束 `try` 语句并添加 `catch` 语句以完成该方法。

```java
}   catch (Exception e) {
    System.out.println(e.getMessage());
    e.printStackTrace();
}
```

## <a name="run-the-application"></a>运行应用程序

可使用以下命令生成应用：

```console
gradle build
```

使用 `gradle run` 命令运行应用程序：

```console
gradle run
```

然后导航到 *src/main/resources/ModerationOutput.json* 文件并查看内容审查的结果。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 Java 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
>[图像审查的概念](https://docs.azure.cn/cognitive-services/content-moderator/image-moderation-api)

* [什么是 Azure 内容审查器？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) 上找到此示例的源代码。