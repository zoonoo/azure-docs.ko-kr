---
title: 상업적 Marketplace 분석, Microsoft AppSource 및 Azure Marketplace의 파트너 센터 주문 대시보드
description: 상용 marketplace 제품 주문에 대 한 분석 보고서에 액세스 하 여 그래픽 및 다운로드 가능한 형식으로 액세스 하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2682202b68deeeb1dbbe2493ecb9592acd1788b8
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94415092"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>상업용 마켓플레이스 분석의 주문 대시보드

이 문서에서는 파트너 센터의 주문 대시보드에 대한 정보를 제공합니다. 이 대시보드에는 그래픽 및 다운로드 가능한 형식으로 제공 되는 증가 추세를 비롯 하 여 주문에 대 한 정보가 표시 됩니다.

파트너 센터의 주문 대시보드에 액세스 하려면 **상업적 Marketplace** 에서 주문 **[분석](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** 을 선택  >  **Orders** 합니다.

>[!NOTE]
> 분석 용어에 대 한 자세한 정의는 [상업적 marketplace 분석 용어 및 일반적인 질문](./partner-center-portal/faq-terminology.md)을 참조 하세요.

## <a name="orders-dashboard"></a>주문 대시보드

주문 대시보드에는 모든 SaaS (software as a service) 제품에 대 한 현재 주문이 표시 됩니다. 다음 항목에 대한 그래픽 표현을 볼 수 있습니다.

- 주문 추세
- 사용자 단위 및 사이트 추세 별 주문
- 제품 및 Sku 별 주문
- 지리별 주문
- 자세한 주문 테이블
- 주문 페이지 필터

> [!NOTE]
> 파트너 센터에서 고객 획득과 보고 사이의 최대 대기 시간은 48 시간입니다.

## <a name="elements-of-the-orders-dashboard"></a>주문 대시보드의 요소

다음 섹션에서는 주문 대시보드를 사용 하는 방법과 데이터를 읽는 방법을 설명 합니다.

### <a name="month-range"></a>월 범위

각 페이지의 오른쪽 위 모서리에서 월 범위 선택을 찾을 수 있습니다. 지난 6 개월 또는 12 개월을 기준으로 월 범위를 선택 하거나 최대 기간이 12 개월 인 사용자 지정 월 범위를 선택 하 여 **주문** 페이지 그래프의 출력을 사용자 지정 합니다. 기본 월 범위 (계산 기간)는 6 개월입니다.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="주문 대시보드의 월 필터를 보여 줍니다.":::

> [!NOTE]
> 시각화 위젯 및 내보내기 보고서의 모든 메트릭은 사용자가 선택한 계산 기간을 사용 합니다.

### <a name="orders-trend"></a>주문 추세

이 섹션에서는 선택한 계산 기간의 활성 및 취소 된 주문의 추세를 보여 주는 **주문** 차트를 찾을 수 있습니다. 메트릭 및 증가율은 꺾은선형 차트로 표현 되며 차트의 선을 가리키면 각 월의 값이 표시 됩니다. 위젯의 주문 메트릭 아래 백분율 값은 선택 된 계산 기간 동안 증가 또는 거부 되는 정도를 나타냅니다.

_활성_ 및 _취소_ 라는 두 개의 Orders 카운터가 있습니다.

- **활성** 은 선택한 날짜 범위 동안 고객이 현재 사용 중인 주문 수와 같습니다.
- **취소** -이전에 구매한 후 선택한 날짜 범위 동안 취소 된 주문 수를 표시 합니다.

[![주문 대시보드의 주문 위젯을 보여 주며,이는 활성 및 취소 된 주문의 추세를 보여 줍니다.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>사용자 단위 및 사이트 추세 별 주문

**좌석 별 및 사이트 기반** 꺾은선형 차트는 고객이 구매한 saas 주문 및 사이트별 saas의 메트릭과 추세를 나타냅니다 (이 차트는 취소 된 주문을 포함 함).

[![사용자 당 주문 및 사이트 추세를 보여 주는 주문 대시보드의 주문 위젯을 보여 줍니다.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

SaaS 제품은 각 계획에 대 한 두 가지 가격 책정 모델 중 하나를 사용할 수 있습니다. 즉, 플랫 요율 (사이트 기반) 또는 사용자 단위 (사용자 기반) 중 하나를 사용할 수 있습니다.

- 고정 **률** : 단일 월별 또는 연간 정액 요금 가격으로 제품에 대 한 액세스를 허용 합니다. 이를 사이트 기반 가격 책정이라고도 합니다.
- **사용자 단위** : 제품에 액세스할 수 있는 사용자 수에 따라 가격으로 제품에 대 한 액세스를 허용 합니다. 이 사용 빈도 기반 모델을 사용 하면 계획에서 지원 되는 사용자의 최소 및 최대 수를 설정할 수 있습니다. 여러 요금제를 만들어 사용자 수에 따라 다양 한 가격을 구성할 수 있습니다. 이러한 필드는 선택 사항입니다. 이를 선택 하지 않은 경우 사용자 수는 제한이 없는 것으로 해석 됩니다 (최소 1 개, 서비스에서 지원할 수 있는 최대 개수). 이러한 필드는 계획에 대 한 업데이트의 일부로 편집할 수 있습니다.
- **요금제 요금제** : 정액 요금 가격 책정. 이 가격 책정 모델을 사용 하면 필요에 따라 marketplace 계량 서비스 API를 사용 하 여 고객에 게 정액 요금에 포함 되지 않은 사용량에 대 한 요금을 부과 하는 요금제 요금제를 정의할 수 있습니다.

사용자, 사이트 및 요금제 기반 요금 청구에 대 한 자세한 내용은 [상용 marketplace에 대 한 SaaS 제품을 계획 하는 방법](plan-saas-offer.md)을 참조 하세요.

### <a name="orders-by-offers-and-skus"></a>제품 및 Sku 별 주문

제품 별 주문 및 SKU 차트는 모든 제품의 측정값과 추세를 보여 줍니다.

- 최상위 제품이 그래프에 표시 되 고 나머지 제품은 **나머지 모두** 로 그룹화 됩니다.
- 범례에서 특정 제품을 선택 하 여 해당 제품 및 관련 Sku만 그래프에 표시할 수 있습니다.
- 그래프에서 조각을 마우스로 가리키면 모든 제품의 총 주문 수와 비교한 주문의 수와 백분율이 표시 됩니다.
- **제품별 주문 추세** 는 월별 증가 추세를 표시합니다. 월 열은 제품 이름별 주문 수를 나타냅니다. 꺾은선형 차트는 z축에 그려진 증가율 추세를 표시합니다.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="주문 대시보드의 주문 별 주문 차트를 보여 줍니다.":::

제품을 선택 하 고 해당 제품의 최대 3 개 Sku를 선택 하 여 제품, Sku 및 사용자에 대 한 월 단위 추세를 볼 수 있습니다.

### <a name="orders-by-geography"></a>지리별 주문

선택한 계산 기간에 대해 열 지도는 전체 주문 수와 지리에 대해 새로 추가 된 주문의 증가율을 표시 합니다.  지도의 밝은 색에서 짙은 색은 고객 수의 낮은 값에서 높은 값을 나타냅니다. 테이블에서 특정 국가 또는 지역으로 확대할 레코드를 선택 합니다.

[![주문 대시보드의 지리적 스프레드 차트를 보여 줍니다.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

다음 사항에 유의하십시오.

- 지도를 이동하여 정확한 위치를 볼 수 있습니다.
- 특정 위치를 확대할 수 있습니다.
- 열 지도에는 특정 위치에 대 한 고객 수, 주문 수 및 표준화 된 사용 시간에 대 한 세부 정보를 볼 수 있는 보조 표가 있습니다.
- 표에서 국가/지역을 검색하고 선택하여 지도에서 해당 위치를 확대할 수 있습니다. 지도에서 **홈** 단추를 선택 하 여 원래 뷰로 되돌립니다.

### <a name="orders-details-table"></a>주문 정보 테이블

주문 세부 정보 테이블에는 획득 날짜별로 정렬 된 1000 상위 주문의 번호가 매겨진 목록이 표시 됩니다.

- 그리드의 각 열을 정렬할 수 있습니다.
- 데이터를로 추출할 수 있습니다. CSV 또는 레코드 수가 1000 보다 작은 경우 TSV 파일입니다.
- 1000를 초과 하는 레코드를 기록 하는 경우 내보낸 데이터는 다음 30 일 동안 비동기적으로 다운로드 페이지에 배치 됩니다.
- **주문 정보** 테이블에 필터를 적용 하 여 관심 있는 데이터만 표시 합니다. 국가/지역, Azure 라이선스 유형, 상업적 마켓플레이스 라이선스 유형, 제안 유형, 주문 상태, 무료 내역, 상업적 marketplace 구독 ID, 고객 ID 및 회사 이름으로 필터링 합니다.
- SaaS는 Azure Marketplace 또는 Microsoft AppSource를 통해 구매한 경우 Azure 구독이 필요 하지 않으므로 마켓플레이스 구독 ID는 **자세한 주문 데이터** 섹션의 00000000-0000-0000-0000-000000000000 형식으로 표시 됩니다.
- 보호 된 고객이 주문을 구매 하면 **자세한 데이터 주문** 정보가 마스킹 됩니다 (* * * * * * * * * * * *).

**_표 1: 데이터 용어 _ 사전_*

| 열 이름 | 특성 이름 | 정의 |
| ------------ | ------------- | ------------- |
| Marketplace 구독 Id | Marketplace 구독 ID | 고객이 상용 마켓플레이스 제품을 구매 하는 데 사용한 Azure 구독과 연결 된 고유 식별자입니다. ID는 이전에 Azure 구독 GUID였습니다. |
| MonthStartDate | 월 시작 날짜 | 월 시작 날짜는 구매 월을 나타냅니다. |
| 제품 유형 | 제품 유형 | 상용 marketplace 제품의 유형입니다. |
| Azure 라이선스 유형 | Azure 라이선스 유형 | Azure 구매 고객이 체결하는 라이선싱 계약 유형입니다. 채널이라고도 합니다. 가능한 값은 다음과 같습니다.<ul><li>클라우드 솔루션 공급자</li><li>Enterprise</li><li>대리점을 통해 엔터프라이즈</li><li>종 량 제</li></ul> |
| Marketplace 라이선스 유형 | Marketplace 라이선스 유형 | 상업적 marketplace 제안의 청구 방법입니다. 다른 값은 다음과 같습니다.<ul><li>Azure를 통해 청구</li><li>자신의 라이선스 가져오기</li><li>무료</li><li>Microsoft as 재판매인</li></ul> |
| SKU | SKU | 제품에 연결 된 계획입니다. |
| 고객 국가 | 고객 국가/지역 | 고객이 입력한 국가/지역 이름. 국가/지역은 고객의 Azure 구독에서 국가/지역과 다를 수 있습니다. |
| 미리 보기 SKU | 미리 보기 SKU | 이 값은 SKU를 "미리 보기"로 태그 지정한 경우를 알려 줍니다. 이에 따라 SKU가 태그 지정된 경우 값은 "예"이며, 사용자가 승인한 Azure 구독만 이 이미지를 배포하고 사용할 수 있습니다. SKU가 "미리 보기"로 식별되지 않은 경우 값은 "아니요"입니다. |
| 주문 Id | 주문 ID | 상업적 marketplace 서비스의 고객 주문에 대 한 고유 식별자입니다. Virtual Machine 사용량 기준 제품은 주문에 연결되지 않습니다. |
| Order Quantity | Order Quantity | 활성 주문의 주문 ID와 연결 된 자산 수 |
| 클라우드 인스턴스 이름 | 클라우드 인스턴스 이름 | VM이 배포된 Microsoft Cloud입니다. |
| 새 고객 | 새 고객 | 이 값은 새 고객이 하나 이상의 제품을 처음 획득 했는지 여부를 식별 합니다. "취득일"에 대해 동일한 월 내인 경우 값이 "예"로 나타납니다. 고객이 보고한 월 이전에 제품을 구매한 경우 값은 "아니요"입니다. |
| 주문 상태 | 주문 상태 | 데이터를 마지막으로 새로 고친 시점의 상용 marketplace 주문 상태입니다. |
| 주문 취소 날짜 | 주문 취소 날짜 | 상용 marketplace 주문이 취소 된 날짜입니다. |
| 고객 회사 이름 | 고객 회사 이름 | 고객이 입력한 회사 이름입니다. 이름은 고객의 Azure 구독에서 구/군/시와 다를 수 있습니다. |
| 주문 구매 날짜 | 주문 구매 날짜 | 상용 marketplace 주문이 만들어진 날짜입니다. |
| 제품 이름 | 제품 이름 | 상용 marketplace 제품의 이름입니다. |
| 평가판 종료 날짜 | 평가판 종료 날짜 | 이 주문의 평가 기간이 종료되었거나 종료될 예정인 날짜입니다. |
| Customer Id | 고객 ID | 고객에게 할당된 고유 식별자입니다. 고객은 Azure Marketplace 구독이 0 개 이상 있을 수 있습니다. |
| 청구 계정 Id | 청구 계정 ID | 청구를 생성 하는 계정의 식별자입니다. _ *청구 계정 ID* *를 **CustomerID** 에 매핑하여 지급 트랜잭션 보고서를 고객, 주문 및 사용 현황 보고서에 연결 합니다. |
| AssetCount | 자산 수 | 주문 ID와 연결 된 자산 수입니다. |
||||

### <a name="orders-page-filters"></a>주문 페이지 필터

주문 **페이지 필터** 는 주문 페이지 수준에서 적용 됩니다. 하나 이상의 필터를 선택 하 여 보려는 조건과 ' 자세한 주문 데이터 ' 그리드/내보내기에서 보려는 데이터에 대 한 차트를 렌더링할 수 있습니다. Orders 페이지의 오른쪽 위 모서리에서 선택한 월 범위에 대해 추출 된 데이터에 필터가 적용 됩니다.

> [!TIP]
> 위젯의 오른쪽 위 모퉁이에 있는 다운로드 아이콘을 사용 하 여 데이터를 다운로드할 수 있습니다. "엄지 손가락 위로" 또는 "엄지 손가락 아래로 이동" 아이콘을 클릭 하 여 각 위젯에 대 한 피드백을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 상용 marketplace에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터에서 상업용 marketplace에 대 한 분석 보고서 액세스](./partner-center-portal/analytics.md)를 참조 하세요.
- 제품에 대한 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값은 [상업용 Marketplace 분석의 요약 대시보드](./summary-dashboard.md)를 참조하세요.
- 그래픽 및 다운로드 가능 형식으로 주문 정보를 보려면 [상업용 마켓플레이스 분석의 주문 대시보드](orders-dashboard.md)를 참조하세요.
- VM(가상 머신) 제품 사용량 및 요금제 청구 메트릭은 [상업용 Marketplace 분석의 사용량 대시보드](./usage-dashboard.md)를 참조하세요.
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 Marketplace 분석의 다운로드 대시보드](./partner-center-portal/downloads-dashboard.md)를 참조하세요.
- Azure Marketplace 및 AppSource의 제품에 대 한 고객 의견의 통합 보기를 보려면 [파트너 센터의 등급 & 리뷰 분석 대시보드](./partner-center-portal/ratings-reviews.md)를 참조 하세요.
- 상용 marketplace 분석 및 데이터 용어의 포괄적인 사전에 대 한 질문과 대답은 [상용 marketplace 분석 용어 및 일반적인 질문](./partner-center-portal/faq-terminology.md)을 참조 하세요.
