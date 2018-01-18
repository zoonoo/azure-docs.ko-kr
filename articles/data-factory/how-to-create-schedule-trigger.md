---
title: "Azure Data Factory에서 일정 트리거를 만드는 방법 | Microsoft Docs"
description: "일정에 따라 파이프라인을 실행하는 Azure Data Factory에서 트리거를 만드는 방법에 대해 알아봅니다."
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
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>일정에 따라 파이프라인을 실행하는 트리거를 만드는 방법
이 문서에서는 일정 트리거 및 트리거를 만들고 시작 및 모니터링하는 단계에 대한 정보를 제공합니다. 다른 트리거 유형의 경우 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.

### <a name="schedule-trigger-json-definition"></a>일정 트리거 JSON 정의
일정 트리거를 만들 때 이 섹션의 예제와 같이 JSON을 사용하여 일정 계획 및 되풀이를 지정할 수 있습니다.

일정 트리거가 파이프라인 실행을 시작하게 하려면 특정 파이프라인의 파이프라인 참조를 트리거 정의에 포함합니다. 파이프라인 및 트리거는 다 대 다 관계를 가지고 있습니다. 다중 트리거는 단일 파이프라인을 시작할 수 있습니다. 단일 트리거는 여러 파이프라인을 시작할 수 있습니다.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  **매개 변수** 속성은 **파이프라인** 내에서 필수 속성입니다. 파이프라인에서 매개 변수를 사용하지 않는 경우에도 속성이 있어야 하므로 매개 변수에 대한 빈 json을 포함합니다.


### <a name="overview-schedule-trigger-schema"></a>개요: 일정 트리거 스키마
다음 테이블은 트리거의 되풀이 및 일정 계획과 관련된 주요 요소의 공급 개요를 제공합니다.

JSON 속성 |     설명
------------- | -------------
startTime | startTime은 날짜-시간입니다. 간단한 일정의 경우 startTime은 첫 번째 항목입니다. 복잡한 일정의 경우 트리거는 startTime 이후에 시작합니다.
endTime | 트리거에 대한 종료 날짜-시간을 지정합니다. 이 시간 이후 트리거가 실행되지 않습니다. 과거의 endTime을 갖는 것은 유효하지 않습니다. 선택적 속성입니다.
timeZone | 현재는 UTC만 지원됩니다.
되풀이 | recurrence 개체는 트리거에 대한 되풀이 규칙을 지정합니다. recurrence 개체는 frequency, interval, endTime, count 및 schedule 요소를 지원합니다. recurrence가 정의된 경우 frequency는 필수 요소이며 recurrence의 다른 요소는 선택 사항입니다.
frequency | 트리거가 되풀이되는 빈도 단위를 나타냅니다. 지원되는 값은 `minute`, `hour`, `day`, `week`또는 `month`입니다.
interval | interval은 양의 정수입니다. 이는 트리거가 실행하는 빈도를 결정하는 frequency에 대한 간격을 정의합니다. 예를 들어 interval이 3이고 frequency가 "week"인 경우 매 3주마다 트리거가 되풀이됩니다.
schedule | 지정된 빈도를 가진 트리거는 되풀이 일정을 기반으로 되풀이를 변경합니다. schedule에는 분, 시간, 요일, 날짜, 주차를 기반으로 하는 조건이 포함됩니다.


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>개요: 일정 트리거 스키마 기본값, 제한 및 예

JSON 이름 | 값 형식 | Required? | 기본값 | 유효한 값 | 예
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | 문자열 | 적용 | 없음 | ISO-8601 날짜-시간 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
되풀이 | Object | 적용 | 없음 | 되풀이 개체 | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | 아니요 | 1 | 1 ~ 1000입니다. | ```"interval":10```
endTime | 문자열 | 적용 | 없음 | 현재 이후의 시간을 나타내는 날짜-시간입니다. | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Object | 아니요 | 없음 | 일정 개체 | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>자세히 알아보기: startTime
다음 테이블은 startTime으로 트리거가 실행되는 방식을 제어하는 방법을 설명합니다.

startTime 값 | 일정 없이 되풀이 | 일정대로 되풀이
--------------- | --------------------------- | ------------------------
시작 시간이 이전임 | 시작 시간 이후 첫 실행 시간을 계산하고 해당 시간에 실행합니다.<p>마지막 실행 시간에서 계산된 값을 기반으로 후속 실행을 수행합니다.</p><p>이 테이블 다음에 나오는 예제를 참조하세요.</p> | 트리거는 지정된 시작 시간 _이후에_ 시작합니다. 시작 시간에서 계산된 일정을 기반으로 첫 되풀이를 실행합니다. <p>되풀이 일정에 따라 후속 실행을 수행</p>
시작 시간이 이후이거나 현재임 | 지정된 시작 시간에 한 번 실행합니다. <p>마지막 실행 시간에서 계산된 값을 기반으로 후속 실행을 수행합니다.</p> | 트리거는 지정된 시작 시간 _이후에_ 시작합니다. 시작 시간에서 계산된 일정을 기반으로 첫 되풀이를 실행합니다.<p>되풀이 일정에 따라 후속 실행을 수행합니다.</p>

startTime이 이전이고 recurrence가 있으나 schedule이 없는 경우에 어떻게 되는지 예제를 보겠습니다. 현재 시간이 `2017-04-08 13:00`이고 startTime이 `2017-04-07 14:00`이며 recurrence는 2일마다라고 가정합니다(frequency: day 및 interval: 2를 사용하여 정의됨). startTime이 현재 시간보다 이전임에 유의하십시오.

이러한 조건일 경우 첫 실행은 `2017-04-09 at 14:00`입니다. Scheduler 엔진이 시작 시간에서 되풀이 실행 시간을 계산합니다. 현재보다 이전의 모든 인스턴스는 무시됩니다. 엔진은 이후에 발생하는 다음 인스턴스를 사용합니다. 따라서 이 경우 startTime은 `2017-04-07 at 2:00pm`이므로 다음 인스턴스는 `2017-04-09 at 2:00pm`인 시간부터 2일 후입니다.

첫 번째 실행 시간은 startTime이 `2017-04-05 14:00` 또는 `2017-04-01 14:00`이더라도 동일합니다. 첫 번째 실행 후 후속 실행은 일정을 사용하여 계산됩니다. 따라서 `2017-04-11 at 2:00pm`, 그 다음에 `2017-04-13 at 2:00pm`, 그 다음에 `2017-04-15 at 2:00pm` 식으로 실행됩니다.

마지막으로, 트리거에 일정이 있을 경우 일정에 시간 및/또는 분이 설정되어 있지 않으면 첫 실행의 시간 및/또는 분이 기본값이 됩니다.

### <a name="deep-dive-schedule"></a>자세히 알아보기: schedule
한편 schedule은 트리거 실행의 횟수를 제한할 수 있는 방법이기도 합니다. 예를 들어 빈도가 "month"인 트리거에 31일에만 실행되는 schedule이 있을 경우, 이 작업은 31일이 있는 달에만 실행됩니다.

반면 schedule로 트리거 실행의 횟수를 늘릴 수도 있습니다. 예를 들어 빈도가 "month"인 트리거의 schedule이 매월 1일과 2일로 설정된 경우, 트리거는 한 달에 한 번 실행되는 것이 아니라 매월 1일과 2일에 실행됩니다.

여러 일정 요소가 지정된 경우, 계산되는 순서는 가장 큰 것부터 가장 작은 것의 순서입니다. 즉 주차, 날짜, 요일, 시간, 분의 순서입니다.

다음 테이블은 schedule 요소의 세부 정보입니다.


JSON 이름 | 설명 | 유효한 값
--------- | ----------- | ------------
minutes | 트리거가 실행될 시간(분)입니다. | <ul><li>정수 </li><li>정수 배열</li></ul>
hours | 트리거가 실행될 일(시간)입니다. | <ul><li>정수 </li><li>정수 배열</li></ul>
weekDays | 트리거가 실행될 요일입니다. 빈도가 주인 경우에만 지정할 수 있습니다. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday 또는 Sunday</li><li>값의 배열(최대 배열 크기는 7)</li></p>대/소문자 구분 안 함</p>
monthlyOccurrences | 며칠에 트리거를 실행할지 결정합니다. 빈도가 월인 경우에만 지정할 수 있습니다. | monthlyOccurence 개체의 배열: `{ "day": day,  "occurrence": occurence }`. <p> day는 트리거를 실행할 요일입니다. 예를 들어 `{Sunday}`는 해당 월의 매주 일요일입니다. 필수 사항입니다.<p>occurrence는 월 중에 되풀이되는 날입니다. 예를 들어 `{Sunday, -1}`은 해당 월의 마지막 일요일입니다. 선택 사항입니다.
monthDays | 트리거가 실행될 날짜입니다. 빈도가 월인 경우에만 지정할 수 있습니다. | <ul><li>1 이상 31 이하 사이의 모든 값</li><li>1 이하 및 31 이상의 모든 값</li><li>값의 배열</li>


## <a name="examples-recurrence-schedules"></a>예제: 되풀이 일정
이 섹션은 schedule 개체와 하위 요소에 초점을 맞춘 되풀이 일정의 예를 제공합니다.

이 예제 일정에서는 간격이 1로 설정된 것으로 가정합니다. 또한 일정에 속하는 내용에 따라 정확한 빈도를 가정합니다. 예를 들어 빈도 "day"를 사용하거나 일정의 "monthDays"를 수정할 수 없습니다. 이러한 제한 사항은 이전 섹션의 테이블에서 언급했습니다.

예 | 설명
------- | -----------
`{"hours":[5]}` | 매일 오전 5시에 실행
`{"minutes":[15], "hours":[5]}` | 매일 오전 5시 15분에 실행
`{"minutes":[15], "hours":[5,17]}` | 매일 오전 5시 15분과 오후 5시 15분에 실행
`{"minutes":[15,45], "hours":[5,17]}` | 매일 오전 5시 15분, 오전 5시 45분, 오후 5시 15분, 오후 5시 45분에 실행
`{"minutes":[0,15,30,45]}` | 매 15분마다 실행
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 매 1시간마다 실행합니다. 이 트리거는 매 1시간마다 실행합니다. 분은 startTime이 지정된 경우 이를 통해 제어하며, 지정되지 않은 경우 생성 시간에 의해 제어합니다. 예를 들어 시작 시간 또는 생성 시간(둘 중 해당하는 것)이 12:25PM이라면 트리거는 00:25, 01:25, 02:25, …, 23:25에 실행합니다. 이 일정은 트리거의 frequency가 "hour"이고 interval은 1이며 schedule이 없는 것과 동일합니다. 차이점은 이 일정의 frequency와 interval을 다르게 하여 다른 트리거도 만들 수 있다는 점입니다. 예를 들어 frequency가 "month"라면 frequency가 "day"일 때와 달리 일정은 한 달에 한 번만 실행됩니다.
`{"minutes":[0]}` | 매 시간 정각에 실행합니다. 이 트리거는 매 시간, 정각(예: 오전 12시, 오전 1시, 오전 2시 등)에만 실행됩니다. 이 설정은 frequency가 "hour", startTime이 0분인 트리거와 동일합니다. 하지만 frequency가 "day", "week" 또는 "month"라면 각각 하루에 한 번, 일주일에 한 번, 한 달에 한 번만 실행됩니다.
`{"minutes":[15]}` | 매시간 15분에 실행됩니다. 오전 0시 15분, 1시 15분, 2시 15분 등으로 시작하여 오후 10시 15분, 11시 15분에 완료됩니다.
`{"hours":[17], "weekDays":["saturday"]}` | 매주 토요일 오후 5시에 실행
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 매주 월요일, 수요일 및 금요일 오후 5시에 실행
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 매주 월요일, 수요일 및 금요일 오후 5시 15분과 오후 5시 45분에 실행
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 월요일부터 금요일까지 15분마다 실행
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 월요일부터 금요일까지 오전 9시에서 오후 4시 45분 사이에 매 15분마다 실행
`{"weekDays":["tuesday", "thursday"]}` | 화요일과 목요일의 지정된 시작 시간에 실행합니다.
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | 매월 28일 오전 6시에 실행(frequency가 month라고 가정)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 월의 마지막 날짜 오전 6시에 실행합니다. 월의 마지막 날짜에 트리거를 실행하려면 28, 29, 30 또는 31 대신 -1을 사용합니다.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 매월 1일과 말일의 오전 6시에 실행
`{monthDays":[1,14]}` | 매월 1일과 14일의 지정된 시작 시간에 실행합니다.
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 매월 첫 번째 금요일 오전 5시에 실행
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 매월 첫 번째 금요일의 지정된 시작 시간에 실행합니다.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 매월 끝에서 세 번째 금요일의 시작 시간에 실행
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 매월 첫 번째와 마지막 금요일 오전 5시 15분에 실행
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 매월 첫 번째 및 마지막 금요일의 지정된 시작 시간에 실행
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 매월 5번째 금요일 시작 시간에 실행합니다. 5번째 금요일에만 실행되도록 설정되었으므로 5번째 금요일이 없는 달에는 파이프라인이 실행되지 않습니다.  해당 월의 마지막 금요일에 트리거를 실행되도록 하려면 occurrence로 5대신 -1을 사용하면 됩니다.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 매월 마지막 금요일에 매 15분마다 실행합니다.
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 매월 세 번째 수요일 오전 5시 15분, 오전 5시 45분, 오후 5시 15분 및 오후 5시 45분에 실행합니다.


## <a name="use-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 Azure PowerShell을 사용하여 스케줄러 트리거를 만들고 시작 및 모니터링하는 방법을 보여 줍니다. 이 샘플이 작동하는지 확인하려면 먼저 [빠른 시작: Azure PowerShell을 사용하여 데이터 팩토리 만들기](quickstart-create-data-factory-powershell.md)를 진행하세요. 그런 다음 15분마다 실행되는 일정 트리거를 만들고 시작하는 다음 코드를 주 메서드에 추가하세요. 트리거는 빠른 시작의 일부로 만든 파이프라인(**Adfv2QuickStartPipeline**)과 연결되어 있습니다.

1. 다음 내용을 포함하는 MyTrigger.json이라는 JSON 파일을 C:\ADFv2QuickStartPSH\ 폴더에 만듭니다.

    > [!IMPORTANT]
    > JSON 파일을 저장하기 전에 **startTime**은 현재 UTC 시간으로, **endTime**은 현재 UTC 시간의 1시간 후로 설정합니다.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    JSON 코드 조각에서 다음을 수행합니다.
    - 트리거의 **type**은 **ScheduleTrigger**로 설정합니다.
    - **frequency**는 **Minute**로 설정하고 **interval**은 **15**로 설정합니다. 따라서 트리거는 시작 시간과 종료 시간 사이에 15분마다 파이프라인을 실행합니다.
    - **endTime**은 **startTime**의 1시간 후이므로 트리거는 startTime 후 15분, 30분 및 45분에 파이프라인을 실행합니다. startTime은 현재 UTC 시간으로, endTime은 startTime의 1시간 후로 업데이트하는 것을 잊지 마세요.  
    - 트리거는 **Adfv2QuickStartPipeline** 파이프라인과 연결되어 있습니다. 여러 파이프라인을 트리거와 연결하려면 **pipelineReference 섹션** 섹션을 더 추가하세요.
    - 빠른 시작의 파이프라인은 두 개의 **parameters**를 사용합니다. 따라서 해당 매개 변수에 대한 값을 트리거에서 전달합니다.
2. **Set-AzureRmDataFactoryV2Trigger** cmdlet을 사용하여 트리거를 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. **Get-AzureRmDataFactoryV2Trigger** cmdlet을 사용하여 트리거의 상태가 **중지됨**인지 확인합니다.

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

## <a name="use-net-sdk"></a>.NET SDK 사용
이 섹션에서는 .NET SDK를 사용하여 트리거를 만들고 시작하고 모니터링하는 방법을 보여 줍니다. 이 코드가 작동하는지 확인하려면 먼저 [.NET SDK를 사용하여 데이터 팩토리 만들기에 대한 빠른 시작](quickstart-create-data-factory-dot-net.md)을 진행하세요. 그런 다음 15분마다 실행되는 일정 트리거를 만들고 시작하는 다음 코드를 주 메서드에 추가하세요. 트리거는 빠른 시작의 일부로 만든 파이프라인(**Adfv2QuickStartPipeline**)과 연결되어 있습니다.

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method.
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

트리거 실행을 모니터링하려면 마지막 `Console.WriteLine` 문 앞에 다음 코드를 추가하세요.

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Azure Portal에서 트리거 실행/파이프라인 실행을 모니터링하려면 [파이프라인 실행 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

## <a name="use-python-sdk"></a>Python SDK 사용
이 섹션에서는 Python SDK를 사용하여 트리거를 만들고 시작하고 모니터링하는 방법을 보여 줍니다. 이 코드가 작동하는지 확인하려면 먼저 [Python SDK를 사용하여 데이터 팩토리 만들기에 대한 빠른 시작](quickstart-create-data-factory-python.md)을 진행하세요. 그런 다음 Python 스크립트에서 “monitor the pipeline run” 코드 블록 뒤에 다음 코드 블록을 추가하세요. 이 코드는 지정된 시작 시간과 종료 시간 사이에 15분마다 실행되는 일정 트리거를 만듭니다. start_time은 현재 UTC 시간으로, end_time은 현재 UTC 시간의 1시간 후로 업데이트하세요.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Azure Portal에서 트리거 실행/파이프라인 실행을 모니터링하려면 [파이프라인 실행 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

## <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용
Azure Resource Manager 템플릿을 사용하여 트리거를 만들 수 있습니다. 단계별 지침은 [Resource Manager 템플릿을 사용하여 Azure Data Factory 만들기](quickstart-create-data-factory-resource-manager-template.md)를 참조하세요.  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>파이프라인에 트리거 시작 시간 전달
버전 1에서 Azure Data Factory는 SliceStart/SliceEnd/WindowStart/WindowEnd 시스템 변수를 사용하여 분할된 데이터 읽기 또는 쓰기를 지원했습니다. 버전 2에서는 파이프라인 매개 변수와 트리거의 시작 시간/예약된 시간을 매개 변수의 값으로 사용하여 이 동작을 수행할 수 있습니다. 다음 예제에서는 트리거의 예약된 시간이 파이프라인 매개 변수 scheduledRunTime에 값으로 전달됩니다.

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
자세한 내용은 [분할된 데이터를 읽거나 쓰는 방법](how-to-read-write-partitioned-data.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#triggers)를 참조하세요.
