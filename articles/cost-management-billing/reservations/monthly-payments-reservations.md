---
title: 선불 또는 월별 결제로 Azure 예약 구매
description: 선불 또는 월별 결제로 Azure 예약을 구매하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: ede60adc13dadc38e18ee5ade468e01b16523f4f
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235741"
---
# <a name="purchase-reservations-with-monthly-payments"></a>월별 결제로 예약 구매

월별 결제로 예약 비용을 지불할 수 있습니다. 전체 금액을 지불하는 선불 구매와 달리, 월별 결제 옵션은 예약의 총 비용을 기간에 대해 매월 균등하게 나눕니다. 선불과 월별 예약의 총 비용은 동일하며, 매달 지불하기로 선택하면 추가 요금이 청구되지 않습니다.

MCA(Microsoft 고객 계약)를 통해 예약을 구매한 경우 월별 결제 금액은 현지 통화에 대한 이번 달 시장 환율에 따라 달라질 수 있습니다.

월별 결제는 다음에 사용할 수 없습니다. Databricks, SUSE Linux 예약, Red Hat 플랜 및 Azure Red Hat OpenShift Compute.

[Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs)에서 예약을 구매합니다.

![예약 구매를 보여주는 예](./media/monthly-payments-reservations/purchase-reservation.png)

예약 구매 중에 결제 일정을 볼 수 있습니다. **전체 결제 일정 보기**를 클릭하세요.

![예약 지불 일정을 보여주는 예](./media/monthly-payments-reservations/prepurchase-schedule.png)

구입 후 지불 일정을 보려면 예약을 선택하고 **예약 주문 ID**를 클릭한 다음, **지불** 탭을 클릭합니다.

## <a name="view-payments-made"></a>결제 내역 보기

지불한 금액은 API, 사용 현황 데이터를 사용하여 그리고 비용 분석에서 볼 수 있습니다. 매월 지불한 예약의 경우, 사용 현황 데이터 및 Reservation Charges API에 빈도 값이 **되풀이**로 표시됩니다. 선불 예약의 경우 이 값이 **한 번**으로 표시됩니다.

비용 분석의 기본 보기에 월별 구매가 표시됩니다. 모든 구매를 보려면 **구매** 필터를 **요금 유형**에 적용하고 **되풀이** 필터를 **빈도**에 적용합니다. 예약만 보려면 **예약**에 필터를 적용합니다.

![비용 분석에서 예약 구매 비용을 보여주는 예](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>갱신 시 월별 결제로 전환

예약을 갱신할 때 대금 청구 주기를 매달로 변경할 수 있습니다.

## <a name="exchange-and-refunds"></a>취소 및 환불

다른 예약과 마찬가지로 월별 청구를 통해 구매한 예약을 환불하거나 교환할 수 있습니다. 

매월 지불하는 예약을 교환하는 경우에는, 새 구매의 총 수명 비용이 반환되는 예약에 대해 취소되는 남은 지불액보다 커야 합니다. 교환에 대한 다른 제한이나 수수료는 없습니다. 선불로 지불한 예약을 교환하여 매월 청구되는 새로운 예약을 구매할 수 있습니다. 단, 새 예약의 수명 값이 반환되는 예약에 대해 비례 배분된 값보다 커야 합니다.

매달 지불되는 예약을 취소하면 취소된 향후 지불금은 $50,000 USD 환불 제한에 도달하게 됩니다.

교환 및 환불에 대한 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 예약에 대한 자세한 내용은 [Azure Reservations란?](save-compute-costs-reservations.md)을 참조하세요.
- 예약을 구매하기 전에 수행해야 하는 작업에 대해 알아보려면 [구매하기 전에 적절한 VM 크기 결정](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)을 참조하세요.
