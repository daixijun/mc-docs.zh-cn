---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 12/22/2020
title: SQL Analytics 的 SQL 参考 - Azure Databricks
description: 了解 Azure Databricks 中支持的 SQL 语言构造。
ms.openlocfilehash: cd147298e709f5df554803e6d27c9e8f9d04450b
ms.sourcegitcommit: bb7497d5a11e8fb506907221ff65a18e6c523372
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98692159"
---
# <a name="sql-reference-for-sql-analytics"></a>SQL Analytics 的 SQL 参考

这是 Azure Databricks SQL Analytics 的 SQL 命令参考。

## <a name="general-reference"></a>常规参考

这篇通用参考介绍了数据类型、函数、标识符、文本和语义：

* [数据类型](sql-ref-datatypes.md)
* [日期/时间模式](sql-ref-datetime-pattern.md)
* [内置函数](sql-ref-functions-builtin.md)
* [标识符](sql-ref-identifiers.md)
* [文字](sql-ref-literals.md)
* [NULL 语义](sql-ref-null-semantics.md)

## <a name="ddl-statements"></a>DDL 语句

使用数据定义语句可以创建或修改数据库中数据库对象的结构：

* [ALTER DATABASE](sql-ref-syntax-ddl-alter-database.md)
* [ALTER TABLE](sql-ref-syntax-ddl-alter-table.md)
* [ALTER VIEW](sql-ref-syntax-ddl-alter-view.md)
* [CREATE DATABASE](sql-ref-syntax-ddl-create-database.md)
* [CREATE TABLE](sql-ref-syntax-ddl-create-table.md)
* [CREATE VIEW](sql-ref-syntax-ddl-create-view.md)
* [DROP DATABASE](sql-ref-syntax-ddl-drop-database.md)
* [DROP TABLE](sql-ref-syntax-ddl-drop-table.md)
* [DROP VIEW](sql-ref-syntax-ddl-drop-view.md)
* [MSCK REPAIR TABLE](sql-ref-syntax-ddl-repair-table.md)
* [TRUNCATE TABLE](sql-ref-syntax-ddl-truncate-table.md)
* [USE DATABASE](sql-ref-syntax-ddl-usedb.md)

## <a name="dml-statements"></a>DML 语句

使用数据操作语句可以添加、更改或删除数据：

* [INSERT INTO](sql-ref-syntax-dml-insert-into.md)
* [INSERT OVERWRITE](sql-ref-syntax-dml-insert-overwrite-table.md)

## <a name="data-retrieval-statements"></a>数据检索语句

使用 ``SELECT`` 语句可以根据指定的子句检索一个或多个表中的行。 [SELECT](sql-ref-syntax-qry-select.md) 中介绍了受支持子句的完整语法和简短说明。 本部分还包含与 ``SELECT`` 相关的 SQL 语句。

通过 SQL Analytics 还可以使用 [EXPLAIN](sql-ref-syntax-qry-explain.md) 语句生成查询的逻辑和物理计划。

* [SELECT](sql-ref-syntax-qry-select.md)
* [EXPLAIN](sql-ref-syntax-qry-explain.md)

## <a name="delta-lake-statements"></a>Delta Lake 语句

使用 Delta Lake SQL 语句可以管理以 Delta Lake 格式存储的表：

* [CACHE（Azure Databricks 上的 Delta Lake）](delta-cache.md)
* [CLONE（Azure Databricks 上的 Delta Lake）](delta-clone.md)
* [CONVERT TO DELTA（Azure Databricks 上的 Delta Lake）](delta-convert-to-delta.md)
* [COPY INTO（Azure Databricks 上的 Delta Lake）](delta-copy-into.md)
* [CREATE BLOOM FILTER INDEX（Azure Databricks 上的 Delta Lake）](delta-create-bloomfilter-index.md)
* [DELETE FROM（Azure Databricks 上的 Delta Lake）](delta-delete-from.md)
* [DESCRIBE HISTORY（Azure Databricks 上的 Delta Lake）](delta-describe-history.md)
* [DROP BLOOM FILTER INDEX（Azure Databricks 上的 Delta Lake）](delta-drop-bloomfilter-index.md)
* [FSCK REPAIR TABLE（Azure Databricks 上的 Delta Lake）](delta-fsck.md)
* [MERGE INTO（Azure Databricks 上的 Delta Lake）](delta-merge-into.md)
* [OPTIMIZE（Azure Databricks 上的 Delta Lake）](delta-optimize.md)
* [UPDATE（Azure Databricks 上的 Delta Lake）](delta-update.md)
* [VACUUM](delta-vacuum.md)

有关使用 Delta Lake 语句的详细信息，请参阅 [Delta Lake 和 Delta Engine 指南](../../delta/index.md)。

## <a name="auxiliary-statements"></a>辅助语句

使用辅助语句可以收集统计信息、管理缓存、浏览元数据、设置配置和管理资源：

* [分析语句](#analyze-statement)
* [描述语句](#describe-statements)
* [显示语句](#show-statements)
* [配置管理](#configuration-management)

### <a name="analyze-statement"></a>分析语句

* [ANALYZE TABLE](sql-ref-syntax-aux-analyze-table.md)

### <a name="describe-statements"></a>描述语句

* [DESCRIBE DATABASE](sql-ref-syntax-aux-describe-database.md)
* [DESCRIBE FUNCTION](sql-ref-syntax-aux-describe-function.md)
* [DESCRIBE QUERY](sql-ref-syntax-aux-describe-query.md)
* [DESCRIBE TABLE](sql-ref-syntax-aux-describe-table.md)

### <a name="show-statements"></a>显示语句

* [SHOW COLUMNS](sql-ref-syntax-aux-show-columns.md)
* [SHOW CREATE TABLE](sql-ref-syntax-aux-show-create-table.md)
* [SHOW DATABASES](sql-ref-syntax-aux-show-databases.md)
* [SHOW FUNCTIONS](sql-ref-syntax-aux-show-functions.md)
* [SHOW PARTITIONS](sql-ref-syntax-aux-show-partitions.md)
* [SHOW TABLE EXTENDED](sql-ref-syntax-aux-show-table.md)
* [SHOW TABLES](sql-ref-syntax-aux-show-tables.md)
* [SHOW TBLPROPERTIES](sql-ref-syntax-aux-show-tblproperties.md)
* [SHOW VIEWS](sql-ref-syntax-aux-show-views.md)

### <a name="configuration-management"></a>配置管理

* [RESET](sql-ref-syntax-aux-conf-mgmt-reset.md)
* [SET](sql-ref-syntax-aux-conf-mgmt-set.md)
* [SET TIME ZONE](sql-ref-syntax-aux-conf-mgmt-set-timezone.md)

## <a name="security-statements"></a>安全语句

使用安全 SQL 语句可以管理对数据的访问：

* [DENY](security-deny.md)
* [GRANT](security-grant.md)
* [MSCK](security-msck.md)
* [REVOKE](security-revoke.md)
* [SHOW GRANT](security-show-grant.md)

有关使用这些语句的详细信息，请参阅[数据对象特权](../../security/access-control/table-acls/object-privileges.md)。