---
title: Red Hat Enterprise Linux 계획-Azure 예약 구입 | Microsoft Docs
description: Red Hat 사용에 대 한 요금을 선불 종 량 제 비용 보다 비용 절약 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653019"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>Azure 예약에서 Red Hat Enterprise Linux 계획에 대 한 요금을 선불합니다

Red Hat 사용 하기 위한 선불 하 및 비용에 종 량 제 비용 보다 저장 키를 누릅니다. 할인 미터에 Red Hat 및 가상 머신 사용량에 없는만 적용 됩니다. 비용을 더 절약하기 위해 가상 머신에 대한 예약을 개별적으로 구매할 수 있습니다.

Azure portal에서 Red Hat 소프트웨어 계획을 구입할 수 있습니다. 플랜을 구매하려면:

- 사용자가 하나 이상의 Enterprise 또는 종량제 구독에 대해 소유자 역할이어야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 또는 해당 설정을 사용하지 않도록 설정한 경우 사용자가 구독에 대한 EA 관리자여야 합니다.
- 클라우드 솔루션 공급자 (CSP) 프로그램에 대 한 관리 에이전트 또는 영업 담당자는 Red Hat 계획을 구입할 수 있습니다.

## <a name="buy-a-red-hat-software-plan"></a>Red Hat 소프트웨어 요금제 구매

1. Azure Portal에서 [Reservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)로 이동합니다.
1. 선택 **추가** Red Hat Linux를 선택 합니다.
1. 필수 필드를 입력합니다. 구입 하기의 특성과 일치 하는 Red Hat Linux VM 할인을 가져옵니다. 할인을 받을 실제 배포 수는 선택한 범위 및 수량에 따라 달라집니다.

    | 필드      | 설명|
    |:------------|:--------------|
    |name        |이 구매의 이름입니다.|
    |구독|이 플랜에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독 시 지불 방법은 예약에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.|
    |범위       |범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: <ul><li>단일 구독-계획 할인이이 구독의 Red Hat Linux 사용에 적용 됩니다. </li><li>공유-계획 할인이 청구 컨텍스트 내의 모든 구독에서 Red Hat Linux 사용에 적용 됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</li></ul>|
    |계획     |Red Hat Linux 계획을 선택 합니다. 구입 하려면 대상을 식별 하는 도움말, Red Hat Enterprise 소프트웨어 예약 할인은 적용 하는 방법을 이해를 참조 하세요.|
    |VM 크기     |Red Hat Linux 가격 Vcpu VM의 수에 따라 달라 집니다. Red Hat Linux Vm에서 Vcpu 수를 나타내는 옵션을 선택 합니다.|
    |용어        |1년 또는 3년입니다.|
    |수량    |에 대 한이 Red Hat Linux 계획을 구입 하는 Vm의 수입니다. 수량은 청구 할인을 받을 수 있는 Red Hat Linux 인스턴스 실행의 수입니다.|
1. **구매**를 선택합니다.
1. 구매 상태를 보려면 **이 예약 보기**를 선택합니다.

예약 할인은 예약 일치 하는 실행 중인 Red Hat 가상 컴퓨터를 자동으로 적용 됩니다. 할인은 Red Hat 미터에만 적용 됩니다. VM 계산 요금은 이 계획에 포함되지 않습니다.

## <a name="discount-applies-to-different-vm-sizes"></a>다양 한 VM 크기에 할인이 적용 됩니다.

Reserved VM Instances와 같은 Red Hat Linux 계획 인스턴스 크기 유연성을 제공합니다. 이 할인에 구매한 Red Hat 계획에서 크기가 다른 VM를 배포할 때에 적용 된다는 것을 의미 합니다. 자세한 내용은 Red Hat Enterprise 소프트웨어 예약 할인은 적용 하는 방법을 이해를 참조 하세요.

## <a name="cancellation-and-exchanges-not-allowed"></a>취소 및 교환은 허용되지 않습니다.

취소 하거나 exchange 구입한 Red Hat 계획 수 없습니다. 올바른 플랜을 구매했는지 확인하려면 사용량을 확인합니다. 구입 하려면 대상을 식별 하는 도움말, Red Hat Enterprise 소프트웨어 예약 할인은 적용 하는 방법을 이해를 참조 하세요.

## <a name="next-steps"></a>다음 단계

예약을 관리하는 방법을 알아보려면 [Azure 예약 관리](../../billing/billing-manage-reserved-vm-instance.md)를 참조합니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../../billing/billing-save-compute-costs-reservations.md)
- [Azure에서 Reservations 관리](../../billing/billing-manage-reserved-vm-instance.md)
- Red Hat 예약 할인은 적용 하는 방법 이해
- [종량제 구독의 예약 사용량 이해](../../billing/billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.