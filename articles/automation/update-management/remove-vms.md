---
title: 从 Azure 自动化更新管理中删除 VM
description: 本文介绍如何删除使用“更新管理”管理的计算机。
services: automation
ms.topic: conceptual
origin.date: 01/05/2021
ms.date: 01/18/2021
ms.custom: mvc
ms.openlocfilehash: 2b1acae915c08450a55827f39cf953e77c3620f0
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231084"
---
# <a name="remove-vms-from-update-management"></a>从“更新管理”中删除 VM

完成对环境中 VM 的更新管理后，可以停止通过[更新管理](overview.md)功能管理 VM。 若要停止管理 VM，需在关联到自动化帐户的 Log Analytics 工作区中编辑已保存的搜索查询 `MicrosoftDefaultComputerGroup`。

## <a name="sign-into-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.cn)。

## <a name="to-remove-your-vms"></a>删除 VM

1. 使用以下命令识别希望从管理中删除的计算机的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

2. 在 Azure 门户中，导航到 Log Analytics 工作区。 从列表中选择你的工作区。

3. 在 Log Analytics 工作区中，选择“高级设置”，然后从左侧菜单中选择“计算机组”。

4. 从右侧窗格的“计算机组”中，选择“已保存组”。

5. 在表中，对于已保存的搜索查询“Updates:MicrosoftDefaultComputerGroup”，单击“查看成员”图标以运行并查看其成员。

6. 在查询编辑器中，查看该查询并找到 VM 的 UUID。 删除 VM 的 UUID，并对要删除的任何其他 VM 重复这些步骤。

7. 完成编辑操作后，通过从顶部栏中选择“保存”来保存搜索。 出现提示时，请指定以下项：

    * **名称**：MicrosoftDefaultComputerGroup
    * **另存为**：函数
    * **别名**：Updates__MicrosoftDefaultComputerGroup
    * **类别**：更新

>[!NOTE]
>取消注册后，系统仍会显示这些计算机，因为我们会报告在过去 24 小时内评估的所有计算机。 删除计算机后，需要等待 24 小时，系统才不会再次列出这些计算机。

## <a name="next-steps"></a>后续步骤

若要重新启用对虚拟机的管理，请参阅[从 Azure VM 启用更新管理](enable-from-vm.md)。
