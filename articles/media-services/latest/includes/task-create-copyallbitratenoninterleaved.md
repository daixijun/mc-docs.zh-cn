---
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 11/18/2020
ms.date: 12/14/2020
ms.author: v-jay
ms.custom: REST
ms.openlocfilehash: 32bb799f0f6cb3d02bb89f269e0043f8e9105140
ms.sourcegitcommit: 8f438bc90075645d175d6a7f43765b20287b503b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97004291"
---
<!--Create a copyAllNonInterleave transform with REST-->

以下 Azure REST 命令使用 `#Microsoft.Media.BuiltInStandardEncoderPreset` 预设中的 `copyAllBitrateNonInterleaved` 创建转换。 将 `subscriptionID`、`resourceGroup` 和 `accountName` 值替换为当前正在使用的值。 通过设置 `transformName` 为转换命名。 

若要查看所有可用的 REST API 转换，请参阅[转换 - 创建或更新](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#definitions)。

```REST
PUT https://management.chinacloudapi.cn/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>正文

```json
{
    "name": "myTransform",
    "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/myTransform",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "description": "Basic Transform using a copyAllBitrateNonInterleaved encoding preset from the library of built-in Standard Encoder presets",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                    "presetName": "CopyAllBitrateNonInterleaved"
                }
            }
        ]
    }
}
```

## <a name="sample-response"></a>示例响应
```json
{
  "name":"myTransform",
    "id":"/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/myTransform","type":"Microsoft.Media/mediaservices/transforms","properties":{
        "created":"2020-11-18T19:19:27.4405209Z",
        "description":"Basic Transform using a copyAllBitrateNonInterleaved encoding preset from the library of built-in Standard Encoder presets","lastModified":"2020-11-18T19:19:27.4405209Z",
        "outputs":[
            {
                "onError":"StopProcessingJob","relativePriority":"Normal","preset":{
                "@odata.type":"#Microsoft.Media.BuiltInStandardEncoderPreset",
                "presetName":"CopyAllBitrateNonInterleaved"
        }
      }
    ]
  }
}
```
