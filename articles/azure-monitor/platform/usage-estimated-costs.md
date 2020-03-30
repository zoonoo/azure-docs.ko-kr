---
title: Azure Monitor의 모니터링 사용량 및 예상 비용
description: Azure Monitor의 모니터링 사용량 및 예상 비용 프로세스 개요 페이지
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658818"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Azure Monitor의 모니터링 사용량 및 예상 비용

> [!NOTE]
> 이 문서에서는 여러 Azure 모니터링 기능에서 사용량 및 예상 비용을 보는 방법을 설명합니다. Azure Monitor의 특정 구성 요소에 대한 관련 문서는 다음과 같습니다.
> - [Azure Monitor Logs를 사용하여 사용량 및 비용 관리에서는](manage-cost-storage.md) 데이터 보존 기간을 변경하여 비용을 제어하는 방법과 데이터 사용량을 분석하고 경고하는 방법을 설명합니다.
> - [애플리케이션 인사이트 사용 및 비용 관리는 애플리케이션 인사이트에서](../../azure-monitor/app/pricing.md) 데이터 사용량을 분석하는 방법을 설명합니다.

## <a name="azure-monitor-pricing-model"></a>Azure 모니터 가격 책정 모델

기본 Azure Monitor 청구 모델은 클라우드 친화적인 사용량 기반 가격 책정("종량제")입니다. 사용한 양만큼만 요금을 지급합니다. 가격 세부 정보는 [경고, 메트릭, 알림,](https://azure.microsoft.com/pricing/details/monitor/) [로그 분석](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [애플리케이션 인사이트에](https://azure.microsoft.com/pricing/details/application-insights/)사용할 수 있습니다. 

로그 데이터에 대한 종량제 모델 외에도 Log Analytics에는 용량 예약이 있어 종량제 가격대비 최대 25%를 절약할 수 있습니다. 용량 예약 가격을 사용하면 1일 100GB부터 예약을 구매할 수 있습니다. 예약 수준 이상의 모든 사용량은 종량제 요금으로 청구됩니다. 용량 예약 가격에 대해 [자세히 알아보세요.](https://azure.microsoft.com/pricing/details/monitor/)

일부 고객은 레거시 Log Analytics 가격 책정 계층 및 [레거시 엔터프라이즈 애플리케이션 인사이트 가격 책정 계층에](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier)액세스할 수 [있습니다.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) 

## <a name="understanding-your-azure-monitor-costs"></a>Azure 모니터 비용 이해

비용을 이해하기 위한 두 단계가 있습니다. 첫 번째는 Azure Monitor를 모니터링 솔루션으로 고려할 때입니다. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>환경 관리 비용 예측

아직 Azure 모니터 로그를 사용하지 않는 경우 [Azure 모니터 가격 계산기를](https://azure.microsoft.com/pricing/calculator/?service=monitor) 사용하여 Azure Monitor 사용 비용을 예측할 수 있습니다. 먼저 검색 상자에 "Azure 모니터"를 입력하고 결과 Azure 모니터 타일을 클릭합니다. 페이지를 아래로 스크롤하여 Azure 모니터로 이동한 다음 유형 드롭다운에서 옵션 중 하나를 선택합니다.

- 메트릭 쿼리 및 경고  
- Log Analytics
- 애플리케이션 정보

이들 각각에서 가격 계산기는 예상 사용률에 따라 예상 비용을 예측하는 데 도움이 됩니다.

예를 들어 Log Analytics를 사용하면 각 VM에서 수집할 것으로 예상되는 데이터의 수와 GB를 입력할 수 있습니다. 일반적으로 1GB ~ 3GB의 데이터 월은 일반적인 Azure VM에서 수집됩니다. 이미 Azure 모니터 로그를 이미 평가중인 경우 사용자 고유의 환경에서 데이터 통계를 사용할 수 있습니다. [모니터링되는 VM 수와](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) [작업 영역에서 수집되는 데이터의 양을](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)확인하는 방법은 아래를 참조하십시오.

마찬가지로 응용 프로그램 인사이트(Application Insights)의 경우 "응용 프로그램 활동을 기반으로 데이터 볼륨 예측" 기능을 사용하도록 설정하면 응용 프로그램에 대한 입력을 제공할 수 있습니다(클라이언트 측 원격 분석을 수집하는 경우 월별 요청 및 월별 페이지 뷰). 그런 다음 계산기는 유사한 응용 프로그램에서 수집 한 데이터의 중앙값과 90 번째 백분위수 양을 알려줍니다. 이러한 응용 프로그램은 응용 프로그램 인사이트 구성 범위(예: 기본 샘플링이 있고 일부는 샘플링이 없는 등)에 걸쳐 있으므로 샘플링을 사용하여 중앙값 수준보다 훨씬 낮은 데이터 볼륨을 줄이는 제어가 가능합니다. 그러나 이것은 다른 유사한 고객이 보고 있는 것을 이해하기 위한 출발점입니다. 애플리케이션 인사이트를 위한 비용 추정에 대해 [자세히 알아보십시오.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application)

### <a name="understanding-your-usage-and-estimated-costs"></a>사용량 및 예상 비용 파악

Azure Monitor를 사용하면 사용량을 이해하고 추적하는 것이 중요하며 이를 용이하게 하기 위한 다양한 도구 집합이 있습니다. 

Azure는 [Azure 비용 관리 + 청구](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) 허브에서 많은 유용한 기능을 제공합니다. **Azure 비용 관리 + 청구** 허브를 연 후 비용 관리를 클릭하고 [범위(조사할](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) 리소스 집합)를 **선택합니다.** 

그런 다음 지난 30일 동안의 Azure Monitor 비용을 보려면 **일일 비용** 타일을 클릭하고 상대 날짜 아래에서 "마지막 30일"을 선택하고 서비스 이름을 선택하는 필터를 추가합니다.

1. Azure Monitor
2. 애플리케이션 정보
3. Log Analytics
4. Insight and Analytics

그러면 다음과 같은 뷰가 생성됩니다.

![Azure 비용 관리 스크린샷](./media/usage-estimated-costs/010.png)

여기에서 이 누적된 비용 요약에서 드릴인하여 "리소스별 비용" 보기에서 자세한 세부 정보를 얻을 수 있습니다. 현재 가격 책정 계층에서 Azure Log 데이터는 로그 분석 또는 응용 프로그램 인사이트에서 시작된지 여부에 관계없이 동일한 미터 집합에 대해 요금이 부과됩니다. 로그 분석 또는 애플리케이션 인사이트 사용과 비용을 구분하려면 **리소스 유형에**필터를 추가할 수 있습니다. 모든 응용 프로그램 인사이트 비용을 보려면 리소스 유형을 "microsoft.insights/구성 요소"로 필터링하고 로그 분석 비용에 대해 리소스 유형을 "microsoft.운영 인사이트/작업 영역"으로 필터링합니다. 

[Azure 포털에서 사용량을 다운로드하여 사용에](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)대한 자세한 내용을 확인할 수 있습니다. 다운로드한 스프레드시트에서 하루에 Azure 리소스당 사용량을 볼 수 있습니다. 이 Excel 스프레드시트에서 먼저 "미터 범주" 열을 필터링하여 "응용 프로그램 인사이트" 및 "로그 분석"을 표시한 다음 "microsoft.insights/components"인 "인스턴스 ID" 열에 필터를 추가하여 응용 프로그램 인사이트 리소스의 사용량을 확인할 수 있습니다.  모든 Azure Monitor 구성 요소에 대한 단일 로그 백엔드가 있기 때문에 대부분의 응용 프로그램 인사이트 사용량은 로그 분석의 미터 범주가 있는 미터에서 보고됩니다.  레거시 가격 책정 계층 및 다단계 웹 테스트에 대한 응용 프로그램 인사이트 리소스만 미터 범주의 응용 프로그램 인사이트와 함께 보고됩니다.  사용량은 "소모된 수량" 열에 표시되고 각 항목의 단위는 "측정 단위" 열에 표시됩니다.  자세한 내용은 [Microsoft Azure 청구서를 이해하는](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)데 도움이 됩니다. 

> [!NOTE]
> **Azure 비용 관리 + 청구** 허브에서 비용 **관리를** 사용하는 것이 모니터링 비용을 광범위하게 이해하는 데 선호되는 방법입니다.  [로그 분석](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) 및 [응용 프로그램 인사이트를](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) 위한 **사용 및 예상 비용** 환경은 Azure Monitor의 각 부분에 대한 심층적인 통찰력을 제공합니다.

Azure 모니터 사용량을 보기 위한 또 다른 옵션은 모니터 허브의 **사용량 및 예상 비용** 페이지입니다. 여기에는 [경고, 메트릭, 알림,](https://azure.microsoft.com/pricing/details/monitor/) [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)및 Azure 응용 프로그램 [인사이트와](https://azure.microsoft.com/pricing/details/application-insights/)같은 핵심 모니터링 기능의 사용이 표시됩니다. 2018년 4월 이전 제공된 요금제 고객의 경우 Insights 및 Analytics 제안을 통해 구매한 Log Analytics 사용량도 여기에 포함됩니다.

사용자는 이 페이지에서 구독별로 집계된 지난 31일 간의 리소스 사용량을 볼 수 있습니다. `Drill-ins`31일 동안의 사용 동향을 보여줍니다. 예상치를 내기 위해서는 상당한 데이터를 취합해야 하므로 페이지 로드에 다소 시간이 소요됩니다.

이 예에서는 모니터링 사용량과 그에 따른 비용의 추정치를 보여 줍니다.

![사용량 및 예상 비용 포털 스크린 샷](./media/usage-estimated-costs/001.png)

월별 사용량 열의 링크를 클릭하면 지난 31일 간의 사용량 추세를 보여 주는 차트가 열립니다. 

![노드당 포함 가로 막대형 차트 스크린 샷](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>운영 관리 제품군 구독 권한

Microsoft Operations Management Suite E1 및 E2를 구매한 고객은 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 및 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing)에 대한 노드별 데이터 주입 자격이 있습니다. 지정된 구독에서 Log Analytics 작업 영역 또는 Application Insights 리소스에 대해 이러한 자격을 얻으려면: 

- Log Analytics 작업 영역에서 "노드당(OMS)" 가격 책정 계층을 사용해야 합니다.
- 응용 프로그램 인사이트 리소스는 "엔터프라이즈" 가격 책정 계층을 사용해야 합니다.

조직에서 구입한 제품군의 노드 수에 따라 일부 구독을 GB당 종량제(GB) 가격 책정 계층으로 이동하는 것이 유리할 수 있지만 신중하게 고려해야 합니다.

> [!WARNING]
> 조직에 현재 Microsoft 운영 관리 제품군 E1 및 E2가 있는 경우 일반적으로 Log Analytics 작업 영역을 "노드별(OMS)) 가격 책정 계층"에 보관하고 "엔터프라이즈" 가격 책정 계층의 응용 프로그램 인사이트 리소스를 유지하는 것이 가장 좋습니다. 
>
