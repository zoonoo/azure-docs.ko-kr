---
title: 소프트웨어 계획에 대 한 선불-Azure Reservations
description: 소프트웨어 요금제를 선불 하 여 종 량 제 비용에 대 한 비용을 절감 하는 방법을 알아보세요.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: e4cab8963e3083d265d0254c81b78fbabb84a269
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091533"
---
# <a name="prepay-for-azure-software-plans"></a>Azure 소프트웨어 플랜 선결제

Azure에서 SUSE 및 RedHat 소프트웨어 사용을 선불 때 종 량 제 비용에 대 한 비용을 절감할 수 있습니다. 할인은 SUSE 및 RedHat 미터에만 적용 되 고 가상 컴퓨터 사용에는 적용 되지 않습니다. 추가 절감을 위해 가상 컴퓨터에 대 한 예약을 별도로 구입할 수 있습니다.

Azure Portal에서 SUSE 및 RedHat 소프트웨어 계획을 구매할 수 있습니다. 플랜을 구매하려면:

- 종 량 제 가격으로 하나 이상의 Enterprise 또는 개별 구독에 대 한 소유자 역할이 있어야 합니다.
- 엔터프라이즈 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 된 인스턴스 추가** 옵션을 사용 하도록 설정 해야 합니다. 설정을 사용 하지 않도록 설정한 경우에는 구독에 대 한 EA 관리자 여야 합니다.
- CSP (클라우드 솔루션 공급자) 프로그램의 경우 관리 에이전트 또는 판매 에이전트가 소프트웨어 요금제를 구매할 수 있습니다.

## <a name="buy-a-software-plan"></a>소프트웨어 요금제 구입

1. Azure Portal에 로그인 하 고 [예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)으로 이동 합니다.
2. **추가** 를 클릭 한 후 구매할 소프트웨어 계획을 선택 합니다.
필수 필드를 입력합니다. 구입 하는 항목의 특성과 일치 하는 모든 SUSE Linux VM 또는 RedHat VM은 할인을 가져옵니다. 할인을 받을 실제 배포 수는 선택한 범위 및 수량에 따라 달라집니다.
3. 구독을 선택합니다. 계획에 대 한 요금을 지불 하는 데 사용 됩니다.
구독 지불 방법에는 예약에 대 한 선행 비용이 청구 됩니다. 구독 유형은 기업계약 (제품 번호) 여야 합니다. MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p) 또는 종 량 제 가격의 개별 계약 (제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다.
    - Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다.
    - 종 량 제 가격의 개별 구독에서 요금은 구독의 신용 카드 또는 송장 지불 방법으로 청구 됩니다.
4. 범위를 선택 합니다. 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다.
    - 단일 구독-계획 할인이 구독의 일치 사용에 적용 됩니다.
    - 공유-계획 할인이 청구 컨텍스트의 모든 구독에 있는 일치 하는 인스턴스에 적용 됩니다. 기업 고객의 경우 청구 컨텍스트는 등록 이며 등록의 모든 구독을 포함 합니다. 종 량 제 가격 책정 고객이 포함 된 개별 요금제의 경우 청구 컨텍스트는 계정 관리자가 만든 종 량 제 가격 책정 구독의 모든 개별 계획입니다.
5. 제품을 선택 하 여 VM 크기와 이미지 유형을 선택할 수 있습니다. 할인은 선택한 VM 크기에만 적용 됩니다.
6. 1 년 또는 3 년 기간을 선택 합니다.
7. 청구 할인을 받을 수 있는 선불 VM 인스턴스 수 인 수량을 선택 합니다.
8. 카트에 제품을 추가 하 고 검토 및 구매 합니다.

예약 할인은에 대해 사전 지불 하는 소프트웨어 측정기에 자동으로 적용 됩니다. VM 계산 요금은 계획에서 적용 되지 않습니다. VM 예약은 별도로 구입할 수 있습니다.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>할인은 다른 SUSE VM 크기에 적용 됩니다.

예약 VM 인스턴스와 마찬가지로 SUSE Linux 요금제는 인스턴스 크기의 유연성을 제공 합니다. 할인은 구매한 SUSE 계획과는 다른 크기의 VM을 배포 하는 경우에도 적용 됩니다. 자세한 내용은 [소프트웨어 계획 할인이 적용 되는 방법 이해](../../billing/billing-understand-suse-reservation-charges.md)를 참조 하세요.

## <a name="redhat-plan-discount"></a>RedHat 계획 할인

요금제는 Red Hat Enterprise Linux 가상 컴퓨터에만 사용할 수 있습니다. 이 할인은 RedHat Enterprise Linux SAP HANA Vm 또는 RedHat Enterprise Linux SAP Business Apps Vm에 적용 되지 않습니다.

RedHat 계획 할인은 구매할 때 선택한 VM 크기에만 적용 됩니다. RHEL 요금제는 구매 후 환불 하거나 교환할 수 없습니다.


## <a name="cancellation-and-exchanges-not-allowed"></a>취소 및 교환은 허용되지 않습니다.

구매한 SUSE 또는 RedHat 계획은 취소 하거나 교환할 수 없습니다. 올바른 플랜을 구매했는지 확인하려면 사용량을 확인합니다. 구매할 항목을 식별 하는 데 도움이 필요한 경우 [소프트웨어 계획 할인이 적용 되는 방법 이해](../../billing/billing-understand-suse-reservation-charges.md)를 참조 하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

예약을 관리하는 방법을 알아보려면 [Azure 예약 관리](../../billing/billing-manage-reserved-vm-instance.md)를 참조합니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../../billing/billing-save-compute-costs-reservations.md)
- [Azure에서 Reservations 관리](../../billing/billing-manage-reserved-vm-instance.md)
- [SUSE 예약 할인이 적용되는 방식 이해](../../billing/billing-understand-suse-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](../../billing/billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../../billing/billing-understand-reserved-instance-usage-ea.md)
