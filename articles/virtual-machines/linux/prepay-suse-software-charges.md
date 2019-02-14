---
title: SUSE Linux 플랜 구매 - Azure Reservations | Microsoft Docs
description: SUSE 사용량에 대한 요금을 선불로 지급하고 종량제 비용에 대한 비용을 절약하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: 4f70a34febcf0b39d051053a6ddd9abe5c9a6726
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745982"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>Azure Reservations에서 SUSE 소프트웨어 플랜에 대한 선불

SUSE 사용량에 대한 요금을 선불로 지급하고 종량제 비용에 대한 비용을 절약합니다. 할인은 가상 머신 사용량이 아닌 SUSE 미터에 대해서만 적용됩니다. 비용을 더 절약하기 위해 가상 머신에 대한 예약을 개별적으로 구매할 수 있습니다.

Azure Portal에서 SUSE 소프트웨어 플랜을 구매할 수 있습니다. 플랜을 구매하려면:

- 사용자가 하나 이상의 Enterprise 또는 종량제 구독에 대해 소유자 역할이어야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 또는 해당 설정을 사용하지 않도록 설정한 경우 사용자가 구독에 대한 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트만 SUSE 플랜을 구매할 수 있습니다.

## <a name="buy-a-suse-software-plan"></a>SUSE 소프트웨어 플랜 구매

1. Azure Portal에서 [Reservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)로 이동합니다.
1. **추가**를 선택하여 SUSE Linux를 선택합니다.
1. 필수 필드를 입력합니다. 구매하려는 특성과 일치하는 모든 SUSE Linux VM은 할인을 받습니다. 할인을 받을 실제 배포 수는 선택한 범위 및 수량에 따라 달라집니다.

    | 필드      | 설명|
    |:------------|:--------------|
    |Name        |이 구매의 이름입니다.|
    |구독|이 플랜에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독 시 지불 방법은 예약에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.|
    |범위       |범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: <ul><li>단일 구독 - 플랜 할인이 이 구독의 SUSE Linux 사용량에 적용됩니다. </li><li>공유 - 플랜 할인이 청구 컨텍스트 내의 모든 구독에서 SUSE Linux 사용량에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</li></ul>|
    |소프트웨어 플랜     |SUSE Linux 플랜을 선택합니다. 구매할 대상 식별에 대한 도움말은 [SUSE Linux Enterprise 소프트웨어 예약 할인이 적용되는 방식 이해](../../billing/billing-understand-suse-reservation-charges.md)를 참조하세요.|
    |VM 크기     |SUSE Linux 가격 책정은 VM의 vCPU 수에 따라 달라집니다. SUSE Linux VM에서 vCPU 수를 나타내는 옵션을 선택합니다.|
    |용어        |1년 또는 3년입니다.|
    |수량    |이 SUSE Linux 플랜을 구매하는 VM의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 SUSE Linux 인스턴스의 수입니다.|
1. **구매**를 선택합니다.
1. 구매 상태를 보려면 **이 예약 보기**를 선택합니다.

예약 할인은 예약과 일치하는 실행 중인 SUSE 가상 머신에 자동으로 적용됩니다. 할인은 SUSE 미터에만 적용됩니다. VM 계산 요금은 이 계획에 포함되지 않습니다.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>할인은 인스턴스 크기 유연성이 있는 다양한 VM 크기에 적용됩니다.

예약 VM 인스턴스와 같이 SUSE Linux 플랜은 인스턴스 크기 유연성을 제공합니다. 즉 구매한 SUSE 계획과 크기가 다른 VM을 배포할 때에도 할인이 적용된다는 것을 의미합니다. 자세한 내용은 [SUSE Linux Enterprise 소프트웨어 예약 할인이 적용되는 방식 이해](../../billing/billing-understand-suse-reservation-charges.md)를 참조하세요.

## <a name="cancellation-and-exchanges-not-allowed"></a>취소 및 교환은 허용되지 않습니다.

구매한 SUSE 플랜을 취소하거나 교환할 수 없습니다. 올바른 플랜을 구매했는지 확인하려면 사용량을 확인합니다. 구매할 대상 식별에 대한 도움말은 [SUSE Linux Enterprise 소프트웨어 예약 할인이 적용되는 방식 이해](../../billing/billing-understand-suse-reservation-charges.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

예약을 관리하는 방법을 알아보려면 [Azure 예약 관리](../../billing/billing-manage-reserved-vm-instance.md)를 참조합니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../../billing/billing-save-compute-costs-reservations.md)
- [Azure에서 Reservations 관리](../../billing/billing-manage-reserved-vm-instance.md)
- [SUSE 예약 할인이 적용되는 방식 이해](../../billing/billing-understand-suse-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](../../billing/billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).