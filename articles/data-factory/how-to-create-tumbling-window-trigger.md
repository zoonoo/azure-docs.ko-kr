---
title: "Azure Data Factory에서 연속 창 트리거를 만드는 방법 | Microsoft Docs"
description: "연속 창에 따라 파이프라인을 실행하는 Azure Data Factory에서 트리거를 만드는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>연속 창에 따라 파이프라인을 실행하는 트리거를 만드는 방법
이 문서에서는 연속 창 트리거를 만들고 시작 및 모니터링하는 단계를 제공합니다. 트리거 및 지원되는 유형에 대한 일반적인 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.

연속 창 트리거는 상태를 유지하면서 지정된 시작 시간부터 주기적 시간 간격으로 실행되는 트리거 유형입니다. 연속 창은 고정된 크기의 겹치지 않고 연속적인 일련의 시간 간격입니다. 연속 창 트리거는 파이프라인과 1:1 관계이며 단일 파이프라인만 참조할 수 있습니다.

## <a name="tumbling-window-trigger-type-properties"></a>연속 창 트리거 유형 속성
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

다음 표에는 연속 창 트리거의 일정 및 되풀이에 관련된 주요 요소에 대한 간략한 개요가 나와 있습니다.

| **JSON 이름** | **설명** | **허용되는 값** | **필수** |
|:--- |:--- |:--- |:--- |
| **type** | 트리거 유형입니다. "TumblingWindowTrigger"로 수정되었습니다. | 문자열 | 적용 |
| **runtimeState** | <readOnly> 가능한 값: Started, Stopped, Disabled | 문자열 | 예, readOnly |
| **frequency** |트리거가 되풀이되는 빈도 단위를 나타내는 *frequency* 문자열입니다. 지원되는 값은 “minute” 및 “hour”입니다. 시작 시간의 날짜 부분이 빈도보다 세분화된 경우 창 경계를 계산하는 데 고려됩니다. 예를 들어 빈도가 시간이고 시작 시간이 2016-04-01T10:10:10Z이면 첫 번째 창은 (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z)입니다.  | 문자열입니다. 지원되는 유형은 “minute”, “hour”입니다. | 적용 |
| **interval** |*interval*은 양의 정수이며 트리거가 실행되는 빈도를 결정하는 *frequency*의 간격을 나타냅니다. 예를 들어 *interval*이 3이고 *frequency*가 “hour”이면 트리거가 3시간마다 되풀이됩니다. | 정수  | 적용 |
| **startTime**|*startTime*은 날짜-시간입니다. *startTime*은 첫 번째 발생이며 과거일 수 있습니다. 첫 번째 트리거 간격은 (startTime, startTime + interval)입니다. | Datetime | 적용 |
| **endTime**|*endTime*은 날짜-시간입니다. *endTime*은 마지막 발생이며 과거일 수 있습니다. | Datetime | 적용 |
| **delay** | 창의 데이터 처리 작업이 시작되기 전에 지연을 지정합니다. 예상 실행 시간 + 지연 후에 파이프라인 실행이 시작됩니다. 지연은 새 실행을 트리거하기 전에 트리거가 기한 이후 대기하는 기간을 정의합니다. 창 시작 시간은 변경되지 않습니다. | 시간 간격(예: 00:10:00은 10분 지연을 의미함) |  번호 기본값은 “00:00:00”입니다. |
| **max concurrency** | 준비된 창에 발생하는 동시 트리거 실행 수입니다. 예: 어제 매 시간마다 백필을 시도했다면 24개의 창이 됩니다. 동시성 = 10이면 처음 10개 창(00:00-01:00 - 09:00-10:00)에만 트리거 이벤트가 발생합니다. 처음 10개의 트리거된 파이프라인 실행이 완료되면 다음 10개 창(10:00-11:00 - 19:00-20:00)에 트리거 실행이 발생합니다. 동시성 = 10 예제에서 10개 창이 준비되면 10개의 파이프라인 실행이 발생합니다. 1개 창만 준비되면 1개의 파이프라인 실행만 발생합니다. | 정수  | 예. 가능한 값: 1-50 |
| **retryPolicy: Count** | 파이프라인 실행이 “Failed”로 표시되기 전의 다시 시도 횟수입니다.  | 정수  |  번호 기본값은 재시도 0회입니다. |
| **retryPolicy: intervalInSeconds** | 다시 시도 사이의 지연(초) | 정수  |  번호 기본값은 30초입니다. |

### <a name="using-system-variables-windowstart-and-windowend"></a>시스템 변수: WindowStart 및 WindowEnd 사용

**파이프라인** 정의(즉, 쿼리의 일부)에 연속 창 트리거의 WindowStart 및 WindowEnd를 사용하려는 경우 다음과 같이 **트리거** 정의에서 변수를 파이프라인에 매개 변수로 전달해야 합니다.
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

그런 다음 파이프라인 정의에서 WindowStart 및 WindowEnd 값을 사용하려면 “MyWindowStart” 및 “MyWindowEnd”에 해당하는 매개 변수를 적절하게 사용합니다.

### <a name="notes-on-backfill"></a>백필에 대한 참고 사항
특히 백필 시나리오에서)에 실행할 창이 여러 개인 경우 창 실행 순서는 결정적이며 가장 오래된 간격부터 최신 간격으로 실행됩니다. 지금은 이 동작을 변경할 수 없습니다.

### <a name="updating-an-existing-triggerresource"></a>기존 TriggerResource 업데이트
* 트리거 빈도(또는 창 크기)를 변경하는 경우 이미 처리된 창의 상태는 다시 설정되지 않습니다. 트리거는 마지막으로 실행된 창부터 새 창 크기를 사용하는 창에서 계속 실행됩니다.
* 트리거 종료 시간을 변경(추가 또는 업데이트)하는 경우 이미 처리된 창의 상태는 다시 설정되지 않습니다. 단순히 트리거에 새 종료 시간이 적용됩니다. 종료 시간이 이미 실행된 창보다 이전이면 트리거가 중지됩니다. 이후이면 새 종료 시간이 발생할 때 중지됩니다.

## <a name="sample-using-azure-powershell"></a>Azure PowerShell을 사용하는 샘플
이 섹션에서는 Azure PowerShell을 사용하여 트리거를 만들고 시작하고 모니터링하는 방법을 보여 줍니다.

1. 다음 내용을 포함하는 MyTrigger.json이라는 JSON 파일을 C:\ADFv2QuickStartPSH\ 폴더에 만듭니다.

   > [!IMPORTANT]
   > JSON 파일을 저장하기 전에 **startTime**은 현재 UTC 시간으로, **endTime**은 현재 UTC 시간의 1시간 후로 설정합니다.

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  
2. **Set-AzureRmDataFactoryV2Trigger** cmdlet을 사용하여 트리거를 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. **Start-AzureRmDataFactoryV2Trigger** cmdlet을 사용하여 트리거를 시작합니다.

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. **Get-AzureRmDataFactoryV2Trigger** cmdlet을 사용하여 트리거의 상태가 **시작됨**인지 확인합니다.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  **Get-AzureRmDataFactoryV2TriggerRun** cmdlet을 사용하여 PowerShell을 사용하는 트리거 실행을 가져옵니다. 트리거 실행에 대한 정보를 가져오려면 다음 명령을 주기적으로 실행하여 트리거 정의의 값과 일치하도록 **TriggerRunStartedAfter** 및 **TriggerRunStartedBefore** 값을 업데이트하세요.

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
Azure Portal에서 트리거 실행/파이프라인 실행을 모니터링하려면 [파이프라인 실행 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

## <a name="next-steps"></a>다음 단계
트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#triggers)를 참조하세요.
