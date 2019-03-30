---
title: Azure Service Fabric - Windows Azure 진단 확장을 사용하여 성능 모니터링 | Microsoft Docs
description: Windows Azure 진단을 사용하여 Azure Service Fabric 클러스터에 대한 성능 카운터를 수집합니다.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: ec66a4fdffcff2d2ff7c11c969900c8b12dda755
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669698"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Windows Azure 진단 확장을 사용하여 성능 모니터링

이 문서에서는 Windows 클러스터에 대한 WAD(Windows Azure Diagnostics) 확장을 통해 성능 카운터의 컬렉션을 설정하는 데 필요한 단계를 설명합니다. Linux 클러스터의 경우 [Log Analytics 에이전트](service-fabric-diagnostics-oms-agent.md)를 설정하여 노드에 대한 성능 카운터를 수집합니다. 

 > [!NOTE]
> WAD 확장은 이러한 단계가 작동하도록 클러스터에 배포되어야 합니다. 설정되지 않은 경우 [Miscrosoft Azure 진단을 사용하여 이벤트 집계 및 수집](service-fabric-diagnostics-event-aggregation-wad.md)으로 이동합니다.  

## <a name="collect-performance-counters-via-the-wadcfg"></a>WadCfg를 통해 성능 카운터 수집

WAD를 통해 성능 카운터를 수집하려면 클러스터의 Resource Manager 템플릿에서 구성을 적절하게 수정해야 합니다. 다음 단계를 따라 템플릿에 수집하려는 성능 카운터를 추가하고 리소스 관리자 리소스 업그레이드를 실행합니다.

1. 클러스터의 템플릿에서 WAD 구성을 찾습니다. `WadCfg`를 찾습니다. `DiagnosticMonitorConfiguration` 아래에서 수집할 성능 카운터를 추가합니다.

2. 다음 섹션을 `DiagnosticMonitorConfiguration`에 추가하여 성능 카운터를 수집하는 구성을 설정합니다. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod`는 수집된 카운터의 값이 Azure 스토리지 테이블 및 구성된 싱크로 전송되는 빈도를 정의합니다. 

3. 이전 단계에서 선언된 `PerformanceCounterConfiguration`에 수집하려는 성능 카운터를 추가합니다. 수집하려는 각 카운터는 `counterSpecifier`, `sampleRate`, `unit`, `annotation` 및 관련 `sinks`로 정의됩니다.

*총 프로세서 시간*(CPU를 처리 작업에 사용하는 시간)에 대한 카운터를 사용한 구성 및 Service Fabric 사용자 지정 성능 카운터 중 하나인 *초당 Service Fabric 작업자 메서드 호출*의 예제는 다음과 같습니다. Service Fabric 사용자 지정 성능 카운터의 전체 목록은 [Reliable Actor 성능 카운터](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) 및 [Reliable Service 성능 카운터](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters)를 참조하세요.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 카운터에 대한 샘플 속도는 필요에 따라 수정될 수 있습니다. 이에 대한 형식은 `PT<time><unit>`이므로 1초마다 카운터를 수집하려는 경우 `"sampleRate": "PT15S"`를 설정해야 합니다.

 ARM 템플릿에서 변수를 사용하여 성능 카운터 배열을 수집할 수도 있습니다. 프로세스당 성능 카운터를 수집하는 경우 이 작업을 편리하게 수행할 수 있습니다. 아래 예제에서는 프로세스당 프로세서 시간 및 가비지 수집기 시간을 수집한 후, using 변수를 사용하여 노드 자체에서 2개의 성능 카운터를 수집합니다. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

 >[!NOTE]
 >`*`를 사용하여 비슷한 이름의 성능 카운터 그룹을 지정할 수 있지만 싱크를 통해 카운터를 보내려면(Application Insights에) 개별적으로 선언되어야 합니다. 

1. 수집되어야 하는 적절한 성능 카운터를 추가하면 실행 중인 클러스터에서 이러한 변경 내용이 반영되도록 클러스터 리소스를 업그레이드해야 합니다. 수정된 `template.json`을 저장하고 PowerShell을 엽니다. `New-AzureRmResourceGroupDeployment`를 사용하여 클러스터를 업그레이드할 수 있습니다. 호출에는 리소스 그룹의 이름, 업데이트된 템플릿 파일 및 매개 변수 파일이 필요하며, 리소스 관리자가 업데이트한 리소스에 대해 적절하게 변경하도록 합니다. 계정에 로그인하고 올바른 구독에 있게 되면 다음 명령을 사용하여 업그레이드를 실행합니다.

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. 업그레이드가 롤아웃을 완료하면(첫 번째 배포인지 여부 및 리소스 그룹의 크기에 따라 15-45분 소요) WAD는 성능 카운터를 수집하고 클러스터와 연결된 스토리지 계정에서 WADPerformanceCountersTable이라는 표로 보내야 합니다. [Resource Manager 템플릿에 AI 싱크를 추가](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)하여 Application Insights에서 성능 카운터를 확인합니다.

## <a name="next-steps"></a>다음 단계
* 클러스터에 대한 더 많은 성능 카운터를 수집합니다. 수집해야 하는 카운터 목록은 [성능 메트릭](service-fabric-diagnostics-event-generation-perf.md)을 참조하세요.
* [Windows VM 및 Azure Resource Manager 템플릿으로 모니터링 및 진단을 사용](../virtual-machines/windows/extensions-diagnostics-template.md)하여 진단 데이터를 보내는 추가 저장소 계정 구성을 포함한 `WadCfg`에 대한 추가 수정을 수행합니다.
* [WadCfg 작성기](https://azure.github.io/azure-diagnostics-tools/config-builder/)를 방문하여 처음부터 새로 템플릿을 빌드하고 구문이 올바른지 확인합니다.