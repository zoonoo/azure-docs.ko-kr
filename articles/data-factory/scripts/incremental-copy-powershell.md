---
title: 'PowerShell 스크립트: Azure Data Factory를 사용하여 데이터 증분 로드 | Microsoft Docs'
description: 이 PowerShell 스크립트에서는 Azure Data Factory를 사용하여 Azure SQL Database에서 Azure Blob Storage로 데이터를 증분 방식으로 복사하는 방법을 보여줍니다.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 5ae6d6ed06aa5734dc601e6e72ba55ec8ddf7bcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160634"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell 스크립트: Azure Data Factory를 사용하여 증분 방식으로 데이터 로드
이 샘플 PowerShell 스크립트는 원본에서 싱크로 데이터를 초기 전체 복사한 후에 원본 데이터 저장소에서 싱크 데이터 저장소로 새롭거나 업데이트된 레코드만을 로드합니다.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

이 샘플을 실행하기 위한 필수 구성 요소는 [자습서: 증분 복사](../tutorial-incremental-copy-powershell.md#prerequisites)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

> [!IMPORTANT]
> 이 스크립트는 하드 드라이브의 c:\ 폴더에 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)를 정의하는 JSON 파일을 만듭니다.

```powershell
# Set variables with your own values
$resourceGroupName = "<azure resource group name>" # group will be created if it does not exist already
$dataFactoryName = "<data factory name>" # must be globally unique
$dataFactoryRegion = "East US" 
$storageAccountName = "<Az.Storage account name>"
$storageAccountKey = "<Az.Storage account key>"
$azureSqlServer = "<azure sql server name>"
$azureSqlDatabase = "<azure sql database name>"
$azureSqlUser = "<azure sql server - user name>"
$azureSqlPassword = "<azure sql server - user password>"
$outputBlobFolderPath = "<azure blob container>/<folder>" # output folder where the records are copied. 

$azureStorageLinkedServiceName = "AzureStorageLinkedService"
$azureSqlDatabaseLinkedServiceName = "AzureSQLDatabaseLinkedService"
$sourceDatasetName = "SourceDataset"
$sinkDatasetName = "SinkDataset"
$sourceSqlTableName = "datasource"
$watermarkDatasetName = "WatermarkDataset"
$pipelineName = "IncrementalCopyPipeline"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryRegion


# Create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Location $dataFactoryRegion -Name $dataFactoryName 

# Create an Az.Storage linked service in the data factory

## JSON definition of the linked service. 
$storageLinkedServiceDefinition = @"
{
    "name": "$azureStorageLinkedServiceName",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$storageLinkedServiceDefinition | Out-File c:\$azureStorageLinkedServiceName.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$azureStorageLinkedServiceName" -File c:\$azureStorageLinkedServiceName.json

## JSON definition of the linked service. 
$azureSQLDatabaseLinkedServiceDefinition = @"
{
    "name": "$azureSqlDatabaseLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server = tcp:$azureSqlServer.database.windows.net,1433;Initial Catalog=$azureSqlDatabase; Persist Security Info=False; User ID=$azureSqlUser; Password=$azureSqlPassword; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$azureSQLDatabaseLinkedServiceDefinition | Out-File c:\$azureSqlDatabaseLinkedServiceName.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$azureSqlDatabaseLinkedServiceName" -File c:\$azureSqlDatabaseLinkedServiceName.json

# Create an Azure SQL dataset in the data factory

## JSON definition of the dataset
$sourceDataset = @"
{
    "name": "$sourceDatasetName",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties": {
            "tableName": "$sourceSqlTableName"
        },
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sourceDataset | Out-File c:\$sourceDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$sourceDatasetName" -File "c:\$sourceDatasetName.json"


# Create an Azure Blob dataset in the data factory

## JSON definition of the dataset
$sinkDataset = @"
{
    "name": "$sinkDatasetName",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "$outputBlobFolderPath",
            "format": {
                "type": "TextFormat"
            }
        },
        "linkedServiceName": {
            "referenceName": "$azureStorageLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sinkDataset | Out-File c:\$sinkDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$sinkDatasetName" -File "c:\$sinkDatasetName.json"

## JSON definition of the dataset
$watermarkDataset = @"
{
    "name": "$watermarkDatasetName",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties": {
            "tableName": "watermarktable"
        },
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$watermarkDataset | Out-File c:\$watermarkDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$watermarkDatasetName" -File "c:\$watermarkDatasetName.json"

# Create a pipeline in the data factory

## JSON definition of the pipeline
$pipelineDefinition = @"
{
    "name": "$pipelineName",
    "properties": {
        "activities": [
            {
                "name": "LookupWaterMarkActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                    "type": "SqlSource",
                    "sqlReaderQuery": "select * from watermarktable"
                    },

                    "dataset": {
                    "referenceName": "$watermarkDatasetName",
                    "type": "DatasetReference"
                    }
                }
            },
            {
                "name": "LookupMaxValuefromSourceActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "SELECT MAX(LastModifytime) as NewWatermarkvalue FROM dbo.datasource"
                    },

                    "dataset": {
                    "referenceName": "$sourceDatasetName",
                    "type": "DatasetReference"
                    }
                }
            },

            {
                "name": "IncrementalCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from dbo.datasource where LastModifytime > '@{activity('LookupWaterMarkActivity').output.WatermarkValue}' and LastModifytime <= '@{activity('LookupMaxValuefromSourceActivity').output.NewWatermarkvalue}'"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "dependsOn": [
                    {
                        "activity": "LookupMaxValuefromSourceActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    },
                    {
                        "activity": "LookupWaterMarkActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],

                "inputs": [
                    {
                        "referenceName": "$sourceDatasetName",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "$sinkDatasetName",
                        "type": "DatasetReference"
                    }
                ]
            },

            {
                "name": "StoredProceduretoWriteWatermarkActivity",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {

                    "storedProcedureName": "sp_write_watermark",
                    "storedProcedureParameters": {
                        "LastModifiedtime": {"value": "@{activity('LookupMaxValuefromSourceActivity').output.NewWatermarkvalue}", "type": "datetime" },
                        "TableName":  { "value":"@{activity('LookupWaterMarkActivity').output.TableName}", "type":"String"}
                    }
                },

                "linkedServiceName": {
                    "referenceName": "$azureSqlDatabaseLinkedServiceName",
                    "type": "LinkedServiceReference"
                },

                "dependsOn": [
                    {
                        "activity": "IncrementalCopyActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ]
            }
        ],

    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipelineDefinition | Out-File c:\$pipelineName.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$pipelineName" -File "c:\$pipelineName.json"


$RunId = Invoke-AzDataFactoryPipeline -PipelineName "$pipelineName" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName

# Check the pipeline run status until it finishes the copy operation
Start-Sleep -Seconds 30
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}


$result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName `
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

# INSERT INTO datasource
# VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

# INSERT INTO datasource
# VALUES (7, 'newdata','9/7/2017 9:01:00 AM')

# Inovke the pipeline and see that only new records are copied to the destination. 


# To remove the data factory from the resource gorup
# Remove-AzDataFactory -Name $dataFactoryName -ResourceGroupName $resourceGroupName
# 
# To remove the whole resource group
# Remove-AzResourceGroup  -Name $resourceGroupName
```

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트를 실행한 후에는 다음 명령을 사용하여 리소스 그룹 및 해당 그룹에 연결된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
리소스 그룹에서 Data Factory를 제거하려면 다음 명령을 실행합니다. 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | 데이터 팩터리를 만듭니다. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | 데이터 팩터리에서 연결된 서비스를 만듭니다. 연결된 서비스는 데이터 저장소 또는 계산을 데이터 팩터리에 연결합니다. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | 데이터 팩터리에서 데이터 세트를 만듭니다. 데이터 세트는 파이프라인의 작업에 대한 입/출력을 나타냅니다. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | 데이터 팩터리에서 파이프라인을 만듭니다. 파이프라인에는 특정 작업을 수행하는 하나 이상의 작업이 있습니다. 이 파이프라인에서 복사 작업은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | 파이프라인에 대한 실행을 만듭니다. 즉, 파이프라인을 실행합니다. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | 파이프라인의 작업 실행 방법에 대한 세부 정보를 가져옵니다. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Data Factory PowerShell 스크립트 샘플은 [Azure Data Factory PowerShell 스크립트](../samples-powershell.md)에 있습니다.
