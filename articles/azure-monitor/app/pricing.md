---
title: Azure Application Insights의 사용량 및 비용 관리 | Microsoft Docs
description: Application Insights에서 원격 분석을 관리하고 비용을 모니터링합니다.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 05/29/2019
ms.author: dalek
ms.openlocfilehash: ebcb0922335a2bdc5423ec4e4bfce7c1cd71c46a
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357272"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Application Insights의 사용량 및 비용 관리

> [!NOTE]
> 이 문서에서는 데이터 사용량 Application Insights를 분석하는 방법을 설명합니다.  관련 정보는 다음 문서를 참조하세요.
> - [사용량 및 예상 비용 모니터링](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md)에서는 다른 가격 책정 모델에 대해 여러 Azure 모니터링 기능에서 사용량 및 예상 비용을 보는 방법을 설명합니다. 또한 가격 책정 모델을 변경하는 방법을 설명합니다.

Application Insights의 가격 책정 방식에 대해 궁금한 사항이 있으면 [포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights)에 질문을 게시할 수 있습니다.

## <a name="pricing-model"></a>가격 책정 모델

[Azure Application Insights][start]의 가격 책정은 수집된 데이터 볼륨을 기준으로 합니다. 각 Application Insights 리소스는 별도의 서비스로 요금이 부과되고 Azure 구독에 대한 청구서에 추가됩니다.

### <a name="data-volume-details"></a>데이터 볼륨 세부 정보

* 데이터 볼륨은 Application Insights에서 받은 원격 분석의 바이트 수입니다. 데이터 볼륨은 애플리케이션의 Application Insights에서 받은 압축되지 않은 JSON 데이터 패키지의 크기로 측정됩니다. [Analytics로 가져온 표 형식 데이터](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import)의 경우 데이터 볼륨은 Application Insights로 전송된 파일의 압축되지 않은 크기로 측정됩니다.
* 이제 2018년 4월부터 애플리케이션의 데이터 볼륨 요금이 **데이터 수집**이라는 새로운 청구 미터에 보고됩니다. 이 새 미터 모니터링 Application Insights 및 Log Analytics와 같은 기술을 공유할 이며 현재 서비스 이름으로 **Log Analytics**합니다. 
* [라이브 메트릭 스트림](../../azure-monitor/app/live-stream.md) 데이터는 가격 책정에 계산 되지 않습니다.

사용자의 통화 및 지역에 따른 현재 가격은 [Application Insights 가격 책정][pricing]을 참조하세요.

### <a name="multi-step-web-tests"></a>다중 단계 웹 테스트

[다중 단계 웹 테스트](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests)는 추가 요금이 발생합니다. 다중 단계 웹 테스트는 일련의 작업을 수행하는 웹 테스트입니다.

단일 페이지의 *ping 테스트*에 대해 별도의 요금이 부과되지 않습니다. ping 테스트와 다중 단계 테스트의 원격 분석은 앱의 다른 원격 분석과 동일하게 청구됩니다.

## <a name="review-usage-and-estimate-costs"></a>사용량 검토 및 비용 예상

Application Insights를 사용하면 최근 사용 패턴에 따른 예상 비용을 쉽게 파악할 수 있습니다. 시작하려면 Application Insights 리소스의 경우 Azure Portal에서 **사용량 및 예상 비용** 페이지로 이동합니다.

![가격 책정 선택](./media/pricing/pricing-001.png)

a. 해당 월의 데이터 볼륨을 검토합니다. 여기에는 서버 및 클라이언트 앱과 가용성 테스트에서 수신되고 보유되는([샘플링](../../azure-monitor/app/sampling.md) 이후) 모든 데이터가 포함됩니다.  
B. [다단계 웹 테스트](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests)에 대해서는 별도 요금이 부과됩니다. (여기에는 간단한 가용성 테스트는 포함되지 않습니다. 이 테스트의 경우 데이터 볼륨 요금에 포함됩니다.)  
C. 지난 달의 데이터 볼륨 추세를 봅니다.  
D. 데이터 수집 [샘플링](../../azure-monitor/app/sampling.md)을 사용합니다.   
E. 일일 데이터 볼륨 한도를 설정합니다.  

Application Insights 사용량을 보다 자세히 조사하려면 **메트릭** 페이지를 열고, "데이터 요소 볼륨"이라는 메트릭을 추가한 다음, *분할 적용* 옵션을 선택하여 데이터를 "원격 분석 항목 유형"에 따라 분할합니다. 

Application Insights 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure Portal의 **청구** 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다. 

![왼쪽 메뉴에서 청구를 선택합니다.](./media/pricing/02-billing.png)

## <a name="data-rate"></a>데이터 속도
보내는 데이터의 볼륨은 세 가지 방법으로 제한됩니다.

* **샘플링**: 샘플링을 사용하여 메트릭 왜곡을 최소화하면서 서버 및 클라이언트 앱에서 전송되는 원격 분석의 양을 줄일 수 있습니다. 샘플링은 보내는 데이터의 양을 조정하는 데 사용할 수 있는 기본 도구입니다. [샘플링 기능](../../azure-monitor/app/sampling.md)에 대해 자세히 알아보세요. 
* **일일 한도**: Azure Portal에서 Application Insights 리소스를 만들 때 일일 한도는 100GB/일로 설정됩니다. Visual Studio에서 Application Insights 리소스를 만들 때 기본값은 적습니다(불과 32.3MB/일). 일일 한도 기본값은 테스트가 용이하도록 설정됩니다. 앱을 프로덕션에 배포하기 전에 사용자가 일일 한도를 높여야 합니다. 

    트래픽이 많은 애플리케이션에 대해 더 높은 최대값을 요구하지 않으면 최대 한도는 하루 1,000GB입니다. 

    일일 한도를 설정할 때 주의해야 합니다. *일일 한도에 도달하지 않도록* 해야 합니다. 일일 한도에 도달하는 경우 해당 날짜의 남은 기간 동안 데이터가 손실되고 애플리케이션을 모니터링할 수 없습니다. 일일 한도를 변경하려면 **일일 볼륨 한도** 옵션을 사용합니다. **사용량 및 예상 비용** 창에서 이 옵션에 액세스할 수 있습니다(문서의 뒷부분에 자세히 설명되어 있음).
    Application Insights에 대해 사용될 수 없는 크레딧이 있는 일부 구독 유형에 대한 제한을 제거했습니다. 이전에 구독에 지출 한도가 있는 경우 일일 한도 대화 상자에 지출 한도를 제거하고 일일 한도가 하루 32.3MB가 넘도록 설정하는 지침이 표시됩니다.
* **제한**: 제한은 데이터 속도를 계측 키당 평균 1분 이상 초당 32,000개 이벤트로 제한합니다.

*내 앱에서 제한 속도를 초과하면 어떻게 되나요?*

* 앱에 보내는 데이터의 양은 1분마다 평가됩니다. 해당 분에 대한 평균 초당 속도를 초과하면 서버는 일부 요청을 거부합니다. SDK는 데이터를 버퍼링한 다음, 다시 전송하도록 시도합니다. 몇 분 동안 급증을 분산시킵니다. 앱이 제한율 이상으로 일관되게 데이터를 보내는 경우 일부 데이터가 삭제됩니다. (ASP.NET, Java 및 JavaScript SDK는 이러한 방식으로 데이터 재전송을 시도합니다. SDK는 제한된 데이터를 제거하기만 합니다.) 제한이 발생하는 경우 이를 경고하는 알림이 표시됩니다.

*앱에서 보내는 데이터의 양을 어떻게 알 수 있습니까?*

다음 옵션 중 하나를 사용하여 앱에서 보내는 데이터 양을 확인할 수 있습니다.

* **사용량 및 예상 비용** 창으로 이동하여 일일 데이터 볼륨 차트를 표시합니다. 
* 메트릭 탐색기에서 새 차트를 추가합니다. 차트 메트릭의 경우 **데이터 요소 볼륨**을 선택합니다. **그룹화**를 킨 다음, **데이터 형식**을 기준으로 그룹화합니다.

## <a name="reduce-your-data-rate"></a>데이터 속도 줄이기
다음 작업을 수행하여 데이터 볼륨을 줄일 수 있습니다.

* [샘플링](../../azure-monitor/app/sampling.md)을 사용합니다. 이 기술은 메트릭을 기울이지 않고 데이터의 속도를 줄입니다. 검색에서 관련된 항목 간을 탐색하는 기능을 손실하지 않습니다. 서버 앱에서는 샘플링이 자동으로 수행됩니다.
* [보고될 수 있는 Ajax 호출 수를 제한](../../azure-monitor/app/javascript.md#detailed-configuration) 하거나 Ajax 보고를 해제합니다.
* [ApplicationInsights.config를 편집](../../azure-monitor/app/configuration-with-applicationinsights-config.md)하여 필요하지 않은 컬렉션 모듈을 끕니다. 예를 들어 성능 카운터 또는 종속성 데이터가 필요하지 않다고 결정할 수 있습니다.
* 별도 계측 키에서 원격 분석을 분할합니다. 
* 메트릭을 미리 집계합니다. 앱에 TrackMetric에 대한 호출을 추가한 경우 측정 일괄 처리의 평균 및 표준 편차 계산을 허용하는 오버로드를 사용하여 트래픽을 줄일 수 있습니다. 또는 [사전 집계 패키지](https://www.myget.org/gallery/applicationinsights-sdk-labs)를 사용할 수 있습니다.

## <a name="manage-the-maximum-daily-data-volume"></a>최대 일일 데이터 볼륨 관리

일일 볼륨 한도를 사용하여 수집된 데이터를 제한할 수 있습니다. 그러나 한도가 충족되는 경우 해당 날짜의 나머지 기간 동안 애플리케이션에서 보낸 모든 원격 분석의 손실이 발생합니다. 애플리케이션이 일일 한도에 도달하는 것은 *권장되지 않습니다*. 일일 한도에 도달한 후 애플리케이션의 상태 및 성능을 추적할 수 없습니다.

일별 볼륨 한도를 사용하는 대신 [샘플링](../../azure-monitor/app/sampling.md)을 사용하여 원하는 수준으로 데이터 볼륨을 조정합니다. 그런 다음, 애플리케이션이 예기치 않게 높은 볼륨의 원격 분석을 보내기 시작하는 경우 "최후의 수단"으로만 일일 한도를 사용합니다.

일일 한도를 변경하려면 Application Insights 리소스의 **구성** 섹션의 **사용량 및 예상 비용** 창에서 **일일 상한**을 선택합니다.

![일별 원격 분석 볼륨 한도 조정](./media/pricing/pricing-003.png)

## <a name="sampling"></a>샘플링
[샘플링](../../azure-monitor/app/sampling.md)은 진단 검색 중에 관련 이벤트를 찾는 기능은 유지하면서 앱에 원격 분석이 전송되는 속도를 줄이는 방법입니다. 또한 올바른 이벤트 개수를 유지합니다.

샘플링은 요금을 줄이고 월간 할당량 내로 유지하는 효과적인 방법입니다. 샘플링 알고리즘은 예를 들어 검색을 사용하면 특정 예외와 관련된 요청을 찾을 수 있도록 원격 분석의 관련된 항목을 유지합니다. 알고리즘은 또한 요청 속도, 예외 속도 및 기타 카운트에 대해 메트릭 탐색기에 올바른 값을 확인할 수 있도록 올바른 카운트를 유지합니다.

샘플링에는 여러 가지 유형이 있습니다.

* [적응 샘플링](../../azure-monitor/app/sampling.md)은 ASP.NET SDK에 대한 기본값입니다. 적응 샘플링은 앱이 보내는 원격 분석의 양을 자동으로 조정합니다. 웹앱의 SDK에서 자동으로 작동하여 네트워크에서 원격 분석 트래픽이 감소됩니다. 
* *수집 샘플링*은 한 가지 대안으로서 앱의 원격 분석이 Application Insights 서비스에 들어가는 지점에서 작동합니다. 수집 샘플링은 앱에서 보낸 원격 분석의 양에 영향을 주지 않지만 서비스에서 보존하는 양을 줄여 줍니다. 수집 샘플링을 사용하여 브라우저 및 다른 SDK의 원격 분석에서 사용한 할당량을 줄일 수 있습니다.

수집 샘플링을 설정하려면 **가격 책정** 창으로 이동합니다.

![할당량 및 가격 책정 창에서 샘플 타일을 선택한 다음, 샘플링 비율을 선택합니다.](./media/pricing/pricing-004.png)

> [!WARNING]
> **데이터 샘플링** 창은 수집 샘플링의 값만을 제어합니다. 앱에 있는 Application Insights SDK에서 적용하는 샘플링 속도를 반영하지 않습니다. 들어오는 원격 분석이 SDK에서 이미 샘플링된 경우 수집 샘플링은 적용되지 않습니다.
>

적용된 위치에 관계 없이 실제 샘플링 주기를 검색하려면 [Analytics 쿼리](analytics.md)를 사용합니다. 쿼리는 다음과 같습니다.

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

보존된 각 레코드에서 `itemCount`은 나타내는 원래 레코드 수를 나타냅니다. 1 + 이전에 삭제된 레코드의 수와 같습니다. 

## <a name="limits-summary"></a>제한 요약

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>일일 상한도 이메일을 사용하지 않도록 설정

일일 볼륨 상한 이메일을 사용하지 않도록 설정하려면 Application Insights 리소스의 **구성** 섹션 아래에 있는 **사용량 및 예상 비용** 창에서 **일일 상한**을 선택합니다. 상한에 도달할 때, 그리고 조정 가능한 경고 수준에 도달할 때 이메일을 보내는 설정이 있습니다. 볼륨 관련 모든 일일 한도 사용 하지 않도록 설정 하려는 경우 전자 메일 상자 모두 선택 취소 합니다.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>가격 책정 계층 레거시 Enterprise (노드)

Azure Application Insights의 초기 도입자에 대 한 가지 여전히 두 가지 가능한 가격 책정 계층 Basic 및 Enterprise의 두 가지 가격 책정 요금제가 있습니다. 기본 가격 책정 계층 위에서 설명한 것 처럼 동일한 이며 기본 계층입니다. 추가 비용 없이 모든 Enterprise 계층 기능을 포함합니다. 주로 수집 된 데이터 양에 따라 기본 계층 청구 합니다. 

> [!NOTE]
> 이러한 레거시 가격 책정 계층 변경 되었습니다. 엔터프라이즈 가격 책정 계층 이라고 **노드당** 가격 책정 계층은 Basic 라고 하 고 **GB 당**합니다. 아래 및 Azure portal에서 이러한 새 이름은 사용 합니다.  

노드 (이전의 Enterprise) 계층에는 노드당 요금을 하 고 각 노드는 일일 데이터 허용량을 받습니다. 노드당 가격 책정 계층에 포함된 된 허용량 이상으로 수집 된 데이터에 대 한 요금이 청구 됩니다. Operations Management Suite를 사용 하는 경우 노드당 계층을 선택 해야 합니다. 

사용자의 통화 및 지역에 따른 현재 가격은 [Application Insights 가격 책정](https://azure.microsoft.com/pricing/details/application-insights/)을 참조하세요.

> [!NOTE]
> 2018년 4월에는 Azure 모니터링을 위한 새로운 가격 책정 모델이 [도입](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)되었습니다. 이 모델은 모니터링 서비스의 전체 포트폴리오에서 간단한 "종량제" 모델을 채택합니다. [새 가격 책정 모델](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), 사용 패턴에 따라 [이 모델로 전환하는 영향을 평가](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model)하는 방법 및 [새 모델을 옵트인하는 방법](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)에 대해 자세히 알아봅니다.

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>노드 계층 및 Operations Management Suite 구독 자격

Operations Management Suite E1 및 E2를 구매 하는 고객으로 추가 비용 없이 추가 구성 요소로 노드당 Application Insights를 가져올 수 있습니다 [발표](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)합니다. 특히, Operations Management Suite E1 및 E2의 각 단위에는 Application Insights 당 노드 계층의 노드 하나에 대 한 자격이 포함 됩니다. 각 Application Insights 노드에는 매일 수집되는 데이터가 200MB까지 포함되고(Log Analytics 데이터 수집과 별도) 데이터는 추가 비용 없이 90일 간 보존됩니다. 계층은이 문서의 뒷부분에 자세히 설명 되어 있습니다. 

이 계층을 Operations Management Suite 구독이 있는 고객에만 적용 되므로, Operations Management Suite 구독이 없는 고객은이 계층을 선택 하는 옵션이 표시 되지 않습니다.

> [!NOTE]
> 이 자격은 표시 하도록 Application Insights 리소스 당 노드 가격 책정 계층에에서 있어야 합니다. 이 자격은 노드로만 적용됩니다. GB 당 계층에서 application Insights 리소스는 이점이 없습니다. 이 자격은 **사용량 및 예상 비용** 창에 표시된 예상 비용에서 볼 수 없습니다. 또한 새 azure 모니터링 2018 년 4 월 가격 책정 모델로 구독을 이동 하는 경우는 GB 당 계층은 사용 가능한 유일한 계층입니다. Operations Management Suite 구독이 있는 경우 새 Azure 모니터링 가격 책정 모델로 구독을 이동하는 것을 권장하지 않습니다.

### <a name="how-the-per-node-tier-works"></a>노드당 계층의 작동 원리

* 노드당 계층의 모든 앱에 대 한 원격 분석을 전송 하는 각 노드에 대 한 비용을 지불 합니다.
  * *노드*는 앱을 호스트하는 실제/가상 서버 컴퓨터 또는 PaaS(Platform-as-a-Service) 역할 인스턴스입니다.
  * 개발 컴퓨터, 클라이언트 브라우저 및 모바일 디바이스는 노드로 계산되지 않습니다.
  * 원격 분석을 보내는 여러 구성 요소(예: 웹 서비스 및 백 엔드 작업자)가 앱에 있는 경우 해당 구성 요소는 개별적으로 집계됩니다.
  * [라이브 메트릭 스트림](../../azure-monitor/app/live-stream.md) 데이터는 가격 책정에 계산 되지 않습니다. 구독에서 요금은 앱 기준이 아니라 노드 기준으로 부과됩니다. 12개 앱에 대해 원격 분석을 보내는 노드가 5개 있는 경우 5개 노드에 대한 요금이 부과됩니다.
* 요금이 매월 견적되지만 노드에서 앱의 원격 분석을 보내는 모든 시간에 대해서만 부과됩니다. 시간당 요금은 견적된 월별 요금을 744(한 달이 31일일 때 시간 수)로 나눈 것입니다.
* 시간별로 감지되는 각 노드에 대해 1일 200MB의 데이터 볼륨 할당이 제공됩니다. 사용되지 않은 데이터 할당은 다음 날로 이월되지 않습니다.
  * 노드당 가격 책정 계층을 선택 하면 각 구독은 해당 구독의 Application Insights 리소스에 원격 분석을 보내는 노드 수에 따라 데이터의 일일 허용량을 받습니다. 따라서 하루 종일 데이터를 전송하는 5개의 노드가 있는 경우 해당 구독의 모든 Application Insights 리소스에 풀링된 1GB 허용량이 적용됩니다. 모든 노드에서 포함된 데이터를 공유하기 때문에 특정 노드에서 다른 노드보다 더 많은 데이터를 보내는 것은 중요하지 않습니다. 지정된 된 날에 Application Insights 리소스를 받을 경우이 구독에 대 한 일일 데이터 할당에 포함 된 것 보다 더 많은 데이터를 GB 당 초과분 데이터 요금이 적용 됩니다. 
  * 일일 데이터 허용량은 각 노드에서 원격 분석을 보내는 날의 시간 수(UTC 사용)를 24로 나눈 값에 200MB를 곱해 계산합니다. 따라서 하루 24시간 중 15시간 동안 원격 분석을 보내는 4개의 노드가 있는 경우 해당일의 포함된 데이터는 ((4 x 15) / 24) x 200MB = 500MB가 됩니다. 데이터 초과분의 가격 조건이 GB당 2.30 달러일 때 노드에서 해당일 1GB의 데이터를 보내는 경우 요금은 1.15달러입니다.
  * 노드당 계층 일일 허용량은 GB 당 계층은 선택한 응용 프로그램과 공유 되지 않습니다. 사용하지 않는 허용량은 다음 날로 넘어가지 않습니다. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>고유 노드 수를 결정하는 방법 예제

| 시나리오                               | 일일 총 노드 수 |
|:---------------------------------------|:----------------:|
| 1개 애플리케이션에서 3개 Azure App Service 인스턴스 및 1개 가상 서버를 사용합니다. | 4 |
| 2 개 Vm에서 실행 되는 3 개 응용 프로그램 이러한 응용 프로그램에 대 한 Application Insights 리소스가 동일한 구독에 노드당 계층 | 2 | 
| 4개의 애플리케이션은 해당 Application Insights 리소스가 같은 구독에 있고 각 애플리케이션은 사용률이 낮은 16시간 동안 2개의 인스턴스를 실행하고, 사용률이 최대로 높은 8시간 동안 4개의 인스턴스를 실행합니다. | 13.33 | 
| 1개 작업자 역할 및 1개 웹 역할이 부여된 클라우드 서비스에서 각 역할마다 2개 인스턴스를 실행합니다. | 4 | 
| 5개 노드 Azure Service Fabric 클러스터에서 50개의 마이크로 서비스를 실행하며 각 마이크로 서비스에서 3개의 인스턴스를 실행합니다. | 5|

* 정확한 노드 계산은 애플리케이션에서 사용하는 Application Insights SDK에 따라 다릅니다. 
  * SDK 버전 2.2 이상에서 Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 및 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 둘 다 각 애플리케이션 호스트를 노드로 보고합니다. 물리적 서버 및 VM 호스트에 대한 컴퓨터 이름이나 클라우드 서비스에 대한 인스턴스 이름을 예로 들 수 있습니다.  유일한 예외는 [.NET Core](https://dotnet.github.io/) 및 Application Insights Core SDK만 사용하는 애플리케이션입니다. 이 경우 호스트 이름을 사용할 수 없으므로 모든 호스트에 대해 하나의 노드만 보고됩니다. 
  * 이전 버전의 SDK에서는 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)가 최신 SDK 버전과 마찬가지로 작동하지만, [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)는 애플리케이션 호스트 수에 관계 없이 하나의 노드만 보고합니다. 
  * 애플리케이션에서 SDK를 사용하여 **roleInstance**를 사용자 지정 값으로 설정하는 경우 기본적으로 이 동일한 값이 노드 수를 결정하는 데 사용됩니다. 
  * 클라이언트 컴퓨터 또는 모바일 디바이스에서 실행되는 앱과 함께 새 SDK 버전을 사용하는 경우, 노드 계산 시 매우 큰 숫자가 반환될 수 있습니다(클라이언트 컴퓨터 또는 모바일 디바이스 수가 많으므로). 

## <a name="automation"></a>Automation

Azure 리소스 관리를 사용 하 여 가격 책정 계층을 설정 하는 스크립트를 작성할 수 있습니다. [방법을 알아보세요](powershell.md#price).


## <a name="next-steps"></a>다음 단계

* [샘플링](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
