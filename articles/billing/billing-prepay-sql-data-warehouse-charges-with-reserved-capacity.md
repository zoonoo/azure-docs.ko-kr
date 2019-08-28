---
title: Azure reserved capacity를 사용 하는 SQL Data Warehouse 요금에 대 한 선불
description: 요금을 절감 하기 위해 예약 된 용량으로 SQL Data Warehouse 요금을 선불 수 있는 방법에 대해 알아봅니다.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: 6ee7a661434bb756c6cf196937229db19d06b373
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779977"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>예약 된 용량을 사용 하는 SQL Data Warehouse 요금에 대 한 선불

1 년 또는 3 년 동안 cDWU 사용에 대 한 총액를 통해 Azure SQL Data Warehouse를 사용 하 여 비용을 절감할 수 있습니다. SQL Data Warehouse reserved capacity를 구매 하려면 Azure 지역 및 용어를 선택 해야 합니다. 그런 다음, 카트에 SQL Data Warehouse SKU를 추가 하 고 구매할 cDWU 단위의 수량을 선택 합니다.

예약을 구매 하는 경우 예약 특성과 일치 하는 SQL Data Warehouse 사용량은 더 이상 종 량 제 요금으로 청구 되지 않습니다.

예약은 SQL Data Warehouse 사용량과 관련 된 저장소 또는 네트워킹 요금을 포함 하지 않습니다.

예약 된 용량이 만료 되 면 SQL Data Warehouse 인스턴스는 계속 실행 되지만 종 량 제 요금으로 청구 됩니다. 예약은 자동으로 갱신 되지 않습니다.

가격 책정 정보는 [SQL Data Warehouse 예약 된 용량 제공](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)을 참조 하세요.

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)에서 Azure SQL Data Warehouse 예약 된 용량을 구매할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

- 하나 이상의 enterprise 또는 종 량 제 구독에 대 한 소유자 역할이 있어야 합니다.
- 엔터프라이즈 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 된 인스턴스 추가** 옵션을 사용 하도록 설정 해야 합니다. 설정을 사용 하지 않도록 설정한 경우 EA 관리자 여야 합니다.
- CSP (클라우드 솔루션 공급자) 프로그램의 경우 관리 에이전트 또는 판매 에이전트만 SQL Data Warehouse 예약 된 용량을 구매할 수 있습니다.

엔터프라이즈 고객과 종 량 제 고객이 예약 구매에 대해 부과 하는 방법에 대 한 자세한 내용은 [기업 등록에 대 한 azure 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md) 및 다음 [에 대 한 azure 예약 사용 이해를 참조 하세요. 종 량 제 구독](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>구매 하기 전에 올바른 크기 선택

SQL Data Warehouse 예약 크기는 사용 하는 총 cDWU (계산 데이터 웨어하우스 단위)를 기반으로 해야 합니다. 구매는 100 cDWU 증분로 생성 됩니다.

예를 들어 SQL Data Warehouse 총 사용량을 DW3000c 이라고 가정 합니다. 모든 it에 대 한 예약 된 용량을 구매 하려고 합니다. 따라서 cDWU 예약 용량의 30 단위를 구매 해야 합니다.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>SQL Data Warehouse 예약 된 용량 구입

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. 구독 선택 구독 목록을 사용 하 여 예약 된 용량에 대 한 비용을 지불 하는 데 사용 되는 구독을 선택 합니다. 구독의 지불 방법에는 예약 된 용량에 대 한 선행 비용이 청구 됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다.
   - Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다.
   - 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.
4. 범위를 선택 합니다. 범위 목록을 사용 하 여 구독 범위를 선택 합니다.
   - **단일 리소스 그룹 범위** -선택한 리소스 그룹의 일치 하는 리소스에만 예약 할인을 적용 합니다.
   - **단일 구독 범위** -선택한 구독의 일치 하는 리소스에 예약 할인을 적용 합니다.
   - **공유 범위** -청구 컨텍스트에 있는 적격 구독의 일치 하는 리소스에 예약 할인을 적용 합니다. 기업계약 고객의 경우 요금 청구 컨텍스트가 등록입니다. 종 량 제 요금이 있는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.
   - 기업 고객의 경우 청구 컨텍스트는 EA 등록입니다.
   - 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.
5. 영역을 선택 하 여 예약 된 용량에서 적용 되는 Azure 지역을 선택 합니다.
6. 수량을 선택 합니다. 구입 하려는 100 cDWU (데이터 웨어하우스 단위)의 수량을 입력 합니다.    
   예를 들어 수량 30은 1 시간 마다 3000 cDWU를 예약 된 용량으로 제공 합니다.
7. **비용** 섹션에서 SQL Data Warehouse 예약 된 용량 예약 비용을 검토 합니다.
8. **구매**를 선택합니다.
9. **이 예약 보기** 를 선택 하 여 구매 상태를 확인 합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>취소, 교환 또는 환불 예약

특정 제한 사항을 사용 하 여 예약을 취소, 교환 또는 환불 할 수 있습니다. 자세한 내용은 [셀프 서비스 교환 및 Azure Reservations에 대 한 환불](billing-azure-reservations-self-service-exchange-and-refund.md)를 참조 하세요.

예약 할인은 SQL Data Warehouse 예약 된 용량 범위 및 영역과 일치 하는 SQL Data Warehouse 인스턴스 수에 자동으로 적용 됩니다. [Azure Portal](https://portal.azure.com/), POWERSHELL, CLI 또는 API를 통해 SQL Data Warehouse 예약 된 용량의 범위를 업데이트할 수 있습니다.

## <a name="need-help-contact-us"></a>도움이 필요하십니까? 문의

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/).

## <a name="next-steps"></a>다음 단계

- Azure SQL Data Warehouse에 예약 할인이 적용 되는 방법에 대 한 자세한 내용은 [Azure SQL Data Warehouse에 예약 할인이 적용 되는 방법](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)을 참조 하세요.

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](billing-save-compute-costs-reservations.md)
  - [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
  - [Azure 예약 할인 이해](billing-understand-reservation-charges.md)
  - [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
