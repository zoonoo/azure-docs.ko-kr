---
title: 비용을 절감하기 위해 Azure 전용 호스트에 대한 선불
description: 계산 비용을 절감하기 위해 Azure 전용 호스트 예약 인스턴스를 구입하는 방법을 알아봅니다.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207746"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Azure 전용 호스트의 예약 인스턴스로 비용 절감

Azure 전용 호스트의 예약된 인스턴스에 커밋하면 비용을 절약할 수 있습니다. 예약 할인은 예약 범위 및 속성과 일치하는 실행 중인 전용 호스트 수에 자동으로 적용됩니다. 할인을 받기 위해 전용 호스트에 예약을 할당할 필요가 없습니다. 예약인스턴스 구매는 사용량의 계산 부분만 포함하며 소프트웨어 라이선스 비용이 포함됩니다. 가상 [시스템에 대한 Azure 전용 호스트 의 개요를](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)참조하십시오.

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>구매하기 전에 적합한 전용 호스트 SKU 결정


예약을 구입하기 전에 필요한 전용 호스트를 결정해야 합니다. SKU는 VM 계열 및 형식을 나타내는 전용 호스트에 대해 정의됩니다. 

[먼저 Windows 가상 컴퓨터](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 또는 [Linux용으로](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 지원되는 크기를 이동하여 VM 시리즈를 식별합니다.

그런 다음 Azure 전용 호스트에서 지원되는지 확인합니다. [Azure 전용 호스트 가격 책정](https://aka.ms/ADHPricing) 페이지에는 전용 호스트 SCO, 해당 CPU 정보 및 예약된 인스턴스 를 포함한 다양한 가격 책정 옵션의 전체 목록이 있습니다.

VM 시리즈를 지원하는 여러 SCO(유형이 다를 수 있음)를 찾을 수 있습니다. 호스트의 용량(vCPU 수)을 비교하여 최상의 SKU를 식별합니다. 동일한 VM 시리즈(예: DSv3_Type1 및 DSv3_Type2)를 지원하지만 다른 VM 시리즈(예: DSv3 및 ESv3)에서는 지원하지 않는 여러 전용 호스트 SCO에 예약을 적용할 수 있습니다.



## <a name="purchase-restriction-considerations"></a>구매 제한 고려 사항

예약 인스턴스는 일부 예외를 제외하고 대부분의 전용 호스트 크기에 사용할 수 있습니다.

예약 할인은 다음 의 경우 적용되지 않습니다.

- **클라우드** - 독일 또는 중국 지역에서는 예약을 구매할 수 없습니다.

- **할당량 부족** - 단일 구독으로 범위가 지정된 예약에는 새 예약 인스턴스에 대한 구독에서 vCPU 할당량을 사용할 수 있어야 합니다. 예를 들어 대상 구독의 할당량 제한이 DSv3-Series에 대한 10vCPU인 경우 이 시리즈를 지원하는 예약 전용 호스트를 구입할 수 없습니다. 예약에 대한 할당량 검사에는 구독에 이미 배포된 VM 및 전용 호스트가 포함됩니다. 이 문제를 해결하기 위해 [할당량 증가 요청을](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) 만들 수 있습니다.

- **용량 제한** - 드문 경우지만 Azure는 지역의 용량이 낮기 때문에 전용 호스트 SCO의 하위 집합에 대한 새 예약 구매를 제한합니다.

## <a name="buy-a-reservation"></a>예약 구입

Azure [포털에서](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)Azure 전용 호스트 인스턴스의 예약된 인스턴스를 구입할 수 있습니다.

예약금을 [선불로 지불하거나 월 납부한 금액을 지불합니다.](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations) 이러한 요구 사항은 예약된 전용 호스트 인스턴스 를 구입하는 데 적용됩니다.

- 하나 이상의 EA 구독또는 종량제 요금이 있는 구독의 소유자 역할에 있어야 합니다.

- EA 구독의 경우 **예약 인스턴스** 추가 옵션은 [EA 포털에서](https://ea.azure.com/)활성화되어야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.

- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트는 예약 구매를 할 수 있습니다.

인스턴스를 구입하려면:

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2.  **모든 서비스** \> **예약을 선택합니다.**

3. 새 예약을 구입하려면 **추가를** 선택한 다음 **전용 호스트를**클릭합니다.

4. 필수 필드를 입력합니다. 선택한 특성과 일치하는 전용 호스트 인스턴스를 실행하여 예약 할인을 받을 수 있습니다. 할인을 받는 전용 호스트 인스턴스의 실제 수는 선택한 범위 및 수량에 따라 다릅니다.

EA 계약이 있는 경우 **추가 옵션을** 사용하여 인스턴스를 빠르게 추가할 수 있습니다. 다른 구독 유형에는 이 옵션을 사용할 수 없습니다.

| **필드**           | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription        | 예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 수단에는 예약 비용이 부과됩니다. 구독 유형은 기업 계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 Microsoft 고객 계약 또는 종량제 요금(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이 있는 개별 구독이어야 합니다. 요금은 가능한 경우 금전적 약정 잔액에서 차감되거나 초과로 청구됩니다. 종량제 요금이 있는 구독의 경우 구독의 신용 카드 또는 송장 결제 방법으로 요금이 청구됩니다. |
| Scope               | 예약 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 지역              | 예약 범위에 해당하는 Azure 지역입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 전용 호스트 크기 | 전용 호스트 인스턴스의 크기입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 용어                | 1년 또는 3년입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 수량            | 예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 전용 호스트 인스턴스를 실행하는 수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **단일 리소스 그룹 범위** — 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.

- **단일 구독 범위** — 선택한 구독의 일치하는 리소스에 예약 할인을 적용합니다.

- **공유 범위** — 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. EA 고객의 경우 청구 컨텍스트는 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.

## <a name="usage-data-and-reservation-utilization"></a>사용 데이터 및 예약 활용

예약 할인이 적용되는 사용량의 경우 사용량 데이터의 유효 가격은 0입니다. 각 예약에 대해 예약 할인을 받은 VM 인스턴스를 확인할 수 있습니다.

사용 데이터에 예약 할인이 표시되는 방식에 대한 자세한 내용은 EA 고객인 경우 엔터프라이즈  [등록에 대한 Azure 예약 사용량 이해하기를](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)참조하십시오. 개별 구독이 있는 경우 [종량제 구독에 대한 Azure 예약 사용량 이해하기를](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)참조하십시오.

## <a name="change-a-reservation-after-purchase"></a>구매 후 예약 변경

구매한 후 예약에 대해 다음과 같은 유형의 변경을 수행할 수 있습니다.

- 예약 범위 업데이트

- 인스턴스 크기 유연성(해당하는 경우)

- 소유권

또한 예약을 더 작은 청크로 분할하고 이미 분할된 예약을 병합할 수도 있습니다. 변경 사항 중 어느 것도 새로운 상업 거래를 일으키거나 예약 종료 날짜를 변경하지 않습니다.

구매 후 다음과 같은 유형의 변경은 직접 수행할 수 없습니다.

- 기존 예약 지역

- SKU

- 수량

- Duration

그러나 변경하려는 경우 예약을 *교환할* 수 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure 예약에 대한 셀프 서비스 교환 및 환불을](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)참조하십시오.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만듭니다.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>다음 단계

예약을 관리하는 방법에 대해 알아보려면 [Azure 예약 관리를](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)참조하십시오.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란 무엇입니까?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Azure 전용 호스트 사용](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [전용 호스트 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Azure에서 예약 관리](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [예약 할인이 적용되는 방식 이해](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [종량제 요금을 사용하는 구독에 대한 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [엔터프라이즈 등록에서 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [예약에 포함되지 않는 Windows 소프트웨어 비용](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)


