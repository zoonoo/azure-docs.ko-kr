---
title: Azure Reservations란? | Microsoft Docs
description: 가상 머신, SQL Database, Azure Cosmos DB 및 기타 리소스 비용을 절약하기 위한 Azure 예약 및 가격 책정 정보에 대해 알아봅니다.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b20983c45cd62b9812cdb52de32a6e29da459efe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370801"
---
# <a name="what-are-azure-reservations"></a>Azure Reservations란?

Azure 예약은 1년치 또는 3년치 가상 머신, SQL Database 컴퓨팅 용량, Azure Cosmos DB 처리량 또는 기타 Azure 리소스를 선결제하여 비용을 줄일 수 있는 제도입니다. 선결제하면 사용하는 리소스 요금에 대한 할인을 받을 수 있습니다. 예약을 사용하면 가상 머신, SQL Database 계산, Azure Cosmos DB 또는 기타 리소스 비용이 종량제 가격의 최대 72%까지 감소합니다. 예약은 청구 할인을 제공하며, 리소스의 런타임 상태에 영향을 주지 않습니다.

예약은 [Azure Portal](https://aka.ms/reservations)에서 구입할 수 있습니다.

## <a name="why-buy-a-reservation"></a>예약을 구매 하는 이유

Virtual machines, Azure Cosmos DB 또는 오랜 시간 동안 실행 되는 SQL 데이터베이스에 있는 경우 예약 구매 하면 가장 비용 효율적인 옵션입니다. 예를 들어 지속적으로 예약 되지 않은 서비스의 네 인스턴스를 실행 하면 종 량 제 요금이 청구 됩니다. 이러한 리소스에 대 한 예약을 구입 하는 경우 즉시 예약 할인을 가져옵니다. 리소스 요금이 더 이상 종량제로 청구되지 않습니다.

## <a name="charges-covered-by-reservation"></a>예약 다루지 요금

서비스 플랜:

- Reserved Virtual Machine Instance: 가상 머신 컴퓨팅 비용만 예약에 포함됩니다. 추가 소프트웨어, 네트워킹 또는 저장소 요금은 포함되지 않습니다.
- Azure Cosmos DB 예약 용량: 예약을 리소스에 대 한 프로 비전 된 처리량을 설명 합니다. 저장소 및 네트워킹 요금은 포함하지 않습니다.
- SQL Database 예약 vCore: 컴퓨팅 비용만 예약에 포함됩니다. 라이선스는 별도로 청구됩니다.

Windows 가상 머신 및 SQL Database의 경우 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 라이선스 비용을 처리할 수 있습니다.

## <a name="whos-eligible-to-purchase-a-reservation"></a>예약을 구매할 수 있는 주체는?

계획을 구입 하려면 Enterprise (-0017 Ms-azr 또는-0148 Ms-azr) 또는 종 량 제 구독 (-003 Ms-azr 또는-0023 Ms-azr)에서 구독 소유자 역할이 있어야 합니다. 클라우드 솔루션 공급자는 Azure portal을 사용할 수 또는 [파트너 센터](/partner-center/azure-reservations) Azure 예약을 구입할 수 있습니다.

EA 고객은 사용 하지 않도록 설정 하 여 EA 관리자에 게 구매를 제한할 수는 **Reserved Instances 추가** EA 포털에서 옵션입니다. EA 관리자는 예약을 구매 하려면 EA 구독을 하나 이상에 대 한 구독 소유자 여야 합니다. 옵션을 중앙 집중식된 팀을 서로 다른 비용 센터에 대 한 예약을 구입 하려는 기업에 유용 합니다. 를 구입한 후에 중앙 집중식된 팀 예약을 비용 센터 소유자를 추가할 수 있습니다. 다음 소유자 해당 구독에 대 한 예약을 범위를 지정할 수 있습니다. 중앙 팀 예약을 구매 하는 구독 소유자 액세스 권한이 필요가 없습니다.

예약 할인은 Enterprise, 종량제 또는 CSP 구독 유형과 연결된 리소스에만 적용됩니다.

## <a name="reservation-scope"></a>예약 범위

예약 범위를 예약 할인이 적용 되는 리소스를 결정 합니다. 예약 범위는 다음 값을 포함할 수 있습니다.

**공유 범위** -예약 할인이 청구 컨텍스트 내에서 적합 한 구독에서 일치 하는 리소스에 적용 됩니다.

- 기업 계약 고객에 대 한 청구 컨텍스트는 등록 합니다.
 종 량 제 고객에 대 한 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.

**단일 구독** -예약 할인이 선택한 구독에서 일치 하는 리소스에 적용 됩니다.

할 수 있습니다 [예약을 구매한 후 범위를 업데이트할](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)합니다.

## <a name="discounted-subscription-and-offer-types"></a>구독 및 제품 유형이 할인된

예약 할인 다음 적합 한 구독에 적용 및 유형을 제공 합니다.

- 기업 계약 (번호를 제공 합니다. MS-AZR-0017P 또는-0148 Ms-azr)
- 종량제(제안 번호: MS-AZR-0003P 또는-0023 Ms-azr)
- CSP 구독

다른 유형의 구독에서 실행되는 리소스는 예약 할인을 받지 못합니다.

## <a name="how-is-a-reservation-billed"></a>예약 요금은 어떻게 청구되나요?

예약 요금은 구독에 연결된 결제 방법으로 청구됩니다. Enterprise 구독이 있는 경우 예약 비용은 현금 약정 잔액에서 차감됩니다. 현금 약정 잔액으로 예약 비용이 해결되지 않는 경우 초과분이 청구됩니다. 종량제 구독이 있는 경우 계정에 등록된 신용 카드로 즉시 청구됩니다. 청구서로 청구하는 경우 다음 번 청구서에 청구 요금이 표시됩니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인은 적용 하는 방법

예약 할인이 예약을 구매 하는 경우 선택한 특성과 일치 하는 리소스 사용에 적용 됩니다. 이러한 특성에는 일치하는 VM, SQL Database, Azure Cosmos DB 또는 기타 리소스가 실행되는 범위가 포함됩니다. 예를 들어, 미국 서 부 지역에서 4 개의 표준 D2 가상 머신에 대 한 예약 할인을 원하는 경우 Vm이 실행 하는 구독 한 다음 선택 합니다.

예약 할인은 "*사용 하 여-it 또는-손실-it*"입니다. 가 없는 모든 시간에 대 한 리소스를 일치 하는 경우 손실 된 경우 예약 수량을 해당 시간에 대 한 합니다. 수행할 수 없습니다. 사용 되지 않는 예약 된 시간을 전달 합니다.

리소스를 종료 하면 예약 할인은 지정된 된 범위에서 일치 하는 다른 리소스에 자동으로 적용 됩니다. 일치 하는 리소스가 없는 지정된 된 범위에 있는 경우 예약 된 시간은 *손실*합니다.

예를 들어 수 나중에 리소스를 만들고 보유 미달 사용 되는 일치 하는 예약 합니다. 이 예제에서는 예약 할인 일치 하는 새 리소스를 자동으로 적용 됩니다.

가상 머신이 사용자의 등록/계정 내 다른 구독에서 실행되는 경우는 범위를 공유로 선택합니다. 공유 범위를 사용하면 예약 할인을 구독 전체에 적용할 수 있습니다. 예약 구매 후 범위를 변경할 수 있습니다. 자세한 내용은 [Azure 예약 관리](billing-manage-reserved-vm-instance.md)를 참조하세요.

예약 할인은 Enterprise, 종량제 또는 CSP 구독 유형과 연결된 리소스에만 적용됩니다. 다른 유형의 구독에서 실행되는 리소스는 예약 할인을 받지 못합니다.

## <a name="when-the-reservation-term-expires"></a>예약 기간이 만료 되는 경우

예약 기간이 끝나면 청구 할인이 만료되고 가상 머신, SQL Database, Azure Cosmos DB 또는 기타 리소스에 종량제 요금이 청구됩니다. Azure 예약은 자동으로 갱신되지 않습니다. 청구 할인을 계속 받으려면 적격 서비스 및 소프트웨어에 대한 새 예약을 구매해야 합니다.

## <a name="discount-applies-to-different-sizes"></a>다양 한 크기에 할인이 적용 됩니다.

예약 구매 시에는 동일 크기 그룹 내의 특성이 적용되는 다른 인스턴스에도 할인을 적용할 수 있습니다. 이 기능 인스턴스 크기 유연성 이라고 합니다. 할인 유동 적용 여부는 예약 구매 시 선택하는 특성과 예약의 유형에 따라 달라집니다.

서비스 플랜:

- Reserved VM Instances: 예약을 구입 하 고 선택 하는 경우 **에 대 한 최적화**: **인스턴스 크기 유연성**, 할인 범위를 선택 하는 VM 크기에 따라 달라 집니다. 같은 크기 시리즈 그룹의 VM(가상 머신) 크기에 예약을 적용할 수 있습니다. 자세한 내용은 [예약 VM 인스턴스를 통해 유동적으로 가상 머신 크기 조정](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)을 참조하세요.
- SQL Database 예약 용량: 할인 적용 범위는 선택하는 성능 계층에 따라 달라집니다. 자세한 내용은 [Azure 예약 할인이 적용되는 방식 이해](billing-understand-reservation-charges.md)를 참조하세요.
- Azure Cosmos DB 예약 용량: 할인 적용 범위는 프로비전되는 처리량에 따라 달라집니다. 자세한 내용은 [Azure Cosmos DB 예약 할인이 적용되는 방식 이해](billing-understand-cosmosdb-reservation-charges.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
    - [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
    - [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)

- 서비스 계획에 대 한 예약에 대 한 자세한 정보:
    - [Azure 예약된 VM 인스턴스를 사용 하 여 Virtual Machines](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB를 사용 하 여 azure Cosmos DB 리소스 예약 된 용량](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Azure SQL Database를 사용 하 여 SQL Database 계산 리소스를 예약 된 용량](../sql-database/sql-database-reserved-capacity.md) 소프트웨어 계획에 대 한 예약에 자세히 알아보려면:
    - [Azure 예약에서 Red Hat 소프트웨어 계획](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure 예약에서 SUSE 소프트웨어 계획](../virtual-machines/linux/prepay-suse-software-charges.md)
