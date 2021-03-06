---
title: 全局参数
description: 为每个 Azure 数据工厂环境设置全局参数
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: WenJason
ms.author: v-jay
origin.date: 08/31/2020
ms.date: 11/02/2020
ms.openlocfilehash: be5e43989c223fbd7ea7a636cf4c803797c5ccef
ms.sourcegitcommit: 93309cd649b17b3312b3b52cd9ad1de6f3542beb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93106081"
---
# <a name="global-parameters-in-azure-data-factory"></a>Azure 数据工厂中的全局参数

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

全局参数是整个数据工厂的常量，可以由任何表达式中的管道使用。 当多个管道具有相同的参数名称和值时，这些全局参数会很有用。 

## <a name="creating-global-parameters"></a>创建全局参数

若要创建全局参数，请转到“管理” 部分中的“全局参数”选项卡。 选择“新建”以打开“创建”侧导航栏。

![突出显示选择用于创建全局参数的“新建”按钮的屏幕截图。](media/author-global-parameters/create-global-parameter-1.png)

在侧导航栏中，输入名称，选择数据类型，并指定参数的值。

![显示添加新的全局参数的名称、数据类型和值的位置的屏幕截图。](media/author-global-parameters/create-global-parameter-2.png)

创建全局参数后，可以通过单击参数的名称对其进行编辑。 若要同时更改多个参数，请选择“全部编辑”。

![创建全局参数](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>在管道中使用全局参数

全局参数可用于任何[管道表达式](control-flow-expression-language-functions.md)。 如果管道引用其他资源（如数据集），则可以通过该资源的参数向下传递全局参数值。 全局参数以 `pipeline().globalParameters.<parameterName>` 形式进行引用。

![使用全局参数](media/author-global-parameters/expression-global-parameters.png)

## <a name="next-steps"></a>后续步骤

* 了解如何使用[控制流表达式语言](control-flow-expression-language-functions.md)