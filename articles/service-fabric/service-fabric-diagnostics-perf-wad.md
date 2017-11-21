---
title: "Azure Service Fabric - Windows Azure 진단 확장을 사용하여 성능 모니터링 | Microsoft Docs"
description: "Windows Azure 진단을 사용하여 Azure Service Fabric 클러스터에 대한 성능 카운터를 수집합니다."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Windows Azure 진단 확장을 사용하여 성능 모니터링

이 문서에서는 Windows 클러스터에 대한 WAD(Windows Azure Diagnostics) 확장을 통해 성능 카운터의 컬렉션을 설정하는 데 필요한 단계를 설명합니다. Linux 클러스터의 경우 [OMS 에이전트](service-fabric-diagnostics-oms-agent.md)를 설정하여 노드에 대한 성능 카운터를 수집합니다. 

 > [!NOTE]
> WAD 확장은 이러한 단계가 작동하도록 클러스터에 배포되어야 합니다. 설정하지 않은 경우 [Windows Azure 진단을 사용하여 이벤트 집계 및 컬렉션](service-fabric-diagnostics-event-aggregation-wad.md)으로 이동하고 *진단 확장 배포*의 단계를 따릅니다.

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

    `scheduledTransferPeriod`는 카운터의 값이 Azure 저장소 테이블 및 모든 구성된 싱크에 수집되고 전송되는 빈도를 정의합니다. 

3. 이전 단계에서 선언된 `PerformanceCounterConfiguration`에 수집하려는 성능 카운터를 추가합니다. 수집하려는 각 카운터는 `counterSpecifier`, `sampleRate`, `unit`, `annotation` 및 관련 `sinks`로 정의됩니다. *총 프로세서 시간*(CPU가 작업 처리에 사용된 시간의 양)에 대한 성능 카운터의 예제는 다음과 같습니다.

    ```json
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
            }
        ]
    }
    ```

    카운터에 대한 샘플 속도는 필요에 따라 수정될 수 있습니다. 이에 대한 형식은 `PT<time><unit>`이므로 1초마다 카운터를 수집하려는 경우 `"sampleRate": "PT15S"`를 설정해야 합니다.

    마찬가지로 `PerformanceCounterConfiguration`의 목록에 추가하여 다른 몇 가지 성능 카운터를 수집할 수 있습니다. `*`를 사용하여 비슷한 이름의 성능 카운터 그룹을 지정할 수 있지만 싱크를 통해 카운터를 보내려면(Application Insights에) 개별적으로 선언되어야 합니다. 

4. 수집되어야 하는 적절한 성능 카운터를 추가하면 실행 중인 클러스터에서 이러한 변경 내용이 반영되도록 클러스터 리소스를 업그레이드해야 합니다. 수정된 `template.json`을 저장하고 PowerShell을 엽니다. `New-AzureRmResourceGroupDeployment`를 사용하여 클러스터를 업그레이드할 수 있습니다. 호출에는 리소스 그룹의 이름, 업데이트된 템플릿 파일 및 매개 변수 파일이 필요하며, 리소스 관리자가 업데이트한 리소스에 대해 적절하게 변경하도록 합니다. 계정에 로그인하고 올바른 구독에 있게 되면 다음 명령을 사용하여 업그레이드를 실행합니다.

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. 업그레이드가 롤아웃을 마치면(15~45분 소요) WAD는 성능 카운터를 수집하고 `WadCfg`에서 선언된 저장소 계정의 테이블에 보내야 합니다.

## <a name="next-steps"></a>다음 단계
* Application Insights 싱크를 `WadCfg`에 추가하여 Application Insights에서 성능 카운터를 봅니다. Application Insights 싱크를 구성하려면 [Application Insights를 사용하여 이벤트 분석 및 시각화](service-fabric-diagnostics-event-analysis-appinsights.md)에서 *Resource Manager 템플릿에 AI 싱크 추가* 섹션을 참조하세요.
* 클러스터에 대한 더 많은 성능 카운터를 수집합니다. 수집해야 하는 카운터 목록은 [성능 메트릭](service-fabric-diagnostics-event-generation-perf.md)을 참조하세요.
* [Windows VM 및 Azure Resource Manager 템플릿으로 모니터링 및 진단을 사용](../virtual-machines/windows/extensions-diagnostics-template.md)하여 진단 데이터를 보내는 추가 저장소 계정 구성을 포함한 `WadCfg`에 대한 추가 수정을 수행합니다.