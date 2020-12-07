---
title: 什么是 Azure 认知服务？
titleSuffix: Azure Cognitive Services
description: 认知服务让每个开发人员都可以使用 AI，而无需机器学习和数据科学专业知识。 你只需要从应用程序中进行 API 调用即可向你的应用中添加看（高级图像搜索和识别）、听、说、搜索和决策制定的功能。
services: cognitive-services
author: Johnnytechn
manager: nitinme
keywords: cognitive services, cognitive intelligence, cognitive solutions, ai services, cognitive understanding, cognitive features
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 11/23/2020
ms.author: v-johya
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 0bd3e8669a79c1ed83f0620063ebd117214538c1
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306168"
---
# <a name="what-are-azure-cognitive-services"></a>什么是 Azure 认知服务？

Azure 认知服务是具有 REST API 和客户端库 SDK 的基于云的服务，可用于帮助你将认知智能构建到应用程序中。 即使你没有人工智能 (AI) 或数据科学技能，也可向应用程序添加认知功能。 Azure 认知服务包含各种 AI 服务，让你能够构建可以看、听、说、理解，甚至可以决策的认知解决方案。

## <a name="categories-of-cognitive-services"></a>认知服务类别

提供认知理解功能的认知服务主要分为五大类：

* 影像
* 语音
* 语言
* 决策

本文的以下部分提供了属于这四大类的服务的列表。

## <a name="vision-apis"></a>视觉 API

|服务名称|服务说明|
|:-----------|:------------------|
|[计算机视觉](./computer-vision/index.yml "计算机视觉")|使用计算机视觉服务，你可以访问用于处理图像并返回信息的高级认知算法。|
|[人脸](./face/index.yml "人脸")| 使用人脸服务可访问高级人脸算法，从而实现人脸属性检测和识别。|

## <a name="speech-apis"></a>语音 API

|服务名称|服务说明|
|:-----------|:------------------|
|[语音服务](./speech-service/index.yml "语音服务")|语音服务将支持语音的功能添加到应用程序。 语音服务包括各种功能，如语音到文本、文本到语音以及语音翻译等。|

## <a name="language-apis"></a>语言 API

|服务名称|服务说明|
|:-----------|:------------------|
|[语言理解 LUIS](./luis/index.yml "语言理解")|语言理解服务 (LUIS) 使应用程序可以理解用户以自己的语言表达的内容。|
|[文本分析](./text-analytics/index.yml "文本分析")| 文本分析提供对原始文本的自然语言处理，用于情绪分析、关键短语提取和语言检测。|
|[翻译](./translator/index.yml "转换器")|“翻译”近乎实时地提供基于机器的文本翻译。|

## <a name="decision-apis"></a>决策 API

|服务名称|服务说明|
|:-----------|:------------------|
|[内容审查器](./content-moderator/overview.md "内容审查器")|内容审查器监视可能的冒犯性、不可取和危险内容。|
|[指标顾问](./metrics-advisor/index.yml)（预览版） | 指标顾问提供可自定的多变量时序数据义异常情况检测，并提供一个功能齐全的 Web 门户来帮助使用该服务。|

## <a name="development-options"></a>开发选项 

通过 Azure 和认知服务，可以访问若干开发选项，例如：

* 自动化和集成工具，例如逻辑应用和 Power Automate。
* 部署选项，例如 Azure Functions 和应用服务。 
* 用于安全访问的认知服务 Docker 容器。

若要了解详细信息，请参阅[认知服务开发选项](./cognitive-services-development-options.md)。

## <a name="learn-with-the-quickstarts"></a>通过快速入门学习

首先，让我们使用以下方法，通过实操性快速入门创建认知服务资源：

* [Azure 门户](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure 门户")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK 客户端库](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")

## <a name="using-cognitive-services-securely"></a>安全地使用认知服务

Azure 认知服务提供了分层的安全模型，包括通过 Azure Active Directory 凭据进行的[身份验证](authentication.md "身份验证")、有效的资源密钥以及 [Azure 虚拟网络](cognitive-services-virtual-networks.md "Azure 虚拟网络")。

## <a name="containers-for-cognitive-services"></a>认知服务的容器

 认知服务提供用于在 Azure 云或本地部署的容器。 详细了解[认知服务容器](cognitive-services-container-support.md "认知服务容器")。

## <a name="regional-availability"></a>区域可用性

认知服务中的 API 托管在不断扩大的 Azure 托管数据中心网络上。 你可以在 [Azure 区域列表](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services "Azure 区域列表")中找到每个 API 的区域可用性。

## <a name="supported-cultural-languages"></a>支持的区域性语言

认知服务在服务级别支持各种区域性语言。 可以在[支持的语言列表](language-support.md "支持的语言列表")中找到每个 API 的语言可用性。

## <a name="certifications-and-compliance"></a>认证和合规性

认知服务已获得 CSA STAR 认证、FedRAMP 中等 和HIPAA BAA 等认证。 可以[下载](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "下载")认证进行自己的审核和安全评审。

若要了解隐私和数据管理，请访问[信任中心](https://servicetrust.microsoft.com/ "信任中心")。

## <a name="support"></a>支持

认知服务提供若干支持选项，可帮助你继续创建智能应用程序。 认知服务还有一个强大的开发人员社区，可以帮助回答你的特定问题。 有关你可以使用的选项的完整列表，请参阅[认知服务支持和帮助选项](cognitive-services-support-options.md "认知服务支持和帮助选项")。

## <a name="next-steps"></a>后续步骤

* [创建认知服务帐户](cognitive-services-apis-create-account.md "创建认知服务帐户")

