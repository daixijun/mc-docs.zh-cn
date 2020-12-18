---
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 11/19/2020
ms.date: 12/14/2020
ms.author: v-jay
ms.custom: CLI
ms.openlocfilehash: 915896cf7777034d63f45d8ea5016308a9e8b6be
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004282"
---
<!--Create a basic audio transform REST-->

使用以下 Azure REST 命令可将视频和音频从一个位置复制到另一个位置，而无需进一步编码。 将 `subscriptionID`、`resourceGroup` 和 `accountName` 值替换为当前正在使用的值。 通过设置 `transformName` 为转换命名。

```REST

PUT https://management.chinacloudapi.cn/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>正文

```json
{
    "properties": {
        "description": "Copy video and audio files with no encoding",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.CopyAudio"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.CopyVideo"
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "{Basename}_Copy{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="sample-response"></a>示例响应
```json
{
    "name": "aacaudio",
    "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/aacaudio",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "created": "2020-11-19T00:12:48.6001786Z",
        "description": "Copy audio files with no encoding",
        "lastModified": "2020-11-19T17:50:23.9602077Z",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.CopyAudio"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.CopyVideo"
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "{Basename}_Copy{Extension}",
                            "outputFiles": []
                        }
                    ]
                }
            }
        ]
    }
}
```