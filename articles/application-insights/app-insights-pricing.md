---
title: "Application Insights의 기능 및 데이터 볼륨 관리 | Microsoft Docs"
description: "Application Insights에서 원격 분석을 관리하고 비용을 모니터링합니다."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2fb91689d6fbce5d90aba32b0acd65bcb4bd709d
ms.openlocfilehash: c852187847b69627d2f27c25fbe2ee8d8d1cf49d


---
# <a name="manage-features-and-data-volume-in-application-insights"></a>Application Insights에서 기능 및 데이터 볼륨 관리


[Azure Application Insights][start]의 가격 책정은 응용 프로그램당 데이터 볼륨을 기반으로 합니다. 월별 원격 분석 데이터 사용 요금은 무료이므로 개발 중일 때나 작은 앱을 사용할 때의 적은 사용량은 무료일 수 있습니다.

각 Application Insights 리소스는 별도의 서비스로 요금이 부과되고 Azure 구독에 대한 청구서에 추가됩니다.

가격 책정 계획에는 두 가지가 있습니다. 기본 계획은 Basic입니다. 일별 요금이 부과되지만 [연속 내보내기](app-insights-export-telemetry.md)와 같은 특정 추가 기능을 사용할 수 있는 Enterprise 계획을 선택할 수도 있습니다.

[가격 책정 계획을 참조하세요][pricing].

## <a name="review-pricing-plan-for-your-application-insights-resource"></a>Application Insights 리소스의 가격 책정 계획 검토
응용 프로그램에 대한 Application Insights 리소스에서 기능 + 가격 책정 블레이드를 엽니다.

![가격 책정을 선택합니다.](./media/app-insights-pricing/01-pricing.png)

1. 해당 월의 데이터 볼륨을 검토합니다. 여기에는 서버 및 클라이언트 앱과 가용성 테스트에서 수신되고 보유되는([샘플링](app-insights-sampling.md) 이후) 모든 데이터가 포함됩니다.
2. [다단계 웹 테스트](app-insights-monitor-web-app-availability.md#multi-step-web-tests)에 대해서는 별도 요금이 부과됩니다. (여기에는 간단한 가용성 테스트는 포함되지 않습니다. 이 테스트의 경우 데이터 볼륨 요금에 포함됩니다.)
3. Enterprise 계획에서 제공하는 추가 기능을 사용하도록 설정합니다. 이 계획의 경우 무료 데이터 할당량이 없습니다.
4. 데이터 관리 옵션에서 일일 한도를 설정하거나 수집 샘플링을 설정합니다.

Application Insights 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure 포털의 청구 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다. 

![측면 메뉴에서 대금 청구를 선택합니다.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>데이터 속도
다음 세 가지 방법으로 데이터 전송 볼륨이 제한됩니다.

* 일일 한도. 기본적으로 100GB/일로 설정됩니다. 앱이 이 한도에 도달하면 전자 메일이 전송되며 하루가 끝날 때까지 데이터가 삭제됩니다. 이 설정은 데이터 볼륨 관리 블레이드에서 변경합니다.
* [샘플링](app-insights-sampling.md) 이 메커니즘은 메트릭을 최소로만 조정하면서 서버 및 클라이언트 앱에서 전송되는 원격 분석의 양을 줄일 수 있습니다.
* 제한 기능을 통해 1분당 데이터 속도를 제한할 수 있습니다. Basic 가격 책정 계획의 경우 5분에 걸쳐 평균 초당 200개의 데이터 지점으로 제한되며, Enterprise의 경우에는 1분에 걸쳐 평균 초당 500개 데이터 지점으로 제한됩니다. 

제한 기능을 사용할 경우 다음과 같은 3가지 버킷이 따로 계산됩니다.

* [TrackTrace 호출](app-insights-api-custom-events-metrics.md#track-trace) 및 [캡처된 로그](app-insights-asp-net-trace-logs.md)
* [예외](app-insights-api-custom-events-metrics.md#track-exception)는 초당 50개 지점으로 제한됩니다.
* 다른 모든 원격 분석(페이지 보기, 세션, 요청, 종속성, 메트릭, 사용자 지정 이벤트, 웹 테스트 결과)

*내 앱이 초당 속도를 초과하게 되면 어떻게 됩니까?*

* 앱에 보내는 데이터의 양은 1분마다 평가됩니다. 해당 분에 대한 평균 초당 속도를 초과하면 서버는 일부 요청을 거부합니다. SDK는 데이터를 버퍼링하고, 재전송을 시도하여 몇 분 동안 서지를 분산시킵니다. 앱이 제한율 이상으로 일관되게 데이터를 보내는 경우 일부 데이터가 삭제됩니다. (ASP.NET, Java 및 JavaScript SDK는 이러한 방식으로 재전송을 시도합니다. SDK는 제한된 데이터를 제거하기만 합니다.)

제한이 발생하는 경우 이를 경고하는 알림이 표시됩니다.

*내 앱이 보내는 데이터 요소 수를 어떻게 알 수 있나요?*

* 가격 책정 블레이드를 열고 데이터 볼륨 차트를 확인합니다.
* 또는 메트릭 탐색기에서 새 차트를 추가하고 **데이터 요소 볼륨**을 메트릭으로 선택합니다. 그룹화로 전환한 다음 **데이터 형식**을 기준으로 그룹화합니다.

## <a name="to-reduce-your-data-rate"></a>데이터 속도를 줄이려면
다음 작업을 수행하여 데이터 볼륨을 줄일 수 있습니다.

* 일별 볼륨 한도를 줄입니다. 기본값은 하루당 100GB입니다.
* [샘플링](app-insights-sampling.md)을 사용합니다. 이 기술은 메트릭을 왜곡하지 않으며 검색에서 관련 항목 간 이동 기능을 중단하지 않고 데이터 속도를 낮춥니다. 서버 앱에서는 이 기능이 자동으로 수행됩니다.
* [보고될 수 있는 Ajax 호출 수를 제한](app-insights-javascript.md#detailed-configuration) 하거나 Ajax 보고를 해제합니다.
* [ApplicationInsights.config를 편집](app-insights-configuration-with-applicationinsights-config.md)하여 필요하지 않은 컬렉션 모듈을 끕니다. 예를 들어 성능 카운터 또는 종속성 데이터가 필요하지 않다고 결정할 수 있습니다.
* 원격 분석을 분할하여 계측 키를 구분합니다. 
* 메트릭을 미리 집계합니다. 앱에 TrackMetric에 대한 호출을 추가한 경우 측정 일괄 처리의 평균 및 표준 편차 계산을 허용하는 오버로드를 사용하여 트래픽을 줄일 수 있습니다. 또는 [사전 집계 패키지](https://www.myget.org/gallery/applicationinsights-sdk-labs)를 사용할 수 있습니다. 

## <a name="sampling"></a>샘플링
[샘플링](app-insights-sampling.md) 은 진단 검색 중에 관련 이벤트를 찾는 기능은 계속 유지하고 올바른 이벤트 개수를 유지하면서 앱에 원격 분석이 전송되는 속도를 줄이는 방법입니다. 

샘플링은 요금을 줄이고 월간 할당량 내로 유지하는 효과적인 방법입니다. 샘플링 알고리즘은 예를 들어 검색을 사용하면 특정 예외와 관련된 요청을 찾을 수 있도록 원격 분석의 관련된 항목을 유지합니다. 알고리즘은 또한 요청 속도, 예외 속도 및 기타 카운트에 대해 메트릭 탐색기에 올바른 값을 확인할 수 있도록 올바른 카운트를 유지합니다.

샘플링에는 여러 가지 유형이 있습니다.

* [적응 샘플링](app-insights-sampling.md) 은 ASP.NET SDK에 대한 기본값이며 앱이 보내는 원격 분석의 양을 자동으로 조정합니다. 웹앱의 SDK에서 자동으로 작동하여 네트워크에서 원격 분석 트래픽이 감소됩니다. 
* *수집 샘플링* 은 한 가지 대안으로서 앱의 원격 분석이 Application Insights 서비스에 들어가는 지점에서 작동합니다. 앱에서 보낸 원격 분석의 양에 영향을 주지 않지만 서비스에서 보존하는 양을 줄여 줍니다. 브라우저 및 다른 SDK의 원격 분석에서 사용한 할당량을 줄이기 위해 사용할 수 있습니다.

수집 샘플링을 설정하려면 가격 책정 블레이드에서 컨트롤을 설정합니다.

![할당량 및 가격 책정 블레이드에서 샘플 타일을 클릭하고 샘플링 비율을 선택합니다.](./media/app-insights-pricing/04.png)

> [!WARNING]
> 보존되는 샘플 타일에 표시되는 값은 수집 샘플링에 대해 설정한 값만을 나타냅니다. 앱의 SDK에서 작동되는 샘플링 속도를 표시하지 않습니다. 
> 
> 들어오는 원격 분석이 SDK에서 이미 샘플링된 경우 수집 샘플링은 적용되지 않습니다.
> 
> 

적용된 위치에 관계 없이 실제 샘플링 주기를 검색하려면 다음과 같은 [분석 쿼리](app-insights-analytics.md) 를 사용합니다.

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

보존된 각 레코드에서 `itemCount`은 나타내는 원래 레코드 수를 나타내며 1 + 이전에 삭제된 레코드의 수와 같습니다. 

## <a name="which-pricing-plan-should-i-choose"></a>어떤 가격 책정 계획을 선택해야 하나요?

엔터프라이즈 계획에서 제공하는 고급 기능이 필요하지 않는 한 기본 계획이 보다 간단하면서도 좀 더 비용 효율적입니다. 월별 앱당 무료 데이터 할당량을 받은 다음 앱에서 보낸 원격 분석의 추가 GB당 요금이 부과됩니다. 

## <a name="nodes-in-the-enterprise-plan"></a>엔터프라이즈 계획에서의 노드

엔터프라이즈 가격 책정 계획을 선택하면 요금의 일부는 Application Insights로 데이터를 전송하는 노드 수로 계산됩니다.

노드는 응용 프로그램을 호스트하는 서버입니다. 가상 컴퓨터, Platform-as-a-Service 인스턴스 또는 물리적 컴퓨터일 수 있습니다. 

노드 수에는 디버깅 중에 응용 프로그램을 실행하는 개발자 워크스테이션이 포함되지 않습니다. 브라우저 또는 모바일 장치에서 실행되는 클라이언트 앱은 포함되지 않습니다.

노드는 매 시간마다 계산됩니다. 노드 가격은 월별로 명시되지만 실제로 가격은 시간당으로 부과되므로 한 달에 몇 시간 동안 원격 분석을 보내는 노드에 대해서는 요금이 적게 듭니다.

많거나 적은 서버 인스턴스를 사용하는 다양한 부하에 따라 응용 프로그램의 크기가 조정되는 경우에는 Application Insights 엔터프라이즈 계획 요금도 마찬가지로 확장 및 축소됩니다.

노드는 앱 간에 공유할 수 있습니다. 예를 들어 두 개의 VM에서 실행 중인 세 개의 응용 프로그램이 있고 이러한 응용 프로그램에 대한 Application Insights 리소스가 동일한 구독 및 엔터프라이즈 계획인 경우에는 이 구독에서의 노드 수는 2입니다.

일별 노드당 데이터 허용량 200MB는 동일한 구독의 노드 간에 풀링됩니다. 엔터프라이즈 계획에서 앱을 호스트하는 두 개의 노드가 있고 하루에 16시간 및 20시간 동안 데이터를 전송하는 경우에는 해당 날짜의 데이터 허용량은 ((16+20)/24)*200 MB = 300MB입니다. 그 날 늦게 엔터프라이즈 계획의 앱에서 300MB를 초과하여 데이터를 전송한 경우에는 초과한 GB당 요금이 부과됩니다.

엔터프라이즈 계획 허용량은 기본 계획을 선택한 응용 프로그램과는 공유되지 않습니다.

## <a name="transition-from-the-old-pricing-tiers"></a>이전 가격 책정 계층에서 전환

기존 응용 프로그램은 2017년 2월까지 이전 가격 책정 계층을 계속해서 사용할 수 있습니다. 2017년 2월 이후 대부분의 응용 프로그램은 자동으로 기본 계획으로 이동됩니다. 연속 내보내기 또는 OMS Log Analytics용 커넥터를 사용하는 응용 프로그램은 엔터프라이즈 계획으로 이동됩니다.


## <a name="limits-summary"></a>제한 요약
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>다음 단계

* [샘플링](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/




<!--HONumber=Dec16_HO1-->


