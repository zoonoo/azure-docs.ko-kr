---
title: 상업용 마켓플레이스 분석의 Marketplace Insights 대시보드
description: 파트너 센터에서 marketplace 웹 분석의 요약에 액세스 하 여 Microsoft AppSource 및 Azure Marketplace에서 고객 참여를 측정할 수 있습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 479e42c2ea48ea56c0b2dd70752a3b1a330f7969
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94415227"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>상업용 마켓플레이스 분석의 Marketplace Insights 대시보드

이 문서에서는 파트너 센터의 Marketplace 인사이트 대시보드에 대한 정보를 제공합니다. 이 대시보드에는 상업적 marketplace 온라인 상점: Microsoft AppSource 및 Azure Marketplace에 나열 된 각 제품 정보 페이지에 대 한 고객 참여를 평가할 수 있도록 하는 상용 마켓플레이스 웹 분석 요약이 표시 됩니다.

파트너 센터의 **marketplace insights** 대시보드에 액세스 하려면 상업적 marketplace에서 **[Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **marketplace 정보** 분석을 선택 합니다.

분석 용어에 대 한 자세한 정의는 [상업적 marketplace 분석 용어 및 일반적인 질문](./partner-center-portal/faq-terminology.md)을 참조 하세요.

## <a name="marketplace-insights-dashboard"></a>Marketplace 인사이트 대시보드

Marketplace Insights 대시보드는 Azure Marketplace의 개요를 제공 하 고 AppSource는 비즈니스 성과를 제공 합니다. 이 대시보드는 다음에 대 한 광범위 한 개요를 제공 합니다.

- 페이지 방문 추세
- 작업 추세에 대 한 호출
- 페이지 방문 및 제안, 조회 도메인 및 캠페인 id에 대 한 작업 호출
- 지리 별 Marketplace 정보
- Marketplace 인사이트 세부 정보 테이블

Marketplace Insights 대시보드는 클릭 스트림 데이터를 제공 하며,이는 리드 대상 끝점에서 생성 된 잠재 고객과 상관 되어서는 안 됩니다.

> [!NOTE]
> Azure Marketplace 또는 AppSource에서 제품을 방문한 사용자와 파트너 센터의 보고 간 최대 대기 시간은 48 시간입니다.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Marketplace Insights 대시보드의 요소

Marketplace Insights 대시보드는 Azure Marketplace 및 AppSource에 대 한 웹 원격 분석 세부 정보를 별도의 두 탭에 표시 합니다. 다음 섹션에서는 Marketplace Insights 대시보드를 사용 하는 방법 및 데이터를 읽는 방법을 설명 합니다.

### <a name="month-range"></a>월 범위

각 페이지의 오른쪽 위 모서리에서 월 범위 선택을 찾을 수 있습니다. 지난 6 개월 또는 12 개월을 기준으로 월 범위를 선택 하거나 최대 12 개월의 사용자 지정 월 범위를 선택 하 여 **Marketplace Insights** 페이지 그래프의 출력을 사용자 지정 합니다. 기본 월 범위 (계산 기간)는 6 개월입니다.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Marketplace Insights 대시보드의 월 필터를 보여 줍니다.":::

> [!NOTE]
> 시각화 위젯 및 내보내기 보고서의 모든 메트릭은 사용자가 선택한 계산 기간을 사용 합니다.

### <a name="page-visits-trends"></a>페이지 방문 추세

Marketplace Insights **방문자** 차트에는 선택한 계산 기간에 대 한 _페이지 방문_ 횟수와 _고유 방문자_ 수가 표시 됩니다.

**페이지 방문** : 선택한 계산 기간에 대 한 제품 정보 페이지 (제품 정보 페이지)의 고유한 사용자 세션 수를 나타냅니다. 빨강 및 녹색 백분율 지표는 페이지 방문의 증가율을 나타냅니다. 추세 차트는 월간 페이지 방문 횟수를 나타냅니다.

**고유 방문자** :이 숫자는 Azure Marketplace 및 appsource의 제안에 대해 선택한 계산 기간 동안의 고유한 방문자 수를 나타냅니다. 하나 이상의 제품 세부 정보 페이지를 방문한 방문자는 하나의 고유 방문자로 계산됩니다.

[![Marketplace Insights 대시보드의 방문자 차트를 보여 줍니다.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>작업 추세에 대 한 호출

이 숫자는 제품 목록 페이지 (제품 정보 페이지)에서 완료 된 작업 단추 클릭 완료에 대 한 **호출** 수를 나타냅니다. 사용자가 **지금 가져오기** , **무료 평가판** , **나에 게 연락** 또는 **드라이브 테스트** 단추를 선택 하면 _작업에 대 한 호출이_ 카운트 됩니다.

[![Marketplace Insights 대시보드의 작업 차트에 대 한 호출을 보여 줍니다.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>페이지 방문 및 제안, 조회 도메인 및 캠페인 Id에 대 한 작업 호출

**조회 도메인** : 특정 조회 도메인을 선택 하면 페이지 방문의 월별 추세와 오른쪽에 있는 차트의 동작에 대 한 호출이 표시 됩니다.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Marketplace Insights 대시보드의 조회 도메인 차트를 보여 줍니다.":::

**제안** : 특정 제품을 선택 하 여 페이지 방문의 월별 추세와 오른쪽에 있는 차트의 동작에 대 한 호출을 볼 수 있습니다.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Marketplace Insights 대시보드의 제품 별칭 차트를 보여 줍니다.":::

**캠페인 id** : 특정 캠페인 id를 선택 하면 캠페인의 성공 여부를 이해할 수 있습니다. 각 캠페인에 대해 페이지 방문의 월별 추세와 오른쪽의 차트에 대 한 작업 호출을 볼 수 있어야 합니다.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Marketplace Insights 대시보드의 캠페인 차트를 보여 줍니다.":::

### <a name="marketplace-insights-by-geography"></a>지리 별 Marketplace 정보

선택한 계산 기간 동안 열 지도는 페이지 방문 횟수, 고유 방문자 수 및 CTA (작업 호출)를 표시 합니다. 지도의 밝은 색은 고유 방문자의 낮은 값에서 높은 값을 나타냅니다. 국가/지역을 확대할 테이블의 레코드를 선택 합니다.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Marketplace Insights 대시보드의 지리적 스프레드 차트를 보여 줍니다.":::

다음 사항에 유의하십시오.

- 지도를 이동하여 정확한 위치를 볼 수 있습니다.
- 특정 위치를 확대할 수 있습니다.
- 열 지도에는 특정 위치의 고객 수, 주문 수 및 표준화 된 사용 시간에 대 한 세부 정보를 볼 수 있는 보조 표가 있습니다.
- 표에서 국가/지역을 검색하고 선택하여 지도에서 해당 위치를 확대할 수 있습니다. 지도에서 **홈** 단추를 선택 하 여 원래 뷰로 되돌립니다.

### <a name="marketplace-insights-details-table"></a>Marketplace 인사이트 세부 정보 테이블

이 표에서는 선택한 페이지의 목록 뷰 및 선택한 제품의 작업 호출을 날짜별로 정렬 하 여 보여 줍니다.

- 데이터를로 추출할 수 있습니다. TSV 또는입니다. 레코드 수가 1000 보다 작은 경우 CSV 파일입니다.
- 레코드 수가 1000를 초과 하는 경우 내보낸 데이터는 다음 30 일 동안 비동기적으로 다운로드 페이지에 배치 됩니다.
- 원하는 데이터를 표시 하기 위해 제품 이름 및 캠페인 이름을 기준으로 데이터를 필터링 합니다.

> [!TIP]
> 위젯의 오른쪽 위 모퉁이에 있는 다운로드 아이콘을 사용 하 여 데이터를 다운로드할 수 있습니다. "엄지 손가락 위로" 또는 "엄지 손가락 아래로 이동" 아이콘을 클릭 하 여 각 위젯에 대 한 피드백을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 상용 marketplace에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터에서 상업용 marketplace에 대 한 분석 보고서 액세스](./partner-center-portal/analytics.md)를 참조 하세요.
- 그래픽 및 다운로드 가능한 형식으로 주문에 대 한 자세한 내용은 [상용 marketplace 분석의 주문 대시보드](./orders-dashboard.md)를 참조 하세요.
- VM (가상 컴퓨터)은 사용량 및 요금제 청구 메트릭을 제공 합니다. [상용 marketplace 분석의 사용량 대시보드](./usage-dashboard.md)를 참조 하세요.
- 증가 추세를 비롯한 고객에 대한 자세한 내용은 [상업용 Marketplace 분석의 고객 대시보드](./customer-dashboard.md)를 참조하세요.
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 Marketplace 분석의 다운로드 대시보드](./partner-center-portal/downloads-dashboard.md)를 참조하세요.
- Azure Marketplace 및 AppSource의 제품에 대 한 고객 의견의 통합 보기를 보려면 [파트너 센터의 등급 & 리뷰 분석 대시보드](./partner-center-portal/ratings-reviews.md)를 참조 하세요.
- 상용 marketplace 분석 및 데이터 용어의 포괄적인 사전에 대 한 질문과 대답은 [상용 marketplace 분석 용어 및 일반적인 질문](./partner-center-portal/faq-terminology.md)을 참조 하세요.
