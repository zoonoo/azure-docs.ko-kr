---
title: Azure 리소스에 대한 예약 보기 | Microsoft Docs
description: Azure Portal에서 Azure 예약을 보는 방법을 알아봅니다.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 872837c774368820527b12778b1a7dd4ddc5c7af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60369166"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Azure portal에서 Azure 예약 확인

구독 유형 및 권한에 따라 여러 가지 방법으로 Azure에 대 한 예약을 볼 수 있습니다.

## <a name="view-purchased-reservations"></a>구매한 예약 확인

기본적으로 예약을 구매하면 구매한 사용자와 계정 관리자가 예약을 볼 수 있습니다. 와 계정 관리자가 자동으로 예약 주문 및 예약에 소유자 역할을 가져옵니다. 다른 사람이 예약을 볼 수 있도록 추가 해야 하는 **소유자** 하거나 **판독기** 예약 순서 또는 예약 합니다.

자세한 내용은 [예약을 관리할 수 있는 사용자 추가 또는 변경](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)을 참조하세요.

소유자 또는 읽기 권한자로 예약을 보려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **예약**에서 검색합니다.
    ![Azure portal 검색을 보여 주는 스크린샷](./media/billing-view-reservation/portal-reservation-search.png)  
3. 목록은 소유자 또는 읽기 권한자 역할이 있는 모든 예약을 보여 줍니다. 각 예약 마지막 알려진된 사용률 백분율을 보여 줍니다.  
    ![예약의 목록을 보여 주는 예제](./media/billing-view-reservation/view-reservations.png)
4. 예약을 선택 하 고 지난 5 일 동안의 사용률 추세를 참조 하세요.  
    ![예제 표시 예약 사용률 추세](./media/billing-view-reservation/reservation-utilization.png)
5. 가져올 수도 있습니다는 [예약 사용률](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) Reserved Instance 사용량 API를 사용 하 여와 합니다 [Microsoft Azure 소비 Insights Power BI 콘텐츠 팩](/power-bi/service-connect-to-azure-consumption-insights)합니다.

예약 범위를 변경하거나, 예약을 분할하거나, 예약을 관리할 수 있는 사용자를 변경해야 하는 경우에는 [Azure 예약 관리](billing-manage-reserved-vm-instance.md)를 참조하세요.

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>기업 등록계약의 예약 트랜잭션 보기

 파트너가 기업 등록계약을 진행하게 하려면 EA 포털에서 **보고서**로 이동하여 예약을 봅니다. 기타 기업 등록계약의 경우 EA 포털 및 Azure Portal에서 예약을 볼 수 있습니다. 예약 트랜잭션을 보려면 EA 관리자여야 합니다.

Azure Portal에서 예약 트랜잭션을 보려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**에서 검색합니다.

    ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-view-reservation/portal-cm-billing-search.png)

1. **예약 트랜잭션**을 선택합니다.
1. 결과를 필터링하려면 **시간 범위**, **유형** 또는 **설명**을 선택합니다.
1. **적용**을 선택합니다.

    ![예약 트랜잭션 결과를 보여 주는 스크린샷](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

API를 사용하여 데이터를 가져오려면 [엔터프라이즈 고객의 예약 인스턴스 트랜잭션 청구 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure에 대 한 예약 이란?](billing-save-compute-costs-reservations.md)
- [Azure에 대 한 예약 관리](billing-manage-reserved-vm-instance.md)

서비스 계획을 구입 합니다.

- [Cosmos DB 예약 용량 선불](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)

소프트웨어 플랜을 구입 합니다.

- [Azure 예약에서 Red Hat 소프트웨어 계획에 대 한 요금을 선불합니다](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Reservations에서 SUSE 소프트웨어 요금제에 대한 선불](../virtual-machines/linux/prepay-suse-software-charges.md)

사용량을 파악 합니다.

- [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록의 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
- [CSP 구독의 예약 사용량 이해](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.
