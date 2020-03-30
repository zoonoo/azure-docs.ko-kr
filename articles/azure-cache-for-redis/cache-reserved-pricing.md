---
title: 예약 된 용량계산에 대한 선불 - Redis에 대한 Azure 캐시
description: Redis에 대한 Azure 캐시에 대한 선불은 예약된 용량의 리소스를 계산합니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530303"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Redis에 대한 Azure 캐시에 대한 선불은 예약된 용량의 리소스를 계산합니다.

Redis용 Azure 캐시를 사용하면 종량제 가격과 비교하여 계산 리소스에 대해 선불로 비용을 절감할 수 있습니다. Redis 예약 용량에 대한 Azure 캐시를 사용하면 1년 또는 3년 동안 캐시에 대한 선행 약정을 통해 계산 비용을 크게 할인받을 수 있습니다. Redis 예약 용량에 대한 Azure 캐시를 구입하려면 Azure 지역, 서비스 계층 및 기간을 지정해야 합니다.

Redis 인스턴스에 대한 특정 Azure 캐시에 예약을 할당할 필요가 없습니다. Redis또는 새로 배포된 Azure 캐시를 이미 실행 중인 Azure 캐시는 예약된 캐시 크기까지 예약된 가격 책정의 이점을 자동으로 얻습니다. 예약을 구입하면 1년 또는 3년 동안의 컴퓨팅 비용을 선결제하게 됩니다. 예약을 구매하는 즉시 Redis에 대한 Azure Cache는 예약 속성과 일치하는 요금을 계산하여 종량제 요금으로 더 이상 청구되지 않습니다. 예약에는 캐시와 관련된 네트워킹 또는 저장소 요금이 포함되지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료되고 Redis에 대한 Azure 캐시는 종량제 가격으로 청구됩니다. 예약은 자동 갱신되지 않습니다. 가격 정보는 [Redis 예약 용량 제공에 대한 Azure 캐시를](https://azure.microsoft.com/pricing/details/cache)참조하십시오.

Azure [포털에서](https://portal.azure.com/)Redis 예약 용량에 대한 Azure 캐시를 구입할 수 있습니다. 예약된 용량을 구입하려면 다음을 수행하십시오.

* 종량제 요금으로 하나 이상의 엔터프라이즈 또는 개별 구독의 소유자 역할에 있어야 합니다.
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
* CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트만 Redis 예약 용량에 대한 Azure 캐시를 구입할 수 있습니다.

엔터프라이즈 고객 및 종량제 고객이 예약 구매에 대해 청구되는 방식에 대한 자세한 내용은 [엔터프라이즈 등록에 대한 Azure 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) 및 [종량제 구독의 Azure 예약 사용량을 이해합니다.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-cache-size-before-purchase"></a>구매 하기 전에 올바른 캐시 크기 확인

예약 규모는 특정 지역 내에서 기존 또는 곧 배포될 캐시에서 사용하고 동일한 서비스 계층을 사용하는 총 계산 량을 기반으로 해야 합니다.

예를 들어, 하나의 범용 을 실행 하는 가정 해 봅시다, Gen5 – 32 vCore 캐시, 그리고 두 개의 메모리 최적화, Gen5 – 16 vCore 캐시. 또한 다음 달 내에 추가 범용 인 Gen5 - 32 vCore 데이터베이스 서버 및 최적화된 메모리 1개인 Gen5 – 16vCore 데이터베이스 서버를 추가로 배포할 계획이라고 가정해 보겠습니다. 이러한 리소스가 최소 1년 동안 필요하다는 것을 알고 있다고 가정해 보겠습니다. 이 경우 단일 데이터베이스 메모리최적화를 위한 64(2x32) vCores, 단일 데이터베이스 범용 1년 예약-Gen5 및 48(2x16 + 16) vCore 1년 예약을 구매해야 합니다- Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Redis 예약 용량에 대한 Azure 캐시 구매

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
2. **모든 서비스** > **예약을 선택합니다.**
3. **추가를** 선택한 다음 구매 예약 창에서 **Redis에 대한 Azure 캐시를** 선택하여 캐시에 대한 새 예약을 구입합니다.
4. 필요한 필드를 채웁니다. 선택한 특성과 일치하는 기존 또는 새 데이터베이스는 예약된 용량 할인을 받을 수 있습니다. 할인을 받는 Redis 인스턴스에 대한 Azure 캐시의 실제 수는 선택한 범위 및 수량에 따라 다릅니다.


![예약 가격 개요](media/cache-reserved-pricing/cache-reserved-price.png)


다음 표는 필수 필드에 대해 설명합니다.

| 필드 | 설명 |
| :------------ | :------- |
| Subscription   | Redis 예약 용량 예약에 대 한 Azure 캐시에 대 한 지불 하는 데 사용 하는 구독입니다. 구독의 결제 방법에는 Redis 예약 용량 예약에 대한 Azure 캐시에 대한 선결제 비용이 청구됩니다. 구독 유형은 기업 계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제 가격(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이 있는 개별 계약이어야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종량제 가격이 책정된 개별 구독의 경우 구독의 신용 카드 또는 송장 결제 방법으로 요금이 청구됩니다.
| Scope | 예약 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: </br></br> **공유,** 예약 할인은 청구 컨텍스트 내의 모든 구독에서 실행되는 Redis 인스턴스에 대한 Azure 캐시에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</br></br> **단일 구독,** 예약 할인은 이 구독의 Redis 인스턴스에 대한 Azure 캐시에 적용됩니다. </br></br> **단일 리소스 그룹,** 예약 할인은 선택한 구독의 Redis 인스턴스및 해당 구독 내의 선택한 리소스 그룹에 대한 Azure Cache에 적용됩니다.
| 지역 | Redis 예약 용량 예약에 대 한 Azure 캐시에 의해 적용 되는 Azure 지역입니다.
| 가격 책정 계층 | Redis 서버에 대한 Azure 캐시의 서비스 계층입니다.
| 용어 | 1년 또는 3년
| 수량 | Redis 예약 용량 예약에 대 한 Azure 캐시 내에서 구입 하는 계산 리소스의 양입니다. 수량은 예약중인 선택한 Azure 지역 및 서비스 계층의 여러 캐시이며 청구 할인을 받게 됩니다. 예를 들어 미국 동부 지역에서 총 캐시 용량이 26GB인 Redis 서버에 대한 Azure 캐시를 실행하거나 실행하려는 경우 수량을 26으로 지정하여 모든 캐시의 이점을 최대화합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)을 참조하세요.

## <a name="cache-size-flexibility"></a>캐시 크기 유연성

캐시 크기 유연성을 사용하면 예약된 용량 이점을 잃지 않고 서비스 계층 및 리전 내에서 확장 또는 축소할 수 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만듭니다.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>다음 단계

예약 할인은 예약 범위 및 특성과 일치하는 Redis 인스턴스에 대한 Azure 캐시에 자동으로 적용됩니다. 예약 범위는 Azure Portal, PowerShell, Azure CLI 또는 API를 통해 업데이트할 수 있습니다.

*  Redis에 대한 Azure 캐시에 예약된 용량 할인이 적용되는 방법을 알아보려면 [Azure 예약 할인 이해하기를](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md) 참조하십시오.

* Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

    * [Azure 예약이란 무엇입니까?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Azure 예약 관리](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Azure 예약 할인 이해](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [종량제 구독의 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [엔터프라이즈 등록에서 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

