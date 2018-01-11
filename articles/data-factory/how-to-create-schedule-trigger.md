---
title: "Azure Data Factory에서 트리거를 만드는 방법 | Microsoft Docs"
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
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>일정에 따라 파이프라인을 실행하는 트리거를 만드는 방법
이 문서에서는 트리거를 만들고 시작하고 모니터링하는 단계를 제공합니다. 트리거에 대한 개념 정보는 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.


## <a name="use-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 Azure PowerShell을 사용하여 트리거를 만들고 시작하고 모니터링하는 방법을 보여 줍니다. 이 샘플이 작동하는지 확인하려면 먼저 [빠른 시작: Azure PowerShell을 사용하여 데이터 팩토리 만들기](quickstart-create-data-factory-powershell.md)를 진행하세요. 그런 다음 15분마다 실행되는 일정 트리거를 만들고 시작하는 다음 코드를 주 메서드에 추가하세요. 트리거는 빠른 시작의 일부로 만든 파이프라인(**Adfv2QuickStartPipeline**)과 연결되어 있습니다.

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