---
title: Azure Application Insights의 사용량 및 비용 관리 | Microsoft Docs
description: Application Insights에서 원격 분석을 관리하고 비용을 모니터링합니다.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9ecd0ffd76650efff3a4c9f877522cba6f28d080
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271117"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Application Insights의 사용량 및 비용 관리

> [!NOTE]
> 이 문서에서는 응용 프로그램 인사이트를 위한 비용을 이해하고 제어하는 방법을 설명합니다.  관련 문서, [모니터링 사용량 및 예상 비용은](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) 다양한 가격 책정 모델에 대한 여러 Azure 모니터링 기능에서 사용량 및 예상 비용을 보는 방법을 설명합니다.

Application Insights는 Azure 또는 온-프레미스에서 호스팅되는 웹 응용 프로그램의 가용성, 성능 및 사용을 모니터링하는 데 필요한 모든 것을 제공하도록 설계되었습니다. 응용 프로그램 인사이트는 .NET, Java 및 Node.js와 같은 인기 있는 언어 및 프레임워크를 지원하며 Azure DevOps, Jira 및 PagerDuty와 같은 DevOps 프로세스 및 도구와 통합됩니다. 응용 프로그램 모니터링 비용을 결정하는 원인을 이해하는 것이 중요합니다. 이 문서에서는 응용 프로그램 모니터링 비용을 유발하는 요인과 응용 프로그램 모니터링 비용을 사전에 모니터링하고 제어하는 방법을 검토합니다.

Application Insights의 가격 책정 방식에 대해 궁금한 사항이 있으면 [포럼](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)에 질문을 게시할 수 있습니다.

## <a name="pricing-model"></a>가격 책정 모델

[Azure 응용 프로그램 인사이트에][start] 대한 가격 책정은 수집된 데이터 볼륨을 기반으로 하는 **종량제** 모델이며 선택적으로 더 긴 데이터 보존을 위해 사용됩니다. 각 Application Insights 리소스는 별도의 서비스로 요금이 부과되고 Azure 구독에 대한 청구서에 추가됩니다. 데이터 볼륨은 애플리케이션의 Application Insights에서 받은 압축되지 않은 JSON 데이터 패키지의 크기로 측정됩니다. [라이브 메트릭 스트림을](../../azure-monitor/app/live-stream.md)사용하는 데 대한 데이터 볼륨 요금이 없습니다.

[다중 단계 웹 테스트](../../azure-monitor/app/availability-multistep.md)는 추가 요금이 발생합니다. 다중 단계 웹 테스트는 일련의 작업을 수행하는 웹 테스트입니다. 단일 페이지의 *ping 테스트*에 대해 별도의 요금이 부과되지 않습니다. ping 테스트와 다중 단계 테스트의 원격 분석은 앱의 다른 원격 분석과 동일하게 청구됩니다.

[사용자 지정 메트릭 차원에 대한 경고 활성화를 활성화하는](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) Application Insights 옵션은 추가 집계 메트릭을 생성할 수 있으므로 추가 비용이 발생할 수도 있습니다. [자세히 알아보기] 응용 프로그램 인사이트에서 로그 기반 및 사전 집계된 메트릭및 Azure Monitor 사용자 지정 메트릭에 대한 [가격 책정에](https://azure.microsoft.com/pricing/details/monitor/) 대해 알아봅니다.

## <a name="estimating-the-costs-to-manage-your-application"></a>응용 프로그램 관리 비용 예측

아직 응용 프로그램 인사이트를 사용하지 않는 경우 [Azure Monitor 가격 계산기를](https://azure.microsoft.com/pricing/calculator/?service=monitor) 사용하여 응용 프로그램 인사이트를 사용하는 비용을 예측할 수 있습니다. 먼저 검색 상자에 "Azure 모니터"를 입력하고 결과 Azure 모니터 타일을 클릭합니다. 페이지를 아래로 스크롤하여 Azure 모니터로 이동한 다음 유형 드롭다운에서 응용 프로그램 정보를 선택합니다.  여기서는 매월 수집할 것으로 예상되는 데이터 GB수를 입력할 수 있으므로 응용 프로그램 인사이트가 응용 프로그램을 모니터링하는 데이터의 양을 확인할 수 있습니다.

이 문제를 해결하기 위한 두 가지 방법이 있습니다: ASP.NET SDK에서 사용할 수 있는 기본 모니터링 및 적응형 샘플링사용 또는 다른 유사한 고객이 본 내용에 따라 가능한 데이터 수집을 추정하는 것입니다.

### <a name="data-collection-when-using-sampling"></a>샘플링 사용 시 데이터 수집

ASP.NET SDK의 [적응형 샘플링을](sampling.md#adaptive-sampling)사용하면 데이터 볼륨이 자동으로 조정되어 기본 Application Insights 모니터링을 위해 지정된 최대 트래픽 속도 내에서 유지됩니다. 응용 프로그램에서 디버깅 시 또는 사용량이 낮음과 같이 적은 양의 원격 분석을 생성하는 경우 볼륨이 초당 구성된 이벤트 수준 보다 낮은 경우 샘플링 프로세서에서 항목을 삭제하지 않습니다. 초당 5개의 이벤트의 기본 임계값인 대용량 응용 프로그램의 경우 적응 샘플링은 일일 이벤트 수를 432,000개로 제한합니다. 일반적인 평균 이벤트 크기가 1KB인 경우, 샘플링이 각 노드에 로컬로 수행되므로 응용 프로그램을 호스팅하는 노드당 31일 월별 13.4GB의 원격 분석을 사용합니다. 

적응형 샘플링을 지원하지 않는 SDK의 경우 유지하려는 데이터의 백분율을 기반으로 Application Insights에서 데이터를 수신할 때 샘플링하는 [수집 샘플링을](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling)사용하거나 [ASP.NET, ASP.NET Core 및 Java 웹 사이트에 대한 고정 비율 샘플링을](sampling.md#fixed-rate-sampling) 사용하여 웹 서버 및 웹 브라우저에서 전송되는 트래픽을 줄일 수 있습니다.

### <a name="learn-from-what-similar-customers-collect"></a>유사 고객이 수집하는 항목에서 알아보기

응용 프로그램 인사이트에 대한 Azure 모니터링 가격 계산기에서 "응용 프로그램 활동에 따라 데이터 볼륨 예측" 기능을 사용하도록 설정하면 응용 프로그램에 대한 입력(클라이언트 측 원격 분석을 수집하는 경우 월별 요청 및 월별 페이지 뷰)을 제공할 수 있으며, 계산기는 유사한 응용 프로그램에서 수집한 데이터의 중앙값 과 90번째 백분위수 양을 알려줍니다. 이러한 응용 프로그램은 응용 프로그램 인사이트 구성 범위(예: 기본 [샘플링이](../../azure-monitor/app/sampling.md)있고 일부는 샘플링이 없는 등)에 걸쳐 있으므로 샘플링을 사용하여 중앙값 수준보다 훨씬 낮은 데이터 볼륨을 줄이는 제어가 가능합니다. 그러나 이것은 다른 유사한 고객이 보고 있는 것을 이해하기 위한 출발점입니다.

## <a name="understand-your-usage-and-estimate-costs"></a>사용량 파악 및 예상 비용

Application Insights를 사용하면 최근 사용 패턴에 따른 예상 비용을 쉽게 파악할 수 있습니다. 시작하려면 Application Insights 리소스의 경우 Azure Portal에서 **사용량 및 예상 비용** 페이지로 이동합니다.

![가격 책정 선택](./media/pricing/pricing-001.png)

A. 해당 월의 데이터 볼륨을 검토합니다. 여기에는 서버 및 클라이언트 앱과 가용성 테스트에서 수신되고 보유되는([샘플링](../../azure-monitor/app/sampling.md) 이후) 모든 데이터가 포함됩니다.  
B. [다단계 웹 테스트](../../azure-monitor/app/availability-multistep.md)에 대해서는 별도 요금이 부과됩니다. (여기에는 간단한 가용성 테스트는 포함되지 않습니다. 이 테스트의 경우 데이터 볼륨 요금에 포함됩니다.)  
C. 지난 달의 데이터 볼륨 추세를 봅니다.  
D. 데이터 수집 [샘플링](../../azure-monitor/app/sampling.md)을 사용합니다.
E. 일일 데이터 볼륨 한도를 설정합니다.  

이 문서의 스크린샷에 표시된 모든 가격은 예를 들어서만 해당됩니다. 통화 및 지역의 현재 가격은 [애플리케이션 인사이트 가격 책정을][pricing]참조하십시오.)

Application Insights 사용량을 보다 자세히 조사하려면 **메트릭** 페이지를 열고, "데이터 요소 볼륨"이라는 메트릭을 추가한 다음, *분할 적용* 옵션을 선택하여 데이터를 "원격 분석 항목 유형"에 따라 분할합니다.

Application Insights 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure Portal의 **청구** 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다.

![왼쪽 메뉴에서 청구를 선택합니다.](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>데이터 볼륨 메트릭 사용
<a id="understanding-ingested-data-volume"></a>

데이터 볼륨에 대해 자세히 알아보려면 Application Insights 리소스에 대한 **메트릭을** 선택하여 새 차트를 추가합니다. 차트 메트릭의 경우 **로그 기반 메트릭**아래에서 데이터 포인트 **볼륨을**선택합니다. **분할 적용을**클릭하고 ** `Telemetryitem` 유형별로**그룹을 선택합니다.

![메트릭을 사용하여 데이터 볼륨 보기](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>데이터 볼륨 세부 정보를 이해하는 쿼리

응용 프로그램 인사이트를 위한 데이터 볼륨을 조사하는 방법에는 두 가지가 있습니다. 첫 번째는 `systemEvents` 테이블에서 집계된 정보를 사용하고 두 `_BilledSize` 번째는 수집된 각 이벤트에서 사용할 수 있는 속성을 사용합니다.

#### <a name="using-aggregated-data-volume-information"></a>집계된 데이터 볼륨 정보 사용

예를 들어 `systemEvents` 테이블을 사용하여 쿼리를 사용하여 지난 24시간 동안 수집된 데이터 볼륨을 확인할 수 있습니다.

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

또는 지난 30일 동안의 데이터 유형별 데이터 볼륨 차트(바이트)를 보려면 다음을 사용할 수 있습니다.

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

이 쿼리는 [Azure 로그 경고에서](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) 데이터 볼륨에 대한 경고를 설정하는 데 사용할 수 있습니다.  

원격 분석 데이터 변경 사항에 대해 자세히 알아보려면 쿼리를 사용하여 입력별로 이벤트 수를 얻을 수 있습니다.

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>이벤트 정보당 데이터 크기 사용

데이터 볼륨의 원본에 대한 자세한 내용을 보려면 `_BilledSize` 수집된 각 이벤트에 있는 속성을 사용할 수 있습니다.

예를 들어 지난 30일 동안 가장 많은 데이터 볼륨을 생성하는 `_BilledSize` 작업을 보려면 모든 종속성 이벤트에 대해 합산할 수 있습니다.

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Azure 청구서에서 응용 프로그램 인사이트 사용량 보기

Azure는 [Azure 비용 관리 + 청구](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) 허브에서 많은 유용한 기능을 제공합니다. 예를 들어 "비용 분석" 기능을 사용하면 Azure 리소스에 대한 지출을 볼 수 있습니다. 리소스 유형별 필터를 추가하면(응용 프로그램 인사이트용 microsoft.insights/구성 요소)을 사용하면 지출을 추적할 수 있습니다.

[Azure 포털에서 사용량을 다운로드하여 사용량에](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)대한 더 많은 이해를 얻을 수 있습니다.
다운로드한 스프레드시트에서 하루에 Azure 리소스당 사용량을 볼 수 있습니다. 이 Excel 스프레드시트에서 먼저 "미터 범주" 열을 필터링하여 "응용 프로그램 인사이트" 및 "로그 분석"을 표시한 다음 "microsoft.insights/components"인 "인스턴스 ID" 열에 필터를 추가하여 응용 프로그램 인사이트 리소스의 사용량을 확인할 수 있습니다.  모든 Azure Monitor 구성 요소에 대한 단일 로그 백엔드가 있기 때문에 대부분의 응용 프로그램 인사이트 사용량은 로그 분석의 미터 범주가 있는 미터에서 보고됩니다.  레거시 가격 책정 계층 및 다단계 웹 테스트에 대한 응용 프로그램 인사이트 리소스만 미터 범주의 응용 프로그램 인사이트와 함께 보고됩니다.  사용량은 "소모된 수량" 열에 표시되고 각 항목의 단위는 "측정 단위" 열에 표시됩니다.  자세한 내용은 [Microsoft Azure 청구서를 이해하는](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)데 도움이 됩니다.

## <a name="managing-your-data-volume"></a>데이터 볼륨 관리

보내는 데이터의 양은 다음 기술을 사용하여 관리할 수 있습니다.

* **샘플링:** 샘플링을 사용하여 메트릭 왜곡을 최소화하면서 서버 및 클라이언트 앱에서 전송되는 원격 분석의 양을 줄일 수 있습니다. 샘플링은 보내는 데이터의 양을 조정하는 데 사용할 수 있는 기본 도구입니다. [샘플링 기능](../../azure-monitor/app/sampling.md)에 대해 자세히 알아보세요.

* **Ajax 통화 제한**: 모든 페이지 보기에서 [보고할 수 있는 Ajax 호출 수를 제한하거나](../../azure-monitor/app/javascript.md#configuration) Ajax 보고를 끌 수 있습니다.

* **불필요한 모듈 사용 안 함**: [ApplicationInsights.config를 편집하여](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 필요하지 않은 컬렉션 모듈을 끕니다. 예를 들어 성능 카운터 또는 종속성 데이터가 필요하지 않다고 결정할 수 있습니다.

* **사전 집계 메트릭:** 앱에서 TrackMetric에 호출을 입력하는 경우 측정 일괄 처리의 평균 및 표준 편차 계산을 허용하는 오버로드를 사용하여 트래픽을 줄일 수 있습니다. 또는 [사전 집계 패키지](https://www.myget.org/gallery/applicationinsights-sdk-labs)를 사용할 수 있습니다.
 
* **일일 한도:** Azure Portal에서 Application Insights 리소스를 만들 때 일일 한도는 100GB/일로 설정됩니다. Visual Studio에서 Application Insights 리소스를 만들 때 기본값은 적습니다(불과 32.3MB/일). 일일 한도 기본값은 테스트가 용이하도록 설정됩니다. 앱을 프로덕션에 배포하기 전에 사용자가 일일 한도를 높여야 합니다. 

    트래픽이 많은 애플리케이션에 대해 더 높은 최대값을 요구하지 않으면 최대 한도는 하루 1,000GB입니다.
    
    일일 한도에 대한 경고 이메일은 응용 프로그램 인사이트 리소스에 대한 이러한 역할의 구성원인 계정으로 전송됩니다: "ServiceAdmin", "AccountAdmin", "CoAdmin", "소유자".

    일일 한도를 설정할 때 주의해야 합니다. *일일 한도에 도달하지 않도록* 해야 합니다. 일일 한도에 도달하는 경우 해당 날짜의 남은 기간 동안 데이터가 손실되고 애플리케이션을 모니터링할 수 없습니다. 일일 한도를 변경하려면 **일일 볼륨 한도** 옵션을 사용합니다. **사용량 및 예상 비용** 창에서 이 옵션에 액세스할 수 있습니다(문서의 뒷부분에 자세히 설명되어 있음).
    
    Application Insights에 대해 사용될 수 없는 크레딧이 있는 일부 구독 유형에 대한 제한을 제거했습니다. 이전에 구독에 지출 한도가 있는 경우 일일 한도 대화 상자에 지출 한도를 제거하고 일일 한도가 하루 32.3MB가 넘도록 설정하는 지침이 표시됩니다.
    
* **제한**: 제한은 데이터 속도를 계측 키당 평균 1분 이상 초당 32,000개 이벤트로 제한합니다. 앱에 보내는 데이터의 양은 1분마다 평가됩니다. 해당 분에 대한 평균 초당 속도를 초과하면 서버는 일부 요청을 거부합니다. SDK는 데이터를 버퍼링한 다음, 다시 전송하도록 시도합니다. 몇 분 동안 급증을 분산시킵니다. 앱이 제한율 이상으로 일관되게 데이터를 보내는 경우 일부 데이터가 삭제됩니다. (ASP.NET, Java 및 JavaScript SDK는 이러한 방식으로 데이터를 다시 보내려고 합니다. 다른 SDK는 제한된 데이터를 삭제하기만 하면 됩니다. 제한이 발생하면 알림 경고가 이 발생음을 경고합니다.

## <a name="manage-your-maximum-daily-data-volume"></a>최대 일일 데이터 볼륨 관리

일일 볼륨 한도를 사용하여 수집된 데이터를 제한할 수 있습니다. 그러나 한도가 충족되는 경우 해당 날짜의 나머지 기간 동안 애플리케이션에서 보낸 모든 원격 분석의 손실이 발생합니다. 애플리케이션이 일일 한도에 도달하는 것은 *권장되지 않습니다*. 일일 한도에 도달한 후 애플리케이션의 상태 및 성능을 추적할 수 없습니다.

일별 볼륨 한도를 사용하는 대신 [샘플링](../../azure-monitor/app/sampling.md)을 사용하여 원하는 수준으로 데이터 볼륨을 조정합니다. 그런 다음, 애플리케이션이 예기치 않게 높은 볼륨의 원격 분석을 보내기 시작하는 경우 "최후의 수단"으로만 일일 한도를 사용합니다.

### <a name="identify-what-daily-data-limit-to-define"></a>정의할 일일 데이터 한도 식별

애플리케이션 인사이트 사용량 및 예상 비용을 검토하여 데이터 수집 추세와 정의할 일일 볼륨 한도를 파악합니다. 한도에 도달한 후에는 리소스를 모니터링할 수 없으므로 주의해서 주의해야 합니다.

### <a name="set-the-daily-cap"></a>일일 캡 설정

일일 한도를 변경하려면 응용 프로그램 인사이트 리소스의 **구성** 섹션에서 **사용량 및 예상 비용** 페이지에서 일일 **한도를**선택합니다.

![일별 원격 분석 볼륨 한도 조정](./media/pricing/pricing-003.png)

[Azure 리소스 관리자를 통해 일일 한도를 변경하려면](../../azure-monitor/app/powershell.md)변경할 속성은 입니다. `dailyQuota`  Azure 리소스 관리자를 통해 `dailyQuotaResetTime` 및 일일 한도를 설정할 수도 있습니다. `warningThreshold`

### <a name="create-alerts-for-the-daily-cap"></a>일일 한도에 대한 경고 만들기

응용 프로그램 인사이트 일별 한도는 수집된 데이터 볼륨이 경고 수준 또는 일일 한도 수준에 도달하면 Azure 활동 로그에서 이벤트를 만듭니다.  이러한 [활동 로그 이벤트를 기반으로 경고를 만들](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal)수 있습니다. 이러한 이벤트의 신호 이름은 다음과 같습니다.

* 애플리케이션 인사이트 구성 요소 일일 한도 경고 임계값에 도달

* 애플리케이션 인사이트 컴포넌트 일일 한도 도달

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

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경

응용 프로그램 인사이트 리소스의 기본 보존 기간은 90일입니다. 각 응용 프로그램 인사이트 리소스에 대해 서로 다른 보존 기간을 선택할 수 있습니다. 사용 가능한 보존 기간의 전체 집합은 30일, 60일, 90일, 120일, 180일, 270일, 365일, 550일 또는 730일입니다.

응용 프로그램 인사이트 리소스에서 보존을 변경하려면 **사용량 및 예상 비용** 페이지로 이동하여 데이터 **보존** 옵션을 선택합니다.

![일별 원격 분석 볼륨 한도 조정](./media/pricing/pricing-005.png)

보존은 매개 변수를 [사용하여 PowerShell을 사용하여 프로그래밍시로 설정할](powershell.md#set-the-data-retention) 수도 있습니다. `retentionInDays` 보존이 낮아지면 가장 오래된 데이터가 제거되기 전에 며칠의 유예 기간이 있습니다. 데이터 보존을 30일로 설정하면 `immediatePurgeDataOn30Days` 매개 변수를 사용하여 이전 데이터의 즉각적인 제거를 트리거할 수 있으며, 이는 규정 준수 관련 시나리오에 유용할 수 있습니다. 이 제거 기능은 Azure 리소스 관리자를 통해서만 노출되며 세심한 주의를 기울여 사용해야 합니다. Azure 리소스 관리자를 사용하여 데이터 볼륨 한도에 대한 일일 `dailyQuotaResetTime` 재설정 시간을 구성하여 매개 변수를 설정할 수 있습니다.

## <a name="data-transfer-charges-using-application-insights"></a>애플리케이션 인사이트를 사용한 데이터 전송 요금

애플리케이션 인사이트로 데이터를 전송하면 데이터 대역폭 요금이 발생할 수 있습니다. Azure 대역폭 [가격 책정 페이지에](https://azure.microsoft.com/pricing/details/bandwidth/)설명된 대로 정상 요금으로 아웃바운드 데이터 전송으로 청구되는 두 지역에 있는 Azure 서비스 간의 데이터 전송입니다. 인바운드 데이터 전송은 무료입니다. 그러나,이 요금은 매우 작습니다 (소수의 %) 응용 프로그램 인사이트 로그 데이터 수집에 대한 비용과 비교됩니다. 따라서 Log Analytics의 비용을 제어하려면 수집된 데이터 볼륨에 집중해야 하며 여기에서 이를 이해하는 데 도움이 되는 지침이 [있습니다.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume)

## <a name="limits-summary"></a>제한 요약

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>일일 상한도 이메일을 사용하지 않도록 설정

일일 볼륨 상한 이메일을 사용하지 않도록 설정하려면 Application Insights 리소스의 **구성** 섹션 아래에 있는 **사용량 및 예상 비용** 창에서 **일일 상한**을 선택합니다. 상한에 도달할 때, 그리고 조정 가능한 경고 수준에 도달할 때 이메일을 보내는 설정이 있습니다. 모든 일일 한도 볼륨 관련 이메일을 사용하지 않으려면 두 확인란의 선택을 모두 취소합니다.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>레거시 엔터프라이즈(노드당) 가격 책정 계층

Azure 응용 프로그램 인사이트를 초기 채택하는 경우 기본 및 엔터프라이즈라는 두 가지 가능한 가격 책정 계층이 있습니다. 기본 가격 책정 계층은 위에서 설명한 것과 동일하며 기본 계층입니다. 추가 비용 없이 모든 엔터프라이즈 계층 기능이 포함됩니다. 기본 계층은 주로 수집된 데이터의 양에 대해 청구합니다.

> [!NOTE]
> 이러한 레거시 가격 책정 계층의 이름이 변경되었습니다. 엔터프라이즈 가격 책정 계층은 이제 **노드당이라고** 하며 기본 가격 책정 계층은 이제 **GB당**이라고 합니다. 이러한 새 이름은 아래 및 Azure 포털에서 사용됩니다.  

노드당(이전의 엔터프라이즈) 계층에는 노드당 요금이 있으며 각 노드는 일일 데이터 허용량을 받습니다. 노드별 가격 책정 계층에서 포함된 허용량 이상으로 수집된 데이터에 대한 요금이 부과됩니다. 운영 관리 제품군을 사용하는 경우 노드별 계층을 선택해야 합니다.

사용자의 통화 및 지역에 따른 현재 가격은 [Application Insights 가격 책정](https://azure.microsoft.com/pricing/details/application-insights/)을 참조하세요.

> [!NOTE]
> 2018년 4월에는 Azure 모니터링을 위한 새로운 가격 책정 모델이 [도입](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)되었습니다. 이 모델은 모니터링 서비스의 전체 포트폴리오에서 간단한 "종량제" 모델을 채택합니다. 새로운 가격 [책정 모델,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)사용 패턴에 따라 [이 모델로 이동하는 데 따른 영향을 평가하는](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) 방법 및 새 모델을 선택하는 [방법에](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model) 대해 자세히 알아봅니다.

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>노드 계층별 및 운영 관리 제품군 구독 권한

운영 관리 제품군 E1 및 E2를 구매하는 고객은 [이전에 발표한](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)대로 추가 비용 없이 노드당 애플리케이션 인사이트를 추가 구성 요소로 얻을 수 있습니다. 특히 운영 관리 제품군 E1 및 E2의 각 단위에는 노드별 응용 프로그램 인사이트 계층의 한 노드에 대한 사용 권한이 포함됩니다. 각 Application Insights 노드에는 매일 수집되는 데이터가 200MB까지 포함되고(Log Analytics 데이터 수집과 별도) 데이터는 추가 비용 없이 90일 간 보존됩니다. 계층은 문서의 후반부에서 자세히 설명합니다.

이 계층은 운영 관리 제품군 구독이 있는 고객에게만 적용되므로 운영 관리 제품군 구독이 없는 고객은 이 계층을 선택할 수 있는 옵션이 표시되지 않습니다.

> [!NOTE]
> 이 권한을 얻으려면 응용 프로그램 인사이트 리소스가 노드별 가격 책정 계층에 있어야 합니다. 이 자격은 노드로만 적용됩니다. GB당 응용 프로그램 인사이트 리소스는 어떤 이점도 인식하지 못합니다. 이 자격은 **사용량 및 예상 비용** 창에 표시된 예상 비용에서 볼 수 없습니다. 또한 2018년 4월에 새 Azure 모니터링 가격 책정 모델로 구독을 이동하는 경우 GB당 계층이 사용 가능한 유일한 계층입니다. Operations Management Suite 구독이 있는 경우 새 Azure 모니터링 가격 책정 모델로 구독을 이동하는 것을 권장하지 않습니다.

### <a name="how-the-per-node-tier-works"></a>노드당 계층의 작동 방식

* 노드별 계층의 모든 앱에 대해 원격 분석을 보내는 각 노드에 대해 비용을 지불합니다.
  * *노드는* 실제 또는 가상 서버 컴퓨터 또는 앱을 호스트하는 서비스로서의 플랫폼 역할 인스턴스입니다.
  * 개발 컴퓨터, 클라이언트 브라우저 및 모바일 디바이스는 노드로 계산되지 않습니다.
  * 원격 분석을 보내는 여러 구성 요소(예: 웹 서비스 및 백 엔드 작업자)가 앱에 있는 경우 해당 구성 요소는 개별적으로 집계됩니다.
  * [라이브 메트릭 스트림](../../azure-monitor/app/live-stream.md) 데이터는 가격 책정에 계산 되지 않습니다. 구독에서 요금은 앱 기준이 아니라 노드 기준으로 부과됩니다. 12개 앱에 대해 원격 분석을 보내는 노드가 5개 있는 경우 5개 노드에 대한 요금이 부과됩니다.
* 요금이 매월 견적되지만 노드에서 앱의 원격 분석을 보내는 모든 시간에 대해서만 부과됩니다. 시간당 요금은 견적된 월별 요금을 744(한 달이 31일일 때 시간 수)로 나눈 것입니다.
* 시간별로 감지되는 각 노드에 대해 1일 200MB의 데이터 볼륨 할당이 제공됩니다. 사용되지 않은 데이터 할당은 다음 날로 이월되지 않습니다.
  * 노드별 가격 책정 계층을 선택하면 각 구독은 해당 구독의 Application Insights 리소스로 원격 분석을 보내는 노드 수에 따라 일일 데이터 허용량을 받습니다. 따라서 하루 종일 데이터를 전송하는 5개의 노드가 있는 경우 해당 구독의 모든 Application Insights 리소스에 풀링된 1GB 허용량이 적용됩니다. 모든 노드에서 포함된 데이터를 공유하기 때문에 특정 노드에서 다른 노드보다 더 많은 데이터를 보내는 것은 중요하지 않습니다. 지정된 날에 Application Insights 리소스가 이 구독의 일별 데이터 할당에 포함된 것보다 많은 데이터를 수신하는 경우 GB당 초과 데이터 요금이 부과됩니다. 
  * 일일 데이터 허용량은 각 노드에서 원격 분석을 보내는 날의 시간 수(UTC 사용)를 24로 나눈 값에 200MB를 곱해 계산합니다. 따라서 하루 24시간 중 15시간 동안 원격 분석을 보내는 4개의 노드가 있는 경우 해당일의 포함된 데이터는 ((4 x 15) / 24) x 200MB = 500MB가 됩니다. 데이터 초과분의 가격 조건이 GB당 2.30 달러일 때 노드에서 해당일 1GB의 데이터를 보내는 경우 요금은 1.15달러입니다.
  * 노드당 계층 일일 허용량은 GB당 계층을 선택한 응용 프로그램과 공유되지 않습니다. 사용하지 않는 허용량은 다음 날로 넘어가지 않습니다.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>고유 노드 수를 결정하는 방법 예제

| 시나리오                               | 일일 총 노드 수 |
|:---------------------------------------|:----------------:|
| 1개 애플리케이션에서 3개 Azure App Service 인스턴스 및 1개 가상 서버를 사용합니다. | 4 |
| 2개의 VM에서 실행중인 3개의 응용 프로그램; 이러한 애플리케이션에 대한 애플리케이션 인사이트 리소스는 동일한 구독 및 노드별 계층에 있습니다. | 2 | 
| 4개의 애플리케이션은 해당 Application Insights 리소스가 같은 구독에 있고 각 애플리케이션은 사용률이 낮은 16시간 동안 2개의 인스턴스를 실행하고, 사용률이 최대로 높은 8시간 동안 4개의 인스턴스를 실행합니다. | 13.33 |
| 1개 작업자 역할 및 1개 웹 역할이 부여된 클라우드 서비스에서 각 역할마다 2개 인스턴스를 실행합니다. | 4 | 
| 5개 노드 Azure Service Fabric 클러스터에서 50개의 마이크로 서비스를 실행하며 각 마이크로 서비스에서 3개의 인스턴스를 실행합니다. | 5|

* 정확한 노드 계산은 애플리케이션에서 사용하는 Application Insights SDK에 따라 다릅니다. 
  * SDK 버전 2.2 이상에서 Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 및 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 둘 다 각 애플리케이션 호스트를 노드로 보고합니다. 물리적 서버 및 VM 호스트에 대한 컴퓨터 이름이나 클라우드 서비스에 대한 인스턴스 이름을 예로 들 수 있습니다.  유일한 예외는 [.NET Core](https://dotnet.github.io/) 및 Application Insights Core SDK만 사용하는 애플리케이션입니다. 이 경우 호스트 이름을 사용할 수 없으므로 모든 호스트에 대해 하나의 노드만 보고됩니다.
  * 이전 버전의 SDK에서는 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)가 최신 SDK 버전과 마찬가지로 작동하지만, [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)는 애플리케이션 호스트 수에 관계 없이 하나의 노드만 보고합니다.
  * 애플리케이션에서 SDK를 사용하여 **roleInstance**를 사용자 지정 값으로 설정하는 경우 기본적으로 이 동일한 값이 노드 수를 결정하는 데 사용됩니다.
  * 클라이언트 컴퓨터 또는 모바일 장치에서 실행되는 앱에서 새 SDK 버전을 사용하는 경우 노드 수는 클라이언트 컴퓨터 또는 모바일 장치의 수가 많기 때문에 큰 숫자를 반환할 수 있습니다.

## <a name="automation"></a>Automation

Azure 리소스 관리를 사용하여 가격 책정 계층을 설정하는 스크립트를 작성할 수 있습니다. [방법을 알아보세요](powershell.md#price).

## <a name="next-steps"></a>다음 단계

* [샘플링](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/