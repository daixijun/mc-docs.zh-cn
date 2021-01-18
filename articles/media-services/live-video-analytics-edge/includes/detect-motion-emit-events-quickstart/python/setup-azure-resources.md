---
ms.openlocfilehash: 7f73cdfddd6946dd07427192b79bf16d4afe4e22
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024272"
---
本教程需要以下 Azure 资源：

* IoT 中心
* 存储帐户
* Azure 媒体服务帐户
* Azure 中的 Linux VM，已安装 [IoT Edge 运行时](../../../../../iot-edge/how-to-install-iot-edge.md)

在本快速入门中，我们建议你使用[实时视频分析资源设置脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)在 Azure 订阅中部署所需资源。 为此，请执行下列步骤：

1. 运行以下 Bash 命令。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    如果脚本成功完成，你应该可在订阅中看到所有所需资源。
1. 脚本完成后，选择大括号即可公开文件夹结构。 你将看到一些文件。 与本快速入门相关的文件如下：

     * ***edge-deployment/.env** - 此文件包含 Visual Studio Code 用来将模块部署到边缘设备的属性。
     ***appsetting.json*** - Visual Studio Code 使用此文件来运行示例代码。
     
    在下一节的 Visual Studio Code 中设置开发环境时，你需要这些文件。 现在需要将它们复制到本地文件中。
    
    ![应用设置](../../../media/quickstarts/clouddrive.png)
    