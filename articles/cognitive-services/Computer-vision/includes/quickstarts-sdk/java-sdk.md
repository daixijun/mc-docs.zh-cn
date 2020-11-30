---
title: 快速入门：适用于 Java 的计算机视觉客户端库
description: 在本快速入门中，开始使用适用于 Java 的计算机视觉客户端库。
services: cognitive-services
author: Johnnytechn
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.author: v-johya
ms.openlocfilehash: 5c6a7533865eb024fcfc60bab23b530712efaa7d
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306603"
---
<a name="HOLTop"></a>

[参考文档](https://docs.microsoft.com/java/api/overview/cognitiveservices/client/computervision?view=azure-java-stable) | [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[项目 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [示例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建试用订阅](https://www.azure.cn/pricing/details/cognitive-services/)
* 最新版的 [Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.cn/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
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

### <a name="install-the-client-library"></a>安装客户端库

本快速入门使用 Gradle 依赖项管理器。 可以在 [Maven 中央存储库](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)中找到客户端库以及其他依赖项管理器的信息。

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后在该文件中复制以下生成配置。 此配置将项目定义一个 Java 应用程序，其入口点为 **ComputerVisionQuickstarts** 类。 它将导入计算机视觉库。

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>创建 Java 文件

从工作目录运行以下命令，以创建项目源文件夹：

```console
mkdir -p src/main/java
```

导航到新文件夹，创建名为 *ComputerVisionQuickstarts.java* 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上找到它，其中包含此快速入门中的代码示例。

在应用程序的 ComputerVisionQuickstarts 类中，为资源的密钥和终结点创建变量。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```


> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的 [产品名称] 资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](/cognitive-services/cognitive-services-security)文章。

在应用程序的 main 方法中，添加对本快速入门中使用的方法的调用。 稍后将对这些调用进行定义。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```


## <a name="object-model"></a>对象模型

以下类和接口将处理人脸计算机视觉 Java SDK 的某些主要功能。

|名称|说明|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | 所有计算机视觉功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| 此类来自客户端对象，它直接处理所有图像操作，例如图像分析、文本检测和缩略图生成。|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| 此枚举定义可在标准分析操作中执行的不同类型的图像分析。 请根据需求指定一组 VisualFeatureTypes 值。 |

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的计算机视觉客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [分析图像](#analyze-an-image)
* [读取印刷体文本和手写文本](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>验证客户端


在新方法中，使用终结点和密钥实例化客户端 [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) 对象。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```


## <a name="analyze-an-image"></a>分析图像

以下代码定义方法 `AnalyzeLocalImage`，该方法使用客户端对象分析本地图像并输出结果。 该方法返回文本说明、分类、标记列表、检测到的人脸、成人内容标志、主颜色和图像类型。

> [!TIP]
> 还可以使用其 URL 分析远程图像。 请参阅 [ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable) 方法，例如 AnalyzeImage。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上的示例代码以了解涉及远程图像的方案。

### <a name="set-up-test-image"></a>设置测试图像

首先，在项目的 **src/main/** 文件夹中创建 **resources/** 文件夹，并添加要分析的图像。 然后，将以下方法定义添加到 **ComputerVisionQuickstarts** 类。 请更改 `pathToLocalImage` 的值，使之与图像文件相匹配。 

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="specify-visual-features"></a>指定视觉特性

接下来，指定要在分析中提取的视觉特征。 有关完整列表，请参阅 [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) 枚举。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="analyze"></a>分析
此方法根据每个图像分析范围将详细结果输出到控制台。 建议将此方法调用包含在 Try/Catch 块中。 **analyzeImageInStream** 方法将返回包含所有提取信息的 **ImageAnalysis** 对象。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

以下部分说明如何详细分析此信息。

### <a name="get-image-description"></a>获取图像说明

下面的代码获取为图像生成的描述文字列表。 有关详细信息，请参阅[描述图像](../../concept-describing-images.md)。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="get-image-category"></a>获取图像类别

下面的代码获取所检测到的图像类别。 有关详细信息，请参阅[对图像进行分类](../../concept-categorizing-images.md)。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="get-image-tags"></a>获取图像标记

以下代码获取图像中检测到的标记集。 有关详细信息，请参阅[内容标记](../../concept-tagging-images.md)。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="detect-faces"></a>检测人脸

下面的代码返回图像中检测到的人脸及其矩形坐标，并选择人脸属性。 有关详细信息，请参阅[人脸检测](../../concept-detecting-faces.md)。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="detect-adult-racy-or-gory-content"></a>检测成人、色情或血腥内容

以下代码输出图像中检测到的成人内容。 有关详细信息，请参阅[成人、色情或血腥内容](../../concept-detecting-adult-content.md)。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="get-image-color-scheme"></a>获取图像配色方案

以下代码输出图像中检测到的颜色属性，如主色和主题色。 有关详细信息，请参阅[配色方案](../../concept-detecting-color-schemes.md)。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="get-domain-specific-content"></a>获取特定于域的内容

计算机视觉可以使用专用模型对图像进行进一步分析。 有关详细信息，请参阅[特定于域的内容](../../concept-detecting-domain-content.md)。 

以下代码分析了图像中检测到的名人的相关数据。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

以下代码分析了图像中检测到的地标的相关数据。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="get-the-image-type"></a>获取图像类型

以下代码输出有关图像类型的信息&mdash;无论它是剪贴画还是线条图。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

## <a name="read-printed-and-handwritten-text"></a>读取印刷体文本和手写文本

计算机视觉可以读取图像中的可见文本，并将其转换为字符流。 本部分定义方法 `ReadFromFile`，该方法采用本地文件路径并将图像的文本输出到控制台。

> [!TIP]
> 还可以读取 URL 引用的远程图像中的文本。 请参阅 [ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable) 方法，例如 read。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上的示例代码以了解涉及远程图像的方案。

### <a name="set-up-test-image"></a>设置测试图像

在项目的 src/main/ 文件夹中创建 resources/ 文件夹，并添加要从中读取文本的图像 。 可下载[示例映像](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg)在此使用。

然后，将以下方法定义添加到 **ComputerVisionQuickstarts** 类。 请更改 `localFilePath` 的值，使之与图像文件相匹配。 

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="call-the-read-api"></a>调用读取 API

然后，添加以下代码调用给定图像的 readInStreamWithServiceResponseAsync 方法。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```


下面的代码块从 Read 调用的响应中提取操作 ID。 它将此 ID 与 helper 方法一起使用，以将文本读取结果输出到控制台。 

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

结束 try/catch 块和方法定义。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

### <a name="get-read-results"></a>获取读取结果

然后，添加 helper 方法的定义。 此方法使用上一步中的操作 ID 来查询读取操作并在 OCR 结果可用时获取该结果。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

此方法的其余部分会分析 OCR 结果，并将其输出到控制台。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
}
```

最后，添加上面使用的其他 helper 方法，该方法从初始响应中提取操作 ID。

```java

// <snippet_imports>
import com.microsoft.azure.cognitiveservices.vision.computervision.*;
import com.microsoft.azure.cognitiveservices.vision.computervision.implementation.ComputerVisionImpl;
import com.microsoft.azure.cognitiveservices.vision.computervision.models.*;

import java.io.File;
import java.nio.file.Files;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
// </snippet_imports>

/*  This Quickstart for the Azure Cognitive Services Computer Vision API shows how to analyze
 *  an image and recognize text for both a local and remote (URL) image.
 *
 *  Analyzing an image includes:
 *  - Displaying image captions and confidence values
 *  - Displaying image category names and confidence values
 *  - Displaying image tags and confidence values
 *  - Displaying any faces found in the image and their bounding boxes
 *  - Displaying whether any adult or racy content was detected and the confidence values
 *  - Displaying the image color scheme
 *  - Displaying any celebrities detected in the image and their bounding boxes
 *  - Displaying any landmarks detected in the image and their bounding boxes
 *  - Displaying whether an image is a clip art or line drawing type
 *  Recognize Printed Text: uses optical character recognition (OCR) to find text in an image.
 */

public class ComputerVisionQuickstart {

    // <snippet_creds>
    static String subscriptionKey = "<your-subscription-key>";
    static String endpoint = "<your-api-endpoint>";
    // </snippet_creds>

    // <snippet_maincalls>
    public static void main(String[] args) {
        
        System.out.println("\nAzure Cognitive Services Computer Vision - Java Quickstart Sample");

        // Create an authenticated Computer Vision client.
        ComputerVisionClient compVisClient = Authenticate(subscriptionKey, endpoint); 

        // Analyze local and remote images
        AnalyzeLocalImage(compVisClient);

        // Read from local file
        ReadFromFile(compVisClient);
    }
    // </snippet_maincalls>

    // <snippet_auth>
    public static ComputerVisionClient Authenticate(String subscriptionKey, String endpoint){
        return ComputerVisionManager.authenticate(subscriptionKey).withEndpoint(endpoint);
    }
    // </snippet_auth>

    // <snippet_analyzelocal_refs>
    public static void AnalyzeLocalImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze a local image:
         *
         * Set a string variable equal to the path of a local image. The image path
         * below is a relative path.
         */
        String pathToLocalImage = "src\\main\\resources\\myImage.png";
        // </snippet_analyzelocal_refs>

        // <snippet_analyzelocal_features>
        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromLocalImage = new ArrayList<>();
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromLocalImage.add(VisualFeatureTypes.IMAGE_TYPE);
        // </snippet_analyzelocal_features>

        System.out.println("\nAnalyzing local image ...");

        try {
            // <snippet_analyzelocal_analyze>
            // Need a byte array for analyzing a local image.
            File rawImage = new File(pathToLocalImage);
            byte[] imageByteArray = Files.readAllBytes(rawImage.toPath());

            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImageInStream().withImage(imageByteArray)
                    .withVisualFeatures(featuresToExtractFromLocalImage).execute();

            // </snippet_analyzelocal_analyze>

            // <snippet_analyzelocal_captions>
            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }
            // </snippet_analyzelocal_captions>

            // <snippet_analyzelocal_category>
            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }
            // </snippet_analyzelocal_category>

            // <snippet_analyzelocal_tags>
            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }
            // </snippet_analyzelocal_tags>

            // <snippet_analyzelocal_faces>
            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }
            // </snippet_analyzelocal_faces>

            // <snippet_analyzelocal_adult>
            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());
            // </snippet_analyzelocal_adult>

            // <snippet_analyzelocal_colors>
            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));
            // </snippet_analyzelocal_colors>

            // <snippet_analyzelocal_celebrities>
            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }
            // </snippet_analyzelocal_celebrities>

            // <snippet_analyzelocal_landmarks>
            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }
            // </snippet_analyzelocal_landmarks>

            // <snippet_imagetype>
            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
            // </snippet_imagetype>
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze a local image.

    // <snippet_analyzeurl>
    public static void AnalyzeRemoteImage(ComputerVisionClient compVisClient) {
        /*
         * Analyze an image from a URL:
         *
         * Set a string variable equal to the path of a remote image.
         */
        String pathToRemoteImage = "https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/ComputerVision/Images/faces.jpg";

        // This list defines the features to be extracted from the image.
        List<VisualFeatureTypes> featuresToExtractFromRemoteImage = new ArrayList<>();
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.DESCRIPTION);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.CATEGORIES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.TAGS);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.FACES);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.ADULT);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.COLOR);
        featuresToExtractFromRemoteImage.add(VisualFeatureTypes.IMAGE_TYPE);

        System.out.println("\n\nAnalyzing an image from a URL ...");

        try {
            // Call the Computer Vision service and tell it to analyze the loaded image.
            ImageAnalysis analysis = compVisClient.computerVision().analyzeImage().withUrl(pathToRemoteImage)
                    .withVisualFeatures(featuresToExtractFromRemoteImage).execute();

            // Display image captions and confidence values.
            System.out.println("\nCaptions: ");
            for (ImageCaption caption : analysis.description().captions()) {
                System.out.printf("\'%s\' with confidence %f\n", caption.text(), caption.confidence());
            }

            // Display image category names and confidence values.
            System.out.println("\nCategories: ");
            for (Category category : analysis.categories()) {
                System.out.printf("\'%s\' with confidence %f\n", category.name(), category.score());
            }

            // Display image tags and confidence values.
            System.out.println("\nTags: ");
            for (ImageTag tag : analysis.tags()) {
                System.out.printf("\'%s\' with confidence %f\n", tag.name(), tag.confidence());
            }

            // Display any faces found in the image and their location.
            System.out.println("\nFaces: ");
            for (FaceDescription face : analysis.faces()) {
                System.out.printf("\'%s\' of age %d at location (%d, %d), (%d, %d)\n", face.gender(), face.age(),
                        face.faceRectangle().left(), face.faceRectangle().top(),
                        face.faceRectangle().left() + face.faceRectangle().width(),
                        face.faceRectangle().top() + face.faceRectangle().height());
            }

            // Display whether any adult or racy content was detected and the confidence
            // values.
            System.out.println("\nAdult: ");
            System.out.printf("Is adult content: %b with confidence %f\n", analysis.adult().isAdultContent(),
                    analysis.adult().adultScore());
            System.out.printf("Has racy content: %b with confidence %f\n", analysis.adult().isRacyContent(),
                    analysis.adult().racyScore());

            // Display the image color scheme.
            System.out.println("\nColor scheme: ");
            System.out.println("Is black and white: " + analysis.color().isBWImg());
            System.out.println("Accent color: " + analysis.color().accentColor());
            System.out.println("Dominant background color: " + analysis.color().dominantColorBackground());
            System.out.println("Dominant foreground color: " + analysis.color().dominantColorForeground());
            System.out.println("Dominant colors: " + String.join(", ", analysis.color().dominantColors()));

            // Display any celebrities detected in the image and their locations.
            System.out.println("\nCelebrities: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().celebrities() != null) {
                    for (CelebritiesModel celeb : category.detail().celebrities()) {
                        System.out.printf("\'%s\' with confidence %f at location (%d, %d), (%d, %d)\n", celeb.name(),
                                celeb.confidence(), celeb.faceRectangle().left(), celeb.faceRectangle().top(),
                                celeb.faceRectangle().left() + celeb.faceRectangle().width(),
                                celeb.faceRectangle().top() + celeb.faceRectangle().height());
                    }
                }
            }

            // Display any landmarks detected in the image and their locations.
            System.out.println("\nLandmarks: ");
            for (Category category : analysis.categories()) {
                if (category.detail() != null && category.detail().landmarks() != null) {
                    for (LandmarksModel landmark : category.detail().landmarks()) {
                        System.out.printf("\'%s\' with confidence %f\n", landmark.name(), landmark.confidence());
                    }
                }
            }

            // Display what type of clip art or line drawing the image is.
            System.out.println("\nImage type:");
            System.out.println("Clip art type: " + analysis.imageType().clipArtType());
            System.out.println("Line drawing type: " + analysis.imageType().lineDrawingType());
        }

        catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // END - Analyze an image from a URL.
    // </snippet_analyzeurl>

    /**
     * RECOGNIZE PRINTED TEXT: Displays text found in image with angle and orientation of
     * the block of text. This method is a legacy feature from before the Read API was introduced.
     */
    private static void RecognizeTextOCRLocal(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        
        // Replace this string with the path to your own image.
        String localTextImagePath = "src\\main\\resources\\myImage.png";
        
        try {
            File rawImage = new File(localTextImagePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Recognize printed text in local image
            OcrResult ocrResultLocal = client.computerVision().recognizePrintedTextInStream()
                    .withDetectOrientation(true).withImage(localImageBytes).withLanguage(OcrLanguages.EN).execute();

            // Print results of local image
            System.out.println();
            System.out.println("Recognizing printed text from a local image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultLocal.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultLocal.textAngle());
            System.out.println("Orientation: " + ocrResultLocal.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultLocal.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    private static void RecognizeTextOCRRemote(ComputerVisionClient client, String remoteTextImageURL) {
        System.out.println("-----------------------------------------------");
        System.out.println("RECOGNIZE PRINTED TEXT");
        try {
            // Recognize printed text in remote image
            OcrResult ocrResultRemote = client.computerVision().recognizePrintedText().withDetectOrientation(true)
                    .withUrl(remoteTextImageURL).withLanguage(OcrLanguages.EN).execute();

            // Print results of remote image
            System.out.println();
            System.out.println("Recognizing text from remote image with OCR ...");
            System.out.println("\nLanguage: " + ocrResultRemote.language());
            System.out.printf("Text angle: %1.3f\n", ocrResultRemote.textAngle());
            System.out.println("Orientation: " + ocrResultRemote.orientation());

            boolean firstWord = true;
            // Gets entire region of text block
            for (OcrRegion reg : ocrResultRemote.regions()) {
                // Get one line in the text block
                for (OcrLine line : reg.lines()) {
                    for (OcrWord word : line.words()) {
                        // get bounding box of first word recognized (just to demo)
                        if (firstWord) {
                            System.out.println("\nFirst word in first line is \"" + word.text()
                                    + "\" with  bounding box: " + word.boundingBox());
                            firstWord = false;
                            System.out.println();
                        }
                        System.out.print(word.text() + " ");
                    }
                    System.out.println();
                }
            }
            
        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * READ : Performs a Read Operation on a remote image
     * @param client instantiated vision client
     * @param remoteTextImageURL public url from which to perform the read operation against
     */
    
    private static void ReadFromUrl(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        String remoteTextImageURL = "https://intelligentkioskstore.blob.core.chinacloudapi.cn/visionapi/suggestedphotos/3.png";
        System.out.println("Read with URL: " + remoteTextImageURL);
        try {
            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadHeaders responseHeader = vision.readWithServiceResponseAsync(remoteTextImageURL, OcrDetectionLanguage.FR)
                    .toBlocking()
                    .single()
                    .headers();

            // Extract the operation Id from the operationLocation header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }

    // <snippet_read_setup>
    /**
     * READ : Performs a Read Operation on a local image
     * @param client instantiated vision client
     * @param localFilePath local file path from which to perform the read operation against
     */
    private static void ReadFromFile(ComputerVisionClient client) {
        System.out.println("-----------------------------------------------");
        
        String localFilePath = "src\\main\\resources\\myImage.png";
        System.out.println("Read with local file: " + localFilePath);
        // </snippet_read_setup>
        // <snippet_read_call>

        try {
            File rawImage = new File(localFilePath);
            byte[] localImageBytes = Files.readAllBytes(rawImage.toPath());

            // Cast Computer Vision to its implementation to expose the required methods
            ComputerVisionImpl vision = (ComputerVisionImpl) client.computerVision();

            // Read in remote image and response header
            ReadInStreamHeaders responseHeader =
                    vision.readInStreamWithServiceResponseAsync(localImageBytes, OcrDetectionLanguage.FR)
                        .toBlocking()
                        .single()
                        .headers();
            // </snippet_read_call>
    // <snippet_read_response>
            // Extract the operationLocation from the response header
            String operationLocation = responseHeader.operationLocation();
            System.out.println("Operation Location:" + operationLocation);

            getAndPrintReadResult(vision, operationLocation);
    // </snippet_read_response>
            // <snippet_read_catch>

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
    // </snippet_read_catch>

    // <snippet_opid_extract>
    /**
     * Extracts the OperationId from a Operation-Location returned by the POST Read operation
     * @param operationLocation
     * @return operationId
     */
    private static String extractOperationIdFromOpLocation(String operationLocation) {
        if (operationLocation != null && !operationLocation.isEmpty()) {
            String[] splits = operationLocation.split("/");

            if (splits != null && splits.length > 0) {
                return splits[splits.length - 1];
            }
        }
        throw new IllegalStateException("Something went wrong: Couldn't extract the operation id from the operation location");
    }
    // </snippet_opid_extract>

    // <snippet_read_result_helper_call>
    /**
     * Polls for Read result and prints results to console
     * @param vision Computer Vision instance
     * @return operationLocation returned in the POST Read response header
     */
    private static void getAndPrintReadResult(ComputerVision vision, String operationLocation) throws InterruptedException {
        System.out.println("Polling for Read results ...");

        // Extract OperationId from Operation Location
        String operationId = extractOperationIdFromOpLocation(operationLocation);

        boolean pollForResult = true;
        ReadOperationResult readResults = null;

        while (pollForResult) {
            // Poll for result every second
            Thread.sleep(1000);
            readResults = vision.getReadResult(UUID.fromString(operationId));

            // The results will no longer be null when the service has finished processing the request.
            if (readResults != null) {
                // Get request status
                OperationStatusCodes status = readResults.status();

                if (status == OperationStatusCodes.FAILED || status == OperationStatusCodes.SUCCEEDED) {
                    pollForResult = false;
                }
            }
        }
        // </snippet_read_result_helper_call>
        
        // <snippet_read_result_helper_print>
        // Print read results, page per page
        for (ReadResult pageResult : readResults.analyzeResult().readResults()) {
            System.out.println("");
            System.out.println("Printing Read results for page " + pageResult.page());
            StringBuilder builder = new StringBuilder();

            for (Line line : pageResult.lines()) {
                builder.append(line.text());
                builder.append("\n");
            }

            System.out.println(builder.toString());
        }
    }
    // </snippet_read_result_helper_print>
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

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用计算机视觉 Java 库执行基本任务。 接下来，请在参考文档中详细了解该库。

> [!div class="nextstepaction"]
>[计算机视觉参考 (Java)](https://docs.microsoft.com/java/api/overview/cognitiveservices/client/computervision?view=azure-java-stable)

* [什么是计算机视觉？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上找到此示例的源代码。

