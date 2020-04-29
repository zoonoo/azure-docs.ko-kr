---
title: 상업적 Marketplace의 파트너 센터 분석에 대 한 요약 대시보드
description: 파트너 센터의 요약 대시보드에서 marketplace 활동을 요약 하는 집계 데이터의 그래프, 추세 및 값에 액세스 하는 방법에 대해 알아봅니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 86bce85c6ec273b4ab5f9f00cbae68fc054f53f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262403"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>상업적 marketplace 분석의 요약 대시보드

이 문서에서는 파트너 센터의 요약 대시보드에 대 한 정보를 제공 합니다. 이 대시보드에는 제품의 마켓플레이스 작업을 요약 하는 집계 데이터의 그래프, 추세 및 값이 표시 됩니다.

요약 대시보드에 액세스 하려면 상업적 Marketplace에서 **[분석 대시보드](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** 를 엽니다.

>[!NOTE]
> 분석 용어에 대 한 자세한 정의는 [상업적 marketplace 분석에 대 한 질문과 용어](./faq-terminology.md)를 참조 하세요.

## <a name="summary-dashboard"></a>요약 대시보드

**요약** 대시보드는 각 제안 유형에 따라 개요를 제공 합니다. **통찰력** 은 중요 한 정보를 한눈에 보여 주고 제품의 판매 활동을 광범위 하 게 보여 줍니다. **요약** 대시보드를 사용 하 여 이러한 보고서를 시각화할 수 있습니다. 이 문서에서는 다음 요소 각각에 대해 자세히 설명 합니다.

- [날짜 범위](#date-range)
- [요약 섹션](#summary-section)
- [지리 별 고객](#customers-by-geography)
- [급수 추세 차트](#growth-trend-charts)
- [고객 순위표](#customer-leaderboard)
- [좌석 개수 추세](#seat-count-trend)
- [무료 평가판 SaaS 주문 추세](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>요약 대시보드의 요소

다음 섹션에서는 요약 대시보드를 사용 하는 방법과 데이터를 읽는 방법을 설명 합니다.

### <a name="date-range"></a>날짜 범위

각 페이지의 오른쪽 위 모서리에서 날짜 범위 선택 항목을 찾을 수 있습니다. 지난 3, 6 또는 12 개월을 기준으로 하는 날짜 범위를 선택 하거나 최대 기간이 12 개월 인 사용자 지정 날짜 범위를 선택 하 여 **요약** 페이지 그래프의 출력을 사용자 지정 합니다. 기본 날짜 범위는 6 개월입니다.

![파트너 센터 분석 대시보드](./media/analyze-dashboard.png)

### <a name="summary-section"></a>요약 섹션

요약 섹션에는 선택한 날짜 범위 동안 생성 된 모든 주문 수, 획득 한 고객 및 사용 현황이 표시 됩니다. 일부 현재 월은 이러한 메트릭의 계산에서 제외 됩니다. 예를 들어 6M 시간 프레임을 선택한 경우 현재 달 이전의 6 개월 동안 사용 시간이 계산 됩니다. 사용자 지정 날짜 범위를 선택 하면 현재 월의 일부 금액이 계산에서 제외 됩니다.

![요약 대시보드의 성장 추세](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>요약 섹션 읽기

- **주문**: 지금까지 게시 한 제품에 대해 구매한 주문 (취소 된 주문 제외)의 수입니다.
- **고객**: 제품을 구매 하 고 취소 되지 않은 주문이 하나 이상 있는 모든 고객의 수입니다.
- **정규화 된 사용 시간**: VM 코어 수를 고려 하 여 표준화 된 사용 시간 ([vm 코어 수] x [원시 사용 시간])으로 정의 됩니다. "SHAREDCORE"로 지정 된 Vm은 1/6 (또는 0.1666)를 [VM 코어 수] 승수로 사용 합니다.
- **원시 사용 시간**: vm이 몇 시간 동안 실행 된 시간입니다. **총 주문**, **총 고객**, **표준화 된 사용 시간**, **원시 사용 시간**, **페이지 방문 횟수**및 **작업에** 대 한 호출에 대 한 백분율 값은 선택한 날짜 범위 ([지난 달 사용-첫 달 사용량])/첫 번째 월 사용에 대 한 사용량 증가의 양을 나타냅니다. 위에서 설명한 대로 현재 월의 일부는이 메트릭에 제외 됩니다.
- **급수 추세**: 차트의 열 위로 마우스를 가져가면 막대 그래프가 각 월의 값을 표시 합니다.
- **위쪽을 가리키는 녹색 삼각형**: 양의 증가 추세를 나타냅니다.
- **아래쪽을 가리키는 빨간색 삼각형**: 이전 월을 기준으로 하는 음의 증가 추세를 나타냅니다.

### <a name="customers-by-geography"></a>지리 별 고객

**지리 열 지도 고객** 은 세계 지도에 고객 수를 표시 합니다.

![요약 대시보드의 지리 별 고객](./media/summary-customers-by-geography.png)

- 맵을 이동 하 여 정확한 위치를 볼 수 있습니다.
- 특정 위치를 확대할 수 있습니다.
- 열 지도에는 특정 위치의 고객 수, 주문 수, 표준화 된 사용 시간에 대 한 세부 정보를 볼 수 있는 보조 표가 있습니다.
- 표에서 국가를 검색 하 고 선택 하 여 지도의 위치를 확대할 수 있습니다. 지도에서 **홈** 단추를 눌러 원래 뷰로 되돌립니다.
- **새** 고객이 선택한 날짜 범위 내에서 해당 월에 처음으로 제품 중 하나를 구입 했습니다.

### <a name="growth-trend-charts"></a>급수 추세 차트

**구매한 주문의** 증가 (취소 된 주문 포함), **획득 한 고객** (손실 된 고객 포함) 및 보고 된 **사용량** 을 기준으로 추세를 볼 수 있습니다 .이는 선택한 날짜 범위에 따라 월별로 표시 됩니다. 차트 아래의 링크를 선택 하 여 이러한 추세를 추가로 분석할 수 있습니다. 그러면 해당 **주문**, **사용량**, **고객**또는 **Marketplace 정보** 페이지로 이동 합니다.

Marketplace 제품 **페이지 방문 및 작업** 추세 차트 호출이 두 개의 탭에 Azure Marketplace 및 appsource에 대해 표시 됩니다.

![페이지 방문 및 작업에 대 한 호출 요약 대시보드의 작업 추세 차트](./media/summary-page-visits-and-cta.png)

제품 **별 주문** 차트는 제품 이름에 따라 주문을 구성 합니다.

**매출 별 주문 채널** 원형 차트는 선택한 날짜 범위 동안 판매 채널별 주문 (고객이 취소 한 주문 포함)을 구성 합니다. 판매 채널은 고객이 Azure를 구매 하는 데 사용 하는 라이선스 계약의 유형으로, Azure를 구매 하는 데 사용 됩니다 .이는 CSP (클라우드 솔루션 공급자), Enterprise, Enterprise through 재판매인, GTM 및 종 량 제입니다.

**제품** 및 **판매 채널 별** 사용 현황 별 사용 원형 차트는 각각 인기 제품 및 판매 채널에 대 한 사용량의 분석을 제공 합니다. 내부 원형 차트는 원시 사용을 나타내고 외부 원형 차트는 정규화 된 사용량을 나타냅니다.

Marketplace 라이선스 유형 및 **marketplace 별 사용 라이선스 유형의** **주문** 원형 차트에는 해당 라이선스 유형별로 주문 및 사용 현황에 대 한 분석 결과가 표시 됩니다. 라이선스 종류는 다음과 같습니다.

- **Azure를 통해 청구**:이 라이선스 유형을 사용 하 여 microsoft를 통해 제품을 판매 하도록 선택 하면 microsoft가 사용자를 대신 하 여 고객에 게 요금을 청구 합니다. 지불 유형은 신용 카드나 기업 청구서를 통해 종 량 제를 포함 합니다.
- 사용자 **고유의 라이선스 가져오기**: Microsoft는이 유형의 marketplace 제품을 사용 하 여 고객에 게 요금을 청구 하지 않습니다. 이 사용량은 marketplace에서 **지금 가져오기 (무료)** 로 나열 됩니다.
- **무료**: Microsoft는이 유형의 marketplace 제품을 사용 하 여 고객에 게 요금을 청구 하지 않습니다. 이 사용량은 marketplace에서 **무료 평가판** 으로 나열 됩니다.
- **Microsoft as 재판매인**: microsoft 대리점에서 **CSP (클라우드 솔루션 공급자) 프로그램**의 일부로 판매 되는 제품을 나타냅니다.

### <a name="customer-leaderboard"></a>고객 순위표

주문 수가 가장 많은 상위 50 고객은 최고 주문 수와 주문 비율을 기준으로 하 여 *리더 보드*에 표시 됩니다.

- 고객을 선택 하 여 프로필 세부 정보, 제품 별로 구성 된 주문 또는 Azure 라이선스 유형 및 가격 책정 채널 별로 구성 된 주문을 볼 수 있습니다.
- **주문 별 제품** 도넛형 차트에는 상위 4 개 제품 (주문 수 기준)과 남은 제공이 ' Rest 모두 '로 그룹화 되어 있습니다.
- **제품 별 정규화 된 사용** 도넛형 차트는 사용량에 따라 상위 5 개 제품을 제공 합니다.

> [!NOTE]
> 고객 개인 정보는 고객이 동의를 제공한 경우에만 제공 됩니다. **소유자** 역할 권한 수준으로 로그인 한 경우이 정보를 볼 수 있습니다. **참가자** 역할을 가진 사용자는이 정보를 볼 수 없습니다. [사용자 역할 및 사용 권한에 대해 자세히 알아보세요](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>좌석 개수 추세

사용자 **당/사이트별 주문** 차트는 가격 책정 모델에 따라 구매한 모든 주문에 대 한 분석을 제공 합니다. **좌석 수 추세** 차트에는 모든 사용자의 SaaS (Software As a Service) 제품에 대해 구매한 사용자 및 주문이 표시 됩니다.

### <a name="free-trials-saas-orders-trend"></a>무료 평가판 SaaS 주문 추세

**무료 평가판 saas 주문 추세** 차트는 30 일 평가판으로 무료 평가판 saas 제품의 주문 추세를 나타냅니다.

## <a name="next-steps"></a>다음 단계

- 파트너 센터 상업적 marketplace에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터에서 상업용 marketplace에 대 한 분석](./analytics.md)을 참조 하세요.
- 그래픽 및 다운로드 가능한 형식으로 주문에 대 한 자세한 내용은 [상용 marketplace 분석의 주문 대시보드](./orders-dashboard.md)를 참조 하세요.
- VM (가상 컴퓨터)은 사용량 및 요금제 청구 메트릭을 제공 합니다. [상용 marketplace 분석의 사용량 대시보드](./usage-dashboard.md)를 참조 하세요.
- 성장 추세를 비롯 한 고객에 대 한 자세한 내용은 [상용 marketplace 분석의 고객 대시보드](./customer-dashboard.md)를 참조 하세요.
- 최근 30 일 동안의 다운로드 요청 목록은 [상용 marketplace 분석에서 대시보드 다운로드](./downloads-dashboard.md)를 참조 하세요.
- Azure Marketplace 및 AppSource의 제품에 대 한 고객 의견의 통합 보기를 보려면 [상용 Marketplace 분석의 등급 및 리뷰 대시보드](./ratings-reviews.md)를 참조 하세요.
- 상용 marketplace 분석 및 데이터 용어의 포괄적인 사전에 대 한 질문과 대답은 [상업적 marketplace 분석에 대 한 질문과 대답](./faq-terminology.md)을 참조 하세요.
