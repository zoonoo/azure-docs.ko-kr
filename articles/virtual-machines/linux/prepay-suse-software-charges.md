---
title: 소프트웨어 플랜선불 - Azure 예약
description: 종량제 비용을 절감하기 위해 소프트웨어 계획에 대한 선불 비용을 지불하는 방법을 알아보십시오.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3bb7a62433993f1af26b1ce8bcb4ed258c34623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973126"
---
# <a name="prepay-for-azure-software-plans"></a>Azure 소프트웨어 플랜 선결제

Azure에서 SUSE 및 RedHat 소프트웨어 사용량에 대한 선불요금을 지불하면 종량제 비용을 절감할 수 있습니다. 할인은 SUSE 및 RedHat 미터에만 적용되며 가상 시스템 사용에는 적용되지 않습니다. 추가 비용을 절감하기 위해 가상 시스템에 대한 예약을 별도로 구입할 수 있습니다.

Azure 포털에서 SUSE 및 RedHat 소프트웨어 계획을 구입할 수 있습니다. 플랜을 구매하려면:

- 종량제 가격을 가진 하나 이상의 엔터프라이즈 또는 개별 구독에 대한 소유자 역할이 있어야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 활성화해야 합니다. 설정이 비활성화된 경우 구독의 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 영업 에이전트는 소프트웨어 계획을 구입할 수 있습니다.

## <a name="buy-a-software-plan"></a>소프트웨어 요금제 구매

1. Azure 포털에 로그인하고 [예약으로](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)이동합니다.
2. **추가를** 클릭한 다음 구입하려는 소프트웨어 요금제선택을 선택합니다.
필수 필드를 입력합니다. 당신이 구입 한 속성과 일치하는 모든 수스 리눅스 VM 또는 RedHat VM은 할인을 가져옵니다. 할인을 받을 실제 배포 수는 선택한 범위 및 수량에 따라 달라집니다.
3. 구독을 선택합니다. 그것은 계획에 대 한 지불 하는 데 사용 됩니다.
구독 결제 수단에는 예약에 대한 선결제 비용이 부과됩니다. 구독 유형은 기업 계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제 가격(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이 있는 개별 계약이어야 합니다.
    - Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다.
    - 종량제 가격이 책정된 개별 구독의 경우 요금은 구독의 신용 카드 또는 송장 결제 방법으로 청구됩니다.
4. 범위를 선택합니다. 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다.
    - 단일 구독 - 요금제 할인은 구독의 일치 사용량에 적용됩니다.
    - 공유 - 요금제 할인은 결제 컨텍스트의 모든 구독에서 일치하는 인스턴스에 적용됩니다. 엔터프라이즈 고객의 경우 청구 컨텍스트는 등록이며 등록의 모든 구독을 포함합니다. 종량제 가격 책정 고객을 사용하는 개별 요금제의 경우 결제 컨텍스트는 계정 관리자가 만든 종량제 가격 구독이 있는 모든 개별 요금제입니다.
5. VM 크기와 이미지 유형을 선택할 제품을 선택합니다. 할인은 선택한 VM 크기에만 적용됩니다.
6. 1년 또는 3년 기간을 선택합니다.
7. 청구 할인을 받을 수 있는 선불 VM 인스턴스 수인 수량을 선택합니다.
8. 제품을 장바구니에 추가하고 검토하고 구입하십시오.

예약 할인은 선불한 소프트웨어 미터에 자동으로 적용됩니다. VM 계산 요금은 요금제에 포함되지 않습니다. VM 예약은 별도로 구매할 수 있습니다.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>할인은 다른 SUSE VM 크기에 적용됩니다.

예약된 VM 인스턴스와 마찬가지로 SUSE Linux 요금제는 인스턴스 크기 유연성을 제공합니다. 구입한 SUSE 플랜과 크기가 다른 VM을 배포하는 경우에도 할인이 적용됩니다. 자세한 내용은 [소프트웨어 계획 할인이 적용되는 방법 이해하기를](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)참조하십시오.

## <a name="redhat-plan-discount"></a>레드햇 플랜 할인

계획은 Red Hat 엔터프라이즈 Linux 가상 머신에서만 사용할 수 있습니다. 할인은 레드햇 엔터프라이즈 리눅스 SAP HANA VM 또는 레드햇 엔터프라이즈 리눅스 SAP 비즈니스 앱 VM에는 적용되지 않습니다.

RedHat 플랜 할인은 구매 시 선택한 VM 크기에만 적용됩니다. RHEL 플랜은 구매 후 환불 또는 교환할 수 없습니다.


## <a name="cancellation-and-exchanges-not-allowed"></a>취소 및 교환은 허용되지 않습니다.

구입한 SUSE 또는 RedHat 플랜을 취소하거나 교환할 수 없습니다. 올바른 플랜을 구매했는지 확인하려면 사용량을 확인합니다. 구매할 제품 확인에 대한 도움말은 [소프트웨어 계획 할인이 적용되는 방법 이해하기를](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)참조하십시오.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만듭니다.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>다음 단계

예약을 관리하는 방법에 대해 알아보려면 [Azure 예약 관리를](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)참조하십시오.

자세히 알아보려면 다음 아티클을 참조하세요.

- [Azure 예약이란 무엇입니까?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure에서 예약 관리](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [SUSE 예약 할인이 적용되는 방식 이해](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
