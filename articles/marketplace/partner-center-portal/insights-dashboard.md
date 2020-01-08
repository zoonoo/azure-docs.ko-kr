---
title: 파트너 센터의 상용 Marketplace 분석에서 marketplace Insights 대시보드
description: 게시자가 AppSource 및 Azure Marketplace 상점에서 고객 참여를 측정할 수 있게 해 주는 marketplace 웹 분석의 요약에 액세스 합니다.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: edfdf7bbfaa5fa5abb00aa7efcb35bfbcd6e68a4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480542"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>상업적 Marketplace 분석의 Marketplace Insights 대시보드

이 문서에서는 파트너 센터의 Marketplace Insights 대시보드에 대 한 정보를 제공 합니다. 이 대시보드는 marketplace 웹 분석의 요약을 표시 합니다 .이를 통해 게시자는 marketplace 상점: AppSource 및 Azure Marketplace에 나열 된 해당 제품 세부 정보 페이지에 대 한 고객 참여를 측정할 수 있습니다.

## <a name="marketplace-insights-dashboard"></a>Marketplace Insights 대시보드

파트너 센터에서 **Marketplace Insights 대시보드에** 액세스 하려면 상업적 Marketplace 아래에서 **[분석 탭](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** 을 엽니다.

다음 항목에 대 한 그래픽 표현을 볼 수 있습니다.  

- [Marketplace 정보 요약](#marketplace-insights-summary)
- [지리 별 페이지 방문](#page-visits-by-geography)  
- [페이지 방문 및 고유 방문자 추세](#page-visits-versus-unique-visitors-trend)
- [CTAs를 사용 하 여 작업 및 고유 방문자에 대 한 호출](#call-to-action-versus-unique-visitors-with-ctas)
- [페이지 방문 및 제공 작업에 대 한 호출](#page-visits-and-calls-to-action-by-offers)
- [작업 비율 추세에 대 한 호출](#call-to-action-percentage-trend)
- [조회 도메인에의 한 페이지 방문 및 동작 호출](#page-visits-and-calls-to-action-by-referral-domains)
- [Marketplace Insights 정보 테이블](#marketplace-insights-details-table)

>[!NOTE]
> 분석 용어에 대 한 자세한 정의는 [상업적 Marketplace 분석에 대 한 질문과 용어](./faq-terminology.md)를 참조 하세요.

### <a name="insights-dashboard-layout"></a>Insights 대시보드 레이아웃

다음과 같은 다양 한 방법으로 Marketplace 메트릭을 볼 수 있습니다.

- Storefront 탭
- 페이지 필터
- 날짜 필터

**Storefront 탭**: appsource & Azure Marketplace 탭을 통해 제품의 메트릭을 별도로 볼 수 있습니다. 오른쪽의 제안 드롭다운 목록에서 제품을 선택 하 여 선택한 제품에 대 한 메트릭의 시각화를 볼 수 있습니다. 기본적으로 모든 제품을 선택 합니다.

![파트너 센터 Insights 대시보드 제안 드롭다운 목록](./media/insights-offer-dropdown.png)

**Insights 페이지 필터**: 이러한 필터는 제안 (제품 세부 정보 페이지) 수준에서 적용 됩니다. 보려는 조건에 대해 여러 필터를 선택할 수 있습니다. 이 필터는 차트 및 세부 정보를 포함 하 여 전체 Marketplace 정보 섹션에 적용 됩니다.

![파트너 센터 Insights 대시보드 페이지 필터](./media/insights-page-filter.png)

- 제품 이름은 선택한 날짜 범위에서 페이지 방문이 있는 제품에 대해서만 나열 됩니다.  
- 각 필터 옵션에 대 한 기본 선택 항목은 ' 모두 '입니다.
- 적용 된 필터 선택 항목에 대 한 선택 항목 수를 표시 합니다. 적용 된 필터는 기본 ' 모든 ' 선택에 대해 표시 되지 않습니다.

![파트너 센터 Insights 필터 적용 됨](./media/insights-page-filter-two.png)

**Insights 날짜 필터**:이 필터는 전체 Marketplace 정보 섹션에 적용 됩니다. 필터에는 미리 결정 된 날짜 범위 또는 사용자 지정 날짜 범위가 포함 될 수 있습니다.

![파트너 센터 Insights 날짜 필터](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Marketplace 정보 요약

Marketplace insights 요약 섹션에는 선택한 날짜 범위에 대 한 **페이지 방문**횟수, **작업 호출**및 **고유 방문자** 수가 표시 됩니다.

### <a name="page-visits"></a>페이지 방문

이 수는 선택 된 날짜 범위에 대 한 제품 페이지 (제품 세부 정보 페이지)의 고유한 사용자 세션 수를 나타냅니다. 빨강/녹색 백분율 지표는 페이지 방문의 증가 비율을 나타냅니다. 추세 차트는 월간 페이지 방문 횟수를 나타냅니다.

### <a name="unique-visitors"></a>고유 방문자

이 숫자는 페이지 필터에서 선택한 제품에 대해 선택한 날짜 범위에 있는 고유 방문자 수를 나타냅니다. 하나 이상의 제품 세부 정보 페이지를 방문한 방문자는 하나의 고유 방문자로 계산 됩니다.

### <a name="call-to-action"></a>활용 방안

이 숫자는 제품 페이지 (제품 정보 페이지)에서 완료 된 작업 단추 클릭 완료에 대 한 **호출** 수를 나타냅니다. **지금 가져오기**, **무료 평가판**, **연락처**및 **테스트 드라이브** 단추가 선택 되 면 **작업에 대 한 호출이** 카운트 됩니다.

![작업 요약에 대 한 파트너 센터 Insights 호출](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>지리 별 페이지 방문

아래 열 지도 **고객 국가에 따라** **페이지 방문**횟수, **작업 호출**및 고유 방문자 수를 표시 합니다. 더 높은 페이지 방문은 더 짙은 지도 색으로 표시 되 고 낮은 페이지 방문은 더 밝은 지도 색으로 표시 됩니다.

![파트너 센터 정보 지리적 확산](./media/insights-geography.png)

열 지도에는 다음과 같은 기능이 포함 되어 있습니다.

- 열 지도에는 특정 위치에서 **페이지 방문**, **작업 호출** 및 **고유 방문자** 에 대 한 세부 정보를 볼 수 있는 보조 표가 있습니다. 원하는 경우 특정 위치로 확대할 수 있습니다.  
- **국가 확산** 은 선택한 날짜 범위 동안 고객이 페이지 방문을 보고 한 모든 국가의 수입니다.
- 표에서 국가를 검색 하 고 선택 하 여 지도의 위치를 확대할 수 있습니다. 지도에서 **홈** 을 선택 하 여 원래 보기로 되돌립니다.

## <a name="page-visits-versus-unique-visitors-trend"></a>페이지 방문 및 고유 방문자 추세

아래 열은 Y 축 (차트 왼쪽의 축)에 표시 되는 월간 페이지 방문 횟수를 나타냅니다. 추세 선은 상점: Azure Marketplace 및 AppSource에 게시 된 제품에 대 한 보조 Y 축 (차트 오른쪽의 축)에 표시 되는 고유 방문자의 월별 추세를 나타냅니다.

![파트너 센터 정보 페이지 방문 및 고유 방문자 추세](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>CTAs를 사용 하 여 작업 및 고유 방문자에 대 한 호출

누적 열은 cta (월간 호출)를 나타냅니다. cta는 CTA 유형 (**지금 가져오기**, **나에 게 연락**및 **무료 평가판**)으로 분류 되 고 Y 축 (페이지 왼쪽의 축)에 그려집니다. 추세 선은 Azure Marketplace 및 AppSource에 게시 된 제품에 대 한 보조 Y 축 (차트 오른쪽의 축)에 표시 되는 CTAs의 월별 추세를 나타냅니다.

![CTAs의 작업 및 고유 방문자에 대 한 파트너 센터 Insights 호출](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>페이지 방문 및 제공 작업에 대 한 호출

외부 원형 차트는 marketplace에 게시 하 고 필터에서 선택한 제품을 기반으로 하는 **페이지 방문** 분석을 나타냅니다. 내부 차트는 동일한 제품에 대 한 **작업 분석에** 대 한 호출을 나타냅니다.

![파트너 센터 정보 페이지 방문 및 제품에의 한 작업 호출](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>작업 비율 추세에 대 한 호출

**작업 비율 추세에** 대 한 호출은 marketplace에 게시 된 제품의 cta 비율을 표시 합니다. CTA% = (Cta/페이지 방문) * 100.

![작업 비율 추세에 대 한 파트너 센터 Insights 호출](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>조회 도메인에의 한 페이지 방문 및 동작 호출

아래 그래프는 상위 50 조회 도메인을 표시 합니다. 특정 조회 도메인을 선택 하면 페이지 방문의 월별 추세와 오른쪽에 있는 차트의 동작에 대 한 호출이 표시 됩니다.

![파트너 센터 정보 페이지 방문 및 조회 도메인 및 캠페인의 작업 호출](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Marketplace Insights 정보 테이블

이 표에서는 선택한 제품에 대 한 호출에 대 한 목록 보기를 날짜별로 정렬 하 여 보여 줍니다.

![파트너 센터 정보 테이블](./media/insights-details-page.png)

- 레코드 수가 1000 보다 작은 경우 데이터를 CSV 파일로 추출할 수 있습니다.
- 레코드 수가 1000를 초과 하는 경우 내보낸 데이터는 다음 30 일 동안 비동기적으로 다운로드 페이지에 배치 됩니다.
- 필터를 적용 하 여 관심 있는 데이터를 표시할 수 있습니다. 제품 이름 및 캠페인 이름을 기준으로 데이터를 필터링 할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

- 파트너 센터 상업적 Marketplace에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터에서 상업용 Marketplace에 대 한 분석](./analytics.md)을 참조 하세요.
- 제품의 마켓플레이스 작업을 요약 하는 집계 데이터의 그래프, 추세 및 값은 [상용 marketplace 분석의 요약 대시보드](./summary-dashboard.md)를 참조 하세요.
- 그래픽 및 다운로드 가능한 형식으로 주문에 대 한 자세한 내용은 [상용 Marketplace 분석의 주문 대시보드](./orders-dashboard.md)를 참조 하세요.
- VM (가상 컴퓨터)은 사용량 및 요금제 청구 메트릭을 제공 합니다. [상용 Marketplace 분석의 사용량 대시보드](./usage-dashboard.md)를 참조 하세요.
- 성장 추세를 비롯 한 고객에 대 한 자세한 내용은 [상용 Marketplace 분석의 고객 대시보드](./customer-dashboard.md)를 참조 하세요.
- 최근 30 일 동안의 다운로드 요청 목록은 [상용 Marketplace 분석에서 대시보드 다운로드](./downloads-dashboard.md)를 참조 하세요.
- Azure Marketplace 및 AppSource의 제품에 대 한 고객 의견의 통합 보기를 보려면 [상용 Marketplace 분석의 등급 및 리뷰 대시보드](./ratings-reviews.md)를 참조 하세요.
- 상용 Marketplace 분석 및 데이터 용어의 포괄적인 사전에 대 한 질문과 대답은 [상업적 marketplace 분석에 대 한 질문과 대답](./faq-terminology.md)을 참조 하세요.
