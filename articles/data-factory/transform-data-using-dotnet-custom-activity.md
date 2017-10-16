---
title: "Azure Data Factory 파이프라인에서 사용자 지정 작업 사용"
description: "사용자 지정 작업을 만들고 Azure Data Factory 파이프라인에서 사용하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shengc
ms.openlocfilehash: 24f15168fd716cf317087b8a2ad19b66574ce569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Azure Data Factory 파이프라인에서 사용자 지정 작업 사용
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-use-custom-activities.md)
> * [버전 2 - 미리 보기](transform-data-using-dotnet-custom-activity.md)

Azure Data Factory 파이프라인에서 사용할 수 있는 두 가지 작업 유형이 있습니다.

- [데이터 이동 작업](copy-activity-overview.md)은 [지원되는 원본 및 싱크 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 간에 데이터를 이동하는 작업입니다.
- [데이터 변환 작업](transform-data.md)은 Azure HDInsight, Azure Batch, Azure Machine Learning과 같은 Compute Services를 사용하여 데이터를 변환하는 작업입니다. 

Data Factory에서 지원하지 않는 데이터 저장소 간에 데이터를 이동하거나, Data Factory에서 지원하지 않는 방식으로 데이터를 변환/처리하려면 고유의 데이터 이동 또는 변환 논리가 포함된 **사용자 지정 작업**을 만들어서 파이프라인에 해당 작업을 사용하면 됩니다. 사용자 지정 작업은 사용자 지정된 코드 논리를 가상 컴퓨터의 **Azure Batch** 풀에서 실행합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 사용자 지정 작업](v1/data-factory-use-custom-activities.md)을 참조하세요.
 

Azure 배치 서비스가 처음이라면 다음 항목을 참조하십시오.

* [Azure 배치 기본 사항](../batch/batch-technical-overview.md) 입니다.
* [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) cmdlet을 통해 Azure Portal을 사용하여 Azure Batch 계정을 만들기 위해 Azure Batch 계정 또는 [Azure Portal](../batch/batch-account-create-portal.md)을 만듭니다. 이 cmdlet 사용에 관한 자세한 지침은 [PowerShell을 사용하여 Azure Batch 계정 관리](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) 항목을 참조하십시오.
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) cmdlet을 사용하여 Azure 배치 풀을 만듭니다.

## <a name="azure-batch-linked-service"></a>Azure Batch 연결된 서비스 
다음 JSON은 샘플 Azure Batch 줄 서비스를 정의합니다. 자세한 내용은 [Azure Data Factory에서 지원하는 계산 환경](compute-linked-services.md)을 참조하세요.

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Azure Batch 연결된 서비스에 대한 자세한 내용은 [연결된 Compute Services](compute-linked-services.md) 문서를 참조하세요. 

## <a name="custom-activity"></a>사용자 지정 작업

다음 JSON 코드 조각은 간단한 사용자 지정 작업이 포함된 파이프라인을 정의합니다. 작업 정의에는 Azure Batch 연결된 서비스에 대한 참조가 있습니다. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

이 샘플에서 helloworld.exe는 resourceLinkedService에 사용되는 Azure Storage 계정의 customactv2/helloworld 폴더에 저장된 사용자 지정 응용 프로그램입니다. 사용자 지정 작업은 Azure Batch에서 실행될 이 사용자 지정 응용 프로그램을 제출합니다. Azure Batch 풀 노드의 대상 운영 체제에서 실행될 수 있는 기본 응용 프로그램에 대한 명령을 바꿀 수 있습니다. 

다음 표에는 이 작업과 관련된 속성 이름과 설명이 나와 있습니다. 

| 속성              | 설명                              | 필수 |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | 파이프라인의 작업 이름입니다.     | 예      |
| 설명           | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.  | 아니요       |
| type                  | 사용자 지정 작업의 경우 작업 유형은 **사용자 지정**입니다. | 예      |
| linkedServiceName     | Azure Batch에 연결된 서비스입니다. 이 연결된 서비스에 대한 자세한 내용은 [연결된 Compute Services](compute-linked-services.md) 문서를 참조하세요.  | 예      |
| command               | 실행할 사용자 지정 응용 프로그램의 명령입니다. Azure Batch 풀 노드에 사용할 수 있는 응용 프로그램이 이미 있으면 resourceLinkedService 및 folderPath를 건너뛸 수 있습니다. 예를 들어 명령을 기본적으로 Windows Batch 풀 노드에 의해 지원되는 `cmd /c dir`로 지정할 수 있습니다. | 예      |
| resourceLinkedService | 사용자 지정 응용 프로그램이 저장된 저장소 계정에 대한 Azure Storage 연결된 서비스입니다. | 아니요       |
| folderPath            | 사용자 지정 응용 프로그램 및 모든 해당 종속성 폴더에 대한 경로입니다. | 아니요       |
| referenceObjects      | 기존 연결된 서비스 및 데이터 집합의 배열입니다. 사용자 지정 코드가 Data Factory의 리소스를 참조할 수 있도록 참조된 연결된 서비스 및 데이터 집합은 JSON 형식으로 사용자 지정 응용 프로그램에 전달됩니다. | 아니요       |
| extendedProperties    | 사용자 지정 코드가 추가 속성을 참조할 수 있도록 사용자 정의 속성은 JSON 형식으로 사용자 지정 응용 프로그램에 전달될 수 있습니다. | 아니요       |

## <a name="passing-objects-and-properties"></a>개체 및 속성 전달

이 샘플에서는 referenceObjects 및 extendedProperties를 사용하여 Data Factory 개체 및 사용자 정의 속성을 사용자 지정 응용 프로그램에 전달하는 방법을 보여줍니다. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            }           
        }
      }
    }]
  }
}
```

작업이 실행될 때 referenceObjects 및 extendedProperties는 SampleApp.exe와 동일한 실행 폴더에 배포되는 다음 파일에 저장됩니다. 

- activity.json

  extendedProperties 및 사용자 지정 활동의 속성을 저장합니다. 

- linkedServices.json

  referenceObjects 속성에 정의된 연결된 서비스의 배열을 저장합니다. 

- datasets.json

  referenceObjects 속성에 정의된 데이터 집합의 배열을 저장합니다. 

다음 샘플 코드는 SampleApp.exe가 JSON 파일에서 필요한 정보에 액세스할 수 있는 방법을 보여줍니다. 

```C#
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

####<a name="retrieve-execution-outputs"></a>실행 출력 검색

다음 PowerShell 명령을 사용하여 샘플 파이프라인의 파이프라인 실행을 시작하고 실행 결과를 모니터링할 수 있습니다. 

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -dataFactoryName "factoryName" -PipelineName "pipelineName" 
$result = Get-AzureRmDataFactoryV2ActivityRun -dataFactoryName "factoryName" -PipelineRunId $runId -RunStartedAfter "2017-09-06" -RunStartedBefore "2017-12-31"
$result.output -join "`r`n" 
$result.Error -join "`r`n" 
```

사용자 지정 응용 프로그램의 **stdout** 및 **stderr**은 작업의 GUID를 사용하여 Azure Batch 연결된 서비스를 만들 때 정의한 Azure Storage 연결된 서비스의 **adfjobs** 컨테이너에 저장됩니다. 다음 코드 조각에 나와 있는 것처럼 작업 실행 출력에서 자세한 경로를 얻을 수 있습니다. 

```shell
"exitcode": 0
"outputs": [
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stdout.txt",
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stderr.txt"
]
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

> [!IMPORTANT]
> - activity.json, linkedServices.json 및 datasets.json은 Bath 작업의 런타임 폴더에 저장됩니다. 예를 들어 activity.json, linkedServices.json 및 datasets.json은 https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/runtime/ 경로에 저장됩니다. 필요한 경우 개별적으로 정리해야 합니다. 
> - 연결된 서비스가 자체 호스팅 통합 런타임을 사용하는 경우 키 또는 암호와 같은 중요한 정보를 자체 호스팅 통합 런타임으로 암호화하여 사용자 정의 개인 네트워크 환경에 자격 증명을 유지하도록 합니다. 이러한 방식으로 사용자 지정 응용 프로그램 코드에서 참조하는 경우 일부 중요한 필드가 누락될 수 있습니다. 필요한 경우 연결된 서비스 참조를 사용하는 대신 extendedProperties에서 SecureString을 사용합니다. 



## <a name="auto-scaling-of-azure-batch"></a>Azure Batch의 자동 확장
**자동 크기 조정** 기능으로 Azure 배치 풀을 만들 수 있습니다. 예를 들어 보류 중인 작업의 수에 따라 전용 VM 0개 및 자동 크기 조정 수식을 사용하여 Azure 배치 풀을 만들 수 있습니다. 

여기에 나오는 샘플 수식은 다음과 같은 동작을 구현합니다. 풀이 처음 만들어질 때는 VM 1개로 시작합니다. $PendingTasks 메트릭은 실행되거나 큐에 대기 중인 활성 상태의 작업 수를 정의합니다.  이 수식은 지난 180초 동안에서 보류 중인 작업의 평균 수를 찾은 후 그에 따라 TargetDedicated를 설정합니다. 또한 TargetDedicated가 25개의 VM을 초과하지 않도록 합니다. 따라서 새 작업이 제출되면 풀이 자동으로 커지고, 작업이 완료되면 VM은 하나씩 사용 가능한 상태로 해제된 후 자동 크기 조정에 따라 해당 VM이 축소됩니다. startingNumberOfVMs 및 maxNumberofVMs은 요구에 맞게 조정될 수 있습니다.

자동 크기 조정 수식:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

자세한 내용은 [Azure 배치 풀에서 자동으로 계산 노드 크기 조정](../batch/batch-automatic-scaling.md) 을 참조하세요.

풀에 기본 [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)이 사용되는 경우, 배치 서비스가 사용자 지정 작업을 실행하기 전에 VM을 준비하는 데 15~30분이 소요될 수 있습니다.  풀에 다른 autoScaleEvaluationInterval이 사용되는 경우, 배치 서비스는 autoScaleEvaluationInterval +10분이 소요될 수 있습니다.


## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요. 

* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [Machine Learning Batch 실행 작업](transform-data-using-machine-learning.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
