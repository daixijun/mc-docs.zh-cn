---
title: 内容审查器 .NET 客户端库快速入门
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何开始使用适用于 .NET 的 Azure 内容审查器客户端库。 在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。
services: cognitive-services
author: Johnnytechn
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/28/2020
ms.author: v-johya
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: c97b369d2f52b93736ec025e6936bab68a40cb1f
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857114"
---
适用于 .NET 的 Azure 内容审查器客户端库入门。 请按照以下步骤安装 NuGet 包并试用基本任务的示例代码。 

内容审查器是一种 AI 服务，可用于处理可能的冒犯性、危险或不可取内容。 使用 AI 支持型内容审核服务扫描文本、图像和视频，并自动应用内容标志。

使用适用于 .NET 的内容审查器客户端库可以：

* 审查文本
* 审查图像

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建试用订阅](https://www.microsoft.com/china/azure/index.html?fromtype=cn)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或最新版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.cn/#create/Microsoft.CognitiveServicesContentModerator"  title="创建内容审查器资源"  target="_blank">创建内容审查器资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到内容审查器。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio 创建新的 .NET Core 应用程序。 

### <a name="install-the-client-library"></a>安装客户端库 

创建新项目后，右键单击“解决方案资源管理器”中的项目解决方案，然后选择“管理 NuGet 包”，以安装客户端库 。 在打开的包管理器中，选择“浏览”，选中“包括预发行版”并搜索 `Microsoft.Azure.CognitiveServices.ContentModerator`。 选择版本 `2.0.0`，然后选择“安装”。 

#### <a name="cli"></a>[CLI](#tab/cli)

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

### <a name="install-the-client-library"></a>安装客户端库 

在应用程序目录中，通过以下命令安装适用于 .NET 的内容审查器客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs) 上找到它，其中包含此快速入门中的代码示例。

在首选的编辑器或 IDE 中，从项目目录打开 *Program.cs* 文件。 添加以下 `using` 语句：

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

在“Program”类中，为资源的密钥和终结点创建变量。

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的内容审查器资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../cognitive-services-security.md)文章。

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```


在应用程序的 `main()` 方法中，添加对本快速入门中使用的方法的调用。 稍后将创建这些内容。

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```


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

在新方法中，使用终结点和密钥实例化客户端对象。

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

## <a name="moderate-text"></a>审查文本

以下代码使用内容审查器客户端分析文本的正文，并将结果输出到控制台。 在 Program 类的根中，定义输入和输出文件：

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

然后，在项目的根下添加一个 TextFile.txt 文件。 将你自己的文本添加到此文件中，或使用以下示例文本：

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


然后在 Program 类中的某个位置定义文本审查方法：

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

## <a name="moderate-images"></a>审查图像

以下代码使用内容审查器客户端和 [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) 对象，分析远程图像中的成人内容和猥亵内容。

> [!NOTE]
> 还可以分析本地图像的内容。 有关使用本地图像的方法和操作，请参阅[参考文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)。

### <a name="get-sample-images"></a>获取示例图像

在“Program”类的根中，定义输入和输出文件：

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

然后，在项目的根下创建输入文件 ImageFiles.txt。 在此文件中添加要分析的图像的 URL &mdash; 在每行添加一个 URL。 可使用以下示例图像：

```
https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample2.jpg
https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png
```

### <a name="define-helper-class"></a>使用帮助器类

在 Program 类中添加以下类定义。 此内部类将处理图像审查结果。

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

### <a name="define-the-image-moderation-method"></a>定义图像审查方法

以下方法将循环访问文本文件中的图像 URL，创建 EvaluationData 实例，并分析图像中的成人/猥亵内容、文本和人脸。 然后，它将最终的 EvaluationData 实例添加到列表中，并将返回数据的完整列表写入控制台。

#### <a name="iterate-through-images"></a>循环访问图像

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

#### <a name="analyze-content"></a>分析内容

有关内容审查器屏幕的图像属性的详细信息，请参阅[图像审查概念](../../image-moderation-api.md)指南。

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

#### <a name="write-moderation-results-to-file"></a>将审查结果写入文件

```csharp
// <snippet_using>
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
// </snippet_using>

/*
 * Content Moderator SDK Quickstart
 * 
 * Examples included:
 * - Image moderation - provide URL or image files for moderation
 * - Text moderation - provide text in a file for moderations
 * - Create human reviews for images - create a human review to be processed on the Content Moderator website.
 * 
 * Prerequisites:
 *  - Visual Studio 2019 (or 2017, but this is a .NET Core console app, not .NET Framework)
 *  - Create a C# console app in Visual Studio, then cut/paste this Program.cs file over your own (make sure namespaces match)
 *  - NuGet packages needed: Microsoft.Azure.CognitiveServices.ContentModerator & Newtonsoft.Json
 *  - Add the TextFile.txt and ImageFiles.txt included with this example to your bin/Debug/netcoreapp2.2 folder of your project.
 *  
 * How to Run:
 *  - Select start in Visual Studio
 *  - Image and text moderation results will appear, then the human reviews for images will start
 *  - Wait for the console command to tell you to perform a human review on https://{YOUR REGION}.contentmoderator.cognitive.microsoft.com
 *  - Perform the review on the website under Review-->Image. Select the "a" or "r" to test choosing the adult or racy markers, then choose "next".
 *  - Go back to the console and press enter to process the reviews.
 *  - The JSON returned will have review details in the console.
 *  - Check your output files for the image and text reviews. These files get created, only after the quickstart has run. 
 *    If testing multiple times, the output files will be overwritten with the latest moderation results.
 *    
 * References:
 *  - .NET SDK: https://docs.microsoft.com/dotnet/api/overview/cognitiveservices/client/contentmoderator?view=azure-dotnet
 *  - API (testing console): /cognitive-services/content-moderator/api-reference
 *  - Content Moderator documentation: /cognitive-services/content-moderator/
 */

namespace ContentModeratorQuickstart
{
    class Program
    {
        // AUTHENTICATION - ALL EXAMPLES
        // <snippet_creds>
        // Your Content Moderator subscription key is found in your Azure portal resource on the 'Keys' page.
        private static readonly string SubscriptionKey = "CONTENT_MODERATOR_SUBSCRIPTION_KEY";
        // Base endpoint URL. Found on 'Overview' page in Azure resource. For example: https://api.cognitive.azure.cn
        private static readonly string Endpoint = "CONTENT_MODERATOR_ENDPOINT";
        // </snippet_creds>

        // <snippet_image_vars>
        // IMAGE MODERATION
        //The name of the file that contains the image URLs to evaluate.
        private static readonly string ImageUrlFile = "ImageFiles.txt";
        // The name of the file to contain the output from the evaluation.
        private static string ImageOutputFile = "ImageModerationOutput.json";
        // </snippet_image_vars>

        // <snippet_text_vars>
        // TEXT MODERATION
        // Name of the file that contains text
        private static readonly string TextFile = "TextFile.txt";
        // The name of the file to contain the output from the evaluation.
        private static string TextOutputFile = "TextModerationOutput.txt";
        // </snippet_text_vars>

        // CREATE HUMAN REVIEWS FOR IMAGES
        // <snippet_review_urls>
        // The list of URLs of the images to create review jobs for.
        private static readonly string[] IMAGE_URLS_FOR_REVIEW = new string[] { "https://moderatorsampleimages.blob.core.chinacloudapi.cn/samples/sample5.png" };
        // </snippet_review_urls>
        // <snippet_review_vars>
        // The name of the team to assign the review to. Must be the team name used to create your Content Moderator website account. 
        // If you do not yet have an account, follow this: /cognitive-services/content-moderator/quick-start
        // Select the gear symbol (settings)-->Credentials to retrieve it. Your team name is the Id associated with your subscription.
        private static readonly string TEAM_NAME = "CONTENT_MODERATOR_TEAM_NAME";
        // The callback endpoint for completed human reviews.
        // For example: https://api.cognitive.azure.cn/contentmoderator/review/v1.0
        // As reviewers complete reviews, results are sent using an HTTP POST request.
        private static readonly string ReviewsEndpoint = "CONTENT_MODERATOR_REVIEWS_ENDPOINT";
        // </snippet_review_vars>

        static void Main(string[] args)
        {
            // CLIENTS - each API call needs its own client
            // <snippet_client>
            // Create an image review client
            ContentModeratorClient clientImage = Authenticate(SubscriptionKey, Endpoint);
            // Create a text review client
            ContentModeratorClient clientText = Authenticate(SubscriptionKey, Endpoint);
            // Create a human reviews client
            ContentModeratorClient clientReviews = Authenticate(SubscriptionKey, Endpoint);
            // </snippet_client>

            // <snippet_imagemod_call>
            // Moderate images from list of image URLs
            ModerateImages(clientImage, ImageUrlFile, ImageOutputFile);
            // </snippet_imagemod_call>

            // <snippet_textmod_call>
            // Moderate text from text in a file
            ModerateText(clientText, TextFile, TextOutputFile);
            // </snippet_textmod_call>

            // <snippet_review_call>
            // Create image reviews for human reviewers
            CreateReviews(clientReviews, IMAGE_URLS_FOR_REVIEW, TEAM_NAME, ReviewsEndpoint);
            // </snippet_review_call>

            Console.WriteLine();
            Console.WriteLine("End of the quickstart.");
        }
        /*
         * END - MAIN
         */

        /*
         * AUTHENTICATE
         * Creates a new client with a validated subscription key and endpoint.
         */
        // <snippet_auth>
        public static ContentModeratorClient Authenticate(string key, string endpoint)
        {
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(key));
            client.Endpoint = endpoint;

            return client;
        }
        // </snippet_auth>

        // <snippet_imagemod_iterate>
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
                            // </snippet_imagemod_iterate>
                            // <snippet_imagemod_analyze>
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
                // </snippet_imagemod_analyze>
                // <snippet_imagemod_save>
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
        // </snippet_imagemod_save>

        // <snippet_dataclass>
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
        // </snippet_dataclass>
        /*
         * END - IMAGE MODERATION
         */

        // <snippet_textmod>
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
        // </snippet_textmod>
        /*
         * END - TEXT MODERATION
         */

        /*
         * CREATE HUMAN IMAGE REVIEWS
         * This example shows how to create image reviews for humans to review on the Content Moderator website.
         * Results of the review (once manually performed on the website) are then returned and written to OutputLog.txt.
         * 
         * Prerequisistes: 
         * 1. In your Content Moderator resource go to Resource Management -> Properties, then copy your Resource ID. 
         * 2. Go to the Content Moderator website.
         * 3. Click the gear sign (Settings) and go to "Credentials". 
         * 4. Under "Whitelisted Resource Id(s)" paste your Resource ID, then select the "+" button to add it.
         *    This enables the website to receive programmatic reviews from your Content Moderator resource in Azure.
         * 
         * For more information about the steps:
         * /cognitive-services/content-moderator/moderation-reviews-quickstart-dotnet
         * Use your Azure account with the review APIs:
         * /cognitive-services/content-moderator/review-tool-user-guide/configure
         */
        // <snippet_review_item>
        // Associates the review ID (assigned by the service) to the internal.
        public class ReviewItem
        {
            // The media type for the item to review. 
            public string Type;
            // The URL of the item to review.
            public string Url;
            // The internal content ID for the item to review.
            public string ContentId;
            // The ID that the service assigned to the review.
            public string ReviewId;
        }
        // </snippet_review_item>

        // <snippet_createreview_fields>
        // Create the reviews using the fixed list of images.
        private static void CreateReviews(ContentModeratorClient client, string[] ImageUrls, string teamName, string endpoint)
        {
            Console.WriteLine("--------------------------------------------------------------");
            Console.WriteLine();
            Console.WriteLine("CREATE HUMAN IMAGE REVIEWS");

            // The minimum amount of time, in milliseconds, to wait between calls to the Image List API.
            const int throttleRate = 2000;
            // The number of seconds to delay after a review has finished before getting the review results from the server.
            const int latencyDelay = 45;

            // The name of the log file to create. Relative paths are relative to the execution directory.
            const string OutputFile = "OutputLog.txt";

            // The optional name of the subteam to assign the review to. Not used for this example.
            const string Subteam = null;

            // The media type for the item to review. Valid values are "image", "text", and "video".
            const string MediaType = "image";

            // The metadata key to initially add to each review item. This is short for 'score'.
            // It will enable the keys to be 'a' (adult) and 'r' (racy) in the response,
            // with a value of true or false if the human reviewer marked them as adult and/or racy.
            const string MetadataKey = "sc";
            // The metadata value to initially add to each review item.
            const string MetadataValue = "true";

            // A static reference to the text writer to use for logging.
            TextWriter writer;

            // The cached review information, associating a local content ID to the created review ID for each item.
            List<ReviewItem> reviewItems = new List<ReviewItem>();
            // </snippet_createreview_fields>

            // <snippet_createreview_create>
            using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
            {
                writer = outputWriter;
                WriteLine(writer, null, true);
                WriteLine(writer, "Creating reviews for the following images:", true);

                // Create the structure to hold the request body information.
                List<CreateReviewBodyItem> requestInfo = new List<CreateReviewBodyItem>();

                // Create some standard metadata to add to each item.
                List<CreateReviewBodyItemMetadataItem> metadata =
                    new List<CreateReviewBodyItemMetadataItem>(new CreateReviewBodyItemMetadataItem[]
                    { new CreateReviewBodyItemMetadataItem(MetadataKey, MetadataValue) });

                // Populate the request body information and the initial cached review information.
                for (int i = 0; i < ImageUrls.Length; i++)
                {
                    // Cache the local information with which to create the review.
                    var itemInfo = new ReviewItem()
                    {
                        Type = MediaType,
                        ContentId = i.ToString(),
                        Url = ImageUrls[i],
                        ReviewId = null
                    };

                    WriteLine(writer, $" {Path.GetFileName(itemInfo.Url)} with id = {itemInfo.ContentId}.", true);

                    // Add the item informaton to the request information.
                    requestInfo.Add(new CreateReviewBodyItem(itemInfo.Type, itemInfo.Url, itemInfo.ContentId, endpoint, metadata));

                    // Cache the review creation information.
                    reviewItems.Add(itemInfo);
                }

                var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync("application/json", teamName, requestInfo);
                // </snippet_createreview_create>

                // <snippet_createreview_ids>
                // Update the local cache to associate the created review IDs with the associated content.
                var reviewIds = reviewResponse.Result.Body;
                for (int i = 0; i < reviewIds.Count; i++) { reviewItems[i].ReviewId = reviewIds[i]; }

                WriteLine(outputWriter, JsonConvert.SerializeObject(reviewIds, Formatting.Indented));
                Thread.Sleep(throttleRate);

                // Get details of the reviews created that were sent to the Content Moderator website.
                WriteLine(outputWriter, null, true);
                WriteLine(outputWriter, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(outputWriter, $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                        $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));
                    Thread.Sleep(throttleRate);
                }
                // </snippet_createreview_ids>

                // <snippet_createreview_results>
                Console.WriteLine();
                Console.WriteLine("Perform manual reviews on the Content Moderator site.");
                Console.WriteLine("Then, press any key to continue.");
                Console.ReadKey();

                // After the human reviews, the results are confirmed.
                Console.WriteLine();
                Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
                Thread.Sleep(latencyDelay * 1000);

                // Get details from the human review.
                WriteLine(writer, null, true);
                WriteLine(writer, "Getting review details:", true);
                foreach (var item in reviewItems)
                {
                    var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(teamName, item.ReviewId);
                    WriteLine(writer, $"Review {item.ReviewId} for item ID {item.ContentId} is " + $"{reviewDetail.Result.Body.Status}.", true);
                    WriteLine(outputWriter, JsonConvert.SerializeObject(reviewDetail.Result.Body, Formatting.Indented));

                    Thread.Sleep(throttleRate);
                }

                Console.WriteLine();
                Console.WriteLine("Check the OutputLog.txt file for results of the review.");

                writer = null;
                outputWriter.Flush();
                outputWriter.Close();
            }
            Console.WriteLine("--------------------------------------------------------------");
        }
        // </snippet_createreview_results>

        // <snippet_writeline>
        // Helper function that writes a message to the log file, and optionally to the console.
        // If echo is set to true, details will be written to the console.
        private static void WriteLine(TextWriter writer, string message = null, bool echo = true)
        {
            writer.WriteLine(message ?? String.Empty);
            if (echo) { Console.WriteLine(message ?? String.Empty); }
        }
        // </snippet_writeline>
        /*
         * END - CREATE HUMAN IMAGE REVIEWS
         */
    }
}
```

## <a name="run-the-application"></a>运行应用程序

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

单击 IDE 窗口顶部的“调试”按钮，运行应用程序。

#### <a name="cli"></a>[CLI](#tab/cli)

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 .NET 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
> [图像审查的概念](../../image-moderation-api.md)

* [什么是 Azure 内容审查器？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs) 上找到此示例的源代码。

