---
title: 파트너 센터의 상용 마켓플레이스 분석에서 마켓플레이스 인사이트 대시보드
description: 게시자가 AppSource 및 Azure 마켓플레이스 스토어프론트에서 고객 참여를 측정할 수 있는 마켓플레이스 웹 분석 요약에 액세스합니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: a547ced9df98298361360ecab88036599cd86027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275852"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>상용 마켓플레이스 분석의 마켓플레이스 인사이트 대시보드

이 문서에서는 파트너 센터의 마켓플레이스 인사이트 대시보드에 대한 정보를 제공합니다. 이 대시보드에는 마켓플레이스 웹 분석 요약이 표시되어 게시자가 마켓플레이스 스토어프론트에 나열된 각 제품 세부 정보 페이지(AppSource 및 Azure Marketplace)에 대한 고객 참여를 측정할 수 있습니다.

## <a name="marketplace-insights-dashboard"></a>Marketplace 인사이트 대시보드

파트너 센터의 **마켓플레이스 인사이트 대시보드에** 액세스하려면 상업용 마켓플레이스에서 **[분석 탭을](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** 엽니다.

다음 항목의 그래픽 표현을 볼 수 있습니다.  

- [마켓플레이스 인사이트 요약](#marketplace-insights-summary)
- [지역별 페이지 방문](#page-visits-by-geography)  
- [페이지 방문과 고유 방문자 추세](#page-visits-versus-unique-visitors-trend)
- [클릭 유도문의 경우, CTA를 보유한 고유 방문자와 비교](#call-to-action-versus-unique-visitors-with-ctas)
- [페이지 방문 및 오퍼별 클릭 유도](#page-visits-and-calls-to-action-by-offers)
- [클릭 유도액 비율 추세](#call-to-action-percentage-trend)
- [추천 도메인별 페이지 방문 및 행동 유도](#page-visits-and-calls-to-action-by-referral-domains)
- [마켓플레이스 인사이트 세부 정보 표](#marketplace-insights-details-table)

>[!NOTE]
> 분석 용어에 대한 자세한 정의는 [상용 마켓플레이스 분석을 위한 자주 묻는 질문 및 용어를](./faq-terminology.md)참조하십시오.

### <a name="insights-dashboard-layout"></a>인사이트 대시보드 레이아웃

다양한 방법으로 마켓플레이스 측정항목을 볼 수 있습니다.

- 상점 앞 탭
- 페이지 필터
- 날짜 필터

**스토어프론트 탭:** AppSource & Azure 마켓플레이스 탭을 통해 오퍼의 측정항목을 별도로 볼 수 있습니다. 오른쪽의 오퍼 드롭다운 목록에서 오퍼를 선택하여 선택한 오퍼에 대한 메트릭의 시각화를 확인합니다. 기본적으로 모든 오퍼가 선택됩니다.

![파트너 센터 인사이트 대시보드제공 드롭다운 목록](./media/insights-offer-dropdown.png)

**인사이트 페이지 필터**: 이러한 필터는 제품 세부 정보 페이지 수준에서 적용됩니다. 보려는 조건에 대해 여러 필터를 선택할 수 있습니다. 이 필터는 차트 및 세부 정보를 포함한 전체 마켓플레이스 인사이트 섹션에 적용됩니다.

![파트너 센터 인사이트 대시보드 페이지 필터](./media/insights-page-filter.png)

- 쿠폰 이름은 선택한 날짜 범위의 페이지 방문이 있는 오퍼에 대해서만 나열됩니다.  
- 기본 선택은 각 필터 옵션에 대해 '모두'입니다.
- 적용된 필터는 선택한 선택 항목의 선택 횟수를 표시합니다. 기본 '모두' 선택에 적용된 필터는 표시되지 않습니다.

![파트너 센터 인사이트 필터 적용](./media/insights-page-filter-two.png)

**인사이트 날짜 필터**: 이 필터는 전체 마켓플레이스 인사이트 섹션에 적용됩니다. 필터에는 미리 결정된 날짜 범위 또는 사용자 지정 날짜 범위가 포함될 수 있습니다.

![파트너 센터 인사이트 날짜 필터](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>마켓플레이스 인사이트 요약

마켓플레이스 인사이트 요약 섹션에는 **페이지 방문**수, 행동 **유도**및 선택한 날짜에 대한 **고유 방문자수가** 표시됩니다.

### <a name="page-visits"></a>페이지 방문

이 숫자는 선택한 날짜 범위에 대한 오퍼 페이지(제품 세부 정보 페이지)의 고유한 사용자 세션 수를 나타냅니다. 빨간색/녹색 백분율 표시기는 페이지 방문의 증가율을 나타냅니다. 추세 차트는 페이지 방문의 월별 수를 나타냅니다.

### <a name="unique-visitors"></a>고유 방문자

이 숫자는 페이지 필터에서 선택한 오퍼에 대해 선택한 날짜 범위 동안의 고유 방문자 수를 나타냅니다. 하나 이상의 제품 세부 정보 페이지를 방문한 방문자는 하나의 고유 방문자로 계산됩니다.

### <a name="call-to-action"></a>활용 방안

이 숫자는 제품 페이지(제품 세부정보 페이지)에서 완료된 클릭 수의 **클릭 수를** 나타냅니다. **행동 유도는** 지금 **받기,** **무료 평가판,** **저에게 연락하기**및 **테스트 드라이브** 버튼이 선택될 때 계산됩니다.

![파트너 센터 인사이트 클릭 유도문의 요약](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>지역별 페이지 방문

아래 히트맵에는 페이지 **방문**수, **행동 유도**및 **고객 국가에 따른 고유 방문자 수가**표시됩니다. 페이지 방문이 높을수록 맵 색상이 더 어두워지고 페이지 방문이 더 밝은 맵 색상으로 표시됩니다.

![파트너 센터 인사이트 지리적 스프레드](./media/insights-geography.png)

히트맵에는 다음과 같은 기능이 포함되어 있습니다.

- 히트맵에는 **페이지 방문,** **행동 유도** 및 특정 위치에서 고유 **방문자의** 세부 정보를 볼 수 있는 보조 그리드가 있습니다. 원하는 경우 특정 위치로 확대할 수 있습니다.  
- **국가 스프레드는** 고객이 선택한 기간 동안 페이지 방문을 보고한 모든 국가의 수입니다.
- 그리드에서 국가를 검색하고 선택하여 맵의 위치를 확대할 수 있습니다. 맵에서 **홈을** 선택하여 원래 뷰로 되돌리라고 합니다.

## <a name="page-visits-versus-unique-visitors-trend"></a>페이지 방문과 고유 방문자 추세

아래 열은 Y축(차트 왼쪽축)에 표시되는 월별 페이지 방문 수를 나타냅니다. 추세선은 상점에 게시된 오퍼에 대해 보조 Y축(차트의 오른쪽에 축)에 표시되는 고유 방문자의 월별 추세를 나타냅니다.

![파트너 센터 인사이트 페이지 방문과 고유 방문자 추세 비교](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>클릭 유도문의 경우, CTA를 보유한 고유 방문자와 비교

누적된 열은 CTA**유형(지금 받기,** **연락하기**및 **무료 평가판)으로**세분화되고 Y축(페이지 왼쪽축)에 플롯되는 월별 클릭 유도문안(CTA)을 나타냅니다. 추세선은 Azure Marketplace 및 AppSource에 게시된 오퍼에 대해 보조 Y축(차트 오른쪽 축)에 표시되는 CTA를 사용한 고유 방문자의 월별 추세를 나타냅니다.

![파트너 센터 인사이트, CTA를 통해 고유 방문자와 행동 유도](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>페이지 방문 및 오퍼별 행동 유도

외부 원형 차트는 마켓플레이스에 게시하고 필터에서 선택한 오퍼를 기반으로 **페이지 방문** 내역을 나타냅니다. 내부 차트는 동일한 오퍼에 대한 **행동 유도** 분석 데이터를 나타냅니다.

![파트너 센터 인사이트 페이지 방문 및 오퍼별 행동 유도](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>클릭 유도액 비율 추세

**행동 유도 비율 추세는** 마켓플레이스에 게시된 오퍼에 대한 CTA 백분율을 제공합니다. CTA % = (CTA / 페이지 방문) * 100.

![파트너 센터 인사이트 클릭 유도문의 비율 추세](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>추천 도메인별 페이지 방문 및 행동 유도

아래 그래프는 상위 50개 추천 도메인을 제공합니다. 특정 추천 도메인을 선택하면 오른쪽 차트에서 페이지 방문 및 행동 유도의 월별 추세가 표시됩니다.

![파트너 센터 인사이트 페이지 추천 도메인 및 캠페인을 통해 방문 및 행동 유도](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>마켓플레이스 인사이트 세부 정보 표

이 표에서는 페이지 방문 목록 보기와 선택한 오퍼의 행동 유도가 날짜별로 정렬되어 있습니다.

![파트너 센터 인사이트 세부 정보 표](./media/insights-details-page.png)

- 레코드 수가 1000개 미만이면 데이터를 CSV 파일로 추출할 수 있습니다.
- 레코드 수가 1000개를 초과하면 내보낸 데이터는 다음 30일 동안 다운로드 페이지에 비동기적으로 배치됩니다.
- 관심 있는 데이터를 표시하기 위해 필터를 적용할 수 있습니다. 데이터는 쿠폰 이름 및 캠페인 이름으로 필터링할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

- 파트너 센터 커머셜 마켓플레이스에서 사용할 수 있는 분석 보고서에 대한 개요는 [파트너 센터의 상업용 마켓플레이스에 대한 분석을](./analytics.md)참조하세요.
- 오퍼의 마켓플레이스 활동을 요약하는 집계 데이터의 그래프, 추세 및 값은 [상업용 마켓플레이스 분석의 요약 대시보드를](./summary-dashboard.md)참조하십시오.
- 그래픽 및 다운로드 가능한 형식으로 주문에 대한 자세한 내용은 [상용 마켓플레이스 분석의 주문 대시보드를](./orders-dashboard.md)참조하십시오.
- VM(가상 머신)이 사용량 및 유료 청구 메트릭을 제공하는 경우 [상용 마켓플레이스 분석의 사용 대시보드를](./usage-dashboard.md)참조하십시오.
- 성장 추세를 포함하여 고객에 대한 자세한 내용은 [상용 마켓플레이스 분석의 고객 대시보드를](./customer-dashboard.md)참조하십시오.
- 지난 30일 동안의 다운로드 요청 목록은 [상용 마켓플레이스 분석의 다운로드 대시보드를](./downloads-dashboard.md)참조하십시오.
- Azure 마켓플레이스 및 AppSource에서 제안에 대한 고객 피드백에 대한 통합보기를 보려면 [상용 마켓플레이스 분석의 등급 및 리뷰 대시보드를](./ratings-reviews.md)참조하십시오.
- 상용 마켓플레이스 분석 및 포괄적인 데이터 용어 사전에 대한 자주 묻는 질문은 [상용 마켓플레이스 분석을 위한 자주 묻는 질문 및 용어를](./faq-terminology.md)참조하십시오.
