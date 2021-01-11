---
title: 使用 Azure 媒体服务 REST 编码时对视频进行子剪辑
description: 本主题说明如何在使用 REST 通过 Azure 媒体服务进行编码时对视频进行子剪辑
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
origin.date: 06/10/2019
ms.date: 01/11/2021
ms.author: v-jay
ms.openlocfilehash: f4ab4739789ec3b89faa002955b08814359525a7
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023213"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>使用媒体服务编码时对视频进行子剪辑 - REST

在使用[作业](https://docs.microsoft.com/rest/api/media/jobs)对视频进行编码时，可以对其进行剪裁或子剪辑。 此功能适用于使用 [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 预设或 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 预设生成的任何[转换](https://docs.microsoft.com/rest/api/media/transforms)。 

本主题中的 REST 示例创建了一个作业，该作业会在提交编码作业时剪裁视频。 

## <a name="prerequisites"></a>必备条件

若要完成本主题中所述的步骤，必须：

- [创建 Azure 媒体服务帐户](./create-account-howto.md)。
- [配置 Postman 以便进行 Azure 媒体服务 REST API 调用](media-rest-apis-with-postman.md)。
    
    确保遵循[获取 Azure AD 令牌](media-rest-apis-with-postman.md#get-azure-ad-token)主题中的最后一步。 
- 创建转换和输出资产。 可以在[根据 URL 编码远程文件并流式传输视频 - REST](stream-files-tutorial-with-rest.md) 教程中了解如何创建转换和输出资产。
- 查看[编码概念](encoding-concept.md)主题。

## <a name="create-a-subclipping-job"></a>创建子剪辑作业

1. 在下载的 Postman 集合中，选择“转换和作业”   -> “使用子剪辑创建作业”  。
    
    **PUT** 请求如下所示：
    
    ```
    https://management.chinacloudapi.cn/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. 使用你的转换名称更新“transformName”环境变量的值。 
1. 选择“正文”  选项卡，并使用输出资产名称更新“myOutputAsset”。

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. 按“发送”。 

    你会看到 **响应**，其中包含有关已创建和提交的作业以及作业状态的信息。 

## <a name="next-steps"></a>后续步骤

[如何对自定义转换进行编码](custom-preset-rest-howto.md) 
