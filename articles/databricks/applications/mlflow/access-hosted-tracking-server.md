---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/29/2020
title: 从 Azure Databricks 外部访问 MLflow 跟踪服务器 - Azure Databricks
description: 了解如何从 Azure Databricks 外部访问 MLflow 跟踪服务器。
ms.openlocfilehash: 694ef3bf56d152e675c0a548c711a55db73f6031
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589482"
---
# <a name="access-the-mlflow-tracking-server-from-outside-azure-databricks"></a>从 Azure Databricks 外部访问 MLflow 跟踪服务器

你可能希望从自己的应用程序或从 MLflow CLI 登录到 MLflow 跟踪服务器。

本文介绍了所需的配置步骤。 首先安装 MLflow 并配置凭据（步骤 0）。 然后，可以配置应用程序（步骤 1a），也可以配置 MLflow CLI（步骤 1b）。

有关如何启动并登录到开放源代码跟踪服务器的信息，请参阅[开放源代码文档](https://mlflow.org/docs/latest/quickstart.html#logging-to-a-remote-tracking-server)。

## <a name="step-0-configure-your-environment"></a>步骤 0：配置环境

如果你没有 Azure Databricks 工作区，则可以[免费试用 Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。

配置环境以访问 Azure Databricks 托管 MLflow 跟踪服务器：

1. 使用 `pip install mlflow` 安装 MLflow。
2. 配置身份验证。 执行以下操作之一：
   * 使用 `databricks configure --token` 生成 [REST API 令牌](../../dev-tools/api/latest/authentication.md#token-management) 并创建凭据文件。
   * 通过环境变量指定凭据：

     ```bash
     # Configure MLflow to communicate with a Databricks-hosted tracking server
     export MLFLOW_TRACKING_URI=databricks
     # Specify the workspace hostname and token
     export DATABRICKS_HOST="..."
     export DATABRICKS_TOKEN="..."
     ```

## <a name="step-1a-configure-mlflow-applications"></a>步骤 1a：配置 MLflow 应用程序

通过[将跟踪 URI 设置](https://mlflow.org/docs/latest/tracking.html#logging-to-a-tracking-server)为 `databricks` 或 `databricks://<profileName>`（如果在创建凭据文件时通过 `--profile` 指定了配置文件名称），将 MLflow 应用程序配置为登录到 Azure Databricks。 例如，可以通过将 `MLFLOW_TRACKING_URI` 环境变量设置为“databricks”来实现此目的。

## <a name="step-1b-configure-the-mlflow-cli"></a><a id="configure-cli"> </a><a id="step-1b-configure-the-mlflow-cli"> </a>步骤 1b：配置 MLflow CLI

配置 MLflow CLI 以使用 `MLFLOW_TRACKING_URI` 环境变量与 Azure Databricks 跟踪服务器进行通信。 例如，若要结合使用 CLI 和跟踪 URI `databricks` 来创建试验，请运行：

```bash
# Replace <your-username> with your Databricks username
export MLFLOW_TRACKING_URI=databricks
mlflow experiments create -n /Users/<your-username>/my-experiment
```