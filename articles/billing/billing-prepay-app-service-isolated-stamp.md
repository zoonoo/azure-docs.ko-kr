---
title: 예약 된 용량에 대 한 Azure App Service 격리 된 스탬프 요금 선불
description: 비용 절감을 위해 예약 된 용량으로 Azure App Service 격리 된 스탬프 요금을 선불 수 있는 방법에 대해 알아봅니다.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: f122ec2474c09cdd6c9ada4ddc59b1adb44f619f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780025"
---
# <a name="prepay-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>예약 된 용량에 대 한 Azure App Service 격리 된 스탬프 요금 선불

3 년 동안 스탬프 사용에 대 한 총액으로 Azure App Service 격리 된 스탬프 요금에 대 한 비용을 절감할 수 있습니다. 격리 된 스탬프 요금 예약 용량을 구매 하려면 스탬프를 배포할 Azure 지역 및 구입할 스탬프 수를 선택 해야 합니다.

예약을 구매 하는 경우 예약 특성과 일치 하는 격리 된 스탬프 요금 사용은 더 이상 종 량 제 요금으로 청구 되지 않습니다. 예약은 예약 된 용량 범위 및 지역과 일치 하는 격리 된 스탬프 수에 자동으로 적용 됩니다. 격리 된 스탬프에는 예약을 할당할 필요가 없습니다. 예약은 작업자에 게 적용 되지 않으므로 스탬프와 연결 된 다른 모든 리소스는 별도로 청구 됩니다.

예약 된 용량이 만료 되 면 격리 된 스탬프는 계속 실행 되지만 종 량 제 요금으로 청구 됩니다. 예약은 자동으로 갱신 되지 않습니다.

## <a name="determine-the-right-reservation-to-purchase"></a>구입할 올바른 예약 결정

예약을 구매 하 여 향후 3 년 동안 모든 시간에 예약 된 수량을 사용 하도록 사전 요금을 지불 하 게 됩니다. 사용 현황 데이터를 확인 하 여 지속적으로 사용 하는 App Service 격리 스탬프 수를 확인 하 고 나중에 사용할 수 있습니다.

또한 격리 된 스탬프에서 Linux 또는 Windows 측정기를 내보내는 방법을 이해 해야 합니다.

- 기본적으로 비어 있는 격리 된 스탬프는 Windows 스탬프 측정기를 내보냅니다. 예를 들어, 작업자를 배포 하지 않은 경우 Windows 작업자를 스탬프에 배포 하는 경우에도 계속 해 서이 측정기를 내보냅니다.
- Linux 작업자를 배포 하는 경우 측정기는 Linux 스탬프 측정기로 변경 됩니다.
- Linux 및 Windows 작업자를 모두 배포 하는 경우 스탬프는 Windows 측정기를 내보냅니다.

따라서 스탬프 측정기는 스탬프 수명 동안 Windows와 Linux 사이에서 변경 될 수 있습니다.

스탬프에 Windows 작업자가 하나 이상 있는 경우 Windows 스탬프 예약을 구입 합니다. Linux stamp 예약을 구입 해야 하는 유일한 경우는 스탬프에 Linux 작업자 _만_ 있는 경우입니다.

## <a name="buy-isolated-stamp-reserved-capacity"></a>격리 된 스탬프 예약 된 용량 구입

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)에서 격리 된 스탬프 예약 용량을 구매할 수 있습니다. 예약 된 용량을 구입 하려면 하나 이상의 enterprise 구독 또는 종 량 제 요금이 있는 개별 구독에 대 한 소유자 역할이 있어야 합니다.

- 엔터프라이즈 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 된 인스턴스 추가** 옵션을 사용 하도록 설정 해야 합니다. 또는 설정을 사용 하지 않도록 설정한 경우 EA 관리자 여야 합니다.
- CSP (클라우드 솔루션 공급자) 프로그램의 경우 관리 에이전트 또는 판매 에이전트만 SQL Data Warehouse 예약 된 용량을 구매할 수 있습니다.

**구입 하려면:**

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)로 이동 합니다.
1. 구독 선택 **구독** 목록을 사용 하 여 예약 된 용량에 대 한 비용을 지불 하는 데 사용 되는 구독을 선택 합니다. 구독의 지불 방법에는 예약 된 용량에 대 한 선행 비용이 청구 됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0017P-0003P 또는 MS-AZR-0017P-0023P) 또는 CSP 구독.
    - Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다.
    - 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.
1. **범위** 를 선택 하 여 구독 범위를 선택 합니다.
    - **단일 리소스 그룹 범위** -선택한 리소스 그룹의 일치 하는 리소스에만 예약 할인을 적용 합니다.
    - **단일 구독 범위** -선택한 구독의 일치 하는 리소스에 예약 할인을 적용 합니다.
    - **공유 범위** -청구 컨텍스트에 있는 적격 구독의 일치 하는 리소스에 예약 할인을 적용 합니다. 기업계약 고객의 경우 요금 청구 컨텍스트가 등록입니다. 종 량 제 요금이 있는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.
1. **지역을** 선택 하 여 예약 된 용량에 포함 되는 Azure 지역을 선택 하 고 카트에 예약을 추가 합니다.
1. 격리 된 계획 유형을 선택 하 고 **선택**을 클릭 합니다.  
    ![예 들어](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. 예약할 App Service 격리 스탬프 수량을 입력 합니다. 예를 들어 수량 3은 지역에 예약 된 3 개의 타임 스탬프를 제공 합니다. **다음: 검토 + 구매**.
1. 검토 하 고 **지금 구입**을 클릭 합니다.

구매 후에는 [예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) 으로 이동 하 여 구매 상태를 확인 하 고 언제 든 지 모니터링할 수 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>취소, 교환 또는 환불 예약

특정 제한 사항을 사용 하 여 예약을 취소, 교환 또는 환불 할 수 있습니다. 자세한 내용은 [셀프 서비스 교환 및 Azure Reservations에 대 한 환불](billing-azure-reservations-self-service-exchange-and-refund.md)를 참조 하세요.

## <a name="discount-application-shown-in-usage-data"></a>사용 현황 데이터에 표시 된 할인 응용 프로그램

사용 현황 데이터의 유효 가격은 예약 할인을 가져오는 사용량에 0입니다. 사용 현황 데이터는 각 예약의 각 스탬프 인스턴스에 대 한 예약 할인을 보여 줍니다.

사용 현황 데이터에 예약 할인이 표시 되는 방법에 대 한 자세한 내용은 기업계약 (EA) 고객 인 경우 [예약 비용 및 사용 현황 기업계약 가져오기](billing-understand-reserved-instance-usage-ea.md) 를 참조 하세요. 그렇지 않으면 [종 량 제 요금으로 개별 구독에 대 한 Azure 예약 사용량 이해](billing-understand-reserved-instance-usage.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](billing-save-compute-costs-reservations.md)
  - [Azure App Service 격리 된 스탬프 예약 할인이 적용 되는 방법 이해](billing-reservation-discount-app-service-isolated-stamp.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
