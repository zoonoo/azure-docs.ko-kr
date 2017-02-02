---
title: "Azure Portal로 Azure SQL Database에 대한 지역에서 복제 구성 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure SQL Database에 대한 지역에서 복제 구성"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: d0b29822-714f-4633-a5ab-fb1a09d43ced
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: sashan;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 9faff06ee1856ca6490add759acd3c5f36719853


---
# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Azure 포털로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성
> [!div class="op_single_selector"]
> * [개요](sql-database-geo-replication-overview.md)
> * [쉬운 테이블](sql-database-geo-replication-portal.md)
> * [PowerShell](sql-database-geo-replication-powershell.md)
> * [T-SQL](sql-database-geo-replication-transact-sql.md)
> 
> 

이 문서에서는 [Azure Portal](http://portal.azure.com)을 사용하여 SQL Database에 대한 활성 지역 복제를 구성하는 방법을 보여 줍니다.

Azure 포털에서 장애 조치를 시작하려면 [Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치(Failover) 시작](sql-database-geo-replication-failover-portal.md)을 참조하세요.

> [!NOTE]
> 현재 활성 지역 복제(읽기 가능한 보조)는 모든 서비스 계층에 있는 모든 데이터베이스에 대해 사용 가능합니다. 2017년 4월부로 읽을 수 없는 보조 유형은 사용 중지되며 기존의 읽을 수 없는 데이터베이스는 읽을 수 있는 보조 데이터베이스로 자동으로 업그레이드됩니다.
> 
> 

Azure Portal을 사용하여 지역에서 복제를 구성하려면 다음 리소스가 필요합니다.

* Azure SQL Database: 다른 지역으로 복제하려는 주 데이터베이스입니다.

> [!Note]
활성 지역 복제는 동일한 구독에 있는 데이터베이스 간에 수행되어야 합니다.

## <a name="add-a-secondary-database"></a>보조 데이터베이스 추가
다음 단계에서는 지역에서 복제 파트너 관계에 새 보조 데이터베이스를 만듭니다.  

보조 데이터베이스를 추가하려면 구독 소유자 또는 공동 소유자여야 합니다.

보조 데이터베이스는 주 데이터베이스와 동일한 이름을 포함하며 기본적으로 동일한 수준의 서비스입니다. 보조 데이터베이스는 독립 실행형 데이터베이스 또는 탄력적 풀에 있는 데이터베이스일 수 있습니다. 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요.
보조가 만들어지고 시드된 후 데이터는 주 데이터베이스에서 새로운 보조 데이터베이스로 복제되기 시작합니다.

> [!NOTE]
> 파트너 데이터베이스가 이미 있는 경우(예: 이전 지역에서 복제 관계를 종료한 결과) 명령이 실패합니다.
> 
> 

### <a name="add-secondary"></a>보조 추가
1. [Azure Portal](http://portal.azure.com)에서 지역에서 복제를 위해 설치하려는 데이터베이스를 찾습니다.
2. SQL Database 페이지에서 **지역에서 복제**를 선택하고 보조 데이터베이스를 만들 지역을 선택합니다. 주 데이터베이스를 호스트하는 지역과 다른 지역을 선택할 수 있지만 [쌍을 이루는 지역](../best-practices-availability-paired-regions.md)이 권장됩니다.
   
    ![지역에서 복제 구성](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. 보조 데이터베이스를 위한 서버 및 가격 책정 계층을 선택하거나 구성합니다.
   
    ![보조 구성](./media/sql-database-geo-replication-portal/create-secondary.png)
4. 필요에 따라 탄력적 풀에 보조 데이터베이스를 추가할 수 있습니다. 풀에서 보조 데이터베이스를 만들려면 **탄력적 풀**을 클릭하고 대상 서버에서 풀을 선택합니다. 대상 서버에 풀이 이미 있어야 합니다. 이 워크플로는 풀을 만들지 않습니다.
5. **만들기** 를 클릭하여 보조를 추가합니다.
6. 보조 데이터베이스가 만들어지고 시드 프로세스가 시작됩니다.
   
    ![보조 구성](./media/sql-database-geo-replication-portal/seeding0.png)
7. 시드 프로세스가 완료되면 보조 데이터베이스가 해당 상태를 표시합니다.
   
    ![시드 완료](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="remove-secondary-database"></a>보조 데이터베이스 제거
이 작업은 보조 데이터베이스에 대한 복제를 영구적으로 종료하고 보조의 역할을 일반적인 읽기-쓰기 데이터베이스로 변경합니다. 보조 데이터베이스에 대한 연결이 끊어진 경우 명령이 성공하지만 연결이 복원된 후에야 보조는 읽기-쓰기가 됩니다.  

1. [Azure Portal](http://portal.azure.com)에서 지역에서 복제 파트너 관계에 있는 주 데이터베이스를 찾습니다.
2. SQL Database 페이지에서 **지역에서 복제**를 선택합니다.
3. **보조** 목록에서 지역에서 복제 파트너 관계에서 제거할 데이터베이스를 선택합니다.
4. **복제 중지**를 클릭합니다.
   
    ![보조 제거](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. 확인 창이 열립니다. 지역에서 복제 파트너 관계에서 데이터베이스를 제거하려면 **예**를 클릭합니다. (복제에 포함되지 않는 읽기-쓰기 데이터베이스로 설정)

## <a name="next-steps"></a>다음 단계
* 활성 지역 복제에 대한 자세한 내용은 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.




<!--HONumber=Dec16_HO2-->


