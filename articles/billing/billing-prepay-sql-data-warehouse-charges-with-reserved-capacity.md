---
title: Azure 예약 된 용량을 사용 하 여 SQL Data Warehouse 요금에 선불 | Microsoft Docs
description: 어떻게 수 선불 요금 SQL Data Warehouse에 대 한 비용을 절약 하려면 예약 된 용량을 사용 하 여에 대해 알아봅니다.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371192"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>예약 된 용량을 사용 하 여 SQL Data Warehouse 요금에 선불 결제

1 년 또는 3 년 기간 prepaying cDWU 사용량에 대 한 하 여 Azure SQL Data Warehouse를 사용 하 여 비용을 저장할 수 있습니다. Azure를 선택 해야 예약 된 SQL Data Warehouse 용량을 구매 하려면 지역 및 용어입니다. 그런 다음 SQL 데이터 웨어하우스 SKU 카트에 추가 하 고 구매 하려는 cDWU 단위 수량을 선택 합니다.

예약을 구매 하는 경우 예약 특성과 일치 하는 사용량에는 더 이상 부과 종 량 SQL Data Warehouse는 요금을 이동 합니다.

저장소 또는 SQL Data Warehouse 사용과 관련 된 네트워킹 요금 예약을 다루지 않습니다.

예약된 된 용량에 만료 되 면 SQL Data Warehouse 인스턴스 계속 실행 되지만 종 량 이동 요금으로 요금이 청구 됩니다. 예약 자동으로 갱신 하지 않습니다.

가격 책정 정보에 대 한 참조를 [SQL Data Warehouse 용량 제공 예약](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)합니다.

Azure SQL Data Warehouse 예약 용량을 구매할 수 있습니다 합니다 [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)합니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

- 하나 이상의 enterprise 또는 종 량 제 구독에 대 한 소유자 역할이 있어야 합니다.
- Enterprise 구독에 대 한 합니다 **Reserved Instances 추가** 옵션에서 사용할 수 있어야 합니다 합니다 [EA 포털](https://ea.azure.com/). 설정 하지 않으면 EA 관리자 여야 합니다.
- 클라우드 솔루션 공급자 (CSP) 프로그램에 대 한 관리 에이전트 또는 영업 담당자만 예약 된 SQL Data Warehouse 용량을 구입할 수 있습니다.

엔터프라이즈 고객 및 종 량 제 고객 예약 구매에 대 한 요금이 청구 됩니다 하는 방법에 대 한 자세한 내용은 참조 하세요. [Enterprise 등록에 대 한 Azure 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md) 고 [Azure 이해 종 량 제 구독에 대 한 예약 사용량](billing-understand-reserved-instance-usage.md)합니다.

## <a name="choose-the-right-size-before-purchase"></a>구매 하기 전에 적절 한 크기를 선택 합니다.

예약 크기 합계를 기반으로 해야 하는 SQL Data Warehouse를 사용 하는 데이터 웨어하우스 단위 (cDWU)를 계산 합니다. 구매 100 cDWU 단위로 수행 됩니다.

예를 들어, SQL Data Warehouse의 총 소비는 DW3000c 가정 합니다. 모든 것에 대 한 예약 된 용량을 구입 해야 합니다. 따라서 30 cDWU 예약 용량 단위를 구입 해야 합니다.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>예약 된 SQL Data Warehouse 용량 구입

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. 구독을 선택합니다. 구독 목록을 사용 하 여 예약된 된 용량에 대 한 요금을 지불 하는 데 사용 되는 구독을 선택 합니다. 구독의 결제 예약 된 용량에 대해 선불로 비용이 청구 됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다.
  - Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다.
  - 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.
4. 범위를 선택 합니다. 범위 목록을 사용 하 여 구독 범위를 선택 합니다.
  - 사용 하 여 합니다 **단일** 옵션, 예약 할인은 선택한 구독에 배포 하는 SQL Data Warehouse에 적용 합니다.
  - 사용 하 여 합니다 **공유** 옵션, 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행 중인 인스턴스에 적용 됩니다.
    - 기업 고객을 위한 청구 컨텍스트는 EA 등록 합니다.
    - 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.
5. 예약된 된 용량 보증 하는 Azure 지역을 선택 하는 영역을 선택 합니다.
6. 수량을 선택 합니다. 구입 하려면 100 데이터 웨어하우스 단위 (cDWU)의 수량을 입력 합니다.    
  예를 들어 30 수량을 하면 예약 된 용량 3,000 cDWU 1 시간 마다.
7. SQL Data Warehouse 예약 용량 예약 비용을 검토 합니다 **비용** 섹션입니다.
8. **구매**를 선택합니다.
9. 선택 **이 예약 보기** 구매 상태를 확인 합니다.

## <a name="cancellations-and-exchanges"></a>취소 및 교환

필요한 경우 SQL Data Warehouse를 취소 하려면 예약 된 용량, 12%의 조기 종료 수수료 있을 수 있습니다. 환불은 구매 가격 또는 예약의 현재 가격 중 최저 가격을 기준으로 합니다. 환불 $50,000.00 인스턴스당 1 년으로 제한 됩니다. 받게 환불은 12%의 조기 종료 수수료를 뺀 나머지 비례 배분된 잔액입니다. 취소를 요청하려면 Azure Portal의 예약으로 이동하고 **환불**을 선택하여 지원 요청을 만듭니다.

다른 지역 또는 용어를 예약 하는 SQL Data Warehouse 용량을 변경 해야 할 경우 같거나 더 큰 값은 다른 예약에 대해 교환할 수 있습니다. 새 예약에 대한 기간 시작일은 교환된 예약에서 수행되지 않습니다. 하나 또는 3 년 기간 새 예약을 만들 때 시작 합니다. Exchange를 요청 하려면 Azure portal에서 예약을 엽니다 및 선택 **Exchange** 지원 요청을 만듭니다.

Exchange 또는 환불 예약 하는 방법에 대 한 자세한 내용은 참조 하세요. [예약 교환 및 환불](billing-azure-reservations-self-service-exchange-and-refund.md)합니다.

예약 할인은 예약 된 SQL Data Warehouse 용량 범위 및 지역 일치 하는 SQL Data Warehouse 인스턴스 수를 자동으로 적용 됩니다. 사용 하 여 예약 된 SQL Data Warehouse 용량 범위를 업데이트할 수는 [Azure portal](https://portal.azure.com/), PowerShell, CLI 또는 API를 통해.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/).

## <a name="next-steps"></a>다음 단계

- Azure SQL Data Warehouse에 예약 할인을 적용 하는 방법에 대 한 자세한 내용은 참조 하세요 [Azure SQL Data Warehouse에 예약 할인을 적용 하는 방법을](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)합니다.

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](billing-save-compute-costs-reservations.md)
  - [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
  - [Azure 예약 할인 이해](billing-understand-reservation-charges.md)
  - [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
