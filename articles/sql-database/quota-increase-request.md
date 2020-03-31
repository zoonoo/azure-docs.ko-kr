---
title: 할당량 증가 요청
description: 이 페이지에서는 Azure SQL Database 단일 데이터베이스, 서버 및 관리되는 인스턴스에 대한 할당량을 늘리기 위한 지원 요청을 만드는 방법에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586158"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 요청 할당량 증가

이 문서에서는 단일 데이터베이스, 서버 및 관리되는 인스턴스에 대해 Azure SQL Database에 대한 할당량 증가를 요청하는 방법을 설명합니다. 또한 지역에 대한 구독 액세스를 활성화하는 방법에 대해서도 설명합니다.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>새 지원 요청 만들기

다음 단계를 사용하여 SQL Database에 대한 Azure 포털에서 새 지원 요청을 만듭니다.

1. Azure [포털](https://portal.azure.com) 메뉴에서 **도움말 + 지원을**선택합니다.

   ![도움말 + 지원 링크](./media/quota-increase-request/help-plus-support.png)

1. **도움말 + 지원에서** **새 지원 요청을**선택합니다.

    ![새 지원 요청 만들기](./media/quota-increase-request/new-support-request.png)

1. **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/quota-increase-request/select-quota-issue-type.png)

1. **구독의**경우 할당량을 늘리려는 구독을 선택합니다.

   ![증가된 할당량에 대한 구독 선택](./media/quota-increase-request/select-subscription-support-request.png)

1. **할당량 유형의**경우 다음 할당량 유형 중 하나를 선택합니다.

   - 단일 데이터베이스 및 탄력적 풀 할당량에 대한 **SQL** 데이터베이스입니다.
   - 관리되는 **인스턴스에** 대한 SQL 데이터베이스 관리 인스턴스입니다.

   그런 다음 ** >>을 선택합니다. **

   ![할당량 유형 선택](./media/quota-increase-request/select-quota-type.png)

1. 세부 **정보** 창에서 **세부 정보 제공을** 선택하여 추가 정보를 입력합니다.

   !["세부 정보 제공" 링크](./media/quota-increase-request/provide-details-link.png)

**세부정보 제공을** 클릭하면 추가 정보를 추가할 수 있는 **할당량 세부 정보** 창이 표시됩니다. 다음 섹션에서는 **SQL 데이터베이스** 및 **SQL 데이터베이스 관리 인스턴스** 할당량 형식에 대한 다양한 옵션을 설명합니다.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>SQL 데이터베이스 할당량 유형

다음 섹션에서는 **SQL Database** 할당량 유형에 대한 세 가지 할당량 증가 옵션에 대해 설명합니다.

- 서버당 데이터베이스 트랜잭션 단위(DT).
- 구독당 서버
- 리전에 대한 구독 액세스 활성화

### <a name="database-transaction-units-dtus-per-server"></a>서버당 데이터베이스 트랜잭션 단위(DT).

다음 단계를 사용하여 서버당 DTO 증가를 요청합니다.

1. 서버 할당량 **유형당 데이터베이스 트랜잭션 단위(DT)를** 선택합니다.

1. **리소스** 목록에서 대상리소스를 선택합니다.

1. 새 **할당량** 필드에 요청하는 새 DTU 제한을 입력합니다.

   ![DTU 할당량 세부 정보](./media/quota-increase-request/quota-details-dtus.png)

자세한 내용은 [DTU 구매 모델을 사용하는 DTU 구매 모델](sql-database-dtu-resource-limits-single-databases.md) 및 DTU 구매 모델을 사용하는 [탄력적 풀에 대한 리소스 제한을 사용하는](sql-database-dtu-resource-limits-elastic-pools.md)단일 데이터베이스에 대한 리소스 제한을 참조하십시오.

### <a name="servers-per-subscription"></a>구독당 서버

다음 단계를 사용하여 구독당 서버 수 증가를 요청합니다.

1. 구독 할당량 **유형당 서버를** 선택합니다.

1. **위치** 목록에서 사용할 Azure 지역을 선택합니다. 할당량은 각 지역의 구독당 할당량입니다.

1. 새 **할당량** 필드에 해당 지역의 최대 서버 수에 대한 요청을 입력합니다.

   ![서버 할당량 세부 정보](./media/quota-increase-request/quota-details-servers.png)

자세한 내용은 [SQL Database 리소스 제한 및 리소스 거버넌스를](sql-database-resource-limits-database-server.md)참조하십시오.

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>리전에 대한 구독 액세스 활성화

일부 혜택 유형은 일부 지역에서 사용할 수 없습니다. 다음과 같은 오류가 표시될 수 있습니다.

`This location is not available for subscription`

구독이 특정 리전에서 액세스해야 하는 경우 **기타 할당량 요청** 옵션을 사용하여 액세스를 요청하십시오. 요청에서 지역에 대해 사용할 제품 및 SKU 세부 정보를 지정합니다. 오퍼링 및 SKU 옵션을 탐색하려면 [Azure SQL Database 가격 책정을](https://azure.microsoft.com/pricing/details/sql-database/single/)참조하십시오.

![기타 할당량 세부 정보](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>관리되는 인스턴스 할당량 유형

SQL **Server 관리 인스턴스** 할당량 유형의 경우 다음 단계를 사용합니다.

1. **지역** 목록에서 대상Azure 지역을 선택합니다.

1. **서브넷** 및 **vCore에**대해 요청하는 새 제한을 입력합니다.

   ![관리되는 인스턴스 할당량 세부 정보](./media/quota-increase-request/quota-details-managed-instance.png)

자세한 내용은 [Azure SQL Database 관리 인스턴스 리소스 제한 개요를](sql-database-managed-instance-resource-limits.md)참조하십시오.

## <a name="submit-your-request"></a>요청 제출

마지막 단계는 SQL Database 할당량 요청의 나머지 세부 정보를 채우는 것입니다. 그런 다음: **검토 + 만들기>>** 선택하고 요청 세부 정보를 검토한 후 **만들기를** 클릭하여 요청을 제출합니다.

## <a name="next-steps"></a>다음 단계

요청을 제출하면 검토됩니다. 양식에 제공된 정보에 따라 답변과 함께 연락을 받게 됩니다.

다른 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건을](../azure-resource-manager/management/azure-subscription-service-limits.md)참조하십시오.
