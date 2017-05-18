---
title: "Azure Application Insights의 사용자 지정 라이브 메트릭 및 진단 | Microsoft Docs"
description: "사용자 지정 메트릭으로 웹앱을 실시간으로 모니터링하고 오류, 추적 및 이벤트의 라이브 피드를 통해 문제를 진단할 수 있습니다."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 86e01cf6cb14334e85da4102610fa7feb66cb543
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="custom-live-metrics-and-events-monitor--diagnose-with-1-second-latency"></a>사용자 지정 라이브 메트릭 및 이벤트: 1초 대기 시간으로 모니터링 및 진단 
라이브 메트릭 스트림은 거의 실시간에 가까운 1초의 대기 시간으로 [Application Insights](app-insights-overview.md) 메트릭 및 이벤트를 표시합니다. 이 즉각적인 모니터링은 검색 및 진단 평균 시간을 줄이고 SLA를 충족하는 데 도움을 줍니다. 다음을 수행할 수 있습니다.
* 사용자 지정 KPI 라이브 모니터링: 필터, 기존 Application Insights 원격 분석의 분석 및 분할을 통해 포털에서 가장 관련성이 높은 KPI를 얻을 수 있습니다. 코드 또는 구성 변경이나 배포가 필요 없습니다. 전송할 수 있는 모든 사용자 지정 또는 측정값을 사용할 수 있습니다.
* 라이브 검색 및 진단: 자세한 예외 추적과 함께 발생하는 요청 및 종속성 오류를 확인할 수 있습니다. 알려진 문제는 필터링해서 걸러냄으로써 실제/새 문제에 집중할 수 있습니다.
* 라이브 디버깅: 문제를 재현하고 재현 상호 작용을 식별하는 특정 세션 ID에 대한 사용자 지정 필터링을 사용하여(또는 사용자 지정 특성 사용) 관련된 모든 원격 분석을 라이브로 확인합니다. 적절한 문제 해결을 위해 임의/모든 서버에서 정보를 수집합니다.
* 부하에 따라 리소스 소비량이 영향을 받는 방식을 즉시 확인: 부하 테스트를 실행할 때 *모든* Windows 성능 카운터를 라이브로 모니터링하거나, 프로덕션 환경을 모니터링하여 성능 저하가 발생하기 전에 조치를 취합니다. 구성 변경 또는 배포가 필요 없습니다.
* 릴리스되는 수정 사항의 유효성을 검사합니다. 서비스 업데이트가 잘 작동하는지 확인합니다. 해결한 오류가 더 이상 발생하지 않는지 확인합니다.
* 문제가 있는 서버를 쉽게 식별하고 해당 서버로의 모든 KPI/라이브 피드만 필터링합니다.

라이브 메트릭 및 이벤트 스트림 데이터는 무료이므로 청구서에 비용이 추가되지 않습니다. 포털 환경을 열 때 필요하면 서버에서 데이터가 스트리밍됩니다. 해당 데이터는 차트에 있는 동안만 지속된 후 삭제됩니다. 모든 기능을 ASP.NET 클래식 응용 프로그램에서 사용할 수 있고 .NET Core 응용 프로그램에는 현재 고정된 라이브 메트릭 및 샘플 오류 집합만 포함되어 있습니다. 최신 라이브 스트리밍 기능을 위해 지원되는 모든 SDK를 적절한 상태로 유지합니다. 

샘플링하기 전에 또는 사용자 지정 TelemetryProcessors를 적용하기 전에 응용 프로그램 인스턴스에서 라이브 스트림을 수집합니다. 

![라이브 메트릭 스트림 동영상](./media/app-insights-live-stream/youtube.png) [라이브 메트릭 스트림 동영상](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

왼쪽의 옵션 또는 개요 블레이드의 단추를 클릭하여 라이브 메트릭 및 이벤트에 액세스합니다.

![개요 블레이드에서 라이브 스트림 클릭](./media/app-insights-live-stream/live-stream-2.png)

## <a name="custom-live-kpi"></a>사용자 지정 라이브 KPI
포털에서 Application Insights 원격 분석에 임의 필터를 적용하여 사용자 지정 KPI를 라이브로 모니터링할 수 있습니다. 차트 위로 마우스를 가져가면 표시되는 필터 컨트롤을 클릭합니다. 다음 차트는 URL 및 기간 특성에 필터를 적용하여 사용자 지정 요청 수 KPI를 그래프로 나타냅니다. 언제든지 지정한 조건과 일치하는 원격 분석의 라이브 피드를 표시하는 스트림 미리 보기 섹션을 사용하여 필터의 유효성을 검사합니다. 

![사용자 지정 요청 KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Count와 다른 값을 모니터링할 수 있습니다. 옵션은 Application Insights 원격 분석이 될 수 있는 스트림 유형에 따라 달라집니다. 요청, 종속성, 예외, 추적, 이벤트 또는 메트릭 등이 옵션으로 제공됩니다. 이것은 사용자 고유의 [사용자 지정 측정](app-insights-api-custom-events-metrics.md#properties)일 수 있습니다.

![값 옵션](./media/app-insights-live-stream/live-stream-valueoptions.png)

Application Insights 원격 분석 외에, 스트림 옵션 중에서 선택하고 성능 카운터의 이름을 제공하여 Windows 성능 카운터도 모니터링할 수도 있습니다.

라이브 메트릭은 각 서버에서 로컬로 집계된 후 모든 서버에서 집계됩니다. 해당 드롭다운 목록에서 다른 옵션을 선택하여 기본값을 변경할 수 있습니다.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>샘플 원격 분석: 사용자 지정 라이브 진단 이벤트
기본적으로 이벤트의 라이브 피드는 실패한 요청 및 종속성 호출, 예외, 이벤트 및 추적의 샘플을 보여 줍니다. 필터 아이콘을 클릭하면 특정 시점에 적용된 조건을 확인할 수 있습니다. 

![기본 라이브 피드](./media/app-insights-live-stream/live-stream-eventsdefault.png)

메트릭과 마찬가지로, Application Insights 원격 분석 유형 중 하나에 대해 임의 조건을 지정할 수 있습니다. 이 예제에서는 특정 요청 실패, 추적 및 이벤트를 선택합니다. 모든 예외 및 종속성 오류도 선택합니다.

![사용자 지정 라이브 피드](./media/app-insights-live-stream/live-stream-events.png)

참고: 현재 예외 메시지 기반 조건의 경우 가장 바깥쪽 예외 메시지를 사용하세요. 앞에 나온 예제에서 안쪽 예외 메시지("<--" 구분 기호 다음에 나옴) "클라이언트 연결이 끊어졌습니다."를 포함하는 심각하지 않은 예외를 필터링하기 위해서는 "요청 콘텐츠를 읽는 동안 오류 발생" 조건을 포함하지 않는 메시지를 사용합니다.

라이브 피드 항목을 클릭하여 세부 정보를 확인합니다. **일시 중지**를 클릭하거나, 아래로 스크롤하거나, 항목을 클릭하여 피드를 일시 중지할 수 있습니다. 맨 위로 다시 스크롤하거나 일시 중지된 상태에서 수집된 항목의 카운터를 클릭하면 라이브 피드가 계속됩니다.

![샘플링된 라이브 실패](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>서버 인스턴스별 필터링

특정 서버 역할 인스턴스를 모니터링하려는 경우 서버별로 필터링할 수 있습니다.

![샘플링된 라이브 실패](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK 요구 사항
사용자 지정 라이브 메트릭 스트림은 버전 2.4.0-beta2 또는 최신 버전의 [웹용 Application Insights SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)에서 사용할 수 있습니다. NuGet 패키지 관리자에서 "시험판 포함" 옵션을 선택해야 합니다.

## <a name="authenticated-channel"></a>인증된 채널
지정한 사용자 지정 필터 조건이 Application Insights SDK의 라이브 메트릭 구성 요소에 다시 전송됩니다. 필터는 customerid와 같은 잠재적으로 중요한 정보를 포함할 수 있습니다. 계측 키 외에도 비밀 API 키를 사용해서 채널 보안을 유지할 수 있습니다.
### <a name="create-an-api-key"></a>API 키 만들기

![API 키 만들기](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>구성에 API 키 추가
applicationinsights.config 파일에서 QuickPulseTelemetryModule에 AuthenticationApiKey를 추가합니다.
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add> 

```
또는 코드에서 QuickPulseTelemetryModule에 설정합니다.

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```

그러나 연결된 모든 서버를 인식하고 신뢰하는 경우 인증된 채널을 사용하지 않고 사용자 지정 필터를 시도할 수 있습니다. 이 옵션은 6개월 동안 사용할 수 있습니다. 이 재정의는 새 세션마다 한 번씩 또는 새 서버가 온라인 상태가 될 때마다 필요합니다.

![라이브 메트릭 인증 옵션](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>필터 조건에 CustomerID와 같은 잠재적으로 중요한 정보를 입력하기 전에 인증된 채널을 설정하는 것이 좋습니다.
>

## <a name="troubleshooting"></a>문제 해결

데이터가 없나요? 응용 프로그램이 보호된 네트워크에 있는 경우: 라이브 메트릭 스트림은 Application Insights 원격 분석과는 다른 IP 주소를 사용합니다. 방화벽에서 [해당 포트](app-insights-ip-addresses.md)가 열려 있는지 확인합니다.



## <a name="next-steps"></a>다음 단계
* [Application Insights를 사용하여 사용량 모니터링](app-insights-web-track-usage.md)
* [진단 검색 사용](app-insights-diagnostic-search.md)


