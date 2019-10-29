---
title: Azure Monitor의 모니터링 사용량 및 예상 비용
description: Azure Monitor의 모니터링 사용량 및 예상 비용 프로세스 개요 페이지
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 48abf95e65b6185f5c95a1f5d942091ed0f33122
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044170"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Azure Monitor의 모니터링 사용량 및 예상 비용

> [!NOTE]
> 이 문서에서는 여러 Azure 모니터링 기능을 통해 사용량 및 예상 비용을 보는 방법을 설명 합니다. Azure Monitor의 특정 구성 요소에 대 한 관련 문서는 다음과 같습니다.
> - [Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리](manage-cost-storage.md) 데이터 보존 기간을 변경 하 여 비용을 제어 하는 방법 및 데이터 사용량을 분석 하 고 경고 하는 방법을 설명 합니다.
> - [Application Insights에 대 한 사용량 및 비용 관리](../../azure-monitor/app/pricing.md) Application Insights에서 데이터 사용량을 분석 하는 방법을 설명 합니다.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor 가격 책정 모델

기본 Azure Monitor 청구 모델은 클라우드 기반의 소비 기반 가격 ("종 량 제")입니다. 사용한 만큼만 요금을 지불합니다. 가격 정보는 [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)에 사용할 수 있습니다. 

로그 데이터에 대 한 종 량 제 모델 외에도 Log Analytics에는 용량 예약이 있으므로 종 량 제 가격에 비해 25%까지 절약할 수 있습니다. 용량 예약 가격 책정을 통해 100 g b/일에 시작 하는 예약을 구매할 수 있습니다. 예약 수준 위의 모든 사용량은 종 량 제 요금으로 청구 됩니다. 용량 예약 가격 책정에 [대해 자세히 알아보세요](https://azure.microsoft.com/pricing/details/monitor/) .

일부 고객은 [레거시 Log Analytics 가격 책정 계층](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) 및 [레거시 Enterprise Application Insights 가격 책정 계층](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier)에 액세스할 수 있습니다. 

## <a name="understanding-your-azure-monitor-costs"></a>Azure Monitor 비용 이해

비용을 이해 하는 데는 두 단계가 있습니다. 첫 번째는 모니터링 솔루션으로 Azure Monitor를 고려 하는 경우입니다. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>환경을 관리 하는 비용 예측

Azure Monitor 로그를 아직 사용 하 고 있지 않은 경우 [Azure Monitor 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=monitor) 를 사용 하 여 Azure Monitor를 사용 하는 비용을 예측할 수 있습니다. 검색 상자에 "Azure Monitor"를 입력 하 고 결과 Azure Monitor 타일을 클릭 하 여 시작 합니다. 페이지 아래쪽으로 스크롤하여 Azure Monitor 하 고 유형 드롭다운에서 옵션 중 하나를 선택 합니다.

- 메트릭 쿼리 및 경고  
- Log Analytics
- Application Insights

각각의 가격 계산기는 예상 사용량을 기준으로 가능한 비용을 예측 하는 데 도움이 됩니다.

예를 들어 Log Analytics를 사용 하 여 각 VM에서 수집 해야 하는 Vm 수와 GB 데이터를 입력할 수 있습니다. 일반적으로 전형적인 Azure VM에서 수집의 데이터 월은 3gb입니다. 이미 Azure Monitor 로그를 평가 하 고 있는 경우 자신의 환경에서 데이터 통계를 사용할 수 있습니다. 모니터링 되는 [vm 수](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) 및 [작업 영역에서 수집 된 데이터의](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)양을 확인 하는 방법은 아래를 참조 하세요.

Application Insights의 경우와 마찬가지로 "응용 프로그램 활동을 기반으로 데이터 볼륨 추정" 기능을 사용 하도록 설정 하면 응용 프로그램에 대 한 입력 (클라이언트 쪽 원격 분석을 수집 하는 경우 월별 요청 및 월 당 페이지 보기)을 제공할 수 있습니다. 그리고 계산기는 비슷한 응용 프로그램에 의해 수집 된 데이터의 중앙값 및 90 번째 백분위 수를 알려 줍니다. 이러한 응용 프로그램은 Application Insights 구성 범위 (예: 기본 샘플링, 일부는 샘플링 등)를 포함 하므로 샘플링을 사용 하 여 중앙값 보다 훨씬 낮은 수준 아래로 수집 하는 데이터의 양을 줄일 수 있는 컨트롤이 있습니다. 그러나 다른 유사한 고객이 볼 수 있는 것을 이해 하기 위한 출발점입니다. Application Insights 비용을 예측 하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) .

### <a name="understanding-your-usage-and-estimated-costs"></a>사용량 및 예상 비용 이해

Azure Monitor를 사용 하 여 사용 현황을 이해 하 고 추적 하는 것이 중요 하며,이를 용이 하 게 하는 다양 한 도구 집합이 있습니다. 

Azure는 [Azure Cost Management + 청구](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) 허브에서 상당한 유용한 기능을 제공 합니다. **Azure Cost Management + 청구** 허브를 연 후 **Cost Management** 를 클릭 하 고 [범위](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (조사할 리소스 집합)를 선택 합니다. 

그런 다음 지난 30 일 동안의 Azure Monitor 비용을 확인 하려면 **일별 비용** 타일을 클릭 하 고 상대 날짜 아래의 "지난 30 일"을 선택한 다음 서비스 이름을 선택 하는 필터를 추가 합니다.

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Insight and Analytics

그러면 다음과 같은 뷰가 생성 됩니다.

![Azure Cost Management 스크린 샷](./media/usage-estimated-costs/010.png)

여기에서 누적 비용 요약의 정보를 활용 하 여 "자원별 비용" 보기에서 보다 자세한 정보를 얻을 수 있습니다. 현재 가격 책정 계층에서 Azure 로그 데이터는 Log Analytics 또는 Application Insights에서 생성 된 것과 동일한 미터의 요금으로 청구 됩니다. Log Analytics 또는 Application Insights 사용량의 비용을 구분 하기 위해 **리소스 유형에**필터를 추가할 수 있습니다. 모든 Application Insights 비용을 보려면 리소스 종류를 "microsoft 인 사이트/구성 요소"로 필터링 하 고, Log Analytics 비용의 경우 리소스 종류를 "operationalinsights/작업 영역"으로 필터링 합니다. 

사용 현황에 대 한 자세한 내용은 [Azure Portal에서 사용을 다운로드](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)하 여 확인할 수 있습니다. 다운로드 한 스프레드시트에서 일별 Azure 리소스 별 사용량을 볼 수 있습니다. 이 Excel 스프레드시트에서는 먼저 "측정기 범주" 열을 필터링 하 여 "Application Insights" 및 "Log Analytics"를 표시 한 다음 "contains" 라는 "Instance ID" 열에 필터를 추가 하 여 Application Insights 리소스를 사용 하 여 찾을 수 있습니다. microsoft.  모든 Azure Monitor 구성 요소에 대 한 단일 로그 백 엔드가 있기 때문에 대부분의 Application Insights 사용량은 Log Analytics 측정기 범주를 사용 하 여 미터에 보고 됩니다.  레거시 가격 책정 계층 및 다단계 웹 테스트에 대 한 Application Insights 리소스만 Application Insights 측정기 범주를 사용 하 여 보고 됩니다.  사용량은 "소비 된 수량" 열에 표시 되 고 각 항목에 대 한 단위는 "측정 단위" 열에 표시 됩니다.  [Microsoft Azure 청구서를 이해](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)하는 데 도움이 되는 자세한 정보를 제공 합니다. 

> [!NOTE]
> **Azure Cost Management + 청구** 허브에 **Cost Management** 를 사용 하는 것은 모니터링 비용을 광범위 하 게 이해 하는 권장 되 접근 방식입니다.  [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) 및 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) 에 대 한 **사용량 및 예상 비용** 에는 Azure Monitor의 각 부분에 대 한 심층적인 통찰력을 제공 합니다.

Azure Monitor 사용을 볼 수 있는 또 다른 옵션은 모니터 허브의 **사용량 및 예상 비용** 페이지입니다. 이는 [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)및 [Azure 애플리케이션 Insights](https://azure.microsoft.com/pricing/details/application-insights/)와 같은 핵심 모니터링 기능을 사용 하는 방법을 보여 줍니다. 2018년 4월 이전 제공된 요금제 고객의 경우 Insights 및 Analytics 제안을 통해 구매한 Log Analytics 사용량도 여기에 포함됩니다.

사용자는 이 페이지에서 구독별로 집계된 지난 31일 간의 리소스 사용량을 볼 수 있습니다. 31 일 동안 사용 추세를 표시 `Drill-ins`. 예상치를 내기 위해서는 상당한 데이터를 취합해야 하므로 페이지 로드에 다소 시간이 소요됩니다.

이 예에서는 모니터링 사용량과 그에 따른 비용의 추정치를 보여 줍니다.

![사용량 및 예상 비용 포털 스크린 샷](./media/usage-estimated-costs/001.png)

월별 사용량 열의 링크를 클릭하면 지난 31일 간의 사용량 추세를 보여 주는 차트가 열립니다. 

![노드당 포함 가로 막대형 차트 스크린 샷](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite 구독 자격

Microsoft Operations Management Suite E1 및 E2를 구매한 고객은 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 및 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing)에 대한 노드별 데이터 주입 자격이 있습니다. 지정된 구독에서 Log Analytics 작업 영역 또는 Application Insights 리소스에 대해 이러한 자격을 얻으려면: 

- Log Analytics 작업 영역에서 "노드당(OMS)" 가격 책정 계층을 사용해야 합니다.
- Application Insights 리소스는 "Enterprise" 가격 책정 계층을 사용 해야 합니다.

조직에서 구매한 제품군의 노드 수에 따라 일부 구독을 종 량 제 (GB 당) 가격 책정 계층으로 이동 하는 것이 유용할 수 있지만이를 위해서는 신중 하 게 고려해 야 합니다.

> [!WARNING]
> 조직에 현재 Microsoft Operations Management Suite E1 및 E2가 있는 경우 "노드당 (OMS)" 가격 책정 계층에 Log Analytics 작업 영역을 유지 하 고 "Enterprise" 가격 책정 계층에 Application Insights 리소스를 유지 하는 것이 좋습니다. 
>
