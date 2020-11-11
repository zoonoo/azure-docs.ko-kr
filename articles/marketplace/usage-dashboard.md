---
title: 상용 marketplace 분석의 사용량 대시보드 | Azure Marketplace
description: Azure Marketplace에 게시 된 제품에 대 한 모든 사용량 및 요금제 청구 메트릭에 액세스 하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 1ae30d97e64dc17086e199c3fcc8188f54e6ab69
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490120"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>상업용 마켓플레이스 분석의 사용량 대시보드

이 문서에서는 파트너 센터의 사용량 대시보드에 대 한 정보를 제공 합니다. 이 대시보드는 모든 VM (가상 컴퓨터)에서 표준화 된 사용량, 원시 사용량 및 요금제 청구 메트릭을 3 개의 별도 탭 (VM 정규화 된 사용량, VM 원시 사용량 및 요금제 청구 사용량)으로 표시 합니다.

파트너 센터의 사용량 대시보드에 액세스 하려면 **상업적 Marketplace** 에서 사용량 분석을 선택 **[Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Usage** 합니다.

>[!NOTE]
> 분석 용어에 대 한 자세한 정의는 [상업적 marketplace 분석 용어 및 일반적인 질문](./partner-center-portal/faq-terminology.md)을 참조 하세요.

## <a name="usage-dashboard"></a>사용량 대시보드

**분석** 메뉴의 **사용량** 대시보드에는 모든 SaaS (software as a service) 제품에 대 한 현재 주문이 표시 됩니다. 다음 항목에 대한 그래픽 표현을 볼 수 있습니다.

- 사용량 추세
- 제안 별 정규화 된 사용
- 다른 차원의 정규화 된 사용: VM 크기, 판매 채널 및 제안 유형
- 지리별 사용량
- 자세한 사용 표
- 주문 페이지 필터

> [!NOTE]
> 사용량 이벤트 생성과 파트너 센터의 보고 사이의 최대 대기 시간은 48 시간입니다.

## <a name="elements-of-the-usage-dashboard"></a>사용 대시보드의 요소

다음 섹션에서는 사용 대시보드를 사용 하는 방법과 데이터를 읽는 방법을 설명 합니다.

### <a name="month-range"></a>월 범위

각 페이지의 오른쪽 위 모서리에서 월 범위 선택을 찾을 수 있습니다. 지난 6 개월 또는 12 개월을 기준으로 월 범위를 선택 하거나 최대 기간이 12 개월 인 사용자 지정 월 범위를 선택 하 여 **사용량** 페이지 그래프의 출력을 사용자 지정 합니다. 기본 월 범위 (계산 기간)는 6 개월입니다.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="사용 대시보드의 월 필터를 보여 줍니다.":::

### <a name="usage-trend"></a>사용량 추세

이 섹션에서는 선택 된 계산 기간 동안 고객이 소비 하는 모든 제품에 대 한 총 사용 시간 및 추세를 확인 합니다. 메트릭 및 증가 추세는 꺾은선형 차트로 표시 됩니다. 차트의 선 위로 마우스를 가져가면 각 월의 값을 표시 합니다. 위젯의 사용 메트릭 아래에 있는 백분율 값은 선택 된 계산 기간 동안 증가 또는 거부 되는 정도를 나타냅니다.

사용 시간에 대 한 두 가지 표현은 VM 정규화 된 사용량과 VM raw 사용량입니다.

- 정규화된 사용 시간은 VM 코어 수를 고려하도록 정규화된 사용 시간으로 정의됩니다([VM 코어 수] x [가공되지 않은 사용량 시간])으로 계산됩니다. "SHAREDCORE"로 지정된 VM은 [VM 코어 수] 승수로 1/6(0.1666)을 사용합니다.
- 원시 사용 시간은 Vm이 시간 단위로 실행 되는 시간의 양에 따라 정의 됩니다.

[![사용량 대시보드의 정규화 된 사용량과 원시 사용 현황 데이터를 보여 줍니다.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>제안 별 정규화 된 사용

이 섹션에서는 Azure Marketplace의 사용량 기반 제품에 대 한 총 사용 시간 및 추세를 제공 합니다. 제안 별 정규화 된 사용량 차트는 아래에 설명 되어 있습니다.

- **제공 되는 표준화** 된 세로 막대형 차트에는 선택 된 계산 기간에 따라 상위 5 개 제품에 대 한 정규화 된 사용 시간이 분석 되어 표시 됩니다. 상위 5 개 제안은 그래프에 표시 되 고 나머지는 **나머지 모든** 범주로 그룹화 됩니다.
- 누적 세로 막대형 차트는 선택한 날짜 범위에 대 한 월 단위 급수 추세를 보여 줍니다. 월 열은 해당 월의 사용 시간이 가장 많은 제품의 사용 시간을 나타냅니다. 꺾은선형 차트는 보조 Y축에 그려진 증가율 추세를 표시합니다.
- 범례에서 특정 제품을 선택하여 그래프에 해당 제품만 표시할 수 있습니다.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="사용 대시보드의 데이터를 제공 하는 정규화 된 사용을 보여 줍니다.":::

제품을 선택 하 고 해당 제품의 최대 3 개 Sku를 선택 하 여 제품 및 선택한 Sku에 대 한 월-월 사용 추세를 볼 수 있습니다.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="사용량 대시보드의 정규화 된 사용 제안 및 sku 데이터를 보여 줍니다.":::

### <a name="orders-by-offers-and-skus"></a>제품 및 Sku 별 주문

제품 **별 주문 및 SKU** 차트에는 모든 제품의 측정값과 추세가 표시 됩니다. 다음 사항에 유의하십시오.

- 최상위 제품이 그래프에 표시 되 고 나머지 제품은 **나머지 모두** 로 그룹화 됩니다.
- 범례에서 특정 제품을 선택하여 그래프에 해당 제품만 표시할 수 있습니다.
- 그래프에서 조각을 마우스로 가리키면 모든 제품의 총 주문 수와 비교한 주문의 수와 백분율이 표시 됩니다.
- **제품별 주문 추세** 는 월별 증가 추세를 표시합니다. 월 열은 제품 이름별 주문 수를 나타냅니다. 꺾은선형 차트는 z 축에 그려진 증가율 추세를 표시 합니다.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="사용량 대시보드의 주문 별 주문 및 Sku 차트를 보여 줍니다.":::

제품을 선택 하 고 해당 제품의 최대 3 개 Sku를 선택 하 여 제품, Sku 및 사용자에 대 한 월 단위 추세를 볼 수 있습니다.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="사용량 대시보드의 주문 별 주문 및 Sku 차트를 보여 줍니다. 제안 추세, Sku 추세 및 좌석 추세가 표시 됩니다.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>다른 차원의 정규화 된 사용: VM 크기, 판매 채널 및 제안 유형

차원에는 VM 크기, 판매 채널 및 제안 유형의 세 가지 탭이 있습니다. 이러한 각 차원에 대 한 사용 메트릭 및 월 단위 추세를 볼 수 있습니다.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="사용량 대시보드의 정규화 된 사용 기타 차원 차트를 보여 줍니다.":::

### <a name="usage-by-geography"></a>지리별 사용량

선택한 계산 기간에 대해 열 지도는 geography 차원에 대 한 총 사용량을 표시 합니다. 지도의 밝은 색에서 짙은 색은 고객 수의 낮은 값에서 높은 값을 나타냅니다. 국가/지역을 확대할 테이블의 레코드를 선택 합니다.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="사용량 대시보드의 정규화 된 사용 현황 국가 차트를 보여 줍니다.":::

다음 사항에 유의하십시오.

- 지도를 이동하여 정확한 위치를 볼 수 있습니다.
- 특정 위치를 확대할 수 있습니다.
- 열 지도에는 특정 위치의 고객 수, 주문 수 및 표준화 된 사용 시간에 대 한 세부 정보를 볼 수 있는 보조 표가 있습니다.
- 표에서 국가/지역을 검색하고 선택하여 지도에서 해당 위치를 확대할 수 있습니다. 지도에서 **홈** 단추를 선택 하 여 원래 뷰로 되돌립니다.

### <a name="usage-details-table"></a>사용 정보 테이블

**사용량 정보** 테이블에는 사용을 기준으로 정렬 된 상위 1000 사용 레코드 목록이 표시 됩니다. 다음 사항에 유의하십시오.

- 그리드의 각 열을 정렬할 수 있습니다.
- 데이터를로 추출할 수 있습니다. TSV 또는입니다. 레코드 수가 1000 보다 작은 경우 CSV 파일입니다.
- 레코드 수가 1000를 초과 하는 경우 내보내기 데이터는 다음 30 일 동안 사용할 수 있는 다운로드 페이지에 비동기적으로 배치 됩니다.
- **자세한 사용 현황 데이터** 에 필터를 적용 하 여 관심 있는 데이터만 표시 합니다. 국가/지역, 판매 채널, Marketplace 라이선스 유형, 사용 유형, 제안 이름, 제안 유형, 무료 평가판, Marketplace 구독 ID, 고객 ID 및 회사 이름으로 데이터를 필터링 합니다.

_**표 1: 데이터 용어 사전**_

| 열 이름 | 특성 이름 | 정의 |
| ------------ | ------------- | ------------- |
| Marketplace 구독 Id | Marketplace 구독 ID | 고객이 상용 마켓플레이스 제품을 구매 하는 데 사용한 Azure 구독과 연결 된 고유 식별자입니다. ID는 이전에 Azure 구독 GUID였습니다. |
| MonthStartDate | 월 시작 날짜 | 월 시작 날짜는 구매 월을 나타냅니다. |
| 제품 유형 | 제품 유형 | 상용 marketplace 제품의 유형입니다. |
| Azure 라이선스 유형 | Azure 라이선스 유형 | Azure 구매 고객이 체결하는 라이선싱 계약 유형입니다. 채널 라고도 합니다. 가능한 값은 다음과 같습니다.<ui><li>클라우드 솔루션 공급자</li><li>Enterprise</li><li>대리점을 통해 엔터프라이즈</li><li>종 량 제</li></ul> |
| Marketplace 라이선스 유형 | Marketplace 라이선스 유형 | 상업적 marketplace 제안의 청구 방법입니다. 가능한 값은 다음과 같습니다.<ul><li>Azure를 통해 청구</li><li>자신의 라이선스 가져오기</li><li>무료</li><li>Microsoft as 재판매인</li></ul> |
| SKU | SKU | 제품에 연결 된 계획입니다. |
| 고객 국가 | 고객 국가/지역 | 고객이 입력한 국가/지역 이름. 국가/지역은 고객의 Azure 구독에서 국가/지역과 다를 수 있습니다. |
| 미리 보기 SKU | 미리 보기 SKU | SKU를 "preview"로 태그가 지정 된 경우 값이 표시 됩니다. 이에 따라 SKU가 태그 지정된 경우 값은 "예"이며, 사용자가 승인한 Azure 구독만 이 이미지를 배포하고 사용할 수 있습니다. SKU가 "미리 보기"로 식별되지 않은 경우 값은 "아니요"입니다. |
| SKU 청구 유형 | SKU 청구 유형 | 제품의 각 SKU와 연결 된 청구 유형입니다. 가능한 값은 다음과 같습니다.<ul><li>무료</li><li>유료</li></ul> |
| IsInternal | 사용되지 않음 | 사용되지 않음 |
| VM 크기 | Virtual Machine 크기 | VM 기반 제품 형식의 경우이 엔터티는 제품의 SKU와 연결 된 VM의 크기를 나타냅니다. |
| 클라우드 인스턴스 이름 | 클라우드 인스턴스 이름 | VM이 배포된 Microsoft Cloud입니다. |
| Service설계도 이름 | 사용되지 않음 | 사용 되지 않음 (SKU와 동일한 정의) |
| 제품 이름 | 제품 이름 | 상용 marketplace 제품의 이름입니다. |
| DeploymentMethod | 사용되지 않음 | 사용 되지 않음 (제품 형식과 같은 정의)
 |
| 고객 회사 이름 | 고객 회사 이름 | 고객이 입력한 회사 이름입니다. 이름은 고객의 Azure 구독에서 구/군/시와 다를 수 있습니다. |
| 사용 날짜 | 사용 날짜 | 사용량 기반 자산에 대 한 사용 이벤트 생성 날짜입니다. |
| IsMultisolution | Multisolution | 제품이 Multisolution 제품 유형 인지 여부를 나타냅니다. |
| 새 고객 | 사용되지 않음 | 사용되지 않음 |
| 핵심 크기 | 핵심 크기 | VM 기반 제품에 연결 된 코어 수입니다. |
| 사용 유형 | 사용 유형 | 제품에 연결 된 사용 이벤트가 다음 중 하나 인지 여부를 나타냅니다.<ul><li>정규화 된 사용</li><li>원시 사용</li><li>요금제 사용</li></ul> |
| 평가판 종료 날짜 | 평가판 종료 날짜 | 이 주문의 평가 기간이 종료되었거나 종료될 예정인 날짜입니다. |
| 고객 통화(CC) | 고객 통화 | 상업적 marketplace 거래에 대해 고객이 사용 하는 통화입니다. |
| 가격(CC) | 가격 | 고객 통화로 표시 되는 SKU의 단가입니다. |
| PC(지급 통화) | 지급 Currency | 게시자는 게시자에 의해 구성 된 통화에서 자산과 연결 된 사용 이벤트에 대해 지불 됩니다. |
| 예상 가격(PC) | 예상 가격 | 게시자에 의해 구성 된 통화의 SKU 단가입니다. |
| 사용량 기준 | 사용량 기준 | 지급 트랜잭션 보고서와 함께 사용 보고서 (상용 marketplace 분석)를 연결 하는 데 사용 되는 연결 된 GUID입니다. 사용 참조는 지급 트랜잭션 보고서에서 OrderId 및 LineItemId와 연결 됩니다. |
| 사용 단위 | 사용 단위 | SKU와 연결 된 소비 단위입니다. |
| Customer Id | 고객 ID | 고객에게 할당된 고유 식별자입니다. 고객은 Azure Marketplace 구독이 0 개 이상 있을 수 있습니다. |
| 청구 계정 Id | 청구 계정 ID | 청구를 생성 하는 계정의 식별자입니다. **청구 계정 ID** 를 **CustomerID** 에 매핑하여 고객, 주문 및 사용 현황 보고서에 지급 트랜잭션 보고서를 연결 합니다. |
| 사용 수량 | 사용 수량 | 고객이 배포한 자산에서 사용 하는 총 사용량 단위입니다.<br>사용 유형 항목을 기준으로 합니다. 예를 들어 사용 유형이 정규화 된 사용 인 경우 사용 수량은 정규화 된 사용에 대 한 것입니다. |
| NormalizedUsage | 정규화된 사용량 | 고객이 배포한 자산에서 사용 되는 총 표준화 된 사용 단위입니다.<br>정규화된 사용 시간은 VM 코어 수를 고려하도록 정규화된 사용 시간으로 정의됩니다([VM 코어 수] x [가공되지 않은 사용량 시간])으로 계산됩니다. "SHAREDCORE"로 지정된 VM은 [VM 코어 수] 승수로 1/6(0.1666)을 사용합니다. |
| MeteredUsage | 요금제 사용량 | 고객이 배포한 제품을 사용 하 여 구성 된 미터에서 사용 하는 총 사용량 단위입니다. |
| RawUsage | 원시 사용량 | 고객이 배포한 자산에서 사용 하는 총 원시 사용 단위입니다.<br>원시 사용 시간은 Vm이 사용 단위를 기준으로 실행 되는 시간의 양에 따라 정의 됩니다. |
| 예상 요금 소계(CC) | 고객 통화의 예상 연장 요금 | 사용량과 관련 된 요금을 나타냅니다. 열은 가격 (CC)과 사용 수량입니다. |
| 예상 요금 소계(PC) | 지급 Currency의 예상 연장 요금 | 사용량과 관련 된 요금을 나타냅니다. 이 열은 예상 가격 (PC) 및 사용 수량을 곱한 것입니다. |
||||

### <a name="usage-page-filters"></a>사용 페이지 필터

**사용** 페이지 필터는 주문 페이지 수준에서 적용 됩니다. 하나 이상의 필터를 선택 하 여 보려는 조건과 사용 현황 주문 데이터의 그리드/내보내기에 표시 하려는 데이터에 대 한 차트를 렌더링할 수 있습니다. 사용량 페이지의 오른쪽 위 모서리에서 선택한 월 범위에 대해 추출 된 데이터에 필터가 적용 됩니다.

VM Raw 사용에 대 한 위젯 및 내보내기 보고서는 다음과 같은 차이가 있는 VM 정규화 된 사용량과 유사 합니다.

- 정규화된 사용 시간은 VM 코어 수를 고려하도록 정규화된 사용 시간으로 정의됩니다([VM 코어 수] x [가공되지 않은 사용량 시간])으로 계산됩니다. "SHAREDCORE"로 지정된 VM은 [VM 코어 수] 승수로 1/6(0.1666)을 사용합니다.
- 원시 사용 시간은 Vm이 사용 단위를 기준으로 실행 되는 시간의 양에 따라 정의 됩니다.

### <a name="metered-billing-usage"></a>요금제 사용량 청구

**요금제** 사용량 탭에는 측정기 별 사용량을 측정 하는 제품 유형에 대 한 사용 정보가 표시 됩니다. 현재는 SaaS 제품 유형 초과분이 표시됩니다. 이 탭은 SaaS 요금 청구 사용량에 대한 초과 추세를 그래픽으로 나타냅니다.

- **미터 크기별 초과 추세** : 제품의 선택한 미터 크기에 대한 월별 초과 추세를 표시합니다. X 축은 월을 나타내고 Y 축은 초과분 사용 수량을 나타냅니다. 사용자 지정 미터의 측정 단위도 Y축에 표시됩니다.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="사용 대시보드의 VM 정규화 된 사용 현황 차트를 보여 줍니다.":::

- **Sku 별 초과분 추세** : sku/요금제로 선택한 미터 차원의 사용 수량 추세를 나타냅니다. 선택한 제품에 대 한 사용량이 가장 많은 상위 5 개 요금제가 표시 됩니다.

- **고객에의 한 초과분 추세** : 고객 리더 보드는 사용 시간이 가장 높은 고객의 누적 목록을 나타내며, _리더 보드_ 에 표시 되며 사용자 지정 측정기의 최고 사용량을 기준으로 순위가 지정 됩니다. 선택한 측정기 차원의 초과분 사용량 추세를 보려면 리더 보드의 고객을 선택 합니다.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="사용량 대시보드에서 고객 차트의 요금제 사용량을 보여 줍니다.":::

사용자 지정 측정기를 사용 하는 제품이 여러 개 있는 경우 요금제 청구 사용량 보고서에는 사용자 지정 측정기 차원에 따라 모든 제품에 대 한 사용 정보가 표시 됩니다.

> [!TIP]
> 위젯의 오른쪽 위 모퉁이에 있는 다운로드 아이콘을 사용 하 여 데이터를 다운로드할 수 있습니다. "엄지 손가락 위로" 또는 "엄지 손가락 아래로 이동" 아이콘을 클릭 하 여 각 위젯에 대 한 피드백을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 상용 marketplace에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터에서 상업용 marketplace에 대 한 분석 보고서 액세스](./partner-center-portal/analytics.md)를 참조 하세요.
- 제품에 대한 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값은 [상업용 마켓플레이스 분석의 요약 대시보드](./summary-dashboard.md)를 참조하세요.
- 그래픽 및 다운로드 가능한 형식으로 주문에 대 한 자세한 내용은 [상용 marketplace 분석의 주문 대시보드](./orders-dashboard.md) 를 참조 하세요.
- VM (가상 컴퓨터)은 사용량 및 요금제 청구 메트릭을 제공 합니다. [상용 marketplace 분석의 사용량 대시보드](usage-dashboard.md)를 참조 하세요.
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 Marketplace 분석의 다운로드 대시보드](./partner-center-portal/downloads-dashboard.md)를 참조하세요.
- Azure Marketplace 및 Microsoft AppSource에서 제공 하는 제품에 대 한 고객 피드백의 통합 보기를 보려면 [파트너 센터에서 등급 & 리뷰 분석 대시보드](./partner-center-portal/ratings-reviews.md)를 참조 하세요.
- 상용 marketplace 분석 및 데이터 용어의 포괄적인 사전에 대 한 질문과 대답은 [상용 marketplace 분석 용어 및 일반적인 질문](./partner-center-portal/faq-terminology.md)을 참조 하세요.
