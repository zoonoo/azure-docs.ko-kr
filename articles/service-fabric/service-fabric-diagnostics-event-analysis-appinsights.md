---
title: "Application Insights를 사용하여 Azure Service Fabric 이벤트 분석 | Microsoft Docs"
description: "Azure Service Fabric 클러스터의 모니터링 및 진단을 위해 Application Insights를 사용하여 이벤트를 시각화 및 분석하는 방법에 대해 알아봅니다."
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
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 34f14f42150e46edae2d1352827f96a411117a62
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Application Insights를 사용하여 이벤트 분석 및 시각화

Azure Application Insights는 응용 프로그램 모니터링 및 진단을 위한 확장 가능한 플랫폼입니다. 여기에는 강력한 분석 및 쿼리 도구, 사용자 지정 가능한 대시보드 및 시각화, 자동화된 경고 등의 추가 옵션이 포함됩니다. 이는 Service Fabric 응용 프로그램 및 서비스에 대한 모니터링과 진단을 위해 권장되는 플랫폼입니다.

## <a name="setting-up-application-insights"></a>Application Insights 설정

### <a name="creating-an-ai-resource"></a>AI 리소스 만들기

AI 리소스를 만들려면 Azure Marketplace로 이동하고 "Application Insights"를 검색합니다. 첫 번째 솔루션으로 표시되어야 합니다("웹 + 모바일" 범주 아래). 올바른 리소스가 표시되면 **만들기**를 클릭합니다. 경로가 아래 이미지와 일치하는지 확인합니다.

![새 Application Insights 리소스](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

리소스를 올바르게 프로비저닝하려면 몇 가지 정보를 입력해야 합니다. Service Fabric의 프로그래밍 모델을 사용하거나 클러스터에 .NET 응용 프로그램을 게시하려면 *응용 프로그램 유형* 필드에서 "ASP.NET 웹 응용 프로그램"을 사용합니다. 게스트 실행 파일과 컨테이너를 배포하려면 "일반"을 사용합니다. 일반적으로 옵션이 계속 열린 상태로 유지되도록 "ASP.NET 웹 응용 프로그램"이 기본 설정됩니다. 이름은 기본 설정에 따라 달라지며 리소스 그룹과 구독은 모두 리소스 배포 후 변경 가능합니다. AI 리소스가 클러스터와 동일한 리소스 그룹에 있는 것이 좋습니다. 자세한 내용은 [Application Insights 리소스 만들기](../application-insights/app-insights-create-new-resource.md)를 참조하세요.

이벤트 집계 도구로 AI를 구성하려면 AI 계측 키가 필요합니다. AI 리소스가 설정되면(배포 유효성을 확인한 후 몇 분 걸림) 이 리소스로 이동하고 왼쪽 탐색 모음에서 **속성** 섹션을 찾습니다. *계측 키*를 보여 주는 새 블레이드가 열립니다. 리소스의 리소스 그룹 또는 구독을 변경해야 하는 경우 여기에서 수행할 수 있습니다.

### <a name="configuring-ai-with-wad"></a>WAD를 사용하여 AI 구성

>[!NOTE]
>이는 해당 시점의 Windows 클러스터에만 적용됩니다.

WAD에서 Azure AI로 데이터를 전송하는 두 가지 기본적인 방법이 있습니다. 이 방법은 [이 문서](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)에 설명된 대로 AI 싱크를 WAD 구성에 추가하여 이루어집니다.

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Azure Portal에서 클러스터를 만들 때 AI 계측 키 추가

![AIKey 추가](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

클러스터를 만들 때 진단이 "설정"으로 설정되면 Application Insights 계측 키를 입력할 수 있는 선택 필드가 나타납니다. 여기에 AI IKey를 붙여넣으면 클러스터를 배포하는 데 사용되는 Resource Manager 템플릿에서 AI 싱크가 자동으로 구성됩니다.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Resource Manager 템플릿에 AI 싱크 추가

Resource Manager 템플릿의 "WadCfg"에서 다음 두 가지 변경 사항을 포함하여 "Sink"를 추가합니다.

1. `DiagnosticMonitorConfiguration` 선언이 완료된 직후에 싱크 구성을 추가합니다.

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. `WadCfg`의 `DiagnosticMonitorConfiguration`에 다음 줄을 추가하여(`EtwProviders` 선언 직전) `DiagnosticMonitorConfiguration`에 Sink를 포함합니다.

    ```json
    "sinks": "applicationInsights"
    ```

위의 두 코드 조각에서 "applicationInsights"라는 이름이 싱크를 설명하는 데 사용되었습니다. 이는 요구 사항은 아니며 싱크의 이름이 "sinks"에 포함되어 있는 한 이름을 임의의 문자열로 설정할 수 있습니다.

현재 클러스터의 로그는 AI의 로그 뷰어에 추적으로 표시됩니다. 플랫폼에서 발생하는 대부분의 추적은 "정보" 수준이므로 "위험" 또는 "오류" 유형의 로그만 보내도록 싱크 구성을 변경할 수도 있습니다. 이 작업은 [이 문서](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)에서 설명한 것처럼 싱크에 "채널"을 추가하여 수행할 수 있습니다.

>[!NOTE]
>포털 또는 Resource Manager 템플릿에서 잘못된 AI IKey를 사용하는 경우 수동으로 키를 변경하고 클러스터를 업데이트/재배포해야 합니다. 

### <a name="configuring-ai-with-eventflow"></a>EventFlow를 사용하여 AI 구성

EventFlow를 사용하여 이벤트를 집계하는 경우 `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet 패키지를 가져와야 합니다. *eventFlowConfig.json*의 *outputs* 섹션에 다음이 포함되어야 합니다.

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

필터를 필요한 대로 변경하고 다른 입력(각각의 NuGet 패키지와 함께)도 포함해야 합니다.

## <a name="aisdk"></a>AI.SDK

일반적으로 EventFlow 및 WAD는 진단 및 모니터링에 좀 더 모듈 방식으로 접근할 수 있으므로 집계 솔루션으로 사용하는 것이 좋습니다. 즉, EventFlow의 출력을 변경하려는 경우 실제 계측을 변경할 필요 없이 구성 파일을 간단히 수정하기만 하면 됩니다. 그러나 Application Insights를 사용하기로 결정하고 다른 플랫폼으로 변경할 가능성이 없는 경우 이벤트를 집계하여 AI에 보내는 작업에 대해 AI의 새 SDK 사용에 대해 살펴봐야 합니다. 즉, 데이터를 AI로 보내도록 EventFlow를 구성할 필요가 없으며 대신 ApplicationInsight의 Service Fabric NuGet 패키지를 설치합니다. 패키지에 대한 자세한 내용은 [여기](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)를 참조하세요.

[Application Insights support for Microservices and Containers](https://azure.microsoft.com/app-insights-microservices/)(마이크로 서비스 및 컨테이너에 대한 Application Insights 지원)에서는 작업 중인 새로운 기능 중 일부(현재 베타 버전)를 보여 주며 이를 통해 AI에서 보다 풍부한 기본 모니터링 옵션을 사용할 수 있습니다. 여기에는 종속성 추적(클러스터의 모든 서비스 및 응용 프로그램의 AppMap을 빌드하고 그 사이의 통신을 설정하는 데 사용됨) 및 서비스에서 발생한 추적의 상관 관계가 포함됩니다(앱 또는 서비스의 워크플로 문제를 정확히 발견 가능).

.NET에서 개발 중이고 Service Fabric의 프로그래밍 모델 중 일부를 사용하고 있으며 이벤트 및 로그 데이터를 시각화하고 분석하기 위한 플랫폼으로 AI를 사용하려는 경우 모니터링 및 진단 워크플로인 AI SDK 경로를 통해 이동하는 것이 좋습니다. AI를 사용하여 로그를 수집 및 표시하려면 [이 문서](../application-insights/app-insights-asp-net-more.md) 및 [이 문서](../application-insights/app-insights-asp-net-trace-logs.md)를 참조하세요.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Azure Portal에서 AI 리소스 탐색

AI를 이벤트 및 로그의 출력으로 구성했으면 몇 분 내에 AI 리소스에 정보가 표시되기 시작합니다. AI 리소스로 이동하면 AI 리소스 대시보드가 표시됩니다. AI 작업 표시줄에서 **검색**을 클릭하여 수신한 최신 추적을 보고 필터링할 수 있습니다.

*메트릭 탐색기*는 응용 프로그램, 서비스 및 클러스터가 보고할 수 있는 메트릭을 기반으로 사용자 지정 대시보드를 만드는 데 유용한 도구입니다. 수집 중인 데이터를 기반으로 직접 몇 가지 차트를 설정하려면 [Application Insights에서 메트릭 탐색](../application-insights/app-insights-metrics-explorer.md)을 참조하세요.

**분석**을 클릭하면 Application Insights 분석 포털로 이동합니다. 이 포털에서는 보다 넓은 범위 및 다양한 옵션으로 이벤트 및 추적을 쿼리할 수 있습니다. 자세한 내용은 [Application Insights의 분석](../application-insights/app-insights-analytics.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [AI에 경고 설정](../application-insights/app-insights-alerts.md) - 성능 또는 사용 변경에 대한 알림 받기
* [Application Insights의 스마트 감지](../application-insights/app-insights-proactive-diagnostics.md) - 잠재적인 성능 문제를 경고하기 위해 AI에 전송되는 원격 분석에 대한 사전 분석 수행