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
ms.date: 01/14/2021
ms.author: v-tawe
ms.openlocfilehash: c76ed380e9e72f569e019f05b3345040e518038e
ms.sourcegitcommit: 93063f9b8771b8e895c3bcdf218f5e3af14ef537
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98193220"
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

若要在本地复制 WER 度量，可使用 [SCTK](https://github.com/usnistgov/SCTK) 中的 sclite。

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
-->

## <a name="improve-model-recognition"></a>改善模型识别

可以通过在[自定义语音识别门户](https://speech.azure.cn/customspeech)中添加训练数据来减少识别错误。 

计划通过定期添加源材料来维护自定义模型。 自定义模型需要额外的训练来了解对实体的更改。 例如，可能需要更新的产品名称、歌曲名称或新的服务位置。

以下各节介绍了其他每种类型的训练数据如何减少错误。

### <a name="add-related-text-sentences"></a>添加相关的文本句子

训练新的自定义模型时，首先添加相关文本，来改进对领域特定字词和短语的识别。 相关文本句子主要可通过在上下文中显示常见字词和领域特定字词，来减少与错误识别这些字词相关的替换错误。 特定领域的字词可能不太常见或者属于杜撰的字词，但其发音必须易于识别。

> [!NOTE]
> 避免相关的文本句子包含无法识别的字符或字词等干扰因素。

### <a name="add-audio-with-human-labeled-transcripts"></a>添加具有人为标记的脚本的音频

如果音频来自目标用例，则带有人为标记的脚本的音频可提供最大的准确性改进。 示例必须涵盖整个语音范围。 例如，零售商店的呼叫中心会在夏季接到大量有关泳装和太阳镜的电话。 确保示例包括要检测的整个语音范围。

考虑以下细节：

* 自定义语音识别只能捕获字词上下文来减少替换错误，而不是插入或删除错误。
* 避免使用包含脚本错误的示例，但使用包含各种音频质量的示例。
* 避免使用与问题领域无关的句子。 不相关的句子可能损坏模型。
* 当脚本质量参差不齐时，可以复制非常好的句子（例如包含关键短语的优秀脚本）以增加其权重。
* 语音服务将自动使用脚本来改进对领域特定字词和短语的识别，就像它们被添加作为相关文本一样。
* 如果音频对人们来说也很难理解，那么通过音频训练将提供最大优势。 在大多数情况下，你应该只使用相关文本开始训练。
* 完成训练操作可能需要几天时间。 为了加快训练速度，请确保在[具有专用硬件的区域](custom-speech-overview.md#set-up-your-azure-account)创建语音服务订阅来进行训练。

> [!NOTE]
> 并非所有基本模型都支持通过音频训练。 如果基本模型不支持该训练，语音服务将仅使用脚本中的文本，而忽略音频。

### <a name="add-new-words-with-pronunciation"></a>添加具有发音的新字词

杜撰的字词或高度专业化的字词可能具有独特发音。 如果可以将这类字词分解为更小的字词，则可以识别这些字词。  例如，若要识别 Xbox，可以发音为 X box。 此方法不会提高总体准确性，但可以增加对这些关键字的识别度。

> [!NOTE]
> 此方法目前仅适用于某些语言。 有关详细信息，请参阅[语音转文本表](language-support.md)中的发音自定义。

## <a name="sources-by-scenario"></a>按场景划分的来源

下表显示了语音识别场景，列出了上述三个训练内容类别中需要考虑的来源资料。

| 方案 | 相关文本句子 | 音频和人为标记的听录内容 | 带有发音的新字词 |
|----------|------------------------|------------------------------|------------------------------|
| 呼叫中心             | 与呼叫中心活动相关的营销文档、网站和产品评审 | 呼叫中心通话采用人工转录 | 发音模糊的术语（请查看上面的 Xbox） |
| 语音助手         | 使用命令和实体的各种组合列出句子 | 将语音命令录制到设备中，并转录到文本中 | 具有独特发音的名称（电影、歌曲、产品） |
| 听写               | 书面输入内容，例如即时消息或电子邮件 | 与上述内容相似 | 与上述内容相似 |
| 视频隐藏式字幕 | 电视节目脚本、电影、营销内容、视频摘要 | 视频的确切脚本 | 与上述内容相似 |

## <a name="next-steps"></a>后续步骤

* [训练和部署模型](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>其他资源

* [准备和测试数据](./how-to-custom-speech-test-and-train.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
