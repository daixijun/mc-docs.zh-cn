---
title: 使用 PowerShell 复制云端数据
description: 此 PowerShell 脚本将数据从 Azure Blob 存储中的一个位置复制到同一 Blob 存储中的另一个位置。
services: data-factory
ms.author: jingwang
author: WenJason
manager: digimobile
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
origin.date: 03/12/2020
ms.date: 04/20/2020
ms.openlocfilehash: 078f9db0bb17ac1bf7a16a5230fb0e4164e0f82b
ms.sourcegitcommit: c89f1adcf403f5845e785064350136698eed15b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680423"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>使用 PowerShell 创建用于复制云端数据的数据工厂管道

本 PowerShell 脚本示例在 Azure 数据工厂中创建管道，该管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>先决条件
* **Azure 存储帐户**。 可以将 blob 存储同时用作 **源** 和 **接收器** 数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-account-create.md)创建一个。 
* 在 Blob 存储中创建一个 **blob 容器**，在该容器中创建一个输入 **文件夹**，并向该文件夹上传一些文件。 可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)等工具连接到 Azure Blob 存储、创建 Blob 容器、上传输入文件，以及验证输出文件。

## <a name="sample-script"></a>示例脚本

> [!IMPORTANT]
> 此脚本在硬盘驱动器上的 c:\ 文件夹中创建 JSON 文件，用于定义数据工厂实体（链接服务、数据集和管道）。

```powershell
# Set variables with your own values
$resourceGroupName = "<Azure resource group name>"
$dataFactoryName = "<Data factory name>" # must be globally unquie
$dataFactoryRegion = "China East 2" 
$storageAccountName = "<Az.Storage account name>"
$storageAccountKey = "<Az.Storage account key>"
$sourceBlobPath = "<Azure blob container name>/<Azure blob input folder name>" # example: adftutorial/input
$sinkBlobPath = "<Azure blob container name>/<Azure blob output folder name>" # example: adftutorial/output
$pipelineName = "CopyPipeline"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryRegion

# Create a data factory
$df = Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $dataFactoryRegion -Name $dataFactoryName 

# Create an Az.Storage linked service in the data factory

## JSON definition of the linked service. 
$storageLinkedServiceDefinition = @"
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey;EndpointSuffix=core.chinacloudapi.cn",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryV2LinkedService command. 
$storageLinkedServiceDefinition | Out-File ./StorageLinkedService.json

## Creates a linked service in the data factory
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ./StorageLinkedService.json

# Create an Azure Blob dataset in the data factory

## JSON definition of the dataset
$datasetDefiniton = @"
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryV2Dataset command. 
$datasetDefiniton | Out-File ./BlobDataset.json

## Create a dataset in the data factory
Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -File "./BlobDataset.json"

# Create a pipeline in the data factory

## JSON definition of the pipeline
$pipelineDefinition = @"
{
    "name": "$pipelineName",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                    "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryV2Pipeline command. 
$pipelineDefinition | Out-File ./CopyPipeline.json

## Create a pipeline in the data factory
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "./CopyPipeline.json"

# Create a pipeline run 

## JSON definition for pipeline parameters
$pipelineParameters = @"
{
    "inputPath": "$sourceBlobPath",
    "outputPath": "$sinkBlobPath"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Invoke-AzDataFactoryV2Pipeline command. 
$pipelineParameters | Out-File ./PipelineParameters.json

# Create a pipeline run by using parameters
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName -ParameterFile ./PipelineParameters.json

# Check the pipeline run status until it finishes the copy operation
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline '$pipelineName' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}

# Get the activity run details 
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName `
        -PipelineRunId $runId `
        -RunStartedAfter (Get-Date).AddMinutes(-10) `
        -RunStartedBefore (Get-Date).AddMinutes(10) `
        -ErrorAction Stop

    $result

    if ($result.Status -eq "Succeeded") {`
        $result.Output -join "`r`n"`
    }`
    else {`
        $result.Error -join "`r`n"`
    }

# To remove the data factory from the resource gorup
# Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
# 
# To remove the whole resource group
# Remove-AzResourceGroup  -Name $resourceGroupName
```


## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可以使用以下命令删除资源组以及与其关联的所有资源：

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
若要从资源组中删除数据工厂，请运行以下命令：

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [Set-AzDataFactoryV2](https://docs.microsoft.com/powershell/module/az.datafactory/set-Azdatafactoryv2) | 创建数据工厂。 |
| [Set-AzDataFactoryV2LinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | 在数据工厂中创建链接服务。 链接服务可将数据存储或计算链接到数据工厂。 |
| [Set-AzDataFactoryV2Dataset](https://docs.microsoft.com/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | 在数据工厂中创建数据集。 数据集表示管道中活动的输入/输出。 | 
| [Set-AzDataFactoryV2Pipeline](https://docs.microsoft.com/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | 在数据工厂中创建管道。 一个管道包含一个或多个执行某项操作的活动。 在此管道中，复制活动在 Azure Blob 存储中将数据从一个位置复制到另一个位置。 |
| [Invoke-AzDataFactoryV2Pipeline](https://docs.microsoft.com/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | 为管道创建运行。 换而言之，就是运行管道。 |
| [Get-AzDataFactoryV2ActivityRun](https://docs.microsoft.com/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | 获取管道中活动的运行（活动运行）的相关详细信息。 
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 数据工厂 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 数据工厂 PowerShell 脚本示例。
