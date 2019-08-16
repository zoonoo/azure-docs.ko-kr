---
title: Azure Reservations에 대 한 셀프 서비스 교환 및 환불
description: Azure Reservations을 교환 하거나 환불 하는 방법에 대해 알아보세요.
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 828bc3784a118a81adc4391b1bf222c00ee2025a
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543010"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure Reservations에 대 한 셀프 서비스 교환 및 환불

Azure Reservations는 변화 하는 요구를 충족 하는 데 도움이 되는 유연성을 제공 합니다. 예약을 동일한 유형의 다른 예약으로 교환할 수 있습니다. 예약이 더 이상 필요 없는 경우 연간 50,000USD까지 예약을 환불할 수 있습니다.

US Government 기업계약 고객은 셀프 서비스 교환 및 취소 기능을 사용할 수 없습니다. 종 량 제 및 CSP를 비롯 한 미국 정부 구독 유형이 지원 됩니다.

기존 예약을 교환 하거나 환불 하려면 예약 주문에 대 한 소유자 액세스 권한이 있어야 합니다.

## <a name="exchange-an-existing-reserved-instance"></a>기존 예약 인스턴스 교환

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)에서 3 개의 빠른 단계를 사용 하 여 예약을 교환할 수 있습니다.

1. 환불 하려는 예약을 선택 하 고 **Exchange**를 클릭 합니다.  
    ![반환할 예약을 보여 주는 예제 이미지](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. 구매하려는 VM 제품을 선택하고 수량을 입력합니다. 새 구매 합계가 반환 합계 보다 높은 지 확인 합니다. [구매 하기 전에 적절 한 크기를 결정](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)합니다.  
    ![Exchange를 사용 하 여 구매할 VM 제품을 보여 주는 예제 이미지](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. 트랜잭션을 검토 하 고 완료 합니다.  
    ![Exchange를 사용 하 여 구매할 VM 제품을 보여 주는 예제 이미지 (반환 완료)](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

예약을 환불 하려면 **예약 세부 정보** 로 이동 하 여 **환불**을 클릭 합니다.

## <a name="how-transactions-are-processed"></a>트랜잭션 처리 방법

먼저 Microsoft는 기존 예약을 취소 하 고 해당 예약에 대 한 환불의 등급을 지정 합니다. Exchange가 있는 경우 새 구매가 처리 됩니다. Microsoft는 계정 유형과 지불 방법에 따라 다음 방법 중 하나를 사용 하 여 환불를 처리 합니다.

### <a name="enterprise-agreement-customers"></a>기업 계약 고객

원래 구매가를 사용 하는 경우 교환 및 환불의 현금 약정 금액에 금액이 추가 됩니다. 원래 구매한 이후의 모든 초과분 청구서는 현금 약정 금액을 사용 하도록 다시 열 게 됩니다. 예약을 사용 하는 금액 약정 기간이 더 이상 활성 상태가 아니면 현재 기업 계약 금액 약정 기간에 크레딧이 추가 됩니다.

원래 구매가 초과분 된 경우 Microsoft는 신용 메모를 발급 합니다.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>종 량 제 청구서 지불 및 CSP 프로그램

원래 예약 구매 송장이 취소 되 고 환불을 위해 새 송장이 생성 됩니다. 교환의 경우 새 청구서에는 환불 및 새 구매가 표시 됩니다. 환불 금액은 구매를 기준으로 조정 됩니다. 예약을 환불 하는 경우 비례 금액은 Microsoft에 그대로 유지 되며 향후 예약 구매에 맞게 조정 됩니다.

### <a name="pay-as-you-go-credit-card-customers"></a>종 량 제 신용 카드 고객

원래 송장이 취소 되 고 새 송장이 생성 됩니다. 금액은 원래 구매에 사용 된 신용 카드로 환불 됩니다. 카드를 변경한 경우 [지원 담당자에 게 문의 하세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>취소, 교환 및 환불 정책

Azure에는 취소, 교환 및 환불에 대 한 다음과 같은 정책이 있습니다.

**Exchange 정책**

- 기존 예약을 여러 개 반환 하 여 동일한 유형의 새 예약을 구입할 수 있습니다. 한 유형의 예약을 다른 유형으로 교환할 수 없습니다. 예를 들어 VM 예약을 반환 하 여 SQL 예약을 구입할 수 없습니다.
- 예약 소유자만 exchange를 처리할 수 있습니다. [예약을 관리할 수 있는 사용자를 추가 하거나 변경 하는 방법에 대해 알아봅니다](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Exchange는 환불 및 다시 구매로 처리 되며, 취소 및 새 구매를 위해 서로 다른 트랜잭션이 생성 됩니다. 비례 하는 예약 금액은 환불 예약에 대해 계산 됩니다. 새 구매에 대해 완전히 청구 됩니다. 비례를 계산 하는 예약 금액은 반환 되는 예약의 매일 비례 하는 잔여 값입니다.
- 예약을 구매 하는 데 사용 된 기업 계약이 만료 되어 새 계약으로 갱신 된 경우에도 예약을 교환 하거나 환불 받을 수 있습니다.
- Exchange를 사용 하 여 크기, 지역, 수량 및 용어와 같은 모든 예약 속성을 변경할 수 있습니다.
- 새 구매 금액은 반환 된 금액 보다 크거나 같아야 합니다.
- Exchange의 일부로 구매한 새 예약에는 exchange의 시간부터 시작 하는 새로운 용어가 있습니다.
- 교환에 대 한 페널티 또는 연간 제한은 없습니다.

**환불 정책**
- 예약을 취소 하는 경우 12% 조기 종료 요금이 있을 수 있습니다.
- 취소에 대 한 환불 받은 비용은 12% 조기 종료 수수료를 뺀 나머지 비례 등급 잔액입니다. 취소 하려면 Azure Portal 예약으로 이동 하 여 **환불**을 선택 합니다.
- 총 환불 금액은 12 개월의 롤링 기간에 $5만 USD를 초과할 수 없습니다.
- 환불는 구매 가격이 나 예약의 현재 가격 중 가장 낮은 가격을 기준으로 계산 됩니다.
- 예약 소유자만 환불을 처리할 수 있습니다. [예약을 관리할 수 있는 사용자를 추가 하거나 변경 하는 방법에 대해 알아봅니다](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft는 모든 반환에 대해 12% 페널티를 부과 하는 권리를 보유 합니다. 패널티는 현재 청구 되지 않지만 향후 청구 될 예정입니다.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Premium storage에 대 한 비 premium storage

Premium storage를 지원 하지 않는 VM 크기에 대해 구매한 예약을 해당 하는 VM 크기로 교환할 수 있습니다. 예를 들어 _F1s_에 대 한 _F1_ . Exchange를 만들려면 예약 세부 정보로 이동 하 여 **exchange**를 클릭 합니다. Exchange는 예약 인스턴스의 용어를 다시 설정 하거나 새 트랜잭션을 만들지 않습니다.

## <a name="need-help-contact-us"></a>도움이 필요하십니까? 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)를 참조하세요.
- Azure Reservations에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure Reservations란?](billing-save-compute-costs-reservations.md)
    - [Azure에서 Reservations 관리](billing-manage-reserved-vm-instance.md)
    - [예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
    - [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
    - [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)
