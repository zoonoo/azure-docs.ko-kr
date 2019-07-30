---
title: Azure Reservations란?
description: 가상 컴퓨터, SQL 데이터베이스, Azure Cosmos DB 및 기타 리소스 비용을 절감 하기 위한 Azure Reservations 및 가격 책정에 대해 알아봅니다.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: banders
ms.openlocfilehash: c63447ff0a3329e0cc0dc0605984ae4f26e9c25f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359222"
---
# <a name="what-are-azure-reservations"></a>Azure Reservations란?

Azure 예약은 1년치 또는 3년치 가상 머신, SQL Database 컴퓨팅 용량, Azure Cosmos DB 처리량 또는 기타 Azure 리소스를 선결제하여 비용을 줄일 수 있는 제도입니다. 선결제하면 사용하는 리소스 요금에 대한 할인을 받을 수 있습니다. 예약을 사용하면 가상 머신, SQL Database 컴퓨팅, Azure Cosmos DB 또는 기타 리소스 비용이 종량제 가격의 최대 72%까지 감소합니다. 예약은 청구 할인을 제공하며, 리소스의 런타임 상태에 영향을 주지 않습니다.

예약은 [Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)에서 구입할 수 있습니다.

## <a name="why-buy-a-reservation"></a>예약을 구매 하는 이유

오랜 시간 동안 실행 되는 가상 머신, Azure Cosmos DB 또는 SQL 데이터베이스가 있는 경우 예약을 구매 하면 가장 비용 효율적인 옵션이 제공 됩니다. 예를 들어 예약 없이 서비스의 인스턴스를 지속적으로 실행 하는 경우 종 량 제 요금이 청구 됩니다. 해당 리소스에 대 한 예약을 구매할 때 즉시 예약 할인을 받게 됩니다. 리소스 요금이 더 이상 종량제로 청구되지 않습니다.

## <a name="charges-covered-by-reservation"></a>예약에서 적용 되는 요금

서비스 플랜:

- **예약 가상 머신 인스턴스** -예약에는 가상 머신 계산 비용만 포함 됩니다. 추가 소프트웨어, 네트워킹 또는 저장소 요금은 포함되지 않습니다.
- **Azure Cosmos DB 예약 된 용량** -예약에는 리소스에 대해 프로 비전 된 처리량이 포함 됩니다. 저장소 및 네트워킹 요금은 포함하지 않습니다.
- **SQL Database Reserved vCore** -예약에 계산 비용만 포함 됩니다. 라이선스는 별도로 청구됩니다.

Windows 가상 머신 및 SQL Database의 경우 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 라이선스 비용을 처리할 수 있습니다.

## <a name="whos-eligible-to-purchase-a-reservation"></a>예약을 구매할 수 있는 주체는?

요금제를 구입 하려면 Enterprise (MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p) 또는 종 량 제 구독 (MS-MS-AZR-0017P-0017P 또는 MS-MS-AZR-0017P-0017P)에 구독 소유자 역할이 있어야 합니다. 클라우드 솔루션 공급자는 Azure Portal 또는 [파트너 센터](/partner-center/azure-reservations) 를 사용 하 여 Azure Reservations를 구매할 수 있습니다.

EA (기업계약) 고객은 EA 관리자에 대 한 구매를 제한할 수 있습니다. EA 포털에서 **예약 된 인스턴스 추가** 옵션을 사용 하지 않도록 설정 하 여이 작업을 수행 합니다. 예약을 구매 하려면 EA 관리자가 하나 이상의 EA 구독에 대 한 구독 소유자 여야 합니다. 옵션은 중앙 집중식 팀에서 다양 한 비용 센터에 대 한 예약을 구매 하려는 기업에 유용 합니다. 구매한 후 중앙 집중식 팀은 예약에 비용 센터 소유자를 추가할 수 있습니다. 그러면 소유자는 예약의 범위를 구독으로 지정할 수 있습니다. 중앙 팀은 예약을 구매 하는 구독 소유자 액세스 권한이 필요 하지 않습니다.

예약 할인은 엔터프라이즈, CSP (클라우드 솔루션 공급자) 및 종 량 제 요금이 있는 개별 요금제를 통해 구매한 구독과 연결 된 리소스에만 적용 됩니다.

## <a name="scope-reservations"></a>범위 예약

예약의 범위를 구독 또는 리소스 그룹으로 지정할 수 있습니다. 예약의 범위를 설정 하면 예약 절감 액이 적용 되는 위치가 선택 됩니다. 예약의 범위를 리소스 그룹으로 하는 경우 예약 할인은 전체 구독이 아닌 리소스 그룹에만 적용 됩니다.

### <a name="reservation-scoping-options"></a>예약 범위 지정 옵션

리소스 그룹 범위 지정을 사용 하는 경우 요구 사항에 따라 예약 범위를 지정 하는 세 가지 옵션이 있습니다.

- **단일 리소스 그룹 범위** -선택한 리소스 그룹의 일치 하는 리소스에만 예약 할인을 적용 합니다.
- **단일 구독 범위** -선택한 구독의 일치 하는 리소스에 예약 할인을 적용 합니다.
- **공유 범위** -청구 컨텍스트에 있는 적격 구독의 일치 하는 리소스에 예약 할인을 적용 합니다. 기업계약 고객의 경우 요금 청구 컨텍스트가 등록입니다. 종 량 제 요금이 있는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.

사용에 예약 할인을 적용 하는 동안 Azure는 다음과 같은 순서로 예약을 처리 합니다.

1. 리소스 그룹으로 범위가 지정 된 예약
2. 단일 범위 예약
3. 공유 범위 예약

단일 리소스 그룹은 예약의 범위를 설정 하는 방법에 따라 여러 예약의 예약 할인을 가져올 수 있습니다.

### <a name="scope-a-reservation-to-a-resource-group"></a>리소스 그룹에 대 한 예약 범위

예약을 구매할 때 예약의 범위를 리소스 그룹으로 지정할 수도 있고 구매 후에 범위를 설정할 수도 있습니다. 예약 범위를 리소스 그룹으로 지정할 수 있도록 구독 소유자 여야 합니다.

범위를 설정 하려면 Azure Portal [구매 예약](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) 페이지로 이동 합니다. 구입할 예약 유형을 선택 합니다. 선택 하 여 **구매할 제품을 선택** 하십시오. 양식에서 범위 값을 단일 리소스 그룹으로 변경 합니다. 그런 다음 리소스 그룹을 선택 합니다.

![VM 예약 구매 선택을 보여 주는 예제](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

가상 컴퓨터 예약에서 리소스 그룹에 대 한 구매 권장 사항이 표시 됩니다. 권장 사항은 최근 30 일 동안의 사용량을 분석 하 여 계산 됩니다. 예약 된 인스턴스로 리소스를 실행 하는 비용이 종 량 제 요금으로 리소스를 실행 하는 비용 보다 저렴 한 경우 구매 권장 사항이 적용 됩니다. 예약 구매 권장 사항에 대 한 자세한 내용은 [사용량 패턴에 따라 예약 인스턴스 구매 권장 사항 가져오기](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)를 참조 하세요.

예약을 구매한 후에는 항상 범위를 업데이트할 수 있습니다. 이렇게 하려면 예약으로 이동 하 여 **구성**을 클릭 하 고 예약을 선택 합니다. Resrea 예약은 상업적 트랜잭션이 아닙니다. 예약 기간이 변경 되지 않았습니다. 범위를 업데이트 하는 방법에 대 한 자세한 내용은 [예약을 구매한 후 범위 업데이트](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)를 참조 하세요.

![예약 범위 변경을 보여 주는 예제](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>예약 사용 모니터링 및 최적화

Azure Portal, Api 또는 사용 현황 데이터를 통해 여러 가지 방법으로 예약 사용 현황을 모니터링할 수 있습니다. 액세스 권한이 있는 모든 예약을 보려면 Azure Portal의 **예약** 으로 이동 합니다. 예약 그리드는 예약에 대해 마지막으로 기록 된 사용률 비율을 보여 줍니다. 예약을 클릭 하 여 예약의 장기 사용률을 확인 합니다.

기업 계약 고객의 경우 [api](billing-reservation-apis.md#see-reservation-usage) 를 사용 하 여 [사용 현황 데이터](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) 에서 예약 사용률을 가져올 수도 있습니다.

리소스 그룹 범위 예약의 사용률이 낮은 경우 예약 범위를 단일 구독으로 업데이트 하거나 청구 컨텍스트를 통해 공유할 수 있습니다. 또한 예약을 분할 하 고 결과 예약을 다른 리소스 그룹에 적용할 수 있습니다.

### <a name="other-considerations"></a>기타 고려 사항

리소스 그룹에 일치 하는 리소스가 없는 경우 예약이 미달 사용 됩니다. 낮은 사용률이 있는 다른 리소스 그룹 또는 구독에는 예약이 자동으로 적용 되지 않습니다.

한 구독에서 다른 구독으로 리소스 그룹을 이동 하는 경우 예약 범위가 자동으로 업데이트 되지 않습니다. 리소스 그룹을 삭제 하면 범위가 업데이트 되지 않습니다. [예약을 예약](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)해야 합니다. 그렇지 않으면 예약이 미달 사용 됩니다.

## <a name="discounted-subscription-and-offer-types"></a>할인 구독 및 제안 유형

예약 할인은 다음과 같은 적격 구독 및 제안 유형에 적용 됩니다.

- 기업 계약 (제품 번호: MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p)
- 종 량 제 요금이 있는 개별 요금제 (제품 번호: MS-AZR-0017P-0003P 또는 MS-AZR-0017P-0023P)
- CSP 구독

다른 유형의 구독에서 실행되는 리소스는 예약 할인을 받지 못합니다.

## <a name="how-is-a-reservation-billed"></a>예약 요금은 어떻게 청구되나요?

예약 요금은 구독에 연결된 결제 방법으로 청구됩니다. Enterprise 구독이 있는 경우 예약 비용은 현금 약정 잔액에서 차감됩니다. 현금 약정 잔액이 예약 비용에 포함 되지 않는 경우 초과분 요금이 청구 됩니다. 종 량 제 요금이 있는 개별 요금제의 구독이 있는 경우 계정에 대 한 신용 카드는 즉시 청구 됩니다. 청구서로 청구 되는 경우 다음 청구서에 요금이 표시 됩니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용 되는 방법

예약 할인은 예약을 구매할 때 선택한 특성과 일치 하는 리소스 사용량에 적용 됩니다. 특성에는 일치 하는 Vm, SQL 데이터베이스, Azure Cosmos DB 또는 기타 리소스가 실행 되는 범위가 포함 됩니다. 예를 들어 미국 서 부 지역에 4 개의 표준 D2 가상 컴퓨터에 대 한 예약 할인이 필요한 경우 Vm이 실행 되는 구독을 선택 합니다.

예약 할인은 "*사용-또는 분실*"입니다. 모든 시간에 대해 일치 하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실 됩니다. 사용 하지 않는 예약 시간을 앞으로 사용할 수 없습니다.

리소스를 종료할 때 예약 할인이 지정 된 범위에서 일치 하는 다른 리소스에 자동으로 적용 됩니다. 지정 된 범위에서 일치 하는 리소스를 찾을 수 없는 경우 예약 된 시간이 *손실*됩니다.

예를 들어 나중에 리소스를 만들고 일치 하는 예약을 사용할 수 있습니다. 예약 할인은 새 일치 리소스에 자동으로 적용 됩니다.

가상 머신이 사용자의 등록/계정 내 다른 구독에서 실행되는 경우는 범위를 공유로 선택합니다. 공유 범위를 사용하면 예약 할인을 구독 전체에 적용할 수 있습니다. 예약 구매 후 범위를 변경할 수 있습니다. 자세한 내용은 [Azure 예약 관리](billing-manage-reserved-vm-instance.md)를 참조하세요.

예약 할인은 엔터프라이즈, CSP 또는 종 량 제 요금 구독과 연결 된 리소스에만 적용 됩니다. 다른 유형의 구독에서 실행되는 리소스는 예약 할인을 받지 못합니다.

## <a name="when-the-reservation-term-expires"></a>예약 기간이 만료 되는 경우

예약 기간이 끝나면 청구 할인이 만료 됩니다. 가상 컴퓨터, SQL 데이터베이스, Azure Cosmos DB 또는 기타 리소스는 종 량 제 가격으로 청구 됩니다. Azure Reservations 자동으로 갱신 되지 않습니다. 청구 할인을 계속 받으려면 적격 서비스 및 소프트웨어에 대한 새 예약을 구매해야 합니다.

## <a name="discount-applies-to-different-sizes"></a>할인은 다른 크기에 적용 됩니다.

예약 구매 시에는 동일 크기 그룹 내의 특성이 적용되는 다른 인스턴스에도 할인을 적용할 수 있습니다. 이 기능을 인스턴스 크기 유연성 이라고 합니다. 할인 유동 적용 여부는 예약 구매 시 선택하는 특성과 예약의 유형에 따라 달라집니다.

서비스 플랜:

- Reserved VM Instances: 예약을 구매 하 고 **인스턴스 크기 유연성을 위해 최적화**를 선택 하는 경우 할인 범위는 선택한 VM 크기에 따라 달라 집니다. 같은 크기 시리즈 그룹의 VM(가상 머신) 크기에 예약을 적용할 수 있습니다. 자세한 내용은 [예약 VM 인스턴스를 통해 유동적으로 가상 머신 크기 조정](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)을 참조하세요.
- SQL Database 예약 용량: 할인 적용 범위는 선택하는 성능 계층에 따라 달라집니다. 자세한 내용은 [Azure 예약 할인이 적용되는 방식 이해](billing-understand-reservation-charges.md)를 참조하세요.
- Azure Cosmos DB 예약 용량: 할인 적용 범위는 프로비전되는 처리량에 따라 달라집니다. 자세한 내용은 [Azure Cosmos DB 예약 할인이 적용되는 방식 이해](billing-understand-cosmosdb-reservation-charges.md)를 참조하세요.

## <a name="reservation-notifications"></a>예약 알림

Azure 구독에 대 한 요금을 지불 하는 방법에 따라 조직의 다음 사용자에 게 예약 알림을 전자 메일로 보냅니다. 다음을 비롯 한 다양 한 이벤트에 대 한 알림이 전송 됩니다.

- 구매
- 예정 된 예약 만료
- 만료
- 갱신할
- 취소
- 범위 변경

EA 구독을 사용 하는 고객의 경우:
- 구매 알림이 구매자 및 EA 알림 연락처로 전송 됩니다.
- 다른 예약 수명 주기 알림은 EA 알림 연락처로만 전송 됩니다.
- RBAC (IAM) 권한을 사용 하 여 예약에 추가 된 사용자는 전자 메일 알림을 받지 않습니다.

개별 구독이 있는 고객의 경우:
- 구매 알림이 수신 됩니다.
- 구매 시 구독 청구 계정 소유자는 구매 알림을 받습니다.
- 계정 소유자는 다른 모든 알림을 받습니다.


## <a name="need-help-contact-us"></a>도움이 필요하십니까? 문의하세요.

궁금한 사항이 있거나 도움이 필요 하면 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
    - [종 량 제 요금으로 구독에 대 한 예약 사용 이해](billing-understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
    - [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)

- 서비스 계획 예약에 대해 자세히 알아보세요.
    - [Azure Reserved VM Instances Virtual Machines](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB 예약 된 용량을 사용 하 여 리소스 Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Azure SQL Database 예약 용량을 사용 하 여 계산 리소스 SQL Database](../sql-database/sql-database-reserved-capacity.md) 소프트웨어 계획에 대 한 예약에 대해 자세히 알아보세요.
    - [Azure Reservations의 Red Hat 소프트웨어 계획](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure Reservations에서 소프트웨어 계획 SUSE](../virtual-machines/linux/prepay-suse-software-charges.md)
