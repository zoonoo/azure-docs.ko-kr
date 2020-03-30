---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590730"
---
예약된 용량으로 Azure 디스크 저장소 사용량을 저장합니다. Azure 디스크 저장소 예약과 Azure 예약 가상 시스템 인스턴스를 결합하면 총 VM(가상 시스템) 비용을 절감할 수 있습니다. 예약 할인은 선택한 예약 범위의 일치하는 디스크에 자동으로 적용됩니다. 이 자동 응용 프로그램으로 인해 할인을 받기 위해 관리 디스크에 예약을 할당할 필요가 없습니다.

디스크 사용량에 따라 매시간 할인이 적용됩니다. 사용하지 않은 예약 용량은 이월되지 않습니다. Azure 디스크 저장소 예약 할인관리 되지 않는 디스크, 울트라 디스크 또는 페이지 Blob 소비에 적용 되지 않습니다.

## <a name="determine-your-storage-needs"></a>스토리지 요구 사항 결정

예약을 구입하기 전에 스토리지 요구 사항을 결정합니다. 현재 Azure 디스크 저장소 예약은 일부 Azure 프리미엄 SSD SSD SCO에서만 사용할 수 있습니다. 프리미엄 SSD의 SKU는 디스크의 크기와 성능을 결정합니다.

스토리지 요구 사항을 결정할 때는 용량만으로 디스크를 생각하지 마십시오. 예를 들어 P40 디스크에 대한 예약을 할 수 없으며 이 디스크를 사용하여 두 개의 더 작은 P30 디스크에 대한 비용을 지불할 수 있습니다. 예약을 구매할 때 SKU당 총 디스크 수에 대해서만 예약을 구매합니다.

디스크 SKU당 디스크 예약이 이루어집니다. 결과적으로 예약 소비는 제공된 크기 대신 디스크 SCO의 단위를 기반으로 합니다.

예를 들어 프로비저닝된 저장소 용량이 2TiB인 P40 디스크 하나를 예약한다고 가정합니다. 또한 두 개의 P30 디스크만 할당한다고 가정합니다. 이 경우 P40 예약은 P30 소비를 고려하지 않으며 P30 디스크에서 종량제 요금을 지불합니다.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>구매 고려 사항

디스크 예약 구입을 고려할 때 다음 방법을 권장합니다.

- 사용 정보를 분석하여 구매할 예약을 결정합니다. 프로비저닝되거나 사용된 디스크 용량 대신 디스크 SCO의 사용량을 추적해야 합니다.
- VM 예약과 함께 디스크 예약을 검사합니다. VM 사용량과 디스크 사용량을 모두 예약하여 최대한 절약하는 것이 좋습니다. 올바른 VM 예약을 결정한 다음 디스크 예약을 평가할 수 있습니다. 일반적으로 각 워크로드에 대한 표준 구성이 있습니다. 예를 들어 SQL Server 서버에는 두 개의 P40 데이터 디스크와 하나의 P30 운영 체제 디스크가 있을 수 있습니다.
  
  이러한 종류의 패턴은 구매할 수 있는 예약 금액을 결정하는 데 도움이 될 수 있습니다. 이 방법을 사용하면 평가 프로세스를 단순화하고 VM 및 디스크모두에 대해 정렬된 계획을 사용할 수 있습니다. 계획에는 구독 또는 지역과 같은 고려 사항이 포함되어 있습니다.

## <a name="purchase-restrictions"></a>구매 제한

예약 할인은 현재 다음과 같은 경우 사용할 수 없습니다.

- 관리되지 않는 디스크 또는 페이지 Blob입니다.
- 표준 SSD 또는 표준 하드 디스크 드라이브(HDD)
- P1, P2, P3, P4, P6, P10, P15 및 P20 SSD SUS : P30보다 작은 프리미엄 SSD SUS.
- Azure 정부, Azure 독일 또는 Azure 중국 지역의 디스크입니다.

드문 경우지만 Azure는 지역의 용량이 낮기 때문에 새 예약 구매를 디스크 SCO의 하위 집합으로 제한합니다.

## <a name="buy-a-disk-reservation"></a>디스크 예약 구매

Azure [포털을](https://portal.azure.com/)통해 Azure 디스크 저장소 예약을 구입할 수 있습니다. 예약에 대한 선불 또는 월별 결제로 결제할 수 있습니다. 월별 결제로 구매하는 것에 대한 자세한 내용은 [월별 결제가 있는 구매 예약을](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)참조하세요.

예약된 용량을 구입하려면 다음 단계를 따르세요.

1. Azure 포털의 [구매 예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) 창으로 이동합니다.

1. Azure **관리 디스크를** 선택하여 예약을 구입합니다.

    ![예약 구매 창](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 다음 표에 설명된 필수 값을 지정합니다.

   |요소  |설명  |
   |---------|---------|
   |**Scope**   |  예약과 관련된 청구 혜택을 사용할 수 있는 구독 수입니다. 이 값은 예약이 특정 구독에 적용되는 방법도 지정합니다. <br/><br/> **공유를**선택하면 결제 컨텍스트 내의 모든 구독에서 예약 할인이 Azure Storage 용량에 적용됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 경우 공유 범위에는 계정 관리자가 만든 종량제 요금이 포함된 모든 개별 구독이 포함됩니다.  <br/><br/>  **단일 구독을**선택하면 선택한 구독의 Azure Storage 용량에 예약 할인이 적용됩니다. <br/><br/> **단일 리소스 그룹을**선택하면 선택한 구독 및 해당 구독의 선택한 리소스 그룹의 Azure Storage 용량에 예약 할인이 적용됩니다. <br/><br/> 예약 구매 후 예약 범위를 변경할 수 있습니다.  |
   |**구독**  | Azure 저장소 예약에 대해 지불하는 데 사용하는 구독입니다. 선택한 구독의 결제 방법은 비용을 청구하는 데 사용됩니다. 구독은 다음 유형 중 하나여야 합니다.<br/><ul><li> 기업 계약 (제공 번호 MS-AZR-0017P 및 MS-AZR-0148P). Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과분에 대한 요금으로 청구됩니다.</li><br/><li>종량제 요금(제공 번호 MS-AZR-0003P 및 MS-AZR-0023P)의 개별 구독. 종량제 요금이 있는 개별 구독의 경우 구독의 신용 카드 또는 송장 결제 방법으로 요금이 청구됩니다.</li></ul>    |
   | **디스크** | 만들려는 SKU입니다. |
   | **지역** | 예약이 적용되는 지역입니다. |
   | **청구 빈도** | 예약 요금이 청구되는 빈도입니다. 옵션에는 **월별** 및 **선결제가**포함됩니다. |

    ![구매하려는 제품을 선택하기 위한 창.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 예약 값을 지정하면 Azure 포털에 비용이 표시됩니다. 포털에는 종량제 청구에 대한 할인 비율도 표시됩니다. **구매 예약** 창을 계속하려면 **다음을** 선택합니다.

1. 구매 **예약** 창에서 예약의 이름을 지정하고 원하는 총 예약 수량을 선택할 수 있습니다. 예약 수는 디스크 수에 매핑됩니다. 예를 들어 디스크를 100개 예약하려면 **수량** 값 **100을**입력합니다.

1. 총 예약 비용을 검토합니다.

    ![구매 예약 창](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

예약을 구매한 후에는 예약 조건과 일치하는 기존 Disk Storage 리소스에 자동으로 적용됩니다. 디스크 저장소 리소스를 아직 만들지 않은 경우 예약 조건과 일치하는 리소스를 만들 때마다 예약이 적용됩니다. 두 경우 모두 예약 기간은 성공적인 구매 후 즉시 시작됩니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

특정 한도 내에서 예약을 취소, 교환 또는 환불할 수 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)을 참조하세요.

## <a name="expiration-of-a-reservation"></a>예약 만료

예약이 만료되면 해당 예약에서 사용하는 모든 Azure 디스크 저장소 용량은 종량제 요금으로 청구됩니다. 예약은 자동으로 갱신되지 않습니다.

예약 만료 30일 전과 만료일에 다시 이메일 알림을 받게 됩니다. 예약이 제공하는 비용 절감 효과를 계속 활용하려면 만료 날짜까지 갱신하십시오.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만듭니다.](https://go.microsoft.com/fwlink/?linkid=2083458)

## <a name="next-steps"></a>다음 단계

- [Azure 예약이란 무엇입니까?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Disk Storage에 예약 할인이 적용되는 방식 이해](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
