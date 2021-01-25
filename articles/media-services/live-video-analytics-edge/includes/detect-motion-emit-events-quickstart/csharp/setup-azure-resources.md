---
ms.openlocfilehash: 6b9d756b8af041eb936336e7c42f85e42ae5575c
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98229859"
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
    
    在脚本成功完成后，你应可在订阅中看到所有所需资源。
1. 脚本完成后，选择大括号即可公开文件夹结构。 你将看到一些文件。 与本快速入门相关的文件如下：

     * ***edge-deployment/.env** - 此文件包含 Visual Studio Code 用来将模块部署到边缘设备的属性。
     _ ***appsetting.json** _ - Visual Studio Code 使用此文件来运行示例代码。
     
    在下一节的 Visual Studio Code 中设置开发环境时，你需要这些文件。 现在需要将它们复制到本地文件中。
    
    ![应用设置](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> 如果在创建 Azure 资源时遇到问题，请查看[故障排除指南](../../../troubleshoot-how-to.md#common-error-resolutions)来解决一些常见问题。_*