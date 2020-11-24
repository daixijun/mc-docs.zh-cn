---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
ms.date: 06/09/2020
title: 将群集配置为使用自定义 NTP 服务器 - Azure Databricks
description: 将群集配置为使用自定义 NTP 服务器（公用或专用），而不使用默认服务器
category: General Guidance or Advisory
author: xwang30
db-author: xin.wang@databricks.com
ms.openlocfilehash: 2281181c8df6974b9eafb878f56735413200838f
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589718"
---
# <a name="configure-a-cluster-to-use-a-custom-ntp-server"></a>将群集配置为使用自定义 NTP 服务器

默认情况下，Azure Databricks 群集使用公用 NTP 服务器。 这对于大多数用例来说已经足够，但你可以将群集配置为使用自定义 NTP 服务器。 这不一定是公共 NTP 服务器。 它可以是由你控制的专用 NTP 服务器。 一个常见用例是最大限度地降低来自群集的 Internet 流量。

## <a name="update-the-ntp-configuration-on-a-cluster"></a>更新群集上的 NTP 配置

1. 创建一个包含以下信息的 `ntp.conf` 文件：

   ```
   # NTP configuration
   server <ntp-server-hostname> iburst
   ```

   其中 `<ntp-server-hostname>` 是 NTP 服务器主机名或 NTP 服务器 IP 地址。

   如果有多个 NTP 服务器要列出，请将它们全部添加到该文件。 每个服务器都应列出到其自己的行上。

2. 将 `ntp.conf` 文件上传到群集上的 `/dbfs/databricks/init_scripts/`。
3. 在群集上创建脚本 `ntp.sh`：

   ```python
   dbutils.fs.put("/databricks/init_scripts/ntp.sh","""
   #!/bin/bash
   echo "<ntp-server-ip> <ntp-server-hostname>" >> /etc/hosts
   cp /dbfs/databricks/init_scripts/ntp.conf /etc/
   sudo service ntp restart""",True)
   ```

4. 确认该脚本存在：

   ```python
   display(dbutils.fs.ls("dbfs:/databricks/init_scripts/ntp.sh"))
   ```

5. 单击“群集”，单击群集名称，单击“编辑”，单击“高级选项”，单击“Init 脚本”   。
6. 选择“目标”下的“DBFS” 。
7. 输入 `ntp.sh` 的完整路径并单击“添加”。
8. 单击“确认并重启”。 这时会出现一个确认对话框。 请单击“确认”并等待该群集重启。

## <a name="verify-the-cluster-is-using-the-updated-ntp-configuration"></a>验证该群集是否在使用更新后的 NTP 配置

1. 在笔记本中运行以下代码：

   ```bash
   %sh ntpq -p
   ```

2. 输出会显示正在使用的 NTP 服务器。