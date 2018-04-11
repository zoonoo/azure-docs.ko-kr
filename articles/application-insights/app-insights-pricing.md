---
title: Azure Application Insights의 가격 책정 및 데이터 볼륨 관리 | Microsoft Docs
description: Application Insights에서 원격 분석을 관리하고 비용을 모니터링합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Application Insights에서 가격 및 데이터 볼륨 관리

[Azure Application Insights][start]의 가격 책정은 응용 프로그램당 데이터 볼륨을 기반으로 합니다. 각 Application Insights 리소스는 별도의 서비스로 요금이 부과되고 Azure 구독에 대한 청구서에 추가됩니다.

가격 책정 계획에는 두 가지가 있습니다. 기본 계획은 주로 수집된 데이터 볼륨에 대한 추가 비용 및 청구서 없이 엔터프라이즈 계획과 동일한 기능을 모두 포함하는 기본이라고 부릅니다. Operations Management Suite를 사용하는 경우 노드당 요금과 함께 일일 데이터 허용량이 있는 엔터프라이즈 계획을 선택해야 하며 포함된 허용량 외에 수집된 데이터에 대해 요금을 청구합니다.

Application Insights의 가격 책정 방식에 대해 궁금한 사항이 있으면 [포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights)에 질문을 게시해 주세요.

## <a name="the-price-plans"></a>가격 계획

해당 통화 및 지역의 현재 가격은 [Application Insights 가격 책정 페이지][pricing]를 참조하세요.

### <a name="basic-plan"></a>기본 계획

기본 계획은 새 Application Insights 리소스를 만들 때의 기본값이며 Operations Management Suite 구독이 있는 고객을 제외한 모든 고객에 대해 최적화됩니다.

* 기본 계획에서는 데이터 볼륨(Application Insights에서 받은 원격 분석의 바이트 수)에 따라 요금이 청구됩니다. 데이터 볼륨은 응용 프로그램의 Application Insights에서 받은 압축되지 않은 JSON 데이터 패키지의 크기로 측정됩니다.
For [분석으로 가져온 표 형식 데이터](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import)의 경우 데이터 볼륨은 Application Insights로 전송된 파일의 압축되지 않은 크기로 측정됩니다.
* [라이브 메트릭 스트림](app-insights-live-stream.md) 데이터는 가격 책정에 계산 되지 않습니다.
* 2018년 4월 현재 기본 계획에서 [연속 내보내기](app-insights-export-telemetry.md) 및 [Log Analytics 커넥터](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409)는 추가 요금 없이 사용할 수 있습니다.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>엔터프라이즈 계획 및 Operations Management Suite 구독 자격

[전에 발표](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)된 대로 Microsoft Operations Management Suite E1 및 E2를 구매하는 고객은 추가 비용 없이 Application Insights Enterprise를 추가 구성 요소로 얻을 수 있습니다. 특히, Operations Management Suite E1 및 E2의 각 구매 단위에는 Application Insights 엔터프라이즈 계획의 노드 1개에 대한 자격이 포함됩니다. 아래 더 자세히 설명된 대로 각 Application Insights 노드에는 매일 수집되는 데이터가 200MB까지 포함되고(Log Analytics 데이터 수집과 별도) 데이터는 추가 비용 없이 90일 간 보존됩니다. 이 옵션은 Operations Management Suite 구독이 있는 고객에게만 적용 가능하므로 구독 없는 고객에게는 이 계획을 선택하는 옵션이 표시되지 않습니다.

> [!NOTE]
> 이 자격을 얻으려면 엔터프라이즈 가격 계획에 Application Insights 리소스가 있어야 합니다. 이 자격은 노드로만 적용되므로 기본 계획의 Application Insights 리소스는 아무런 이점도 없습니다. 이 자격은 *사용량 및 예상 비용* 페이지에 표시된 예상 비용에서 볼 수 없습니다. 또한 2018년 4월의 새 Azure 가격 책정 모델 모니터링 구독으로 이동하는 경우 기본 계획은 사용할 수 있는 유일한 계획이므로 Operations Management Suite 구독이 있는 경우 적절하지 않습니다.

엔터프라이즈 계획에 관한 자세한 내용은 [엔터프라이즈 가격 책정 세부 정보 페이지](app-insights-pricing-enterprise-details.md)를 참조하세요.

### <a name="multi-step-web-tests"></a>다중 단계 웹 테스트

[다중 단계 웹 테스트](app-insights-monitor-web-app-availability.md#multi-step-web-tests)에 대한 추가 요금이 있습니다. 이는 일련의 작업을 수행하는 웹 테스트를 나타냅니다.

단일 페이지의 'ping 테스트'에 대해 별도의 요금이 부과되지 않습니다. ping 테스트와 다중 단계 테스트 모두의 원격 분석은 앱의 다른 원격 분석과 함께 청구됩니다.

## <a name="review-pricing-plans-and-estimate-costs"></a>가격 계획 검토 및 비용 추정

Application Insights를 사용하면 사용 가능한 가격 책정 계획 및 최근 사용 패턴에 따른 가능한 비용을 쉽게 파악할 수 있습니다. 먼저 Azure Portal의 Application Insights 리소스에서 **사용량 및 예상 비용** 페이지를 엽니다.

![가격 책정을 선택합니다.](./media/app-insights-pricing/pricing-001.png)

**a.** 해당 월의 데이터 볼륨을 검토합니다. 여기에는 서버 및 클라이언트 앱과 가용성 테스트에서 수신되고 보유되는([샘플링](app-insights-sampling.md) 이후) 모든 데이터가 포함됩니다.

**b.** [다단계 웹 테스트](app-insights-monitor-web-app-availability.md#multi-step-web-tests)에 대해서는 별도 요금이 부과됩니다. (여기에는 간단한 가용성 테스트는 포함되지 않습니다. 이 테스트의 경우 데이터 볼륨 요금에 포함됩니다.)

**c.** 지난 달의 데이터 볼륨 추세 보기.

**d.** 데이터 수집 [샘플링](app-insights-sampling.md)을 사용합니다. 

**e.** 일일 데이터 볼륨 한도를 구성합니다.

Application Insights 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure 포털의 청구 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다. 

![측면 메뉴에서 대금 청구를 선택합니다.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>데이터 속도
다음 세 가지 방법으로 데이터 전송 볼륨이 제한됩니다.

* **샘플링:** 이 메커니즘은 메트릭 왜곡을 최소화하면서 서버 및 클라이언트 앱에서 전송되는 원격 분석의 양을 줄이는 데 사용할 수 있습니다. 이것은 데이터 양을 조정해야 하는 기본 도구입니다. [샘플링 기능](app-insights-sampling.md)에 대해 자세히 알아보세요. 
* **일일 한도:** Azure Portal에서 Application Insights 리소스를 만들 때 하루 100GB로 설정됩니다. Visual Studio에서 Application Insights 리소스를 만들 경우 테스트를 쉽게 하기 위해 기본값이 작습니다(하루 32.3MB). 이런 경우 앱을 프로덕션에 배포하기 전에 사용자가 일일 상한선을 높여야 합니다. 트래픽이 많은 응용 프로그램에 대해 더 높은 최대값을 요구하지 않으면 최대 한도는 하루 1,000GB입니다. 일일 한도를 설정하는 경우 **일일 한도에 절대 도달하지 않도록** 주의해야 합니다. 만약 한도에 도달하면 그날은 나머지 시간 동안 데이터가 없어서 응용 프로그램을 모니터링할 수 없게 됩니다. 이를 변경하려면 사용량 및 예상 비용 페이지(아래 참조)에서 연결된 일일 볼륨 한도 옵션을 사용합니다. Application Insights에 대해 사용될 수 없는 크레딧이 있는 일부 구독 유형에 대한 제한을 제거했습니다. 구독에 지출 한도가 있는 경우 일일 한도 대화 상자에 일일 한도를 제거하고 일일 한도가 하루 32.3MB가 넘도록 설정하는 지침이 표시됩니다.
* **제한**: 데이터 속도를 평균 1분 이상 초당 32,000개 이벤트로 제한합니다.

*내 앱에서 제한 속도를 초과하면 어떻게 되나요?*

* 앱에 보내는 데이터의 양은 1분마다 평가됩니다. 해당 분에 대한 평균 초당 속도를 초과하면 서버는 일부 요청을 거부합니다. SDK는 데이터를 버퍼링하고, 재전송을 시도하여 몇 분 동안 서지를 분산시킵니다. 앱이 제한율 이상으로 일관되게 데이터를 보내는 경우 일부 데이터가 삭제됩니다. (ASP.NET, Java 및 JavaScript SDK는 이러한 방식으로 재전송을 시도합니다. SDK는 제한된 데이터를 제거하기만 합니다.) 제한이 발생하는 경우 이를 경고하는 알림이 표시됩니다.

*앱에서 보내는 데이터의 양을 어떻게 알 수 있습니까?*

* **사용량 및 예상 비용** 페이지를 열어 일일 데이터 볼륨 차트를 표시합니다. 
* 또는 메트릭 탐색기에서 새 차트를 추가하고 **데이터 요소 볼륨**을 메트릭으로 선택합니다. 그룹화로 전환한 다음 **데이터 형식**을 기준으로 그룹화합니다.

## <a name="to-reduce-your-data-rate"></a>데이터 속도를 줄이려면
다음 작업을 수행하여 데이터 볼륨을 줄일 수 있습니다.

* [샘플링](app-insights-sampling.md)을 사용합니다. 이 기술은 메트릭을 왜곡하지 않으며 검색에서 관련 항목 간 이동 기능을 중단하지 않고 데이터 속도를 낮춥니다. 서버 앱에서는 이 기능이 자동으로 수행됩니다.
* [보고될 수 있는 Ajax 호출 수를 제한](app-insights-javascript.md#detailed-configuration) 하거나 Ajax 보고를 해제합니다.
* [ApplicationInsights.config를 편집](app-insights-configuration-with-applicationinsights-config.md)하여 필요하지 않은 컬렉션 모듈을 끕니다. 예를 들어 성능 카운터 또는 종속성 데이터가 필요하지 않다고 결정할 수 있습니다.
* 원격 분석을 분할하여 계측 키를 구분합니다. 
* 메트릭을 미리 집계합니다. 앱에 TrackMetric에 대한 호출을 추가한 경우 측정 일괄 처리의 평균 및 표준 편차 계산을 허용하는 오버로드를 사용하여 트래픽을 줄일 수 있습니다. 또는 [사전 집계 패키지](https://www.myget.org/gallery/applicationinsights-sdk-labs)를 사용할 수 있습니다.

## <a name="managing-the-maximum-daily-data-volume"></a>최대 일일 데이터 볼륨 관리

일별 볼륨 한도를 사용하여 수집되는 데이터를 제한할 수 있지만 해당 한도가 충족될 경우 해당 일의 나머지 시간 동안 응용 프로그램에서 보낸 모든 원격 분석이 손실됩니다. 일별 한도에 도달한 후에는 응용 프로그램의 상태와 성능을 추적할 수 없으므로 응용 프로그램이 일별 한도에 도달하도록 하는 것은 **권장되지 않습니다**.

대신 [샘플링](app-insights-sampling.md)을 사용하여 원하는 수준으로 데이터 볼륨을 조정하고 응용 프로그램이 예기치 않게 훨씬 더 높은 볼륨의 원격 분석을 전송하기 시작할 때만 "최후의 수단"으로 일별 한도를 사용합니다.

일일 한도를 변경하려면 Application Insights 리소스의 구성 섹션의 **사용량 및 예상 비용** 페이지에서 **일일 상한**을 클릭합니다.

![일별 원격 분석 볼륨 한도 조정](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>샘플링
[샘플링](app-insights-sampling.md) 은 진단 검색 중에 관련 이벤트를 찾는 기능은 계속 유지하고 올바른 이벤트 개수를 유지하면서 앱에 원격 분석이 전송되는 속도를 줄이는 방법입니다.

샘플링은 요금을 줄이고 월간 할당량 내로 유지하는 효과적인 방법입니다. 샘플링 알고리즘은 예를 들어 검색을 사용하면 특정 예외와 관련된 요청을 찾을 수 있도록 원격 분석의 관련된 항목을 유지합니다. 알고리즘은 또한 요청 속도, 예외 속도 및 기타 카운트에 대해 메트릭 탐색기에 올바른 값을 확인할 수 있도록 올바른 카운트를 유지합니다.

샘플링에는 여러 가지 유형이 있습니다.

* [적응 샘플링](app-insights-sampling.md) 은 ASP.NET SDK에 대한 기본값이며 앱이 보내는 원격 분석의 양을 자동으로 조정합니다. 웹앱의 SDK에서 자동으로 작동하여 네트워크에서 원격 분석 트래픽이 감소됩니다. 
* *수집 샘플링* 은 한 가지 대안으로서 앱의 원격 분석이 Application Insights 서비스에 들어가는 지점에서 작동합니다. 앱에서 보낸 원격 분석의 양에 영향을 주지 않지만 서비스에서 보존하는 양을 줄여 줍니다. 브라우저 및 다른 SDK의 원격 분석에서 사용한 할당량을 줄이기 위해 사용할 수 있습니다.

수집 샘플링을 설정하려면 가격 책정 대화 상자에서 컨트롤을 설정합니다.

![할당량 및 가격 책정 대화 상자에서 샘플 타일을 클릭하고 샘플링 비율을 선택합니다.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> 데이터 샘플링 대화 상장에서는 수집 샘플링의 값을 제어합니다. 앱에 있는 Application Insights SDK에서 적용하는 샘플링 속도를 반영하지 않습니다. 들어오는 원격 분석이 SDK에서 이미 샘플링된 경우 수집 샘플링은 적용되지 않습니다.
>

적용된 위치에 관계 없이 실제 샘플링 주기를 검색하려면 다음과 같은 [분석 쿼리](app-insights-analytics.md) 를 사용합니다.

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

보존된 각 레코드에서 `itemCount` 은 나타내는 원래 레코드 수를 나타내며 1 + 이전에 삭제된 레코드의 수와 같습니다. 

## <a name="automation"></a>Automation

Azure Resource Management를 사용하여 가격 계획을 설정하는 스크립트를 작성할 수 있습니다. [방법을 알아보세요](app-insights-powershell.md#price).

## <a name="limits-summary"></a>제한 요약

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>다음 단계

* [샘플링](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
