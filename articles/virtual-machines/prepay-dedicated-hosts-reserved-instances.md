---
title: 비용을 절감 하기 위한 Azure 전용 호스트의 선불
description: 계산 비용을 절감 하기 위해 Azure 전용 호스트 예약 인스턴스를 구매 하는 방법을 알아봅니다.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 919ff7474dd64cfe3005975beb4cab6f77c177ce
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87825262"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Azure 전용 호스트 예약을 사용 하 여 비용 절감

Azure 전용 호스트의 예약 된 인스턴스로 커밋하는 경우 비용을 절감할 수 있습니다. 예약 할인은 예약 범위 및 특성과 일치 하는 실행 중인 전용 호스트 수에 자동으로 적용 됩니다. 할인을 얻기 위해 전용 호스트에 예약을 할당할 필요가 없습니다. 예약 인스턴스 구매는 사용량의 계산 부분만 다루며 소프트웨어 라이선스 비용이 포함 됩니다. [가상 컴퓨터에 대 한 Azure 전용 호스트 개요](./windows/dedicated-hosts.md)를 참조 하세요.

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>구매 하기 전에 적절 한 전용 호스트 SKU 확인


예약을 구입 하기 전에 필요한 전용 호스트를 결정 해야 합니다. SKU는 VM 계열과 유형을 나타내는 전용 호스트에 대해 정의 됩니다. 

먼저 [Windows 가상 머신](./sizes.md) 또는 [Linux](./sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 에 대해 지원 되는 크기를 이동 하 여 VM 시리즈를 식별 합니다.

다음으로, Azure 전용 호스트에서 지원 되는지 여부를 확인 합니다. [Azure 전용 호스트 가격](https://aka.ms/ADHPricing) 페이지에는 전용 호스트 sku의 전체 목록, CPU 정보 및 다양 한 가격 책정 옵션 (예약 인스턴스 포함)이 있습니다.

VM 시리즈를 지 원하는 여러 Sku (다른 유형 포함)를 찾을 수 있습니다. 호스트의 용량 (vCPUs 수)을 비교 하 여 최상의 SKU를 식별 합니다. 동일한 VM 시리즈 (예: DSv3_Type1 및 DSv3_Type2)를 지 원하는 여러 개의 전용 호스트 Sku에 대 한 예약을 적용할 수 있지만 다른 VM 시리즈 (예: DSv3 및 ESv3) 간에는 적용 되지 않습니다.



## <a name="purchase-restriction-considerations"></a>구매 제한 고려 사항

예약 인스턴스는 일부 예외를 제외 하 고 대부분의 전용 호스트 크기에 사용할 수 있습니다.

예약 할인은 다음에 적용 되지 않습니다.

- **클라우드**   -예약은 독일 또는 중국 지역에서 구매할 수 없습니다.

- **할당량 부족**   -단일 구독으로 범위가 지정 된 예약은 새 예약 인스턴스의 구독에서 vCPU 할당량을 사용할 수 있어야 합니다. 예를 들어 대상 구독에 DSv3 시리즈에 대 한 10 개 vCPUs의 할당량 한도가 있는 경우이 시리즈를 지 원하는 예약 전용 호스트를 구입할 수 없습니다. 예약에 대 한 할당량 확인에는 구독에 이미 배포 된 Vm 및 전용 호스트가 포함 됩니다.  [할당량 증가 요청을 만들어](../azure-portal/supportability/resource-manager-core-quotas-request.md)   이 문제를 해결할 수 있습니다.

- **용량 제한**   -드문 경우 지만 Azure는 지역에 용량이 부족 하 여 전용 호스트 Sku의 하위 집합에 대 한 새 예약 구매를 제한 합니다.

## <a name="buy-a-reservation"></a>예약 구입

 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)에서 Azure 전용 호스트 인스턴스의 예약 인스턴스를 구입할 수 있습니다.

예약에 대 한 요금을 지불 [하거나 매월 지불](../cost-management-billing/reservations/prepare-buy-reservation.md)합니다. 이러한 요구 사항은 예약 된 전용 호스트 인스턴스를 구입 하는 데 적용 됩니다.

- 하나 이상의 EA 구독에 대 한 소유자 역할 또는 종 량 제 요금이 있는 구독 이어야 합니다.

- EA 구독의 경우 Ea 포털에서 **예약 된 인스턴스 추가**   옵션을 사용 하도록 [EA portal](https://ea.azure.com/)설정 해야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.

- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트는 예약 구매를 할 수 있습니다.

인스턴스를 구매하려면

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2.  **모든 서비스**   \>  **예약**을 선택 합니다.

3.  **추가**   를 선택 하 여 새 예약을 구매 하 고 **전용 호스트**를 클릭 합니다.

4. 필수 필드를 입력합니다. 선택 하는 특성과 일치 하는 전용 호스트 인스턴스를 실행 하면 예약 할인이 적용 됩니다. 할인을 받는 전용 호스트 인스턴스의 실제 수는 선택 된 범위와 수량에 따라 다릅니다.

EA 계약이 있는 경우 추가 **옵션**을 사용   하 여 인스턴스를 빠르게 추가할 수 있습니다. 다른 구독 유형에 대해서는이 옵션을 사용할 수 없습니다.

| **필드**           | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription        | 예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 기업 계약 (제품 번호: MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p) 또는 Microsoft 고객 계약 또는 종 량 제 요금 (제품 번호: MS-MS-AZR-0017P-0003P 또는 MS-AZR-0017P-0017P)을 포함 하는 개별 구독 이어야 합니다. 요금은 약정 잔액에서 차감되거나(있는 경우) 초과분 요금으로 청구됩니다. 종 량 제 요금이 있는 구독의 경우 요금 청구는 구독에 대 한 신용 카드 또는 청구서 지불 방법으로 청구 됩니다. |
| Scope               | 예약 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 지역              | 예약 범위에 해당하는 Azure 지역입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 전용 호스트 크기 | 전용 호스트 인스턴스의 크기입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 용어                | 1년 또는 3년입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 수량            | 예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 전용 호스트 인스턴스 수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **단일 리소스 그룹 범위**   -선택한 리소스 그룹의 일치 하는 리소스에만 예약 할인을 적용 합니다.

- **단일 구독 범위**   -선택한 구독의 일치 하는 리소스에 예약 할인을 적용 합니다.

- **공유 범위**   -청구 컨텍스트에 있는 적격 구독의 일치 하는 리소스에 예약 할인을 적용 합니다. EA 고객의 경우 요금 청구 컨텍스트가 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.

## <a name="usage-data-and-reservation-utilization"></a>사용 현황 데이터 및 예약 사용률

예약 할인이 적용되는 사용량의 경우 사용량 데이터의 유효 가격은 0입니다. 각 예약에 대해 예약 할인을 받은 VM 인스턴스를 확인할 수 있습니다.

사용 현황 데이터에 예약 할인이 표시 되는 방법에 대 한 자세한 내용은 EA 고객 인 경우 [기업 등록에 대 한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)를 참조 하세요   . 개별 구독이 있는 경우 [종 량 제 구독에 대 한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조 하세요.

## <a name="change-a-reservation-after-purchase"></a>구매 후 예약 변경

구매한 후 예약에 대해 다음과 같은 유형의 변경을 수행할 수 있습니다.

- 예약 범위 업데이트

- 인스턴스 크기 유연성 (해당 하는 경우)

- 소유권

또한 예약을 더 작은 청크로 분할 하 고 이미 분할 된 예약을 병합할 수 있습니다. 새 상업용 트랜잭션을 발생 시키거나 예약의 종료 날짜를 변경 하지 않습니다.

구입한 후에는 다음과 같은 유형의 변경 작업을 직접 수행할 수 없습니다.

- 기존 예약 영역

- SKU

- 수량

- 기간

그러나 *exchange*   변경을 수행 하려는 경우 예약을 교환할 수 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [셀프 서비스 교환 및 Azure Reservations에 대 한 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)를 참조 하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우  [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

예약을 관리 하는 방법에 대 한 자세한 내용은 [Azure Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)를 참조 하세요.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Azure Dedicated Host 사용](./windows/dedicated-hosts.md)

- [Dedicated Host 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Azure에서 Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [예약 할인이 적용되는 방식 이해](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [종량제 요금을 사용하는 구독에 대한 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [엔터프라이즈 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [예약에 포함되지 않는 Windows 소프트웨어 비용](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)