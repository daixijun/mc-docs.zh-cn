---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
origin.date: 04/04/2020
ms.date: 01/14/2021
ms.author: v-tawe
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a39d60c1f0b32d7038ea12c3a4fe06795b1b6172
ms.sourcegitcommit: 93063f9b8771b8e895c3bcdf218f5e3af14ef537
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195356"
---
## <a name="prerequisites"></a>先决条件

准备工作：

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp" target="_blank">安装适用于开发环境的语音 SDK，并创建一个空示例项目<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="create-a-luis-app-for-intent-recognition"></a>创建 LUIS 应用以进行意向识别

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中打开项目

接下来，在 Visual Studio 中打开项目。

1. 启动 Visual Studio 2019。
2. 加载项目并打开 `helloworld.cpp`。

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手

添加一些代码作为项目的框架。 请注意，已创建名为 `recognizeIntent()` 的异步方法。

```cpp
#include "stdafx.h"
// <code>
#include <iostream>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Intent;

void recognizeIntent()
{
}

int wmain()
{
    recognizeIntent();
    cout << "Please press a key to continue.\n";
    cin.get();
    return 0;
}
```

## <a name="create-a-speech-configuration"></a>创建语音配置

需要创建一个使用 LUIS 预测资源的密钥和位置的配置，才能初始化 `IntentRecognizer` 对象。

> [!IMPORTANT]
> 起始密钥和创作密钥将不起作用。 必须使用之前创建的预测密钥和位置。 有关详细信息，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

将此代码插入 `recognizeIntent()` 方法。 请确保更新以下值：

* 将 `"YourLanguageUnderstandingSubscriptionKey"` 替换为 LUIS 预测密钥。
* 将 `"YourLanguageUnderstandingServiceRegion"` 替换为 LUIS 位置。  使用[区域](../../../../regions.md)中的“区域标识符”。

>[!TIP]
> 如果需要帮助查找这些值，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

```cpp
auto config = SpeechConfig::FromSubscription("YourLanguageUnderstandingSubscriptionKey", "YourLanguageUnderstandingServiceRegion");
```

此示例使用 `FromSubscription()` 方法来生成 `SpeechConfig`。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)。

语音 SDK 将默认使用 en-us 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../../how-to-specify-source-language.md)。

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

现在，让我们创建 `IntentRecognizer`。 将此代码插入语音配置下的 `recognizeIntent()` 方法。

```cpp
auto recognizer = IntentRecognizer::FromConfig(config);
```

## <a name="add-a-languageunderstandingmodel-and-intents"></a>添加 LanguageUnderstandingModel 和意向

需要将 `LanguageUnderstandingModel` 与意向识别器相关联，并添加要识别的意向。 我们将使用预生成的域中的意向进行主自动化。

将此代码插入到你的 `IntentRecognizer` 下方。 请确保将 `"YourLanguageUnderstandingAppId"` 替换为 LUIS 应用 ID。

>[!TIP]
> 如果需要查找此值的帮助，请参阅[创建 LUIS 应用以进行意向识别](#create-a-luis-app-for-intent-recognition)。

```cpp
auto model = LanguageUnderstandingModel::FromAppId("YourLanguageUnderstandingAppId");
recognizer->AddIntent(model, "HomeAutomation.TurnOn");
recognizer->AddIntent(model, "HomeAutomation.TurnOff");
```

此示例使用 `AddIntent()` 函数单独添加意向。 如果要从模型中添加所有意向，请使用 `AddAllIntents(model)` 并传递模型。

> [!NOTE]
> 可以通过将终结点 URL 传递到 FromEndpoint 方法来创建 LanguageUnderstandingModel。
> 语音 SDK 仅支持 LUIS v2.0 终结点，后者始终遵循以下两种模式之一：
> * `https://{AzureResourceName}.cognitiveservices.azure.cn/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.azure.cn/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>识别意向

在 `IntentRecognizer` 对象中，我们将调用 `RecognizeOnceAsync()` 方法。 此方法是告知语音服务你要发送单个需识别的短语，在确定该短语后会停止识别语音。 此为简写内容，今后将回头补充。

将此代码插入到你的模型下方：

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

语音服务返回识别结果后，将需要对其进行处理。 我们会简单地将结果输出到控制台。

将此代码插在 `auto result = recognizer->RecognizeOnceAsync().get();` 下：

```cpp
if (result->Reason == ResultReason::RecognizedIntent)
{
    cout << "RECOGNIZED: Text=" << result->Text << std::endl;
    cout << "  Intent Id: " << result->IntentId << std::endl;
    cout << "  Intent Service JSON: " << result->Properties.GetProperty(PropertyId::LanguageUnderstandingServiceResponse_JsonResult) << std::endl;
}
else if (result->Reason == ResultReason::RecognizedSpeech)
{
    cout << "RECOGNIZED: Text=" << result->Text << " (intent could not be recognized)" << std::endl;
}
else if (result->Reason == ResultReason::NoMatch)
{
    cout << "NOMATCH: Speech could not be recognized." << std::endl;
}
else if (result->Reason == ResultReason::Canceled)
{
    auto cancellation = CancellationDetails::FromResult(result);
    cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

    if (cancellation->Reason == CancellationReason::Error)
    {
        cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
        cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
        cout << "CANCELED: Did you update the subscription info?" << std::endl;
    }
}
```

## <a name="check-your-code"></a>查看代码

此时，代码应如下所示：

> [!NOTE]
> 我们已向此版本添加了一些注释。

```cpp
#include "stdafx.h"
// <code>
#include <iostream>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Intent;

void recognizeIntent()
{
    // Creates an instance of a speech config with specified subscription key
    // and service region. Note that in contrast to other services supported by
    // the Cognitive Services Speech SDK, the Language Understanding service
    // requires a specific subscription key from https://luis.azure.cn/.
    // The Language Understanding service calls the required key 'endpoint key'.
    // Once you've obtained it, replace with below with your own Language Understanding subscription key
    // and service region (e.g., "chinaeast2").
    // The default recognition language is "en-us".
    auto config = SpeechConfig::FromSubscription("YourLanguageUnderstandingSubscriptionKey", "YourLanguageUnderstandingServiceRegion");

    // Creates an intent recognizer using microphone as audio input.
    auto recognizer = IntentRecognizer::FromConfig(config);

    // Creates a Language Understanding model using the app id, and adds specific intents from your model
    auto model = LanguageUnderstandingModel::FromAppId("YourLanguageUnderstandingAppId");
    recognizer->AddIntent(model, "HomeAutomation.TurnOn");
    recognizer->AddIntent(model, "HomeAutomation.TurnOff");

    cout << "Say something...\n";

    // Starts intent recognition, and returns after a single utterance is recognized. The end of a
    // single utterance is determined by listening for silence at the end or until a maximum of 15
    // seconds of audio is processed.  The task returns the recognition text as result.
    // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
    // shot recognition like command or query.
    // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
    auto result = recognizer->RecognizeOnceAsync().get();

    // Checks result.
    if (result->Reason == ResultReason::RecognizedIntent)
    {
        cout << "RECOGNIZED: Text=" << result->Text << std::endl;
        cout << "  Intent Id: " << result->IntentId << std::endl;
        cout << "  Intent Service JSON: " << result->Properties.GetProperty(PropertyId::LanguageUnderstandingServiceResponse_JsonResult) << std::endl;
    }
    else if (result->Reason == ResultReason::RecognizedSpeech)
    {
        cout << "RECOGNIZED: Text=" << result->Text << " (intent could not be recognized)" << std::endl;
    }
    else if (result->Reason == ResultReason::NoMatch)
    {
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
    }
    else if (result->Reason == ResultReason::Canceled)
    {
        auto cancellation = CancellationDetails::FromResult(result);
        cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

        if (cancellation->Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
            cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
            cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }
}

int wmain()
{
    recognizeIntent();
    cout << "Please press a key to continue.\n";
    cin.get();
    return 0;
```

## <a name="build-and-run-your-app"></a>生成并运行应用

现在，可以使用语音服务构建应用并测试语音识别。

1. “编译代码”- 在 Visual Studio 菜单栏中，选择“生成” > “生成解决方案”  。
2. **启动应用** - 在菜单栏中，选择“调试” > “开始调试”，或按 F5   。
3. **开始识别** - 它将提示你说英语短语。 语音将发送到语音服务，转录为文本，并在控制台中呈现。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
