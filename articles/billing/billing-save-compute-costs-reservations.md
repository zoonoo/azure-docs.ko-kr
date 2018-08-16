---
title: Azure 예약이란? | Microsoft Docs
description: 가상 머신, SQL 데이터베이스 및 기타 리소스 비용을 절약할 수 있도록 Azure 예약 및 가격 책정에 대해 알아보세요.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 93c11852a11e0bb57a0b92090368298fc14b8c2a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626307"
---
# <a name="what-are-azure-reservations"></a>Azure 예약이란?

Azure 예약은 가상 머신 또는 1년치 또는 3년치 SQL Database 계산 용량을 선결제하여 비용을 줄일 수 있는 제도입니다. 선결제하면 사용하는 리소스 요금에 대한 할인을 받을 수 있습니다. Azure 예약은 1년 또는 3년 사전 약정을 통해 종량제 가격의 최대 72%까지 가상 머신 또는 SQL 데이터베이스 비용을 절감할 수 있습니다. 예약은 청구 할인을 제공하며, 가상 머신 또는 SQL 데이터베이스의 런타임 상태에 영향을 주지 않습니다.

예약은 [Azure Portal](https://aka.ms/reservations)에서 구입할 수 있습니다. 자세한 내용은 다음 항목을 참조하십시오.

- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>예약을 구매해야 하는 이유

오래 실행되는 가상 머신 또는 SQL 데이터베이스가 있는 경우 예약을 구매하면 가장 비용 효율적인 가격이 제공됩니다. 예를 들어 예약 없이 한 서비스의 인스턴스 4개를 지속적으로 실행하는 경우 종량제 요금이 청구됩니다. 이러한 리소스에 대한 예약을 구매하면 즉시 예약 할인을 받습니다. 리소스 요금이 더 이상 종량제로 청구되지 않습니다.

## <a name="what-charges-does-a-reservation-cover"></a>예약에 포함되는 요금 범위

- 예약 Virtual Machine 인스턴스: 예약에 가상 머신 계산 비용만 포함됩니다. 추가 소프트웨어, 네트워킹 또는 저장소 요금은 포함되지 않습니다.
- SQL Database 예약 vCore: 계산 비용만 예약에 포함됩니다. 라이선스는 별도로 청구됩니다.

Windows 가상 머신 및 SQL Database의 경우 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 라이선스 비용을 처리할 수 있습니다.

## <a name="whos-eligible-to-purchase-a-reservation"></a>예약 인스턴스 구매 자격

다음과 같은 구독 유형을 사용하는 Azure 고객은 예약을 구매할 수 있습니다.

- Enterprise 계약 구독 제안 유형(MS-AZR-0017P).
- 
  [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독 제안 유형(MS-AZR-003P). 예약을 구매하려면 해당 구독에서 "소유자" 역할이 있어야 합니다. 엔터프라이즈 등록으로 예약을 구매하려면 엔터프라이즈 관리자는 EA 포털에서 예약 구매를 사용하도록 설정해야 합니다. 이 설정은 기본적으로 사용하도록 설정됩니다.
- CSP(클라우드 솔루션 공급자) 파트너는 Azure Portal 또는 [파트너 센터](https://docs.microsoft.com/partner-center/azure-reservations)를 사용하여 Azure 예약을 구입할 수 있습니다. 

Azure 예약 할인은 엔터프라이즈, 종량제 또는 CSP 구독 유형과 연결된 가상 머신 또는 SQL Database에만 적용됩니다.

## <a name="how-is-a-reservation-billed"></a>예약 요금은 어떻게 청구되나요?

예약 요금은 구독에 연결된 결제 방법으로 청구됩니다. Enterprise 구독이 있는 경우 예약 비용은 현금 약정 잔액에서 차감됩니다. 현금 약정 잔액으로 예약 비용이 해결되지 않는 경우 초과분이 청구됩니다. 종량제 구독이 있는 경우 계정에 등록된 신용 카드로 즉시 청구됩니다. 청구서로 청구하는 경우는 다음 번 청구서에 요금이 표시됩니다.

## <a name="how-is-the-reservation-discount-applied"></a>예약 할인은 어떻게 적용되나요?

예약 할인은 예약 구매 시 선택한 특성과 일치하는 리소스 사용에 적용됩니다. 이러한 특성에는 일치하는 VM, SQL 데이터베이스 또는 기타 리소스가 실행되는 범위가 포함됩니다. 예를 들어 미국 서부 지역의 표준 D2 가상 머신 4개에 예약 할인을 원하는 경우 VM이 실행되고 있는 구독을 선택합니다. 가상 머신이 사용자의 등록/계정 내 다른 구독에서 실행되는 경우는 범위를 공유로 선택합니다. 공유 범위를 사용하면 예약 할인을 구독 전체에 적용할 수 있습니다. 예약 구매 후 범위를 변경할 수 있습니다. 자세한 내용은 [Azure에서 예약 관리](billing-manage-reserved-vm-instance.md)를 참조하세요.

예약 할인은 엔터프라이즈 또는 종량제 구독 유형과 연결된 가상 머신 또는 SQL 데이터베이스에만 적용됩니다. 다른 유형의 구독에서 실행되는 가상 머신 또는 SQL 데이터베이스는 예약 할인을 받지 못합니다. 엔터프라이즈 등록의 경우 엔터프라이즈 개발/테스트 구독은 예약 혜택을 이용할 수 없습니다.

예약이 가상 머신 또는 SQL 데이터베이스 요금 청구에 미치는 영향을 보다 정확하게 이해하려면 [예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)를 참조하세요.

## <a name="what-happens-when-the-reservation-term-expires"></a>예약 기간이 만료되는 경우

예약 기간이 끝나면 청구 할인이 만료되고 가상 머신, SQL 데이터베이스 또는 기타 리소스에 종량제 요금이 청구됩니다. Azure 예약은 자동으로 갱신되지 않습니다. 청구 할인을 계속 받으려면 예약 가능 서비스에 대한 새 예약을 구매해야 합니다.

## <a name="next-steps"></a>다음 단계

[예약 VM 인스턴스](../virtual-machines/windows/prepay-reserved-vm-instances.md) 또는 [SQL Database 예약 용량](../sql-database/sql-database-reserved-capacity.md)을 구매하여 가상 머신 비용을 절약하세요.

예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약 관리](billing-manage-reserved-vm-instance.md)
- [예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [종량제 구독의 예약 사용 이해](billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록의 예약 사용 이해](billing-understand-reserved-instance-usage-ea.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
