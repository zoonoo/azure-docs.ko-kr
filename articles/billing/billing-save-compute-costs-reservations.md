---
title: Azure Reservations란? | Microsoft Docs
description: 가상 머신, SQL Database, Azure Cosmos DB 및 기타 리소스 비용을 절약하기 위한 Azure 예약 및 가격 책정 정보에 대해 알아봅니다.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2019
ms.author: banders
ms.openlocfilehash: a93bfd8f71c515bdd5112170f27336a0df62c6e5
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818858"
---
# <a name="what-are-azure-reservations"></a>Azure Reservations란?

Azure 예약은 1년치 또는 3년치 가상 머신, SQL Database 컴퓨팅 용량, Azure Cosmos DB 처리량 또는 기타 Azure 리소스를 선결제하여 비용을 줄일 수 있는 제도입니다. 선결제하면 사용하는 리소스 요금에 대한 할인을 받을 수 있습니다. 예약을 사용하면 가상 머신, SQL Database 계산, Azure Cosmos DB 또는 기타 리소스 비용이 종량제 가격의 최대 72%까지 감소합니다. 예약은 청구 할인을 제공하며, 리소스의 런타임 상태에 영향을 주지 않습니다.

예약은 [Azure Portal](https://aka.ms/reservations)에서 구입할 수 있습니다. 자세한 내용은 다음 항목을 참조하십시오.

- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약된 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)
- [Azure Cosmos DB 예약된 용량을 사용하여 Azure Cosmos DB 리소스 요금 선결제](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>예약을 구매해야 하는 이유

오래 실행되는 가상 머신, Azure Cosmos DB 또는 SQL Database가 있는 경우 예약을 구매하면 가장 비용 효율적인 가격이 제공됩니다. 예를 들어 예약 없이 한 서비스의 인스턴스 4개를 지속적으로 실행하는 경우 종량제 요금이 청구됩니다. 이러한 리소스에 대한 예약을 구매하면 즉시 예약 할인을 받습니다. 리소스 요금이 더 이상 종량제로 청구되지 않습니다.

## <a name="what-charges-does-a-reservation-cover"></a>예약에 포함되는 요금 범위

- Reserved Virtual Machine Instance: 가상 머신 컴퓨팅 비용만 예약에 포함됩니다. 추가 소프트웨어, 네트워킹 또는 저장소 요금은 포함되지 않습니다.
- SQL Database 예약 vCore: 컴퓨팅 비용만 예약에 포함됩니다. 라이선스는 별도로 청구됩니다.
- Azure Cosmos DB 예약 용량: 예약에는 리소스에 대해 프로비전되는 처리량이 포함되지만, 스토리지 및 네트워킹 요금은 포함되지 않습니다.

Windows 가상 머신 및 SQL Database의 경우 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 라이선스 비용을 처리할 수 있습니다.

## <a name="whos-eligible-to-purchase-a-reservation"></a>예약을 구매할 수 있는 주체는?

다음과 같은 구독 유형을 사용하는 Azure 고객은 예약을 구매할 수 있습니다.

- Enterprise 계약 구독 제안 유형(MS-AZR-0017P 또는 MS-AZR-0148P).
- 종량제 구독 제안 유형(MS-AZR-003P 또는 MS-AZR-0023P).
- CSP(클라우드 솔루션 공급 기업) 파트너는 Azure Portal 또는 [파트너 센터](https://docs.microsoft.com/partner-center/azure-reservations)를 사용하여 Azure 예약을 구매할 수 있습니다.

예약 할인은 Enterprise, 종량제 또는 CSP 구독 유형과 연결된 리소스에만 적용됩니다.

 플랜을 구매하려면:

- 사용자가 하나 이상의 Enterprise 또는 종량제 구독에 대해 소유자 역할이어야 합니다.

## <a name="how-is-a-reservation-billed"></a>예약 요금은 어떻게 청구되나요?

예약 요금은 구독에 연결된 결제 방법으로 청구됩니다. Enterprise 구독이 있는 경우 예약 비용은 현금 약정 잔액에서 차감됩니다. 현금 약정 잔액으로 예약 비용이 해결되지 않는 경우 초과분이 청구됩니다. 종량제 구독이 있는 경우 계정에 등록된 신용 카드로 즉시 청구됩니다. 청구서로 청구하는 경우 다음 번 청구서에 청구 요금이 표시됩니다.

## <a name="how-is-the-reservation-discount-applied"></a>예약 할인은 어떻게 적용되나요?

예약 할인은 예약 구매 시 선택한 특성과 일치하는 리소스 사용에 적용됩니다. 이러한 특성에는 일치하는 VM, SQL Database, Azure Cosmos DB 또는 기타 리소스가 실행되는 범위가 포함됩니다. 예를 들어 미국 서부 지역의 표준 D2 가상 머신 4개에 예약 할인을 원하는 경우 VM이 실행되고 있는 구독을 선택합니다. 가상 머신이 사용자의 등록/계정 내 다른 구독에서 실행되는 경우는 범위를 공유로 선택합니다. 공유 범위를 사용하면 예약 할인을 구독 전체에 적용할 수 있습니다. 예약 구매 후 범위를 변경할 수 있습니다. 자세한 내용은 [Azure 예약 관리](billing-manage-reserved-vm-instance.md)를 참조하세요.

예약 할인은 Enterprise, 종량제 또는 CSP 구독 유형과 연결된 리소스에만 적용됩니다. 다른 유형의 구독에서 실행되는 리소스는 예약 할인을 받지 못합니다.

예약이 청구에 미치는 영향을 보다 잘 이해하려면 다음 항목을 참조하세요.

- [Azure Reserved VM Instances 할인 이해](billing-understand-vm-reservation-charges.md)
- [Azure 예약 할인 이해](billing-understand-vm-reservation-charges.md)
- [Azure Cosmos DB 예약 할인 이해](billing-understand-cosmosdb-reservation-charges.md)
- [SUSE에 대한 Azure 예약 할인 및 사용량 이해](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>예약 기간이 만료되는 경우

예약 기간이 끝나면 청구 할인이 만료되고 가상 머신, SQL Database, Azure Cosmos DB 또는 기타 리소스에 종량제 요금이 청구됩니다. Azure 예약은 자동으로 갱신되지 않습니다. 청구 할인을 계속 받으려면 적격 서비스 및 소프트웨어에 대한 새 예약을 구매해야 합니다.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>할인은 인스턴스 크기를 유동적으로 조정할 수 있는 다양한 크기에 적용됩니다.

예약 구매 시에는 동일 크기 그룹 내의 특성이 적용되는 다른 인스턴스에도 할인을 적용할 수 있습니다. 할인 유동 적용 여부는 예약 구매 시 선택하는 특성과 예약의 유형에 따라 달라집니다.

- Reserved VM Instances: 예약을 구매할 때 **다음에 대해 최적화됨**: **인스턴스 크기 유연성**을 선택하는 경우 할인 적용 범위는 선택하는 VM 크기에 따라 달라집니다. 같은 크기 시리즈 그룹의 VM(가상 머신) 크기에 예약을 적용할 수 있습니다. 자세한 내용은 [예약 VM 인스턴스를 통해 유동적으로 가상 머신 크기 조정](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)을 참조하세요.
- SUSE Linux Enterprise 소프트웨어 요금제: 할인 적용 범위는 SUSE 소프트웨어를 실행하는 VM의 vCPU 수에 따라 달라집니다. 자세한 내용은 [SUSE Linux Enterprise 소프트웨어 플랜 할인이 적용되는 방식 이해](billing-understand-suse-reservation-charges.md)를 참조하세요.
- SQL Database 예약 용량: 할인 적용 범위는 선택하는 성능 계층에 따라 달라집니다. 자세한 내용은 [Azure 예약 할인이 적용되는 방식 이해](billing-understand-reservation-charges.md)를 참조하세요.
- Azure Cosmos DB 예약 용량: 할인 적용 범위는 프로비전되는 처리량에 따라 달라집니다. 자세한 내용은 [Azure Cosmos DB 예약 할인이 적용되는 방식 이해](billing-understand-cosmosdb-reservation-charges.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- [예약 VM 인스턴스](../virtual-machines/windows/prepay-reserved-vm-instances.md), [SQL Database 예약된 용량](../sql-database/sql-database-reserved-capacity.md) 또는 [Azure Cosmos DB 예약된 용량](../cosmos-db/cosmos-db-reserved-capacity.md)을 구매하여 가상 머신 비용을 절약하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
    - [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
    - [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)
