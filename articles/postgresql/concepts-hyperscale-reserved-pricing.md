---
title: 예약 된 계산 가격 책정-Azure Database for PostgreSQL-Hyperscale (Citus)
description: 예약 된 용량의 Citus (Azure Database for PostgreSQL-Hyperscale) 계산 리소스에 대 한 선불입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: a5ce99927ce4cd2b04b5dd5cb865299b4be84ecb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86519799"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>예약 된 용량의 Citus (Azure Database for PostgreSQL-Hyperscale) 계산 리소스에 대 한 선불

Azure Database for PostgreSQL – 이제 Citus (Hyperscale)를 사용 하 여 종 량 제 가격과 비교 하 여 계산 리소스에 대 한 총액 비용을 절감할 수 있습니다. Citus (Hyperscale) 예약 용량을 사용 하면 1 년 또는 3 년 동안 Citus (Hyperscale) 서버 그룹에 대 한 선불 약정을 만들어 계산 비용에 대 한 상당한 할인을 얻을 수 있습니다. Citus (Hyperscale) 예약 용량을 구입 하려면 Azure 지역, 예약 기간 및 청구 빈도를 지정 해야 합니다.

> [!IMPORTANT]
> 이 문서는 Azure Database for PostgreSQL – Hyperscale (Citus)에 대 한 예약 된 용량에 대 한 것입니다. Azure Database for PostgreSQL – 단일 서버에 대 한 예약 된 용량에 대 한 자세한 내용은 [선불 for Azure Database for PostgreSQL – reserved capacity이 포함 된 단일 서버 계산 리소스](/azure/postgresql/concept-reserved-pricing)를 참조 하세요.

특정 Citus (Hyperscale) 서버 그룹에 예약을 할당할 필요가 없습니다. 이미 실행 중인 Citus (Hyperscale) 서버 그룹이 나 새로 배포 된 서버 그룹이 자동으로 예약 된 가격 책정 혜택을 받습니다. 예약을 구매 하면 1 년 또는 3 년 동안 계산 비용이 총액 됩니다. 예약을 구매 하는 즉시 예약 특성과 일치 하는 Citus (Hyperscale) 계산 요금은 더 이상 종 량 제 요금으로 청구 되지 않습니다. 

예약은 Citus (Hyperscale) 서버 그룹과 연결 된 소프트웨어, 네트워킹 또는 저장소 요금을 포함 하지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료 되며 Citus (Hyperscale) 서버 그룹은 종 량 제 요금으로 청구 됩니다. 예약이 autorenew 되지 않습니다. 가격 책정 정보는 [Azure Database for PostgreSQL – Hyperscale (Citus) 예약 용량 제공](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)을 참조 하세요.

[Azure Portal](https://portal.azure.com/)에서 Citus (hyperscale) 예약 용량을 구입할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations)로 처리할 수 있습니다. 예약 된 용량을 구입 하려면:

* 하나 이상의 EA (기업계약) 또는 종 량 제 요금이 있는 개별 구독에 대 한 소유자 역할에 속해야 합니다.
* 기업계약 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 된 인스턴스 추가** 를 사용 하도록 설정 해야 합니다. 또는 해당 설정을 사용 하지 않도록 설정한 경우에는 구독에 대 한 기업계약 관리자 여야 합니다.
* CSP (클라우드 솔루션 공급자) 프로그램의 경우 관리 에이전트 또는 판매 에이전트만 Citus (Hyperscale) 예약 용량을 구매할 수 있습니다.

예약 구매에 대해 고객과 종 량 제 고객의 요금을 청구 하 기업계약는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
- [기업계약 등록에 대 한 Azure 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
- [종 량 제 구독에 대 한 Azure 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

## <a name="determine-the-right-server-group-size-before-purchase"></a>구매 하기 전에 올바른 서버 그룹 크기 확인

예약 크기는 특정 지역 내의 Citus (Hyperscale) 서버 그룹에서 기존 또는 곧 배포 된 코디네이터 및 작업자 노드에 사용 되는 총 계산 크기를 기준으로 합니다.

예를 들어, 16 개의 vCore 코디네이터 및 3 8 vCore 작업자 노드를 사용 하 여 Citus (Hyperscale) 서버 그룹 하나를 실행 하 고 있다고 가정해 보겠습니다. 또한 32 vCore 코디네이터 및 2 4 vCore 작업자 노드를 사용 하 여 다음 달에 추가 Citus (Hyperscale) 서버 그룹을 배포할 계획인 경우를 가정해 보겠습니다. 또한 1 년 이상에 대해 이러한 리소스가 필요 하다 고 가정해 보겠습니다.

이 경우 다음에 대 한 1 년 예약을 구매 합니다.

* 총 16 개 vCores + 32 vCores = 코디네이터 노드에 대 한 48 vCores
* 총 3 개 노드 x 8 vCores + 2 노드 x 4 vCores = 24 + 8 = 작업자 노드에 대 한 32 vCores

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL 예약 된 용량 구입

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **모든 서비스** > **예약**을 선택합니다.
1. **추가**를 선택합니다. **구매 예약** 창에서 **Azure Database for PostgreSQL** 을 선택 하 여 PostgreSQL 데이터베이스에 대 한 새 예약을 구매 합니다.
1. 구입할 **Hyperscale (Citus) 계산** 유형을 선택 하 고 **선택**을 클릭 합니다.
1. **제품** 탭에서 선택한 계산 유형의 수량을 검토 합니다.
1. 구매 **+ 검토** 탭으로 이동 하 여 구매를 완료 합니다.

다음 표에서는 필수 필드에 대해 설명 합니다.

| 필드        | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription | 예약 된 Azure Database for PostgreSQL 용량 예약에 대 한 비용을 지불 하는 데 사용 되는 구독입니다. 구독에 대 한 결제 방법은 Azure Database for PostgreSQL 예약 된 용량 예약에 대 한 선행 비용으로 청구 됩니다. 구독 유형은 기업계약 (제품 번호: MS-MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p) 이거나 종 량 제 가격의 개별 계약 (제품 번호: MS-AZR-0017P-0003P 또는 MS-MS-AZR-0017P-0017P) 이어야 합니다. 기업계약 구독의 경우 요금은 등록의 금액 약정 잔액에서 공제 초과분로 청구 됩니다. 종 량 제 가격의 개별 구독에 대해 요금 청구는 구독에 대 한 신용 카드 또는 청구서 지불 방법으로 청구 됩니다.                                                                                  |
| 범위        | VCore 예약의 범위는 하나의 구독 또는 여러 구독 (공유 범위)을 포함할 수 있습니다. **공유**를 선택 하는 경우 청구 컨텍스트 내의 모든 구독에서 실행 중인 Citus (hyperscale) 서버 그룹에 vcore 예약 할인이 적용 됩니다. 기업계약 고객의 경우 공유 범위는 등록 이며 등록 내의 모든 구독을 포함 합니다. 종 량 제 고객의 경우 공유 범위는 계정 관리자가 만든 모든 종 량 제 구독입니다. **단일 구독**을 선택 하는 경우이 구독에서 vcore 예약 할인이 Citus (hyperscale) 서버 그룹에 적용 됩니다. **단일 리소스 그룹**을 선택 하는 경우 예약 할인은 선택한 구독의 Citus (hyperscale) 서버 그룹 및 해당 구독 내에서 선택한 리소스 그룹에 적용 됩니다. |
| Azure 지역       | Azure Database for PostgreSQL – Hyperscale (Citus) 예약 용량 예약이 적용 되는 Azure 지역입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 용어         | 1년 또는 3년입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 수량     | Citus (Hyperscale) 예약 된 용량 예약 내에서 구매한 계산 리소스의 양입니다. 특히 예약 되는 선택 된 Azure 지역의 코디네이터 또는 작업자 노드 vCores 수 이며 청구 할인이 적용 됩니다. 예를 들어 미국 동부 지역에서 64 코디네이터 노드 vCores 및 32 작업자 노드 vCores의 총 계산 용량을 사용 하 여 Hyperscale (Citus) 서버 그룹을 실행 하는 경우 모든 서버에 대 한 혜택을 극대화 하려면 코디네이터 및 작업자 노드에 대 한 수량을 64 및 32로 지정 합니다.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [셀프 서비스 교환 및 Azure 예약에 대 한 환불](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)를 참조 하세요.

## <a name="vcore-size-flexibility"></a>vCore 크기 유연성

vCore 크기 유연성을 사용 하면 예약 된 용량 혜택을 잃지 않고 지역 내에서 코디네이터 및 작업자 노드를 확장 하거나 축소할 수 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

VCore 예약 할인은 Azure Database for PostgreSQL 예약 된 용량 예약 범위 및 특성과 일치 하는 Citus (Hyperscale) 서버 그룹 수에 자동으로 적용 됩니다. Azure Portal, PowerShell, Azure CLI 또는 API를 통해 Azure Database for PostgreSQL – Hyperscale (Citus) 예약 된 용량 예약의 범위를 업데이트할 수 있습니다.

Azure 예약에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure 예약은 무엇인가요?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Azure 예약 관리](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Azure 예약 할인 이해](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [종 량 제 구독에 대 한 예약 사용 이해](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [기업계약 등록에 대 한 예약 사용 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [파트너 센터 클라우드 솔루션 공급자 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)
