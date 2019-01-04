---
title: Azure Monitor에서 메트릭 경고가 작동하는 방식을 이해합니다.
description: 메트릭 경고로 수행할 수 있는 작업과 Azure Monitor에서 메트릭 경고가 작동하는 방식에 대한 개요를 제공합니다.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 24d0965fa2a88bc844a81c7430d46c071a9b874b
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580045"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Azure Monitor에서 메트릭 경고가 작동하는 방식 이해

Azure Monitor에서 메트릭 경고는 다차원 메트릭을 기반으로 작동합니다. 이러한 메트릭은 플랫폼 메트릭, [사용자 지정 메트릭](../../azure-monitor/platform/metrics-custom-overview.md), [메트릭으로 전환된 Log Analytics의 주요 로그](../../azure-monitor/platform/alerts-metric-logs.md), Application Insights 표준 메트릭이 될 수 있습니다. 메트릭 경고는 주기적으로 하나 이상의 메트릭 시계열에서 조건이 참인지 평가하고 평가에 부합하면 사용자에게 알립니다. 메트릭 경고는 상태를 저장합니다. 즉 상태가 변경될 때만 알림을 보냅니다.

## <a name="how-do-metric-alerts-work"></a>메트릭 경고 작동 방식

모니터링할 대상 리소스, 메트릭 이름 및 조건(연산자 및 임계값), 경고 규칙 실행 시 트리거할 작업 그룹을 지정하여 메트릭 경고 규칙을 정의할 수 있습니다.
다음과 같이 간단한 메트릭 경고 규칙을 만들었다고 가정해 보겠습니다.

- 대상 리소스(모니터링할 Azure 리소스): myVM
- 메트릭: 백분율 CPU
- 시간 집계(원시 메트릭 값에 대해 실행되는 통계. 지원되는 시간 집계는 최소, 최대, 평균, 총계): 평균
- 기간(메트릭 값을 검사하는 과거 시간 범위):      지난 5분
- 빈도(메트릭 경고가 조건에 부합하는지 확인하는 주기): 1분
- 연산자:     다음보다 큼
- 임계값:      70

경고 규칙이 만들어진 시점 이후 모니터는 1분 간격으로 실행되며 지난 5분 간의 메트릭 값을 살펴 이 평균이 70을 초과하는지 확인합니다. 조건에 부합하면, 즉 지난 5분의 평균 백분율 CPU가 70을 초과하면 경고 규칙에서 활성화된 알림을 생성합니다. 경고 규칙과 연결된 작업 그룹에서 이메일이나 웹후크 작업을 구성한 경우 모두 활성화된 알림을 받게 됩니다.

이 특정 경고 규칙 실행 인스턴스는 Azure Portal의 모든 경고 블레이드에서도 확인할 수 있습니다.

"myVM"의 사용량이 이후의 검사에서도 임계값을 계속 넘어간다면 경고 규칙은 이 조건이 해결될 때까지 다시 실행되지 않습니다.

일정 시간 후 "myVM"의 사용량이 정상 상태, 즉 지정된 임계값 밑으로 떨어집니다. 경고 규칙은 해결 알림을 보내기 위해 두 번 이상 조건을 모니터링합니다. 조건이 유동적인 경우 소음을 줄이기 위해 세 번 연속 경고 조건에 부합하지 않으면 경고 규칙에서 해결/비활성화 메시지를 보냅니다.

해결 알림은 웹후크나 이메일로 전송되며 Azure Portal의 경고 인스턴스(모니터 상태) 상태도 해결로 변경됩니다.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Azure Monitor에서 메트릭 경고를 사용한 모니터링 축소

### <a name="using-dimensions"></a>차원 사용

Azure Monitor의 메트릭 경고는 한 규칙을 통한 여러 차원 값 조합 모니터링도 지원합니다. 예제를 통해 여러 차원 값 조합을 사용할 수 있는 이유를 살펴 보겠습니다.

웹 사이트에 대해 App Service 계획은 있다고 가정합니다. 웹 사이트/앱을 실행하는 여러 인스턴스의 CPU 사용량을 모니터링하려 합니다. 다음과 같이 메트릭 경고 규칙을 사용할 수 있습니다.

- 대상 리소스: myAppServicePlan
- 메트릭: 백분율 CPU
- 차원
  - 인스턴스 = InstanceName1, InstanceName2
- 시간 집계: 평균
- 기간: 지난 5분
- 빈도: 1분
- 연산자: GreaterThan
- 임계값: 70

이전과 같이 이 규칙은 최근 5분 동안 평균 CPU 사용량이 70%를 초과하는지 모니터링합니다. 그러나 같은 규칙으로 웹 사이트에서 실행되는 두 인스턴스를 모니터링할 수 있습니다. 각 인스턴스는 개별적으로 모니터링되며 알림도 개별적으로 받게 됩니다.

예를 들어 수요가 큰 웹앱이 있고 다른 인스턴스를 더 추가해야 한다고 가정합니다. 위 규칙에서는 두 인스턴스만 모니터링합니다. 그러나 다음과 같이 규칙을 만들 수 있습니다.

- 대상 리소스: myAppServicePlan
- 메트릭: 백분율 CPU
- 차원
  - 인스턴스 = *
- 시간 집계: 평균
- 기간: 지난 5분
- 빈도: 1분
- 연산자: GreaterThan
- 임계값: 70

이 규칙은 자동으로 인스턴스의 모든 값을 모니터링합니다. 즉 메트릭 경고 규칙을 다시 수정하지 않고도 그대로 인스턴스를 모니터링할 수 있습니다.

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>메트릭 경고를 사용하여 여러 리소스 모니터링

앞 섹션에서 본 것처럼 각각의 차원 조합을 모니터링하는 단일 메트릭 경고 규칙(즉 메트릭 시계열)이 가능합니다. 그러나 이전에는 한 번에 하나의 리소스로 제한되었습니다. Azure Monitor는 하나의 경고 규칙으로 여러 리소스를 모니터링할 수도 있습니다. 이 기능은 현재 미리 보기이며 가상 머신에서만 지원됩니다. 또한 단일 메트릭 경고가 한 Azure 지역의 리소스를 모니터링할 수 있습니다.

다음 세 가지 방법 중 하나로 단일 메트릭 경고를 통해 모니터링 범위를 지정할 수 있습니다.

- 구독 내 한 Azure 지역에 있는 가상 머신 목록으로
- 구독 내 하나 이상의 리소스 그룹에 있는 모든 가상 머신(한 Azure 지역에 있는)
- 구독의 모든 가상 머신(한 Azure 지역에 있는)

여러 리소스를 모니터링하는 메트릭 경고 규칙 만들기는 현재 Azure Portal을 통해 지원되지 않습니다. [Azure Resource Manager 템플릿](../../azure-monitor/platform/alerts-metric-create-templates.md#resource-manager-template-for-metric-alert-that-monitors-multiple-resources)을 통해 이러한 규칙을 만들 수 있습니다. 각 가상 머신에 대한 개별 알림을 받게 됩니다. 

## <a name="typical-latency"></a>일반적인 대기 시간

메트릭 경고의 경우, 일반적으로 경고 규칙 빈도를 1분으로 설정하면 5분 안에 알림을 받게 됩니다. 알림 시스템에 부하가 높으면 대기 시간이 더 길어집니다.

## <a name="supported-resource-types-for-metric-alerts"></a>메트릭 경고에 대한 지원되는 리소스 종류

지원되는 리소스 종류의 전체 목록은 이 [문서](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)에서 확인할 수 있습니다.

현재는 클래식 메트릭 경고를 사용하는데 메트릭 경고가 사용 중인 모든 리소스 종류를 지원하는지를 확인하려는 경우 아래 표를 참조하세요. 이 표에는 클래식 메트릭 경고가 지원하는 리소스 종류, 그리고 이러한 리소스 종류가 현재 메트릭 경고에서 지원되는지 여부가 나와 있습니다.

|클래식 메트릭 경고가 지원하는 리소스 종류 | 메트릭 경고의 지원 여부 |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | 예 |
| Microsoft.Batch/batchAccounts| 예|
|Microsoft.Cache/redis| 예
|Microsoft.ClassicCompute/virtualMachines | 아니요 |
|Microsoft.ClassicCompute/domainNames/slots/roles | 아니요|
|Microsoft.CognitiveServices/accounts | 아니요 |
|Microsoft.Compute/virtualMachines | 예|
|Microsoft.Compute/virtualMachineScaleSets| 예|
|Microsoft.ClassicStorage/storageAccounts| 아니요 |
|Microsoft.DataFactory/datafactories | 예|
|Microsoft.DBforMySQL/servers| 예|
|Microsoft.DBforPostgreSQL/servers| 예|
|Microsoft.Devices/IotHubs | 아니요|
|Microsoft.DocumentDB/databaseAccounts| 아니요|
|Microsoft.EventHub/namespaces | 예|
|Microsoft.Logic/workflows | 예|
|Microsoft.Network/loadBalancers |예|
|Microsoft.Network/publicIPAddresses| 예|
|Microsoft.Network/applicationGateways| 예|
|Microsoft.Network/expressRouteCircuits| 예|
|Microsoft.Network/trafficManagerProfiles | 예|
|Microsoft.Search/searchServices | 아니요|
|Microsoft.ServiceBus/namespaces| 아니요|
|Microsoft.Storage/storageAccounts | 예|
|Microsoft.StreamAnalytics/streamingjobs| 예|
|Microsoft.TimeSeriesInsights/environments | 예|
|Microsoft. Web/serverfarms | 예 |
|Microsoft. Web/sites(함수 제외) | 예|
|Microsoft. Web/hostingEnvironments/multiRolePools | 아니요|
|Microsoft. Web/hostingEnvironments/workerPools| 아니요
|Microsoft.SQL/Servers | 아니요|

## <a name="next-steps"></a>다음 단계

- [Azure에서 메트릭 경고 만들기, 보기 및 관리방법 알아보기](alerts-metric.md)
- [Azure Resource Manager 템플릿을 사용한 메트릭 경고 배포 방법 알아보기](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [작업 그룹에 대해 자세히 알아보기](action-groups.md)
