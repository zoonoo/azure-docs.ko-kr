---
title: 선불 for compute 용량-Redis 용 Azure Cache
description: 예약 된 용량의 Redis 계산 리소스에 대 한 Azure Cache 선불
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8af8db6b6853d6b8cbd4ba1105f05ebb9bcf771b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974843"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>예약 된 용량의 Redis 계산 리소스에 대 한 Azure Cache 선불

이제 Redis 용 Azure Cache를 사용 하 여 종 량 제 가격과 비교 하 여 계산 리소스에 대 한 총액 비용을 절감할 수 있습니다. Redis 예약 용량의 Azure Cache를 사용 하 여 계산 비용에 대 한 상당한 할인을 얻기 위해 1 년 또는 3 년 동안 캐시에 대해 사전 약정을 수행 합니다. Redis 예약 된 용량에 대 한 Azure 캐시를 구매 하려면 Azure 지역, 서비스 계층 및 용어를 지정 해야 합니다.

Redis 인스턴스에 대 한 특정 Azure 캐시에 예약을 할당할 필요가 없습니다. Redis 용으로 이미 실행 중인 Azure 캐시 또는 새로 배포 된 Azure Cache는 예약 된 캐시 크기까지 자동으로 예약 된 가격 책정 혜택을 받게 됩니다. 예약을 구입하면 1년 또는 3년 동안의 컴퓨팅 비용을 선결제하게 됩니다. 예약을 구매 하는 즉시 예약 특성과 일치 하는 Redis 계산 요금에 대 한 Azure 캐시가 더 이상 종 량 제 요금으로 청구 되지 않습니다. 예약은 캐시와 연결 된 네트워킹 또는 저장소 요금을 포함 하지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료 되며 Azure Cache for Redis는 종 량 제 가격으로 청구 됩니다. 예약은 자동 갱신되지 않습니다. 가격 책정 정보는 [Redis 예약 된 용량 제공에 대 한 Azure 캐시](https://azure.microsoft.com/pricing/details/cache)를 참조 하세요.

[Azure Portal](https://portal.azure.com/)에서 Redis 예약 된 용량에 대 한 Azure 캐시를 구매할 수 있습니다. 예약 된 용량을 구입 하려면:

* 종 량 제 요금은 하나 이상의 Enterprise 또는 개별 구독에 대 한 소유자 역할에 속해야 합니다.
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
* CSP (클라우드 솔루션 공급자) 프로그램의 경우 관리 에이전트 또는 판매 에이전트만 Redis 예약 용량의 Azure Cache를 구매할 수 있습니다.

엔터프라이즈 고객과 종 량 제 고객이 예약 구매에 대해 부과 하는 방법에 대 한 자세한 내용은 [기업 등록에 대 한 azure 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) 및 [종 량 제 구독에 대 한 azure 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)를 참조 하세요.


## <a name="determine-the-right-cache-size-before-purchase"></a>구매 하기 전에 올바른 캐시 크기 확인

예약 크기는 특정 지역 내에서 그리고 동일한 서비스 계층을 사용 하 여 기존 또는 곧 배포 되는 캐시에 사용 되는 총 메모리 크기를 기준으로 해야 합니다.

예를 들어 두 개의 캐시를 실행 하 고 있다고 가정 합니다. 하나는 13 GB이 고 다른 하나는 248gb입니다. 하나 이상의 연도에 대해 둘 다 필요 합니다. 또한 계절 수요를 충족 하기 위해 한 달 동안 기존 13 GB 캐시를 확장 하 고 나 서 크기를 조정 하려는 경우를 가정해 보겠습니다. 이 경우 1 년 예약에서 1 개의 P1 캐시와 1 개의 P2 캐시 또는 3 개의 P1 캐시를 구매 하 여 절감 액을 최대화할 수 있습니다. 캐시에 할당 되는 방법에 관계 없이 예약 된 총 캐시 메모리 양에 대 한 할인율을 받게 됩니다.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Redis 예약 된 용량에 대 한 Azure 캐시 구입

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. **추가** 를 선택한 다음 구매 예약 창에서 **Redis 용 Azure Cache** 를 선택 하 여 캐시에 대 한 새 예약을 구매 합니다.
4. Fill-필수 필드입니다. 선택한 특성과 일치 하는 기존 또는 새 데이터베이스는 예약 된 용량 할인을 얻기 위해 한정 됩니다. 할인을 받는 Redis 인스턴스에 대 한 Azure Cache의 실제 수는 선택 된 범위와 수량에 따라 다릅니다.


![예약 된 가격 책정 개요](media/cache-reserved-pricing/cache-reserved-price.png)


다음 표에서는 필수 필드에 대해 설명 합니다.

| 필드 | Description |
| :------------ | :------- |
| Subscription   | Redis 예약 된 용량 예약을 위해 Azure 캐시에 대 한 비용을 지불 하는 데 사용 되는 구독입니다. 구독에 대 한 지불 방법에는 Redis 예약 된 용량 예약을 위해 Azure 캐시에 대 한 사전 비용이 청구 됩니다. 구독 유형은 기업 계약 (제품 번호: MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p) 이거나 종 량 제 가격을 포함 하는 개별 계약 (제품 번호: MS-MS-AZR-0017P-0003P 또는-0017P) 이어야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종 량 제 가격의 개별 구독에 대해 요금 청구는 구독에 대 한 신용 카드 또는 청구서 지불 방법으로 청구 됩니다.
| Scope | 예약 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: </br></br> **공유**, 예약 할인은 청구 컨텍스트 내의 모든 구독에서 실행 중인 Redis 인스턴스에 대 한 Azure Cache에 적용 됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</br></br> **단일 구독**, 예약 할인은이 구독에서 Redis 인스턴스에 대 한 Azure Cache에 적용 됩니다. </br></br> **단일 리소스 그룹**, 예약 할인은 선택한 구독의 Redis 인스턴스에 대 한 Azure 캐시와 해당 구독 내에서 선택한 리소스 그룹에 적용 됩니다.
| 지역 | Redis 예약 된 용량 예약을 위해 Azure 캐시에서 적용 되는 Azure 지역입니다.
| 가격 책정 계층 | Redis 서버용 Azure 캐시에 대 한 서비스 계층입니다.
| 용어 | 1 년 또는 3 년
| 수량 | Redis 예약 된 용량 예약을 위해 Azure 캐시 내에서 구매한 계산 리소스의 양입니다. 수량은 선택한 Azure 지역 및 서비스 계층에서 예약 되 고 있으며 청구 할인이 적용 되는 다양 한 캐시입니다. 예를 들어 미국 동부 지역에서 총 캐시 용량을 26GB로 사용 하 여 Redis 서버용 Azure Cache를 실행 하 고 있는 경우 모든 캐시에 대 한 혜택을 최대화 하기 위해 수량을 26으로 지정 합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)을 참조하세요.

## <a name="cache-size-flexibility"></a>캐시 크기 유연성

캐시 크기 유연성을 사용 하면 예약 된 용량 혜택을 잃지 않고 서비스 계층 및 지역 내에서 규모를 확장 하거나 축소할 수 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

예약 할인은 예약 범위 및 특성과 일치 하는 Redis 인스턴스에 대 한 Azure 캐시에 자동으로 적용 됩니다. 예약 범위는 Azure Portal, PowerShell, Azure CLI 또는 API를 통해 업데이트할 수 있습니다.

*  Redis 용 Azure Cache에 예약 된 용량 할인이 적용 되는 방법을 알아보려면 [azure 예약 할인 이해](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md) 를 참조 하세요.

* Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

    * [Azure 예약이란?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Azure Reservations 관리](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Azure 예약 할인 이해](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [종량제 구독의 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [엔터프라이즈 등록에서 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

