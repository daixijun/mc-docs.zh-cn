---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/20/2020
title: Infoworks 集成 - Azure Databricks
description: 了解如何设置 Azure Databricks 以与 Infoworks 集成。
ms.openlocfilehash: c109848200c4b60412653d67ef2a3f0db90839b3
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589926"
---
# <a name="infoworks-integration"></a>Infoworks 集成

> [!IMPORTANT]
>
> 此功能目前以[公共预览版](../../release-notes/release-types.md)提供。

Infoworks DataFoundry 是自动化的企业数据运营和业务流程系统，本地运行于 Azure Databricks，可充分利用 Azure Databricks 来提供简单的数据加入解决方案，而数据加入是运营数据湖的关键第一步。
DataFoundry 不仅自动执行数据引入，还自动执行为建立分析的基础而必须伴随引入的关键功能。
使用 DataFoundry 进行数据加入可自动执行以下操作：

* 数据引入：来自所有企业和外部数据源
* 数据同步：CDC 使数据与源保持同步
* 数据治理：编目、世系、元数据管理、审计和历史记录

以下是结合使用 Infoworks 与 Azure Databricks 的步骤。

## <a name="step-1-generate-a-databricks-personal-access-token"></a><a id="step-1-generate-a-databricks-personal-access-token"> </a><a id="token"> </a>步骤 1：生成 Databricks 个人访问令牌

Infoworks 使用 Azure Databricks 个人访问令牌在 Azure Databricks 中进行身份验证。 若要生成个人访问令牌，请按照[生成个人访问令牌](../../dev-tools/api/latest/authentication.md#token-management)中的说明操作。

## <a name="step-2-set-up-a-cluster-to-support-integration-needs"></a><a id="cluster"> </a><a id="step-2-set-up-a-cluster-to-support-integration-needs"> </a>步骤2：设置群集来支持集成需求

Infoworks 会将数据写入 Azure Data Lake Storage 路径，而 Azure Databricks 集成群集将从该位置读取数据。 因此，集成群集需要能够安全地访问 Azure Data Lake Storage 路径。

### <a name="secure-access-to-an-azure-data-lake-storage-path"></a>安全地访问 Azure Data Lake Storage 路径

若要安全地访问 Azure Data Lake Storage (ADLS) 中的数据，可使用 Azure 存储帐户访问密钥（推荐）或 Azure 服务主体。

#### <a name="use-an-azure-storage-account-access-key"></a>使用 Azure 存储帐户访问密钥

可在配置 Apache Spark 期间在集成群集上配置存储帐户访问密钥。 确保存储帐户可访问用于暂存数据的 ADLS 容器和文件系统，以及要在其中写入 Delta Lake 表的 ADLS 容器和文件系统。 若要将集成群集配置为使用密钥，请按照[使用存储密钥访问 ADLS Gen2](../../data/data-sources/azure/azure-datalake-gen2.md#adls-gen2-access-key) 中的步骤操作。

#### <a name="use-an-azure-service-principal"></a>使用 Azure 服务主体

可在配置 Apache Spark 期间在 Azure Databricks 集成群集上配置服务主体。 确保服务主体可访问用于暂存数据的 ADLS 容器，以及要在其中写入 Delta 表的 ADLS 容器。 若要将集成群集配置为使用服务主体，请按照[使用服务主体访问 ADLS Gen2](../../data/data-sources/azure/azure-datalake-gen2.md#adls-gen2-oauth-2) 或[使用服务主体访问 ADLS Gen1](../../data/data-sources/azure/azure-datalake.md#adls-gen1-oauth-2) 中的步骤操作。

###  <a name="specify-the-cluster-configuration"></a>指定群集配置

1. 在“群集模式”下拉列表中，选择“标准” 。
2. 在“Databricks Runtime 版本”下拉列表中，选择一个 Databricks 运行时版本。
3. 将以下属性添加到 [Spark 配置](../../clusters/configure.md#spark-config)来打开[自动优化](../../delta/optimizations/auto-optimize.md)：

   ```ini
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. 根据集成和缩放需求配置群集。

有关群集配置的详细信息，请参阅[配置群集](../../clusters/configure.md)。

有关获取 JDBC URL 和 HTTP 路径的步骤，请参阅[服务器主机名、端口、HTTP 路径和 JDBC URL](../bi/jdbc-odbc-bi.md#jdbc-odbc-params)。

## <a name="step-3-obtain-jdbc-and-odbc-connection-details-to-connect-to-a-cluster"></a><a id="connection"> </a><a id="step-3-obtain-jdbc-and-odbc-connection-details-to-connect-to-a-cluster"> </a>步骤 3：获取 JDBC 和 ODBC 连接详细信息以连接到群集

若要将 Azure Databricks 群集连接到 Infoworks，你需要以下 JDBC/ODBC 连接属性：

* JDBC URL
* HTTP 路径

## <a name="step-4-get-infoworks-for-azure-databricks"></a>步骤 4：获取 Azure Databricks 的 Infoworks

转到 [Infoworks](https://www.infoworks.io/datafoundry-for-databricks/?utm_source=Databricks&utm_medium=website&utm_campaign=Databricks%20Ingestion%20Network%20Launch&utm_term=ingestion%2C%20onboarding)，了解更多信息并获取演示。