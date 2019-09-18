---
title: 선불 또는 월별 결제로 Azure 예약 구매
description: 선불 또는 월별 결제로 Azure 예약을 구매하는 방법을 알아봅니다.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70806949"
---
# <a name="purchase-reservations-with-monthly-payments"></a>월별 결제로 예약 구매

지금까지는 Azure 예약에 선불 결제가 필요했습니다. 이제 월별 결제로 예약 비용을 지불할 수 있습니다. 전체 금액을 지불하는 선불 구매와 달리, 월별 결제 옵션은 예약의 총 비용을 기간에 대해 매월 균등하게 나눕니다. 선불과 월별 예약의 총 비용은 동일하며, 매달 지불하기로 선택하면 추가 요금이 청구되지 않습니다.

월별 결제 금액은 현지 통화에 대한 이번 달 시장 환율에 따라 달라질 수 있습니다.

월별 결제는 다음에 사용할 수 있습니다.

- 가상 머신
- SQL Database
- SQL Data Warehouse
- Cosmos DB
- App Service 인지세

[Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade)에서 예약을 구매합니다.

![예약 구매를 보여주는 예](./media/billing-monthly-payments-reservations/purchase-reservation.png)

예약 구매 중에 결제 일정을 볼 수 있습니다. **전체 결제 일정 보기**를 클릭하세요.

![예약 지불 일정을 보여주는 예](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

구입 후 지불 일정을 보려면 예약을 선택하고 **예약 주문 ID**를 클릭한 다음, **지불** 탭을 클릭합니다.

## <a name="view-payments-made"></a>결제 내역 보기

지불한 금액은 API, 사용 현황 데이터를 사용하여 그리고 비용 분석에서 볼 수 있습니다. 매월 지불한 예약의 경우, 사용 현황 데이터 및 Reservation Charges API에 빈도 값이 **되풀이**로 표시됩니다. 선불 예약의 경우 이 값이 **한 번**으로 표시됩니다.

비용 분석의 기본 보기에 월별 구매가 표시됩니다. **요금 유형**에 **구매** 필터를 적용하고 **빈도**에 **되풀이** 필터를 적용하면 모든 구매가 표시됩니다. 예약만 보려면 **예약**에 필터를 적용합니다.

![비용 분석에서 예약 구매 비용을 보여주는 예](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>갱신 시 월별 결제로 전환

예약을 갱신할 때 대금 청구 주기를 매달로 변경할 수 있습니다.

## <a name="exchange-and-refunds"></a>취소 및 환불

다른 예약과 마찬가지로 월별 청구를 통해 구매한 예약을 환불하거나 교환할 수 있습니다. 현재 지원 요청을 제출하여, 월별 청구를 통해 구매한 예약에 대한 교환이나 환불을 시작할 수 있습니다.

매월 지불하는 예약을 교환하는 경우에는, 새 구매의 총 수명 비용이 반환되는 예약에 대해 취소되는 남은 지불액보다 커야 합니다. 교환에 대한 다른 제한이나 수수료는 없습니다. 선불로 지불한 예약을 교환하여 매월 청구되는 새로운 예약을 구매할 수 있습니다. 단, 새 예약의 수명 값이 반환되는 예약에 대해 비례 배분된 값보다 커야 합니다.

매월 지불하는 예약을 취소하는 경우에는, 취소되는 향후 확정된 지불액에 대해 취소 수수료가 적용될 수 있습니다. 확정된 남은 지불액은 $50,000USD 환불 한도가 될 때까지 누적됩니다.

교환 및 환불에 대한 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](billing-azure-reservations-self-service-exchange-and-refund.md)을 참조하세요.

## <a name="faq"></a>FAQ

Q. Azure에 "부분 선불 예약"이 제공되나요?<br>
a. 아니요. 선불 및 매달 지불하는 예약 비용은 동일하기 때문에 부분 선불 결제는 지원되지 않습니다.

Q. Microsoft CSP(클라우드 솔루션 공급자) 프로그램에 월별 결제를 사용할 수 있나요?<br>
a. 예, 파트너는 Azure Portal에서 CSP 고객을 위한 예약을 구매할 수 있습니다. 파트너 센터에서는 월별 청구로 예약을 구매하는 기능을 사용할 수 없습니다.

Q. 미국 Azure Government 고객인데, 예약 구매에 대해 매월 지불할 수 있나요?<br>
a. 현재는 아닙니다.

Q. 지원 티켓을 만드는 대신 Azure Portal에서 직접 교환하거나 환불할 수 있는 시기는 언제인가요?<br>
a. 현재는 아닙니다. 월별 결제로 예약을 교환 및 환불해 달라는 요청은 Azure 지원을 통해 처리됩니다.

## <a name="next-steps"></a>다음 단계

- 예약에 대한 자세한 내용은 [Azure Reservations란?](billing-save-compute-costs-reservations.md)을 참조하세요.
- 예약을 구매하기 전에 수행해야 하는 작업에 대해 알아보려면 [구매하기 전에 적절한 VM 크기 결정](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)을 참조하세요.
