---
author: Johnnytechn
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: include
ms.date: 11/23/2020
ms.author: v-johya
ms.openlocfilehash: 1b4de0933cd501983228b759282813c5c630ecd2
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306594"
---
## <a name="set-up"></a>设置

### <a name="create-a-translator-resource"></a>创建“翻译”资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](../../cognitive-services-apis-create-account.md)或 [Azure CLI](../../cognitive-services-apis-create-account-cli.md) 创建用于“翻译”的资源。 你还可以：

* 查看 [Azure 门户](https://portal.azure.cn/)中的现有资源。

从试用订阅或资源获取密钥后，创建两个[环境变量](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)：

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - “翻译”资源的订阅密钥。
* `TRANSLATOR_TEXT_ENDPOINT` - 翻译器的终结点。 改用 `https://api.translator.azure.cn/`

