---
title: 使用 Azure 导入/导出服务从 Azure Blob 导出数据 | Microsoft Docs
description: 了解如何在 Azure 门户中创建导出作业，以便从 Azure Blob 传输数据。
author: WenJason
services: storage
ms.service: storage
ms.topic: how-to
origin.date: 10/29/2020
ms.date: 11/30/2020
ms.author: v-jay
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93701db8f3577c30e03a41853bb4fcebdfefb81a
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96152998"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>使用 Azure 导入/导出服务从 Azure Blob 存储导出数据

本文分步说明如何使用 Azure 导入/导出服务从 Azure Blob 存储安全地导出大量数据。 该服务要求你将空驱动器寄送到 Azure 数据中心。 该服务将数据从存储帐户导出到驱动器，然后将驱动器寄回。

## <a name="prerequisites"></a>先决条件

在创建导出作业以从 Azure Blob 存储传输数据之前，请仔细查看并完成以下此服务的先决条件列表。
必须具备以下条件：

- 拥有可用于导入/导出服务的有效 Azure 订阅。
- 拥有至少一个 Azure 存储帐户。 请参阅[导入/导出服务支持的存储帐户和存储类型](storage-import-export-requirements.md)的列表。 有关创建新存储帐户的信息，请参阅[如何创建存储帐户](storage-account-create.md)。
- 拥有足够数量的[受支持类型](storage-import-export-requirements.md#supported-disks)的磁盘。

## <a name="step-1-create-an-export-job"></a>步骤 1：创建导出作业

### <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户中执行以下步骤来创建导出作业。

1. 登录到 <https://portal.azure.cn/>。
2. 转到“所有服务”>“存储”>“导入/导出作业”。

    ![转到导入/导出作业](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. 单击“创建导入/导出作业”。

    ![单击导入/导出作业](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. 在“基本信息”中：

    - 选择“从 Azure 导出”。
    - 为导出作业输入一个描述性名称。 使用所选名称来跟踪作业进度。
        - 此名称可以包含大写和小写字母、数字、连字符。
        - 此名称必须以字母开头，并且不得包含空格。
    - 选择一个订阅。
    - 输入或选择一个资源组。

        ![基础知识](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. 在“作业详细信息”中：

    - 选择要导出的数据所在的存储帐户。 使用附近位置的存储帐户。
    - 放置位置会根据选定存储帐户所属的区域自动进行填充。
    - 指定要从存储帐户导出到空驱动器的 blob 数据。
    - 选择“全部导出”以导出存储帐户中的所有 blob 数据。

         ![全部导出](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - 可以指定要导出的容器和 blob。
        - **指定要导出的 blob**：使用“等于”选择器。 指定 blob 的相对路径，以容器名称开头。 使用 *$root* 指定根容器。
        - **指定以某个前缀开头的所有 blob**：使用“开头为”选择器。 指定以正斜杠“/”开头的前缀。 该前缀可以是容器名称的前缀、完整容器名称或者后跟 Blob 名称前缀的完整容器名称。 必须以有效格式提供 blob 路径，以免在处理过程中出现错误，如以下屏幕截图所示。 有关详细信息，请参阅[有效 blob 路径示例](#examples-of-valid-blob-paths)。

           ![导出所选容器和 blob](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - 可以从 blob 列表文件进行导出。

        ![从 blob 列表文件导出](./media/storage-import-export-data-from-blobs/export-from-blob6.png)

   > [!NOTE]
   > 如果在复制数据时，要导出的 blob 正在使用中，则 Azure 导入/导出服务将生成该 blob 的快照并复制快照。

6. 在“回寄信息”中：

    - 从下拉列表中选择承运商。
    - 输入你已在该承运商那里创建的有效承运商帐户编号。 如果没有承运人帐号，请键入 null 或 unknown。 当导出作业完成后，我们使用此帐户寄回驱动器。 
    - 提供完整且有效的联系人姓名、电话、电子邮件、街道地址、城市、邮政编码、省/自治区/直辖市和国家/地区。

        > [!TIP]
        > 请提供组电子邮件，而非为单个用户指定电子邮件地址。 这可确保即使管理员离开也会收到通知。

7. 在“摘要”中：

    - 查看作业详细信息。
    - 记下作业名称以及为将磁盘寄送到 Azure 而提供的 Azure 数据中心寄送地址。

        > [!NOTE]
        > 始终将磁盘发送到 Azure 门户中记录的数据中心。 如果磁盘寄送到错误的数据中心，则不会处理该作业。

    - 单击“确定”以完成导出作业的创建。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在 Azure 门户中按照以下步骤来创建导出作业。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>创建作业

1. 使用 [az extension add](/cli/extension#az_extension_add) 命令添加 [az import-export](/cli/ext/import-export/import-export) 扩展：

    ```azurecli
    az extension add --name import-export
    ```

1. 若要获取可从中接收磁盘的位置列表，请使用 [az import-export location list](/cli/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) 命令：

    ```azurecli
    az import-export location list
    ```

1. 运行以下 [az import-export create](/cli/ext/import-export/import-export#ext_import_export_az_import_export_create) 命令来创建使用现有存储帐户的导出作业：

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "China East" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="XXXX XXXX" city="XXXX XXXX" state-or-province=CA postal-code=XXXXXX \
            country-or-region=China phone=XXXXXXXX \
        --return-address recipient-name="XXXX XXXX" street-address1="XXXX XXXXXX XXXX" \
            city=XXXX country-or-region=China state-or-province=CA postal-code=XXXXXX \
            email=gus@contoso.com phone=XXXXXXXX" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > 请提供组电子邮件，而非为单个用户指定电子邮件地址。 这可确保即使管理员离开也会收到通知。

   此作业会导出存储帐户中的所有 blob。 可以通过将此值替换为“--export”来指定要导出的 Blob：

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   此参数值会导出根容器中名为“logo.bmp”的 blob。

   也可以选择使用前缀来选择容器中的所有 blob。 将此值替换为“--export”：

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   有关详细信息，请参阅[有效 blob 路径示例](#examples-of-valid-blob-paths)。

   > [!NOTE]
   > 如果在复制数据时，要导出的 blob 正在使用中，则 Azure 导入/导出服务将生成该 blob 的快照并复制快照。

1. 使用 [az import-export list](/cli/ext/import-export/import-export#ext_import_export_az_import_export_list) 命令查看资源组 myierg 的所有作业：

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. 若要更新作业或取消作业，请运行 [az import-export update](/cli/ext/import-export/import-export#ext_import_export_az_import_export_update) 命令：

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

使用以下步骤在 Azure PowerShell 中创建导出作业。

[!INCLUDE [azure-powershell-requirements-h3.md](../../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> 尽管 Az.ImportExport PowerShell 模块为预览版，但你需要使用 `Install-Module` cmdlet 单独安装它。 

```azurepowershell
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>创建作业

1. 若要获取可从中接收磁盘的位置的列表，请使用 [Get-AzImportExportLocation](https://docs.microsoft.com/powershell/module/az.importexport/get-azimportexportlocation) cmdlet：

   ```azurepowershell
   Get-AzImportExportLocation
   ```

1. 运行以下 [New-AzImportExport](https://docs.microsoft.com/powershell/module/az.importexport/new-azimportexport) 示例来创建使用现有存储帐户的导出作业：

   ```azurepowershell
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'chinaeast'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = 'XXXX XXXX'
      ShippingInformationCity = 'XXXX XXXX'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = 'XXXXXX'
      ShippingInformationCountryOrRegion = 'China'
      ShippingInformationPhone = '1234567890'
      ReturnAddressRecipientName = 'XXXX XXXX'
      ReturnAddressStreetAddress1 = 'XXXX XXXX'
      ReturnAddressCity = 'XXXX'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'China'
      ReturnAddressPhone = '1234567890'
      ReturnAddressEmail = 'gus@contoso.com'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > 请提供组电子邮件，而非为单个用户指定电子邮件地址。 这可确保即使管理员离开也会收到通知。

   此作业会导出存储帐户中的所有 blob。 可以通过将此值替换为“-ExportBlobListblobPath”来指定要导出的 Blob：

   ```azurepowershell
   -ExportBlobListblobPath $root\logo.bmp
   ```

   此参数值会导出根容器中名为“logo.bmp”的 blob。

   也可以选择使用前缀来选择容器中的所有 blob。 将此值替换为“-ExportBlobListblobPath”：

   ```azurepowershell
   -ExportBlobListblobPath '/myiecontainer'
   ```

   有关详细信息，请参阅[有效 blob 路径示例](#examples-of-valid-blob-paths)。

   > [!NOTE]
   > 如果在复制数据时，要导出的 blob 正在使用中，则 Azure 导入/导出服务将生成该 blob 的快照并复制快照。

1. 使用 [Get-AzImportExport](https://docs.microsoft.com/powershell/module/az.importexport/get-azimportexport) cmdlet 查看资源组 myierg 的所有作业：

   ```azurepowershell
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. 若要更新作业或取消作业，请运行 [Update-AzImportExport](https://docs.microsoft.com/powershell/module/az.importexport/update-azimportexport) cmdlet：

   ```azurepowershell
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---
## <a name="step-2-ship-the-drives"></a>步骤 2：寄送驱动器

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>步骤 3：使用跟踪信息更新作业

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>步骤 4：接收磁盘

当仪表板报告作业已完成时，会将磁盘寄送给你，并且门户上会提供货件的跟踪号。

1. 收到包含导出数据的驱动器后，你需要获取 BitLocker 密钥才能解锁驱动器。 转到 Azure 门户中的导出作业。 单击“导入/导出”选项卡。
2. 从列表中选择并单击导出作业。 转到“加密”，然后复制密钥。

   ![查看导出作业的 BitLocker 密钥](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. 使用 BitLocker 密钥解锁磁盘。

导出已完成。

## <a name="step-5-unlock-the-disks"></a>步骤 5：解锁磁盘

使用以下命令解锁驱动器：

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

下面是示例输入的示例。

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

现在，可删除作业，也可将其保留。 作业将在 90 天后自动删除。

## <a name="examples-of-valid-blob-paths"></a>有效 blob 路径示例

下表显示有效 Blob 路径的示例：

   | 选择器 | Blob 路径 | 说明 |
   | --- | --- | --- |
   | 开头为 |/ |导出存储帐户中的所有 Blob |
   | 开头为 |/$root/ |导出根容器中的所有 Blob |
   | 开头为 |/book |导出任何容器中以前缀 book 开头的所有 Blob |
   | 开头为 |/music/ |导出容器 music 中的所有 Blob |
   | 开头为 |/music/love |导出容器 music 中以前缀 love 开头的所有 Blob |
   | 等于 |$root/logo.bmp |导出根容器中的 Blob logo.bmp |
   | 等于 |videos/story.mp4 |导出容器 videos 中的 Blob story.mp4 |

## <a name="next-steps"></a>后续步骤

- [查看作业和驱动器状态](storage-import-export-view-drive-status.md)
- [查看导入/导出要求](storage-import-export-requirements.md)
