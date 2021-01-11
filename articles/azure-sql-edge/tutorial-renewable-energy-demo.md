---
title: 在 Contoso 风力发电厂中的涡轮机上部署 Azure SQL Edge
description: 在本教程中，你将使用 Azure SQL Edge 对 Contoso 风力发电厂中的涡轮机进行尾流检测。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: v-tawe
ms.reviewer: sstein
origin.date: 12/18/2020
ms.date: 12/18/2020
ms.openlocfilehash: 2ff4cc7429080b15a25aa3f5e805920352271c6a
ms.sourcegitcommit: eb742dcade404c9909d01e2570188f0bc4076992
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97820416"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>使用 Azure SQL Edge 构建更智能的可再生资源

此 Azure SQL Edge 演示基于 Contoso Renewable Energy，这是一个风力涡轮发电场，使用 SQL DB edge 进行发电机上的数据处理。 

此演示将引导你解决由于在设备上检测到风湍流而引起的警报。 你将训练模型并将其部署到 SQL DB Edge，它将纠正检测到的风尾流并最终优化功率输出。

<!--
Azure SQL Edge - renewable Energy demo video on Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]
-->

## <a name="setting-up-the-demo-on-your-local-computer"></a>在本地计算机上设置演示
Git 将用于将所有文件从演示复制到本地计算机。 

1. 从[此处](https://git-scm.com/download)安装 git。
2. 打开命令提示符，然后导航到应该将存储库下载到的文件夹。 
3. 发出命令 https://github.com/microsoft/sql-server-samples.git。
4. 导航到“sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo”（位于克隆存储库的位置）。
5. 请按照 README.md 中的说明设置演示环境并执行演示。