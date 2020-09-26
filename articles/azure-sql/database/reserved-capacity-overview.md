---
title: 예약 된 용량으로 계산 비용 절감
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database 및 SQL Managed Instance 예약 된 용량을 구입 하 여 계산 비용을 절감 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 08/29/2019
ms.openlocfilehash: 7a7373f5fcd36298d2feeff6a2a5b67c9e10e40b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321597"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>예약 된 용량을 사용 하 여 리소스에 대 한 비용 절감-Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

종 량 제 가격과 비교 하 여 계산 리소스에 대 한 예약을 커밋하여 Azure SQL Database 및 SQL Managed Instance를 사용 하 여 비용을 절감 하세요. 예약 된 용량을 사용 하면 1 년 또는 3 년 동안 SQL Database 및/또는 SQL Managed Instance를 사용 하 여 계산 비용에 대 한 상당한 할인을 얻을 수 있습니다. 예약 된 용량을 구매 하려면 Azure 지역, 배포 유형, 성능 계층 및 용어를 지정 해야 합니다.

특정 데이터베이스 또는 관리 되는 인스턴스에 예약을 할당할 필요가 없습니다. 이미 실행 중이거나 새로 배포 된 기존 배포와 일치 하면 자동으로 혜택을 받습니다. 예약을 구매 하 여 1 년 또는 3 년 동안 계산 비용에 대 한 사용을 커밋합니다. 예약을 구매 하는 즉시 예약 특성과 일치 하는 계산 요금은 더 이상 종 량 제 요금으로 청구 되지 않습니다. 예약에는 서비스와 관련 된 소프트웨어, 네트워킹 또는 저장소 요금이 포함 되지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료 되며 데이터베이스 또는 관리 되는 인스턴스는 종 량 제 요금으로 청구 됩니다. 예약은 자동으로 갱신 되지 않습니다. 가격 책정 정보는 예약 된 [용량 제공](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조 하세요.

[Azure Portal](https://portal.azure.com)에서 예약 된 용량을 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

- 종 량 제 요금은 하나 이상의 Enterprise 또는 개별 구독에 대 한 소유자 역할에 속해야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다. 예약 된 용량입니다.

엔터프라이즈 고객과 종 량 제 고객이 예약 구매에 대해 부과 하는 방법에 대 한 자세한 내용은 [기업 등록에 대 한 azure 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 및 [종 량 제 구독에 대 한 Azure 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조 하세요.

## <a name="determine-correct-size-before-purchase"></a>구매 하기 전에 올바른 크기 확인

예약 크기는 특정 지역 내에서 기존 또는 예정 된 데이터베이스 또는 관리 되는 인스턴스가 사용 하는 총 계산 용량을 기반으로 하 고 동일한 성능 계층 및 하드웨어 생성을 사용 해야 합니다.

예를 들어, 범용 vCore 탄력적 풀 및 두 개의 업무상 중요 한 Gen5 – 4 vCore 단일 데이터베이스를 실행 하는 경우를 예로 들어 보겠습니다. 뿐만 아니라, 다음 달에 추가 범용 Gen5 – 16 vCore 탄력적 풀 및 하나의 업무상 중요 한 Gen5 – 32 vCore 탄력적 풀을 배포할 예정입니다. 또한 최소 1년 동안 이러한 리소스가 필요할 것으로 가정해 보겠습니다. 이 경우 단일 데이터베이스/탄력적 풀 범용 32 40 Gen5에 대해 32 (2x16) Vcores 1 년 예약을 구매 해야 합니다. 단일 데이터베이스/탄력적 풀에 대 한 Vcores 1 년 예약-Gen5.

## <a name="buy-reserved-capacity"></a>예약된 용량 구입

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. **추가** 를 선택한 다음 **구매 예약** 창에서 **SQL Database** 을 선택 하 여 SQL Database에 대 한 새 예약을 구매 합니다.
4. 필수 필드를 입력합니다. 사용자가 선택한 특성과 일치 하는 SQL Database 및 SQL Managed Instance의 기존 데이터베이스는 예약 된 용량 할인을 받습니다. 할인을 받는 데이터베이스 또는 관리 되는 인스턴스의 실제 수는 선택한 범위와 수량에 따라 달라 집니다.

    ![예약 된 용량 구매를 제출 하기 전의 스크린샷](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    다음 표에서는 필수 필드에 대해 설명 합니다.
    
    | 필드      | Description|
    |------------|--------------|
    |구독|용량 예약에 대 한 비용을 지불 하는 데 사용 되는 구독입니다. 구독 시 지불 방법은 예약에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업 계약 (제품 번호 MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p) 이거나 종 량 제 가격을 포함 하는 개별 계약 (제품 번호 MS-MS-AZR-0017P-0003P 또는-0017P) 이어야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종 량 제 가격의 개별 구독에 대해 요금 청구는 구독에 대 한 신용 카드 또는 청구서 지불 방법으로 청구 됩니다.|
    |Scope       |VCore 예약의 범위는 하나의 구독 또는 여러 구독 (공유 범위)을 포함할 수 있습니다. 선택 하는 경우 <br/><br/>**공유**, vcore 예약 할인은 청구 컨텍스트 내의 모든 구독에서 실행 중인 데이터베이스 또는 관리 되는 인스턴스에 적용 됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.<br/><br/>**단일 구독**-이 구독의 데이터베이스 또는 관리 되는 인스턴스에 vcore 예약 할인이 적용 됩니다. <br/><br/>**단일 리소스 그룹**, 예약 할인은 선택한 구독의 데이터베이스 또는 관리 되는 인스턴스와 해당 구독 내에서 선택한 리소스 그룹의 인스턴스에 적용 됩니다.|
    |지역      |용량 예약이 적용 되는 Azure 지역입니다.|
    |배포 유형|예약을 구매할 SQL 리소스 종류입니다.|
    |성능 계층|데이터베이스 또는 관리 되는 인스턴스의 서비스 계층입니다. |
    |용어        |1년 또는 3년입니다.|
    |수량    |용량 예약 내에서 구매한 계산 리소스의 양입니다. 수량은 선택한 Azure 지역 및 성능 계층의 많은 vCores로, 예약 되 고 청구 할인이 적용 됩니다. 예를 들어 미국 동부 지역에서 Gen5 16 vCores의 총 계산 용량을 사용 하 여 여러 데이터베이스를 실행 하는 경우 모든 데이터베이스에 대 한 혜택을 최대화 하기 위해 수량을 16으로 지정 합니다. |

1. **비용** 섹션에서 용량 예약의 비용을 검토 합니다.
1. **구매**를 선택합니다.
1. 구매 상태를 보려면 **이 예약 보기**를 선택합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="vcore-size-flexibility"></a>vCore 크기 유연성

vCore 크기 유연성을 통해 예약된 용량 이점을 잃지 않고 성능 계층 및 지역 내에서 크기를 확장 또는 축소할 수 있습니다. 또한 예약 된 용량을 사용 하면 예약 된 용량 혜택을 잃지 않고 일반 작업의 일부로 탄력적 풀 (동일한 지역 및 성능 계층 내)에서 핫 데이터베이스를 일시적으로 이동할 수 있습니다. 예약 된 버퍼를 예약 된 상태로 유지 하면 예산을 초과 하지 않고 성능 급증을 효과적으로 관리할 수 있습니다.

## <a name="limitation"></a>제한 사항

SQL Database에서 DTU 기반 (basic, standard 또는 premium) 데이터베이스를 예약할 수 없습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

VCore 예약 할인은 용량 예약 범위 및 특성과 일치 하는 데이터베이스 또는 관리 되는 인스턴스 수에 자동으로 적용 됩니다. [Azure Portal](https://portal.azure.com), PowerShell, AZURE CLI 또는 API를 통해 용량 예약의 범위를 업데이트할 수 있습니다.

용량 예약을 관리 하는 방법을 알아보려면 예약 된 [용량 관리](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)를 참조 하세요.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Reservations 관리](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Azure 예약 할인 이해](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)
