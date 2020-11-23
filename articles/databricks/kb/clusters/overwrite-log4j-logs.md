---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: 如何在 Azure Databricks 群集上覆盖 log4j 配置 - Azure Databricks
description: 了解如何在 Azure Databricks 群集上覆盖 log4j 配置。
ms.openlocfilehash: ab284217dbd5592cfb241ab3625c4cb087c939d8
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589889"
---
# <a name="how-to-overwrite-log4j-configurations-on-azure-databricks-clusters"></a>如何在 Azure Databricks 群集上覆盖 `log4j` 配置

在具有自定义配置的群集上，没有用于覆盖 `log4j` 配置的标准方法。 必须使用 init 脚本覆盖配置文件。

当前配置存储在两个 `log4j.properties` 文件中：

* 在驱动程序上：

  ```bash
  %sh
  cat /home/ubuntu/databricks/spark/dbconf/log4j/driver/log4j.properties
  ```

* 在辅助角色上：

  ```bash
  %sh
  cat /home/ubuntu/databricks/spark/dbconf/log4j/executor/log4j.properties
  ```

若要在驱动程序或辅助角色上设置特定于类的日志记录，请使用以下脚本：

```bash
#!/bin/bash
echo "Executing on Driver: $DB_IS_DRIVER"
if [[ $DB_IS_DRIVER = "TRUE" ]]; then
LOG4J_PATH="/home/ubuntu/databricks/spark/dbconf/log4j/driver/log4j.properties"
else
LOG4J_PATH="/home/ubuntu/databricks/spark/dbconf/log4j/executor/log4j.properties"
fi
echo "Adjusting log4j.properties here: ${LOG4J_PATH}"
echo "log4j.<custom-prop>=<value>" >> ${LOG4J_PATH}
```

将 `<custom-prop>` 替换为属性名称，将 `<value>` 替换为属性值。

将脚本上传到 DBFS，并使用群集配置 UI 选择群集。

还可以采用相同的方式为驱动程序设置 `log4j.properties`。

有关详细信息，请参阅[群集节点初始化脚本](/databricks/clusters/init-scripts)。