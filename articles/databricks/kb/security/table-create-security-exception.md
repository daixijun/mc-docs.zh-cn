---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/23/2020
title: 创建表失败，出现安全异常 - Azure Databricks
description: 了解当创建表失败并出现安全异常时要执行的操作。
ms.openlocfilehash: 0595e98db7e0b6b988240e4b0c04094d13591bec
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589503"
---
# <a name="table-creation-fails-with-security-exception"></a>表创建失败，出现安全异常

## <a name="problem"></a>问题

尝试使用已启用“[表 ACL](/databricks/administration-guide/access-control/table-acls/object-permissions)”的群集创建一个表，但发生以下错误：

```console
Error in SQL statement: SecurityException: User does not have permission SELECT on any file.
```

## <a name="cause"></a>原因

如果你不是管理员，并且你没有足够的权限来创建表，则已启用“表 ACL”的群集会出现此错误。

例如，在笔记本中尝试使用位于 Azure Blob 存储上的 Parquet 数据源创建表：

```sql
CREATE TABLE mytable
  USING PARQUET
  OPTIONS (PATH='wasbs://my-container@my-storage-account.blob.core.windows.net/my-table')
```

## <a name="solution"></a>解决方案

应使用以下选项之一，要求管理员[授予你对 blob 存储文件系统的访问权限](/databricks/administration-guide/access-control/table-acls/object-permissions)。 如果管理员无法授予你对数据对象的访问权限，那么你必须要求管理员为你创建表。

* 如果你要使用 `CTAS (CREATE TABLE AS SELECT)` 语句来创建表，则管理员应该向你授予对文件系统的 `SELECT` 权限：

  ```sql
  GRANT SELECT ON ANY FILE TO `user1`
  ```

  示例 CTAS 语句：

  ```sql
  CREATE TABLE mytable
        AS SELECT * FROM parquet.`wasbs://my-container@my-storage-account.blob.core.windows.net/my-table`
  ```

* 如果你要使用 `CTOP (CREATE TABLE OPTIONS PATH)` 语句来创建表，则管理员必须通过授予 `MODIFY` 和 `SELECT` 来提升你的权限。

  ```sql
  GRANT SELECT, MODIFY ON ANY FILE TO `user1`
  ```

  示例 `CTOP` 语句：

  ```sql
  CREATE TABLE mytable
     USING PARQUET
     OPTIONS (PATH='wasbs://my-container@my-storage-account.blob.core.windows.net/my-table')
  ```

> [!IMPORTANT]
>
> 请务必了解在文件系统上授予 `ANY FILE` 权限的安全隐患。 只应向特权用户授予 `ANY FILE`。 在群集上具有较低权限的用户永不应通过引用实际存储位置来访问数据。 相反，他们应从特权用户创建的表中访问数据，从而确保强制实施“表 ACL”。
>
> 此外，如果 Azure Databricks 根目录和数据 bucket 中的文件可供群集访问，并且用户具有 `MODIFY` 权限，则管理员应锁定 DBFS 根目录。

授予上述数据访问权限不会取代任何基础用户权限或 Blob 存储容器访问控制。 例如，如果执行了 `GRANT SELECT, MODIFY ON ANY FILE TO `user1`` 之类的 grant 语句，但附加到群集的用户权限显式拒绝对目标容器进行读取，则 `GRANT` 语句不会使容器或容器中的对象突然可读取。