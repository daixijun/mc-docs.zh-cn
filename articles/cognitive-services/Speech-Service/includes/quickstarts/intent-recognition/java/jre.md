---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
origin.date: 04/04/2020
ms.date: 01/14/2021
ms.author: v-tawe
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 84d0d6bcc982431897069fc25d17fcc5cf3d4a09
ms.sourcegitcommit: 93063f9b8771b8e895c3bcdf218f5e3af14ef537
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195353"
---
## <a name="prerequisites"></a>先决条件

准备工作：

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">安装适用于开发环境的语音 SDK，并创建一个空示例项目<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="create-a-luis-app-for-intent-recognition"></a>创建 LUIS 应用以进行意向识别

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>打开项目

1. 打开首选 IDE。
2. 加载项目并打开 `Main.java`。

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手

添加一些代码作为项目的框架。

```java
package speechsdk.quickstart;

import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.intent.*;

/**
 * Quickstart: recognize speech using the Speech SDK for Java.
 */
public class Main {

    /**
     * @param args Arguments are ignored in this sample.
     */
    public static void main(String[] args) {
        try {
        } catch (Exception ex) {
            System.out.println("Unexpected exception: " + ex.getMessage());

            assert(false);
            System.exit(1);
        }
    }
}
```

## <a name="create-a-speech-configuration"></a>创建语音配置

需要创建一个使用 LUIS 预测资源的密钥和位置的配置，才能初始化 `IntentRecognizer` 对象。  

将此代码插入到 `main()` 中的 try/catch 块中。 请确保更新以下值：

* 将 `"YourLanguageUnderstandingSubscriptionKey"` 替换为 LUIS 预测密钥。
* 将 `"YourLanguageUnderstandingServiceRegion"` 替换为 LUIS 位置。 使用[区域](../../../../regions.md)中的“区域标识符”

>[!TIP]
> 如果需要帮助查找这些值，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourLanguageUnderstandingSubscriptionKey", "YourLanguageUnderstandingServiceRegion");
```

此示例使用 `FromSubscription()` 方法来生成 `SpeechConfig`。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)。

语音 SDK 将默认使用 en-us 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../../how-to-specify-source-language.md)。

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

现在，让我们创建 `IntentRecognizer`。 将此代码插入语音配置下。

```java
IntentRecognizer recognizer = new IntentRecognizer(config);
```

## <a name="add-a-languageunderstandingmodel-and-intents"></a>添加 LanguageUnderstandingModel 和意向

需要将 `LanguageUnderstandingModel` 与意向识别器相关联，并添加要识别的意向。 我们将使用预生成的域中的意向进行主自动化。

将此代码插入到你的 `IntentRecognizer` 下方。 请确保将 `"YourLanguageUnderstandingAppId"` 替换为 LUIS 应用 ID。

>[!TIP]
> 如果需要查找此值的帮助，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

```java
LanguageUnderstandingModel model = LanguageUnderstandingModel.fromAppId("YourLanguageUnderstandingAppId");
recognizer.addIntent(model, "HomeAutomation.TurnOn");
recognizer.addIntent(model, "HomeAutomation.TurnOff");
```

此示例使用 `addIntent()` 函数单独添加意向。 如果要从模型中添加所有意向，请使用 `addAllIntents(model)` 并传递模型。

> [!NOTE]
> 可以通过将终结点 URL 传递到 FromEndpoint 方法来创建 LanguageUnderstandingModel。
> 语音 SDK 仅支持 LUIS v2.0 终结点，后者始终遵循以下两种模式之一：
> * `https://{AzureResourceName}.cognitiveservices.azure.cn/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.azure.cn/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>识别意向

在 `IntentRecognizer` 对象中，我们将调用 `recognizeOnceAsync()` 方法。 此方法是告知语音服务你要发送单个需识别的短语，在确定该短语后会停止识别语音。

将此代码插入到你的模型下方：

```java
IntentRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

语音服务返回识别结果后，将需要对其进行处理。 我们会简单地将结果输出到控制台。

将此代码插入到对 `recognizeOnceAsync()` 的调用下方。

```java
if (result.getReason() == ResultReason.RecognizedIntent) {
    System.out.println("RECOGNIZED: Text=" + result.getText());
    System.out.println("    Intent Id: " + result.getIntentId());
    System.out.println("    Intent Service JSON: " + result.getProperties().getProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult));
}
else if (result.getReason() == ResultReason.RecognizedSpeech) {
    System.out.println("RECOGNIZED: Text=" + result.getText());
    System.out.println("    Intent not recognized.");
}
else if (result.getReason() == ResultReason.NoMatch) {
    System.out.println("NOMATCH: Speech could not be recognized.");
}
else if (result.getReason() == ResultReason.Canceled) {
    CancellationDetails cancellation = CancellationDetails.fromResult(result);
    System.out.println("CANCELED: Reason=" + cancellation.getReason());

    if (cancellation.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }
}
```

## <a name="release-resources"></a>释放资源

使用完语音资源后，必须将其释放。 将此代码插入到 try/catch 块的末尾：

```java
result.close();
recognizer.close();
```

## <a name="check-your-code"></a>查看代码

此时，代码应如下所示：

> [!NOTE]
> 我们已向此版本添加了一些注释。

```java
package speechsdk.quickstart;

import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.intent.*;

/**
 * Quickstart: recognize speech using the Speech SDK for Java.
 */
public class Main {

    /**
     * @param args Arguments are ignored in this sample.
     */
    public static void main(String[] args) {
        try {
            // <IntentRecognitionWithMicrophone>
            // Creates an instance of a speech config with specified
            // subscription key (called 'endpoint key' by the Language Understanding service)
            // and service region. Replace with your own subscription (endpoint) key
            // and service region (e.g., "chinaeast2").
            // The default language is "en-us".
            SpeechConfig config = SpeechConfig.fromSubscription("YourLanguageUnderstandingSubscriptionKey", "YourLanguageUnderstandingServiceRegion");

            // Creates an intent recognizer using microphone as audio input.
            IntentRecognizer recognizer = new IntentRecognizer(config);

            // Creates a language understanding model using the app id, and adds specific intents from your model
            LanguageUnderstandingModel model = LanguageUnderstandingModel.fromAppId("YourLanguageUnderstandingAppId");
            recognizer.addIntent(model, "HomeAutomation.TurnOn");
            recognizer.addIntent(model, "HomeAutomation.TurnOff");

            System.out.println("Say something...");

            // Starts recognition. It returns when the first utterance has been recognized.
            IntentRecognitionResult result = recognizer.recognizeOnceAsync().get();

            // Checks result.
            if (result.getReason() == ResultReason.RecognizedIntent) {
                System.out.println("RECOGNIZED: Text=" + result.getText());
                System.out.println("    Intent Id: " + result.getIntentId());
                System.out.println("    Intent Service JSON: " + result.getProperties().getProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult));
            }
            else if (result.getReason() == ResultReason.RecognizedSpeech) {
                System.out.println("RECOGNIZED: Text=" + result.getText());
                System.out.println("    Intent not recognized.");
            }
            else if (result.getReason() == ResultReason.NoMatch) {
                System.out.println("NOMATCH: Speech could not be recognized.");
            }
            else if (result.getReason() == ResultReason.Canceled) {
                CancellationDetails cancellation = CancellationDetails.fromResult(result);
                System.out.println("CANCELED: Reason=" + cancellation.getReason());

                if (cancellation.getReason() == CancellationReason.Error) {
                    System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                    System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                    System.out.println("CANCELED: Did you update the subscription info?");
                }
            }

            result.close();
            recognizer.close();
        } catch (Exception ex) {
            System.out.println("Unexpected exception: " + ex.getMessage());

            assert(false);
            System.exit(1);
        }
    }
}
```

## <a name="build-and-run-your-app"></a>生成并运行应用

按 <kbd>F11</kbd>，或选择“运行”   > “调试”  。
接下来的 15 秒，通过麦克风提供的语音输入将被识别并记录到控制台窗口中。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
