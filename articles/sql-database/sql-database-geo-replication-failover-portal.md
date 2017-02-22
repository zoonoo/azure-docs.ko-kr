---
title: "Azure Portal에서 Azure SQL Database에 대해 계획 또는 계획되지 않은 장애 조치 시작 | Microsoft Docs"
description: "Azure 포털을 사용하여 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a9d184a4-09e0-4f41-b364-40425f68f430
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: b0180a9f32e1176667fe8e33a4151b2b70956adc


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Azure 포털에서 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작

이 문서에서는 [Azure 포털](http://portal.azure.com)을 사용하여 보조 SQL 데이터베이스로 장애 조치(failover)를 시작하는 방법을 보여 줍니다. 지역에서 복제를 구성하려면 [Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-portal.md)을 참조하세요.

## <a name="initiate-a-failover"></a>장애 조치(Failover) 시작
보조 데이터베이스가 주 데이터베이스가 되도록 전환할 수 있습니다.  

1. [Azure 포털](http://portal.azure.com) 에서 지역에서 복제 파트너 관계에 있는 주 데이터베이스를 찾습니다.
2. SQL Database 블레이드에서 **모든 설정** > **지역에서 복제**를 선택합니다.
3. **보조** 목록에서 새로운 주 데이터베이스가 될 데이터베이스를 선택하고 **장애 조치**를 클릭합니다.
   
    ![장애 조치][2]
4. 장애 조치를 시작하려면 **예** 를 클릭합니다.

이 명령은 보조 데이터베이스를 주 역할로 즉시 전환합니다. 

역할이 전환되는 동안 두 데이터베이스를 모두 사용할 수 없는 (0-25초의 순서로) 짧은 기간이 있습니다. 주 데이터베이스에 여러 개의 보조 데이터베이스가 있는 경우 이 명령을 사용하면 새로운 주 데이터베이스에 연결할 다른 보조 데이터베이스가 자동으로 다시 구성됩니다. 전체 작업은 정상적인 상황에서 완료하는데&1;분 미만이 걸려야 합니다. 

> [!NOTE]
> 주 데이터베이스가 온라인이고 명령이 실행될 때 트랜잭션을 커밋하면 일부 데이터가 손실될 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* 장애 조치(failover) 후에는 새로운 주 데이터베이스에서 서버 및 데이터베이스의 인증 요구 사항이 구성되어 있는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.
* 사전 및 사후 복구 단계를 비롯한 활성 지역 복제를 사용하고 재해 복구 훈련을 수행하여 재해 후에 복구하는 방법을 알아보려면 [재해 복구 훈련](sql-database-disaster-recovery.md)
* 활성 지역 복제에 대한 Sasha Nosov의 블로그 게시물은 [새로운 지역에서 복제 기능에 대한 주요 내용](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* 활성 지역 복제를 사용하여 클라우드 응용 프로그램을 설계하는 방법에 대한 자세한 내용은 [지역에서 복제를 사용하여 무중단 업무 방식에 사용 가능하도록 클라우드 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* 탄력적 풀의 활성 지역 복제 사용에 대한 자세한 내용은 [탄력적 풀 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)을 참조하세요.
* 무중단 업무 방식에 대한 개략적 정보는 [무중단 업무 방식 개요](sql-database-business-continuity.md)

<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png



<!--HONumber=Feb17_HO3-->


