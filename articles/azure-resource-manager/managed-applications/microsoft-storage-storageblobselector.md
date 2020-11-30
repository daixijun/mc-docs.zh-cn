---
title: StorageBlobSelector UI 元素
description: 介绍 Azure 门户的 Microsoft.Storage.StorageBlobSelector UI 元素。
ms.topic: conceptual
origin.date: 10/27/2020
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: yes
ms.testdate: 11/16/2020
ms.author: v-yeche
ms.openlocfilehash: 8091cd999e00fc8fb60a09e67cf93e1e256fef89
ms.sourcegitcommit: b072689d006cbf9795612acf68e2c4fee0eccfbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849494"
---
<!--Verified successfully-->
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Microsoft.Storage.StorageBlobSelector UI 元素

用于从 Azure 存储帐户选择 blob 的控件。

## <a name="ui-sample"></a>UI 示例

向用户呈现用于浏览可用存储 blob 的选项。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft.Storage.StorageBlobSelector - 浏览":::

选择“浏览”后，用户可以选择存储帐户。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft.Storage.StorageBlobSelector - 选择存储":::

用户会看到存储帐户中的容器，并且可以选择一个。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft.Storage.StorageBlobSelector - 选择容器":::

用户可以从容器中选择一个文件。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft.Storage.StorageBlobSelector - 文件":::

该控件会更新，以显示所选文件名。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft.Storage.StorageBlobSelector - 显示选定文件":::

## <a name="schema"></a>架构

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.chinacloudapi.cn/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>备注

constraints.allowedFileExtensions 属性指定允许的文件类型。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。

<!-- Update_Description: new article about microsoft storage storageblobselector -->
<!--NEW.date: 11/23/2020-->