---
title: 内容审查器 Java 客户端库快速入门
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何开始使用适用于 Java 的 Azure 内容审查器客户端库。 在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。
services: cognitive-services
author: Johnnytechn
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/28/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: v-johya
ms.openlocfilehash: 558fdf5e97e37dd3c7c8997ee71c07d1d96ad0bd
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857115"
---
适用于 Java 的 Azure 内容审查器客户端库入门。 请按照以下步骤安装 Maven 包并试用基本任务的示例代码。 

内容审查器是一种 AI 服务，可用于处理可能的冒犯性、危险或不可取内容。 使用 AI 支持型内容审核服务扫描文本、图像和视频，并自动应用内容标志。 在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。

使用适用于 Java 的内容审查器客户端库可以：

* 审查文本
* 审查图像

[参考文档](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable) | [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator) | [项目 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [示例](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)
* 最新版的 [Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.cn/#create/Microsoft.CognitiveServicesContentModerator"  title="创建内容审查器资源"  target="_blank">创建内容审查器资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到内容审查器。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

## <a name="install-the-client-library"></a>安装客户端库

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后在该文件中复制以下生成配置。 此配置将项目定义一个 Java 应用程序，其入口点为 **ContentModeratorQuickstart** 类。 它会导入内容审查器客户端库以及用于 JSON 序列化的 GSON SDK。

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

### <a name="create-a-java-file"></a>创建 Java 文件


从工作目录运行以下命令，以创建项目源文件夹：

```console
mkdir -p src/main/java
```

导航到新文件夹，创建名为 ContentModeratorQuickstart.java 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) 上找到它，其中包含此快速入门中的代码示例。

在应用程序的 ContentModeratorQuickstart 类中，为资源的密钥和终结点创建变量。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的 [产品名称] 资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../cognitive-services-security.md)文章。

在应用程序的 main 方法中，添加对本快速入门中使用的方法的调用。 稍后将对这些调用进行定义。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```


## <a name="object-model"></a>对象模型

以下类将处理内容审查器 Java 客户端库的某些主要功能。

|名称|说明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|所有内容审查器功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|此类提供用于分析成人内容、个人信息或人脸的功能。|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|此类提供用于在文本中分析语言、猥亵内容、错误和个人信息的功能。|


## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的内容审查器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [审查文本](#moderate-text)
* [审查图像](#moderate-images)


## <a name="authenticate-the-client"></a>验证客户端

在应用程序的 `main` 方法中，使用订阅终结点值和订阅密钥创建一个 [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) 对象。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```



## <a name="moderate-text"></a>审查文本

### <a name="set-up-sample-text"></a>设置示例文本

在 ContentModeratorQuickstart 类的顶部，定义对本地文本文件的引用。 在项目目录中添加一个 .txt 文件，然后输入要分析的文本。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

### <a name="analyze-text"></a>分析文本

创建一个新方法，读取 .txt 文件并在每一行调用 screenText 方法。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

### <a name="print-text-moderation-results"></a>打印文本审查结果

添加以下代码，将审查结果打印到项目目录中的 .json 文件中。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

结束 `try` 和 `catch` 语句以完成该方法。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```


## <a name="moderate-images"></a>审查图像

### <a name="set-up-sample-image"></a>设置示例图像

在新方法中，使用指向图像的给定 URL 字符串构造一个 **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel?view=azure-java-stable)** 对象。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```


### <a name="define-helper-class"></a>使用帮助器类

然后，在 *ContentModeratorQuickstart* 文件中，将以下类定义添加到 **ContentModeratorQuickstart** 类中。 将在图像审查过程中使用此内部类。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```


### <a name="analyze-content"></a>分析内容
以下代码行在给定 URL 处的图像中检查成人或猥亵内容。 有关这些术语的信息，请参阅《图像审查概念指南》。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

### <a name="check-for-text"></a>检查文本
以代码行在图像中检查可见文本。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

### <a name="check-for-faces"></a>检查人脸
以代码行在图像中检查人脸。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

最后，将返回的信息存储在 `EvaluationData` 列表中。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

### <a name="print-results"></a>输出结果

在 `while` 循环的后面添加以下代码，用于将结果输出到控制台和输出文件 *src/main/resources/ModerationOutput.json*。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
}
```

结束 `try` 语句并添加 `catch` 语句以完成该方法。

```java

// <snippet_imports>
import com.google.gson.*;

import com.microsoft.azure.cognitiveservices.vision.contentmoderator.*;
import com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.*;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
// </snippet_imports>

/**
 * \ This quickstart: - Moderates a URL image - Moderates a text file - Creates
 * a human review for an image that posts to the Content Moderator website
 * 
 * A successful response shows a JSON representation of the moderation results.
 * 
 * Prerequisites: 1. Obtain an Azure Content Moderator resource 2. Clone or
 * download this repo:
 * https://github.com/Azure-Samples/cognitive-services-java-sdk-samples.git 3.
 * Use Java 8 or later 4. Set and add your Content Moderator subscription key
 * and endpoint environment variables in main() below. Aquire the endpoint from
 * your Content Moderator account... use only the base endpoint, for example:
 * https://api.cognitive.azure.cn
 * 
 * How to run: Run in your favorite IDE or... To use the command line: - Make
 * sure Maven is installed: https://maven.apache.org/install.html - Run from the
 * root folder (has pom.xml in it): mvn compile exec:java
 * -Dexec.cleanupDaemonThreads=false - All dependencies will be automatically
 * included. To list them (if desired): mvn dependency:list NOTE: You may see a
 * "WARNING: An illegal reflective access operation has occurred..." within the
 * results. This does not impact a successful result, so it can be ignored.
 * 
 * Resources: - Content Moderator documentation:
 * /cognitive-services/content-moderator/
 * - Content Moderator Java SDK:
 * https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator?view=azure-java-stable
 * - Content Moderator API:
 * /cognitive-services/content-moderator/api-reference
 */

public class ContentModeratorQuickstart {

    // MODERATE IMAGES variable
    private static File imageListFile = new File("src\\main\\resources\\ImageFiles.txt");

    // <snippet_textmod_var>
    // TEXT MODERATION variable
    private static File textFile = new File("src\\main\\resources\\TextModeration.txt");
    // </snippet_textmod_var>

    // HUMAN REVIEWS - IMAGES variables
    private static final String REVIEW_URL = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png";
    private static final long LATENCY_DELAY = 30; // milliseconds of delay
    // Add your Content Moderator team name and callback endpoint to your
    // environment variables.
    private static final String TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
    private static final String CALLBACK_ENDPOINT = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";

    // <snippet_creds>
    private static final String subscriptionKey = "<your-subscription-key>";
    private static final String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_evaluationdata>
    // Contains the image moderation results for an image, including text and face
    // detection from the image.
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
    // </snippet_evaluationdata>

    public static void main(String[] args) {

        // <snippet_client>
        // Set CONTENT_MODERATOR_SUBSCRIPTION_KEY in your environment settings, with
        // your key as its value.
        // Set COMPUTER_MODERATOR_ENDPOINT in your environment variables with your Azure
        // endpoint.
        ContentModeratorClient client = ContentModeratorManager.authenticate(AzureRegionBaseUrl.fromString(endpoint),
                "CONTENT_MODERATOR_SUBSCRIPTION_KEY");
        // </snippet_client>

        // <snippet_maincalls>
        // Create a List in which to store the image moderation results.
        List<EvaluationData> evaluationData = new ArrayList<EvaluationData>();

        // Moderate URL images
        moderateImages(client, evaluationData);
        // Moderate text from file
        moderateText(client);
        // Create a human review
        humanReviews(client);
        // </snippet_maincalls>
    }
    // End main()

    /**
     * MODERATE IMAGES Read image URLs from the input file and evaluate/moderate
     * each one.
     */
    // <snippet_imagemod>
    public static void moderateImages(ContentModeratorClient client, List<EvaluationData> resultsList) {
        System.out.println();
        System.out.println("---------------------------------------");
        System.out.println("MODERATE IMAGES");
        System.out.println();

        try {
            String urlString = "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg";
            // Evaluate each line of text
            BodyModelModel url = new BodyModelModel();
            url.withDataRepresentation("URL");
            url.withValue(urlString);
            // Save to EvaluationData class for later
            EvaluationData imageData = new EvaluationData();
            imageData.ImageUrl = url.value();
            // </snippet_imagemod>

            // <snippet_imagemod_ar>
            // Evaluate for adult and racy content.
            imageData.ImageModeration = client.imageModerations().evaluateUrlInput("application/json", url,
                    new EvaluateUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_ar>

            // <snippet_imagemod_text>
            // Detect and extract text from image.
            imageData.TextDetection = client.imageModerations().oCRUrlInput("eng", "application/json", url,
                    new OCRUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_text>

            // <snippet_imagemod_faces>
            // Detect faces.
            imageData.FaceDetection = client.imageModerations().findFacesUrlInput("application/json", url,
                    new FindFacesUrlInputOptionalParameter().withCacheImage(true));
            Thread.sleep(1000);
            // </snippet_imagemod_faces>

            // <snippet_imagemod_storedata>
            resultsList.add(imageData);
            // </snippet_imagemod_storedata>

            System.out.println("Image moderation status: " + imageData.ImageModeration.status().description());

            System.out.println();

            // <snippet_imagemod_printdata>
            // Save the moderation results to a file.
            // ModerationOutput.json contains the output from the evaluation.
            // Relative paths are relative to the execution directory (where pom.xml is
            // located).
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\ImageModerationOutput.json")));
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            writer.write(gson.toJson(resultsList).toString());
            System.out.println("Check ImageModerationOutput.json to see printed results.");
            writer.close();
            // </snippet_imagemod_printdata>

            // <snippet_imagemod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
        // </snippet_imagemod_catch>
        System.out.println();
    }

    /**
     * END - MODERATE IMAGES
     */

    /**
     * MODERATE TEXT Read text as a string from the input file and evaluate/moderate
     * it. Check for profanity, autocorrect the text, check for personally
     * identifying information (PII), and classify the text.
     */
    // <snippet_textmod>
    public static void moderateText(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("MODERATE TEXT");
        System.out.println();

        try (BufferedReader inputStream = new BufferedReader(new FileReader(textFile))) {
            String line;
            Screen textResults = null;
            // For formatting the printed results
            Gson gson = new GsonBuilder().setPrettyPrinting().create();

            while ((line = inputStream.readLine()) != null) {
                if (line.length() > 0) {
                    textResults = client.textModerations().screenText("text/plain", line.getBytes(), null);
                    // Uncomment below line to print in console
                    // System.out.println(gson.toJson(textResults).toString());
                }
            }
            // </snippet_textmod>

            // <snippet_textmod_print>
            System.out.println("Text moderation status: " + textResults.status().description());
            System.out.println();

            // Create output results file to TextModerationOutput.json
            BufferedWriter writer = new BufferedWriter(
                    new FileWriter(new File("src\\main\\resources\\TextModerationOutput.json")));
            writer.write(gson.toJson(textResults).toString());
            System.out.println("Check TextModerationOutput.json to see printed results.");
            System.out.println();
            writer.close();
            // </snippet_textmod_print>
            // <snippet_textmod_catch>
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_textmod_catch>

    /**
     * END - MODERATE TEXT
     */

    /**
     * HUMAN REVIEWS - IMAGES This example will create a review for humans to
     * moderate on the Content Moderator website at: https://{LOCATION OR CUSTOM
     * NAME}.contentmoderator.cognitive.microsoft.com Requires you to moderate an
     * image on website (when prompted), then return to the app (IDE or console) to
     * initiate printing of the results of the review.
     */
    public static void humanReviews(ContentModeratorClient client) {
        System.out.println("---------------------------------------");
        System.out.println("HUMAN REVIEWS - IMAGES");
        System.out.println();

        System.out.println("Creating a review for the image: "
                + REVIEW_URL.substring(REVIEW_URL.lastIndexOf('/') + 1, REVIEW_URL.length()));
        System.out.println();

        // Create List for the review body items
        List<CreateReviewBodyItem> body = new ArrayList<>();
        List<CreateReviewBodyItemMetadataItem> metadata = new ArrayList<>();
        // This establishes the adult or racy score (sc) that will be returned (true)
        // with the results.
        metadata.add(new CreateReviewBodyItemMetadataItem().withKey("sc").withValue("true"));
        body.add(new CreateReviewBodyItem().withCallbackEndpoint(CALLBACK_ENDPOINT).withContent(REVIEW_URL)
                .withMetadata(metadata).withType("Image"));

        // Creates a review by POST to callback URL. Returns a list of string review
        // IDs.
        List<String> reviews = client.reviews().createReviews(TEAM_NAME, "Image", body, null);
        System.out.println();
        System.out.println("Getting review details before human review..."); // for comparison's sake
        System.out.println();

        // Returns a Review object, notice the "reviewerResultTags" is empty.
        Review reviewDetailsBefore = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonBefore = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonBefore.toJson(reviewDetailsBefore).toString());
        System.out.println();

        // Go to website to perform reviews
        System.out.println("Perform manual reviews on the Content Moderator review site, press ENTER when done...");
        try {
            System.in.read();
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Wait for server to process review
        System.out.println("Wait " + LATENCY_DELAY + " seconds for the server to process the request...");
        System.out.println();
        try {
            TimeUnit.SECONDS.sleep(LATENCY_DELAY);
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Get review details again to view changes since the human review. The
        // "reviewerResultTags" is now full.
        System.out.println("Getting review details after human review...");
        System.out.println();
        Review reviewDetailsAfter = client.reviews().getReview(TEAM_NAME, reviews.get(0));
        Gson gsonAfter = new GsonBuilder().setPrettyPrinting().create();
        System.out.println(gsonAfter.toJson(reviewDetailsAfter).toString());
        System.out.println();
    }
    /**
     * END - HUMAN REVIEWS - IMAGES
     */
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

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 Java 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
> [图像审查的概念](../../image-moderation-api.md)

* [什么是 Azure 内容审查器？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) 上找到此示例的源代码。

