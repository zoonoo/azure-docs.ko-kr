---
title: 선불 또는 월별 지불로 Azure 예약 구매
description: 선불 또는 월별 지불로 Azure 예약을 구매 하는 방법을 알아보세요.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806949"
---
# <a name="purchase-reservations-with-monthly-payments"></a>월별 지불로 예약 구매

지금까지 Azure 예약은 앞으로 지불 해야 합니다. 이제 월별 지불로 예약에 대 한 비용을 지불할 수 있습니다. 전체 금액을 지불 하는 최고 판매 구매와 달리 월간 지불 옵션은 해당 기간의 전체 예약 비용을 균등 하 게 나눕니다. 선불 및 월별 예약의 총 비용은 동일 하며, 매월 지불 하기로 선택 하는 경우 추가 요금은 청구 되지 않습니다.

월간 지불 금액은 현지 통화에 대 한 현재 월의 시장 환율에 따라 달라질 수 있습니다.

월간 지불액은 다음에 대해 제공 됩니다.

- 가상 머신
- SQL 데이터베이스
- SQL Data Warehouse
- Cosmos DB
- App Service 스탬프 요금

[Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade)에서 예약을 구매 합니다.

![예약 구매를 보여 주는 예제](./media/billing-monthly-payments-reservations/purchase-reservation.png)

예약 구매를 수행 하는 동안 결제 일정을 볼 수 있습니다. **전체 지불 일정 보기**를 클릭 합니다.

![예약 지불 일정을 보여 주는 예](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

구입 후 지불 일정을 보려면 예약을 선택 하 고 **예약 주문 ID**를 클릭 한 다음 **지불** 탭을 클릭 합니다.

## <a name="view-payments-made"></a>상환 보기

Api, 사용 현황 데이터를 사용 하 여 수행 된 지불액과 비용 분석을 볼 수 있습니다. 매월 요금을 지불 하는 예약의 경우 빈도 값은 사용 데이터 및 예약 요금 API에서 **되풀이** 로 표시 됩니다. 선불 예약의 경우 값은 **onetime**로 표시 됩니다.

비용 분석은 기본 보기의 월간 구매를 표시 합니다. 구매 유형별로 **구매** 필터를 적용 하 고, 모든 구매를 표시 하는 **빈도** 를 **되풀이** 합니다. 예약만 보려면 **예약**에 필터를 적용 합니다.

![비용 분석에서 예약 구매 비용을 보여 주는 예](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>갱신 시 월간 지불로 전환

예약을 갱신 하는 경우 청구 주기를 월로 변경할 수 있습니다.

## <a name="exchange-and-refunds"></a>Exchange 및 환불

다른 예약과 마찬가지로 월별 요금 청구로 구매한 판매를 환불 하거나 교환할 수 있습니다. 현재 지원 요청을 제출 하 여 월간 청구로 구매한 예약에 대해 exchange 또는 환불을 시작할 수 있습니다.

매월 유료 예약을 교환할 때 새 구매의 총 수명 비용은 반환 된 예약에 대해 취소 되는 남은 지불액 보다 커야 합니다. 교환할 수 있는 다른 제한 또는 요금은 없습니다. 앞으로 유료 예약을 교환 하 여 매월 청구 되는 새 예약을 구입할 수 있습니다. 그러나 새 예약의 수명 값은 반환 되는 예약의 비례 값 보다 커야 합니다.

매월 지불 된 예약을 취소 하는 경우 Microsoft는 취소 된 이후에 확정 된 지불에 취소 요금을 적용할 수 있습니다. 남은 확정 된 지불액은 $5만 USD 환불 한도에 대해 계산 됩니다.

Exchange 및 환불에 대 한 자세한 내용은 [셀프 서비스 교환 및 환불 for Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md)를 참조 하세요.

## <a name="faq"></a>FAQ

Q. Azure에서 "부분 업 예약"을 제공 하나요?<br>
1\. 아니요. 프런트 및 매월 선불 예약에 대 한 비용은 동일 하기 때문에 Microsoft는 일부 업 선불 지불액을 지원 하지 않습니다.

Q. CSP (Microsoft 클라우드 솔루션 공급자) 프로그램에서 월간 지불을 사용할 수 있나요?<br>
1\. 예, 파트너는 Azure Portal에서 CSP 고객에 대 한 예약을 구매할 수 있습니다. 월별 청구로 예약을 구매 하는 기능은 파트너 센터에서 사용할 수 없습니다.

Q. 미국 Azure Government 고객 인데, 예약 구매에 대해 매월 지불할 수 있나요?<br>
1\. 현재는 아닙니다.

Q. 지원 티켓을 만드는 대신 Azure Portal에서 직접 교환 하거나 환불 할 수 있는 시기는 언제 입니까?<br>
1\. 현재는 아닙니다. 월별 지불액을 사용한 exchange 및 환불 예약 요청은 Azure 지원에 의해 처리 됩니다.

## <a name="next-steps"></a>다음 단계

- 예약에 대 한 자세한 내용은 [Azure Reservations?](billing-save-compute-costs-reservations.md) 을 참조 하세요.
- 예약을 구입 하기 전에 수행 해야 하는 작업에 대해 알아보려면 [구매 하기 전에 올바른 VM 크기 확인](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy) 을 참조 하세요.
