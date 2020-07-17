---
title: 할당량 증가 요청
description: 이 페이지에서는 Azure SQL Database 및 Azure SQL Managed Instance에 대 한 할당량을 늘리기 위한 지원 요청을 만드는 방법을 설명 합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: 4557d2ecdb49cd50396986f0ea30277f50ecf9f7
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987288"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance에 대 한 요청 할당량이 늘어납니다.
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 문서에서는 Azure SQL Database 및 Azure SQL Managed Instance에 대 한 할당량 증가를 요청 하는 방법을 설명 합니다. 또한 지역에 대 한 구독 액세스를 사용 하도록 설정 하는 방법을 설명 합니다.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>새 지원 요청 만들기

다음 단계를 사용 하 여 SQL Database에 대 한 Azure Portal에서 새 지원 요청을 만듭니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원**을 선택합니다.

   ![도움말 + 지원 링크](./media/quota-increase-request/help-plus-support.png)

1. **도움말 + 지원**에서 **새 지원 요청**을 선택합니다.

    ![새 지원 요청 만들기](./media/quota-increase-request/new-support-request.png)

1. **문제 유형**에서 **서비스 및 구독 제한 (할당량)** 을 선택 합니다.

   ![문제 유형 선택](./media/quota-increase-request/select-quota-issue-type.png)

1. **구독**의 경우 할당량을 늘릴 구독을 선택 합니다.

   ![증가 된 할당량에 대 한 구독 선택](./media/quota-increase-request/select-subscription-support-request.png)

1. **할당량 유형**에서 다음 할당량 유형 중 하나를 선택 합니다.

   - 단일 데이터베이스 및 탄력적 풀 할당량에 대 한 **SQL Database** 합니다.
   - 관리 되는 인스턴스의 **SQL Database Managed Instance** 입니다.

   그런 다음, **Next: 솔루션 >>** 을 선택합니다.

   ![할당량 유형 선택](./media/quota-increase-request/select-quota-type.png)

1. **세부** 정보 창에서 세부 정보 **입력** 을 선택 하 여 추가 정보를 입력 합니다.

   ![세부 정보 링크 입력](./media/quota-increase-request/provide-details-link.png)

**세부 정보 입력** 을 클릭 하면 추가 정보를 추가할 수 있는 **할당량 정보** 창이 표시 됩니다. 다음 섹션에서는 **SQL Database** 및 **SQL Database Managed Instance** 할당량 유형에 대 한 다양 한 옵션을 설명 합니다.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>SQL Database 할당량 유형

다음 섹션에서는 **SQL Database** 할당량 유형에 대 한 할당량 증가 옵션을 설명 합니다.

- 서버당 Dtu (데이터베이스 트랜잭션 단위)
- 구독당 서버
- M 시리즈 영역 액세스
- 지역 액세스

### <a name="database-transaction-units-dtus-per-server"></a>서버당 Dtu (데이터베이스 트랜잭션 단위)

서버당 Dtu 증가를 요청 하려면 다음 단계를 사용 합니다.

1. 서버 할당량 유형 **당 dtu (데이터베이스 트랜잭션 단위)** 를 선택 합니다.

1. **리소스** 목록에서 대상으로 할 리소스를 선택합니다.

1. **새 할당량** 필드에 요청 하는 새 DTU 한도를 입력 합니다.

   ![DTU 할당량 정보](./media/quota-increase-request/quota-details-dtus.png)

자세한 내용은 dtu 구매 모델을 사용 하 여 [단일 데이터베이스에 대 한 리소스 제한](resource-limits-dtu-single-databases.md) 및 [탄력적 풀에 대 한 리소스 제한](resource-limits-dtu-elastic-pools.md)을 참조 하세요.

### <a name="servers-per-subscription"></a>구독당 서버

구독당 서버 수의 증가를 요청하려면 다음 단계를 사용합니다.

1. **구독당 서버** 할당량 유형을 선택합니다.

1. **위치** 목록에서 사용할 Azure 지역을 선택합니다. 할당량은 각 지역의 구독당입니다.

1. **새 할당량** 필드에 해당 지역의 최대 서버 수에 대한 요청을 입력합니다.

   ![서버 할당량 세부 정보](./media/quota-increase-request/quota-details-servers.png)

자세한 내용은 [SQL Database 리소스 제한 및 리소스 관리](resource-limits-logical-server.md)를 참조 하세요.

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a>지역에 대 한 구독 액세스 사용

일부 제품 유형은 모든 지역에서 사용할 수 없습니다. 다음과 같은 오류가 표시될 수 있습니다.

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

특정 지역에서 구독에 액세스 해야 하는 경우 **지역 액세스** 옵션을 선택 합니다. 요청에서 지역에 대해 사용하도록 설정할 제품 및 SKU 세부 정보를 지정합니다. 제품 및 SKU 옵션을 탐색 하려면 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/)을 참조 하세요.

1. **지역 액세스** 할당량 유형을 선택 합니다.

1. **위치 선택** 목록에서 사용할 Azure 지역을 선택 합니다. 할당량은 각 지역의 구독당입니다.

1. **구매 모델**및 **예상 사용량** 세부 정보를 입력 합니다.

   ![요청 영역 액세스](./media/quota-increase-request/quota-details-whitelisting.png)

### <a name="enable-m-series-access-to-a-region"></a><a id="mseries"></a>지역에 대 한 M 시리즈 액세스 사용

구독 및 지역에 M 시리즈 하드웨어를 사용 하도록 설정 하려면 지원 요청을 열어야 합니다.

1. **M 시리즈 지역 액세스** 할당량 유형을 선택 합니다.

1. **위치 선택** 목록에서 사용할 Azure 지역을 선택 합니다. 할당량은 각 지역의 구독당입니다.


   ![요청 영역 액세스](./media/quota-increase-request/quota-m-series.png)

## <a name="sql-managed-instance-quota-type"></a><a id="sqlmiquota"></a>SQL Managed Instance 할당량 유형

**SQL Managed Instance** 할당량 유형에 대해 다음 단계를 사용 합니다.

1. **지역** 목록에서 대상으로 할 Azure 지역을 선택 합니다.

1. **서브넷** 및 **vcore**에 대해 요청 하는 새 제한을 입력 합니다.

   ![SQL Managed Instance 할당량 정보](./media/quota-increase-request/quota-details-managed-instance.png)

자세한 내용은 [AZURE SQL Managed Instance 리소스 제한 개요](../managed-instance/resource-limits.md)를 참조 하세요.

## <a name="submit-your-request"></a>요청 제출

마지막 단계는 SQL Database 할당량 요청에 대한 나머지 세부 정보를 입력하는 것입니다. 그런 다음, **Next: 검토 + 만들기>>** 를 선택하고 요청 세부 정보를 검토한 후 **만들기**를 클릭하여 요청을 제출합니다.

## <a name="next-steps"></a>다음 단계

요청을 제출하면 검토됩니다. 양식에 제공한 정보를 바탕으로 답변을 받게 됩니다.

다른 Azure 제한에 대 한 자세한 내용은 [azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.
