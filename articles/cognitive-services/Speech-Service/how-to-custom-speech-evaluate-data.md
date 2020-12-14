---
title: 评估并提高自定义语音识别准确度 - 语音服务
titleSuffix: Azure Cognitive Services
description: 本文档介绍如何以定量方式度量和提高我们的语音转文本模型或你的自定义模型的质量。 需要使用音频 + 人为标记的听录数据来测试准确度，并应提供 30 分钟到 5 小时的代表性音频。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 11/11/2020
ms.date: 12/10/2020
ms.author: v-tawe
ms.openlocfilehash: d154c6df32481d89889ee4f0c335e750597b92ad
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004082"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>评估并提升自定义语音识别准确度

本文档介绍如何以定量方式度量和提高 Microsoft 的语音转文本模型或你自己的自定义模型的准确度。 需要使用音频 + 人为标记的听录数据来测试准确度，并应提供 30 分钟到 5 小时的代表性音频。

## <a name="evaluate-custom-speech-accuracy"></a>评估自定义语音识别准确度

用于度量模型准确度的行业标准是误字率 (WER)。 计算 WER 时，先对识别过程中标识的错误单词计数，然后将其除以人为标记的听录中提供的单词的总数（下面显示为 N）。 最后将该数字乘以 100%。

![WER 公式](./media/custom-speech/custom-speech-wer-formula.png)

错误标识的单词分为三个类别：

* 插入 (I)：在假设脚本中错误添加的单词
* 删除 (D)：在假设脚本中未检测到的单词
* 替换 (S)：在引用和假设之间替换的单词

下面是一个示例：

![错误标识的单词的示例](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>解决错误并降低 WER

可以使用机器识别结果中的 WER 来评估与应用、工具或产品配合使用的模型的质量。 WER 为 5%-10% 表明质量好，可以使用。 WER 为 20% 可以接受，但可能需要考虑进行更多的训练。 WER 为 30% 或以上表明质量差，需要自定义和训练。

错误的分布情况很重要。 如果遇到许多删除错误，通常是因为音频信号强度弱。 若要解决此问题，需要在收集音频数据时更靠近源。 插入错误意味着音频是在嘈杂环境中记录的，并且可能存在串音，导致识别问题。 如果以人为标记的听录或相关文本形式提供特定于领域的术语样本不足，则通常会遇到替换错误。

可以通过分析单个文件来确定存在的错误的类型，以及哪些错误是特定文件独有的。 在文件级别了解问题将有助于你确定改进目标。

## <a name="create-a-test"></a>创建测试

若要测试 Microsoft 的语音转文本基线模型或你训练的自定义模型的质量，可以将两个模型并排比较一下，评估准确度。 此比较包括 WER 和识别结果。 通常情况下，自定义模型会与 Microsoft 的基线模型比较。

若要并排评估模型，请执行以下操作：

1. 登录到[自定义语音识别门户](https://speech.azure.cn/customspeech)。
2. 导航到“语音转文本”>“自定义语音识别”> [项目名称] >“测试”。
3. 单击“添加测试”。
4. 选择“评估准确度”。 为测试提供名称和说明，然后选择你的音频和人为标记的听录数据集。
5. 选择最多两个要测试的模型。
6. 单击 **创建**。

成功创建测试后，可以并排比较结果。

### <a name="side-by-side-comparison"></a>并排比较

测试完成（状态更改为“成功”即表明完成）后，就可以找到测试中包括的两个模型的 WER 值。 单击测试名称可查看测试详细信息页。 该详细信息页会列出数据集中的所有言语，指示两个模型的识别结果以及提供的数据集中的听录。 可以通过切换各种错误类型（包括插入、删除和替换）来查看并排比较的结果。 通过听音频并比较每个列（显示人为标记的听录和两个语音转文本模型的结果）中的识别结果，你可以确定哪个模型符合自己的需求，以及需要在哪些方面进行更多的训练和改进。

<!-- 
## Improve Custom Speech accuracy

Speech recognition scenarios vary by audio quality and language (vocabulary and speaking style). The following table examines four common scenarios:

| Scenario | Audio Quality | Vocabulary | Speaking Style |
|----------|---------------|------------|----------------|
| Call center | Low, 8 kHz, could be 2 humans on 1 audio channel, could be compressed | Narrow, unique to domain and products | Conversational, loosely structured |
| Voice assistant (such as Cortana, or a drive-through window) | High, 16 kHz | Entity heavy (song titles, products, locations) | Clearly stated words and phrases |
| Dictation (instant message, notes, search) | High, 16 kHz | Varied | Note-taking |
| Video closed captioning | Varied, including varied microphone use, added music | Varied, from meetings, recited speech, musical lyrics | Read, prepared, or loosely structured |

Different scenarios produce different quality outcomes. The following table examines how content from these four scenarios rates in the [word error rate (WER)](how-to-custom-speech-evaluate-data.md). The table shows which error types are most common in each scenario.

| Scenario | Speech Recognition Quality | Insertion Errors | Deletion Errors | Substitution Errors |
|----------|----------------------------|------------------|-----------------|---------------------|
| Call center | Medium (< 30% WER) | Low, except when other people talk in the background | Can be high. Call centers can be noisy, and overlapping speakers can confuse the model | Medium. Products and people's names can cause these errors |
| Voice assistant | High (can be < 10% WER) | Low | Low | Medium, due to song titles, product names, or locations |
| Dictation | High (can be < 10% WER) | Low | Low | High |
| Video closed captioning | Depends on video type (can be < 50% WER) | Low | Can be high due to music, noises, microphone quality | Jargon may cause these errors |

Determining the components of the WER (number of insertion, deletion, and substitution errors) helps determine what kind of data to add to improve the model. Use the [Custom Speech portal](https://speech.azure.cn/customspeech) to view the quality of a baseline model. The portal reports insertion, substitution, and deletion error rates that are combined in the WER quality rate.

## Improve model recognition

You can reduce recognition errors by adding training data in the [Custom Speech portal](https://speech.azure.cn/customspeech). 

Plan to maintain your custom model by adding source materials periodically. Your custom model needs additional training to stay aware of changes to your entities. For example, you may need updates to product names, song names, or new service locations.

The following sections describe how each kind of additional training data can reduce errors.

### Add related text sentences

Additional related text sentences can primarily reduce substitution errors related to misrecognition of common words and domain-specific words by showing them in context. Domain-specific words can be uncommon or made-up words, but their pronunciation must be straightforward to be recognized.

> [!NOTE]
> Avoid related text sentences that include noise such as unrecognizable characters or words.

### Add audio with human-labeled transcripts

Audio with human-labeled transcripts offers the greatest accuracy improvements if the audio comes from the target use case. Samples must cover the full scope of speech. For example, a call center for a retail store would get most calls about swimwear and sunglasses during summer months. Assure that your sample includes the full scope of speech you want to detect.

Consider these details:

* Custom Speech can only capture word context to reduce substitution errors, not insertion or deletion errors.
* Avoid samples that include transcription errors, but do include a diversity of audio quality.
* Avoid sentences that are not related to your problem domain. Unrelated sentences can harm your model.
* When the quality of transcripts vary, you can duplicate exceptionally good sentences (like excellent transcriptions that include key phrases) to increase their weight.

### Add new words with pronunciation

Words that are made-up or highly specialized may have unique pronunciations. These words can be recognized if the word can be broken down into smaller words to pronounce it. For example, to recognize **Xbox**, pronounce as **X box**. This approach will not increase overall accuracy, but can increase recognition of these keywords.

> [!NOTE]
> This technique is only available for some languages at this time. See customization for pronunciation in [the Speech-to-text table](language-support.md) for details.

## Sources by scenario

The following table shows voice recognition scenarios and lists source materials to consider within the three training content categories listed above.

| Scenario | Related text sentences | Audio + human-labeled transcripts | New words with pronunciation |
|----------|------------------------|------------------------------|------------------------------|
| Call center             | marketing documents, website, product reviews related to call center activity | call center calls transcribed by humans | terms that have ambiguous pronunciations (see Xbox above) |
| Voice assistant         | list sentences using all combinations of commands and entities | record voices speaking commands into device, and transcribe into text | names (movies, songs, products) that have unique pronunciations |
| Dictation               | written input, like instant messages or emails | similar to above | similar to above |
| Video closed captioning | TV show scripts, movies, marketing content, video summaries | exact transcripts of videos | similar to above |
-->

## <a name="next-steps"></a>后续步骤

* [训练和部署模型](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>其他资源

* [准备和测试数据](./how-to-custom-speech-test-and-train.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
