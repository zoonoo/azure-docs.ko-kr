---
title: 비용 절감을 위한 Azure SQL Database vCores 선불 | Microsoft Docs
description: 계산 비용을 절약하기 위해 Azure SQL Database 예약된 용량을 구입하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: ec9bd3ee106571484c513c2d005a374a90c1d17e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359731"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Azure SQL Database 예약 용량을 사용하여 SQL Database 계산 리소스 요금 선결제

종량제 가격에 비해 컴퓨팅 리소스 비용을 선결제하면 Azure SQL Database에서의 비용을 절약할 수 있습니다. Azure SQL Database 예약된 용량을 사용하면 1년이나 3년 동안 SQL Database에 선불 약정을 체결하여 계산 비용에서 상당한 할인을 받을 수 있습니다. SQL Database 예약된 용량을 구입하려면 Azure 지역, 배포 유형, 성능 계층 및 용어를 지정해야 합니다.

특정 SQL Database 인스턴스에 예약을 할당할 필요가 없습니다(단일 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스). 이미 실행 중이거나 새로 배포되는 일치하는 SQL Database 인스턴스는 이러한 이점이 자동으로 제공됩니다. 예약을 구입하면 1년 또는 3년 동안의 컴퓨팅 비용을 선결제하게 됩니다. 예약을 구입하는 즉시, 예약 특성과 일치하는 SQL Database 계산은 더이상 종량제 요금으로 부과되지 않습니다. 예약은 SQL Database 인스턴스와 연결된 소프트웨어, 네트워킹 또는 저장소 요금을 포함하지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료되고 SQL Databases는 종량제 요금으로 청구됩니다. 예약은 자동 갱신되지 않습니다. 가격 책정 정보는 [SQL Database 예약된 용량 제품](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.

[Azure Portal](https://portal.azure.com)에서 Azure SQL Database 예약된 용량을 구매할 수 있습니다. SQL Database 예약된 용량을 구입하려면 다음을 수행합니다.

- 하나 이상의 Enterprise 또는 종량제 구독의 소유자 역할이어야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 또는 해당 설정을 사용하지 않도록 설정한 경우 사용자가 구독에 대한 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트가 SQL Database 예약된 용량을 구매할 수 있습니다.

예약 구매에 대해 엔터프라이즈 고객 및 종량제 고객에게 요금이 청구되는 방법에 대한 자세한 내용은 [엔터프라이즈 등록에서 Azure 예약 사용량 이해](../billing/billing-understand-reserved-instance-usage-ea.md) 및 [종량제 구독의 Azure 예약 사용량 이해](../billing/billing-understand-reserved-instance-usage.md)를 참조하세요.

## <a name="determine-the-right-sql-size-before-purchase"></a>구매 전 적절한 SQL 크기 결정

예약의 크기는 특정 지역 내에서 기존 또는 곧 배포될 단일 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스에서 사용되는 컴퓨팅의 총 양을 기반으로 해야 하며, 동일한 성능 계층 및 하드웨어 세대를 사용해야 합니다.

예를 들어 하나의 범용 Gen5 - 16개 vCore 탄력적 풀 및 두 개의 중요 비즈니스용 Gen5 - 4개 vCore 단일 데이터베이스를 실행 중이라고 가정해 보겠습니다. 또한 범용 Gen5 – 16개 vCore 탄력적 풀 및 하나의 중요 비즈니스용 Gen5 – 32개 vCore 탄력적 풀을 추가로 다음 달 안에 배포하려고 가정합니다. 또한 최소 1년 동안 이러한 리소스가 필요할 것으로 가정해 보겠습니다. 이 경우 단일 데이터베이스/탄력적 풀 범용 - Gen5에 대해 32개(2x16) vCore 1년 예약 및 단일 데이터베이스/탄력적 풀 중요 비즈니스용 - Gen5에 대해 40개(2x4 + 32) vCore 1년 예약을 구매해야 합니다.

## <a name="buy-sql-database-reserved-capacity"></a>SQL Database 예약된 용량 구입

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. **추가**를 선택한 다음, 제품 유형 선택 창에서 **SQL Database**를 선택하여 SQL Database에 대한 새 예약을 구입합니다.
4. 필수 필드를 입력합니다. 선택한 특성과 일치하는 기존 또는 새 단일 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스는 예약 용량 할인을 받을 수 있습니다. 할인을 받는 실제 SQL Database 인스턴스 수는 선택한 범위 및 수량에 따라 달라집니다.

   ![SQL Database 예약된 용량 구매를 제출하기 전의 스크린샷](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | 필드      | 설명|
    |:------------|:--------------|
    |name        |이 예약의 이름입니다.|
    |구독|SQL Database 예약된 용량 예약에 대한 요금을 지불하는 데 사용되는 구독입니다. 구독 시 지불 방법은 SQL Database 예약된 용량 예약에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.|
    |범위       |vCore 예약 범위는 하나 또는 여러 개의 구독(공유 범위)을 포함할 수 있습니다. 다음을 선택하는 경우: <br/><br/>**단일 구독** - vCore 예약 할인이 이 구독의 SQL Database 인스턴스에 적용됩니다. <br/><br/>**공유 구독** - vCore 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행 중인 SQL Database 인스턴스에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.|
    |지역      |SQL Database 예약된 용량 예약에 포함되는 Azure 지역입니다.|
    |배포 유형|예약을 구매할 SQL 리소스 종류입니다.|
    |성능 계층|SQL Database 인스턴스에 대한 서비스 계층입니다.
    |용어        |1년 또는 3년입니다.|
    |수량    |SQL Database 예약된 용량 예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 SQL Database 인스턴스의 수입니다. 예를 들어 미국 동부 지역에서 10개의 SQL Database 인스턴스를 실행 중인 경우 실행 중인 모든 머신에 대한 혜택을 극대화하려면 수량을 10으로 지정할 수 있습니다. |
    |||

5. **비용** 섹션에서 SQL Database 예약된 용량 예약의 비용을 검토합니다.
6. **구매**를 선택합니다.
7. 구매 상태를 보려면 **이 예약 보기**를 선택합니다.

## <a name="cancellations-and-exchanges"></a>취소 및 교환

SQL Database 예약된 용량 예약을 취소해야 하는 경우 12%의 조기 종료 수수료가 있을 수 있습니다. 환불은 구매 가격 또는 예약의 현재 가격 중 최저 가격을 기준으로 합니다. 환불은 연간 $50,000로 제한됩니다. 받는 환불은 12%의 조기 종료 수수료를 뺀 나머지 비례 배분한 잔액입니다. 취소를 요청하려면 Azure Portal의 예약으로 이동하고 **환불**을 선택하여 지원 요청을 만듭니다.

SQL Database 예약된 용량 예약을 다른 지역, 배포 유형, 성능 계층 또는 기간으로 변경해야 하는 경우 같거나 더 큰 값인 다른 예약에 대해 교환할 수 있습니다. 새 예약에 대한 기간 시작일은 교환된 예약에서 수행되지 않습니다. 1 또는 3년 기간은 새 예약을 만들 때 시작합니다. 교환을 요청하려면 Azure Portal의 예약으로 이동하고, **교환**을 선택하여 지원 요청을 만듭니다.

Exchange 또는 환불 예약 하는 방법에 대 한 자세한 내용은 참조 하세요. [예약 교환 및 환불](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)합니다.

## <a name="vcore-size-flexibility"></a>vCore 크기 유연성

vCore 크기 유연성을 통해 예약된 용량 이점을 잃지 않고 성능 계층 및 지역 내에서 크기를 확장 또는 축소할 수 있습니다. SQL Database 예약된 용량은 예약된 용량 이점을 잃지 않고 일반 작업(동일한 지역 및 성능 계층 내에서)의 일부로 풀과 단일 데이터베이스 간에 핫 데이터베이스를 일시적으로 이동하는 유연성을 제공합니다. 예약에서 적용되지 않은 버퍼를 유지하여 예산을 초과하지 않고 성능 급증을 효과적으로 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

vCore 예약 할인은 SQL Database 예약된 용량 예약 범위 및 특성과 일치하는 SQL Database 인스턴스의 수에 자동으로 적용됩니다. [Azure Portal](https://portal.azure.com), PowerShell, CLI 또는 API를 통해 SQL Database 예약된 용량 예약의 범위를 업데이트할 수 있습니다.

SQL Database 예약된 용량 예약을 관리하는 방법을 알아보려면 [SQL Database 예약된 용량 관리](../billing/billing-manage-reserved-vm-instance.md)를 참조하세요.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../billing/billing-save-compute-costs-reservations.md)
- [Azure Reservations 관리](../billing/billing-manage-reserved-vm-instance.md)
- [Azure 예약 할인 이해](../billing/billing-understand-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](../billing/billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../billing/billing-understand-reserved-instance-usage-ea.md)
- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
