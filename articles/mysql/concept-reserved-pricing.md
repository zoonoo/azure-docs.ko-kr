---
title: 예약 된 용량계산에 대한 선불 - MySQL용 Azure 데이터베이스
description: 예약 된 용량으로 MySQL 계산 리소스에 대 한 Azure 데이터베이스에 대 한 선불
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 78c8750de7189bad33e9bbc766a3d7543a646f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159356"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>예약 된 용량으로 MySQL 계산 리소스에 대 한 Azure 데이터베이스에 대 한 선불

이제 MySQL용 Azure 데이터베이스는 종량제 가격과 비교하여 계산 리소스에 대해 선불로 지불하여 비용을 절감할 수 있도록 도와줍니다. MySQL 예약 용량에 대한 Azure 데이터베이스를 사용하면 1년 또는 3년 동안 MySQL 서버에 대한 선행 약정을 통해 계산 비용을 크게 할인받을 수 있습니다. MySQL 예약 용량에 대한 Azure 데이터베이스를 구입하려면 Azure 지역, 배포 유형, 성능 계층 및 용어를 지정해야 합니다. </br>

MySQL 서버에 대한 특정 Azure 데이터베이스에 예약을 할당할 필요가 없습니다. MySQL용 Azure 데이터베이스 또는 새로 배포된 Azure 데이터베이스는 예약된 가격 책정의 이점을 자동으로 얻습니다. 예약을 구입하면 1년 또는 3년 동안의 컴퓨팅 비용을 선결제하게 됩니다. 예약을 구입하자마자 예약 속성과 일치하는 MySQL 계산 요금에 대한 Azure 데이터베이스는 더 이상 종량제 요금이 청구되지 않습니다. 예약에는 MySQL 데이터베이스 서버와 관련된 소프트웨어, 네트워킹 또는 저장소 요금이 포함되지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료되고 MySQL용 Azure 데이터베이스는 종량제 가격으로 청구됩니다. 예약은 자동 갱신되지 않습니다. 가격 정보는 [MySQL 예약 용량 제공에 대한 Azure 데이터베이스를](https://azure.microsoft.com/pricing/details/mysql/)참조하십시오. </br>

Azure [포털에서](https://portal.azure.com/)MySQL 예약 용량에 대한 Azure 데이터베이스를 구입할 수 있습니다. 예약된 용량을 구입하려면 다음을 수행하십시오.

* 종량제 요금으로 하나 이상의 엔터프라이즈 또는 개별 구독의 소유자 역할에 있어야 합니다.
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
* CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트만 MySQL 예약 용량에 대해 Azure Database를 구입할 수 있습니다. </br>

엔터프라이즈 고객 및 종량제 고객이 예약 구매에 대해 청구되는 방식에 대한 자세한 내용은 [엔터프라이즈 등록에 대한 Azure 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) 및 [종량제 구독에 대한 Azure 예약 사용량 이해를](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)참조하세요.


## <a name="determine-the-right-database-size-before-purchase"></a>구매 하기 전에 올바른 데이터베이스 크기 확인

예약 규모는 특정 지역 내에서 기존 또는 곧 배포될 서버에서 동일한 성능 계층 및 하드웨어 생성을 사용하는 총 계산량을 기반으로 해야 합니다.</br>

예를 들어, 하나의 범용 을 실행 하는 가정 해 봅시다 5 – 32 vCore MySQL 데이터베이스, 그리고 두 개의 메모리 최적화, Gen5 – 16 vCore MySQL 데이터베이스. 또한 다음 달 내에 추가 범용 인 Gen5 - 32 vCore 데이터베이스 서버 및 최적화된 메모리 1개인 Gen5 – 16vCore 데이터베이스 서버를 추가로 배포할 계획이라고 가정해 보겠습니다. 이러한 리소스가 최소 1년 동안 필요하다는 것을 알고 있다고 가정해 보겠습니다. 이 경우 단일 데이터베이스 메모리최적화를 위한 64(2x32) vCores, 단일 데이터베이스 범용 1년 예약-Gen5 및 48(2x16 + 16) vCore 1년 예약을 구매해야 합니다- Gen5


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>MySQL 예약 용량에 대한 Azure 데이터베이스 구매

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
2. **모든 서비스** > **예약을 선택합니다.**
3. **추가를** 선택한 다음 구매 예약 창에서 **MySQL용 Azure 데이터베이스를** 선택하여 MySQL 데이터베이스에 대한 새 예약을 구입합니다.
4. 필요한 필드를 채웁니다. 선택한 특성과 일치하는 기존 또는 새 데이터베이스는 예약된 용량 할인을 받을 수 있습니다. 할인을 받는 MySQL 서버에 대한 Azure 데이터베이스의 실제 수는 선택한 범위와 수량에 따라 다릅니다.


![예약 가격 개요](media/concepts-reserved-pricing/mysql-reserved-price.png)


다음 표는 필수 필드에 대해 설명합니다.

| 필드 | 설명 |
| :------------ | :------- |
| Subscription   | MySQL 예약 용량 예약에 대 한 Azure 데이터베이스에 대 한 지불 하는 데 사용 하는 구독입니다. 구독의 결제 방법에는 MySQL 예약 용량 예약에 대한 Azure 데이터베이스에 대한 선결제 비용이 청구됩니다. 구독 유형은 기업 계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제 가격(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이 있는 개별 계약이어야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종량제 가격이 책정된 개별 구독의 경우 구독의 신용 카드 또는 송장 결제 방법으로 요금이 청구됩니다.
| Scope | vCore 예약의 범위는 하나의 구독 또는 여러 구독(공유 범위)을 포함할 수 있습니다. 다음을 선택하는 경우: </br></br> **공유**, vCore 예약 할인은 청구 컨텍스트 내의 모든 구독에서 실행 중인 MySQL 서버에 대한 Azure 데이터베이스에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</br></br> **단일 구독,** vCore 예약 할인은 이 구독의 MySQL 서버에 대한 Azure 데이터베이스에 적용됩니다. </br></br> **단일 리소스 그룹,** 예약 할인은 선택한 구독의 MySQL 서버용 Azure 데이터베이스와 해당 구독 내의 선택한 리소스 그룹에 적용됩니다.
| 지역 | MySQL 예약 용량 예약에 대 한 Azure 데이터베이스에 의해 적용 되는 Azure 지역입니다.
| 배포 유형 | 예약을 구입하려는 MySQL 리소스 유형에 대한 Azure 데이터베이스입니다.
| 성능 계층 | MySQL 서버에 대한 Azure 데이터베이스의 서비스 계층입니다.
| 용어 | 1년
| 수량 | MySQL 예약 용량 예약에 대 한 Azure 데이터베이스 내에서 구입 하는 계산 리소스의 양입니다. 수량은 예약중인 선택한 Azure 리전 및 성능 계층의 여러 vCores이며 청구 할인을 받게 됩니다. 예를 들어 미국 동부 지역에서 Gen5 16 vCore의 총 컴퓨팅 용량을 사용하여 MySQL 서버용 Azure 데이터베이스를 실행하거나 실행하려는 경우 수량을 16으로 지정하여 모든 서버의 이점을 최대화합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)을 참조하세요.

## <a name="vcore-size-flexibility"></a>vCore 크기 유연성

vCore 크기 유연성을 통해 예약된 용량 이점을 잃지 않고 성능 계층 및 지역 내에서 크기를 확장 또는 축소할 수 있습니다. 

## <a name="need-help--contact-us"></a>도움이 필요하십니까? 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만듭니다.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>다음 단계

vCore 예약 할인은 MySQL 예약 용량 예약 범위 및 특성에 대한 Azure 데이터베이스와 일치하는 MySQL 서버용 Azure 데이터베이스 수에 자동으로 적용됩니다. Azure 포털, PowerShell, CLI 또는 API를 통해 MySQL 예약 용량 예약에 대한 Azure 데이터베이스의 범위를 업데이트할 수 있습니다. </br></br>
MySQL 예약 용량에 대한 Azure 데이터베이스를 관리하는 방법을 알아보려면 MySQL 예약 용량에 대한 Azure 데이터베이스 관리를 참조하십시오.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 예약이란?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Azure 예약 관리](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Azure 예약 할인 이해](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [종량제 구독의 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
* [엔터프라이즈 등록에서 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

