---
title: KeyVaultCertificateSelector UI 元素
description: 介绍 Azure 门户的 Microsoft.KeyVault.KeyVaultCertificateSelector UI 元素。
ms.topic: conceptual
origin.date: 10/27/2020
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: yes
ms.testdate: 11/16/2020
ms.author: v-yeche
ms.openlocfilehash: ea83abd57ba918c404f85edd7ec861850f681a39
ms.sourcegitcommit: b072689d006cbf9795612acf68e2c4fee0eccfbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849525"
---
<!--Verified successfully-->
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Microsoft.KeyVault.KeyVaultCertificateSelector UI 元素

用于选择密钥保管库证书的控件。

## <a name="ui-sample"></a>UI 示例

向用户提供用于选择可用证书的选项。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector":::

选择“选择证书”后，用户可以指定一个密钥保管库以及该密钥保管库中的证书。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector 选择证书":::

此控件将更新，以显示选定的密钥保管库和证书名称。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft.KeyVault.KeyVaultCertificateSelector 显示选定的证书":::

## <a name="schema"></a>架构

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.cn/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。

<!-- Update_Description: new article about microsoft keyvault keyvaultcertificateselector -->
<!--NEW.date: 11/23/2020-->