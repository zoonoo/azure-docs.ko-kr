---
title: "트랜잭션 복제를 사용하여 SQL Database로 마이그레이션 | Microsoft Docs"
description: "Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 트랜잭션 복제"
services: sql-database
documentationcenter: 
author: jognanay
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 12/09/2016
ms.author: carlrab; jognanay;
translationtype: Human Translation
ms.sourcegitcommit: 8baeadbf7ef24e492c4115745c0d384e4f526188
ms.openlocfilehash: 8380925a56d39bd53fe737bed539b862cc835fad


---
# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>트랜잭션 복제를 사용하여 Azure SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션
> [!div class="op_single_selector"]
> * [SSMS 마이그레이션 마법사](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [BACPAC 파일로 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [BACPAC 파일에서 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [트랜잭션 복제자](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

이 문서에서는 SQL Server 트랜잭션 복제를 사용하여 가동 중지 시간을 최소화하면서 호환되는 SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법을 배웁니다.

## <a name="understanding-the-transactional-replication-architecture"></a>트랜잭션 복제 아키텍처 이해
마이그레이션하는 동안 SQL Server 데이터베이스의 운영을 중지할 수 없는 경우 마이그레이션 솔루션으로 SQL Server 트랜잭션 복제를 사용할 수 있습니다. 이 솔루션을 사용하려면 마이그레이션할 온-프레미스 SQL Server 인스턴스에서 Azure SQL 데이터베이스를 구독자로 구성합니다. 온-프레미스 트랜잭션 복제 배포자는 새 트랜잭션이 계속 발생하는 동안 온-프레미스 데이터베이스의 데이터를 동기화합니다(게시자). 

또한 트랜잭션 복제를 사용하여 온-프레미스 데이터베이스의 하위 집합을 마이그레이션할 수 있습니다. 사용자가 Azure SQL 데이터베이스로 복제하는 게시물을 복제되는 데이터베이스의 테이블 하위 집합으로 제한할 수 있습니다. 복제되는 각 테이블에 대해 데이터를 행의 하위 집합 및/또는 열의 하위 집합으로 제한할 수 있습니다.

트랜잭션 복제를 사용하면 데이터 또는 스키마에 대한 모든 변경 내용이 Azure SQL 데이터베이스에 표시됩니다. 동기화가 완료되고 마이그레이션 준비가 끝나면 Azure SQL 데이터베이스를 가리키도록 응용 프로그램의 연결 문자열을 변경합니다. 온-프레미스 데이터베이스에 남아 있는 모든 변경 사항이 트랜잭션 복제를 통해 Azure DB로 이동되고 모든 응용 프로그램이 Azure DB를 가리키면 트랜잭션 복제를 제거할 수 있습니다. Azure SQL 데이터베이스는 현재 프로덕션 시스템입니다.

 ![SeedCloudTR 다이어그램](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="how-transactional-replication-works"></a>트랜잭션 복제 사용 방법

트랜잭션 복제에는 세 가지 주요 구성 요소가 있습니다. 게시자, 배포자 및 구독자입니다. 이러한 구성 요소가 함께 복제를 수행합니다. 배포자는 서버 간에 데이터를 이동하는 프로세스를 제어합니다. 배포 SQL을 설정하면 배포 데이터베이스가 만들어집니다. 각 게시자는 배포 데이터베이스에 연결해야 합니다. 배포 데이터베이스는 연관된 각 게시물의 메타데이터와 각 복제 진행에 대한 데이터를 보관합니다. 트랜잭션 복제의 경우 구독자에서 실행해야 하는 모든 트랜잭션이 보관됩니다.

게시자는 마이그레이션에 대한 모든 데이터가 발생하는 데이터베이스입니다. 게시자 내에 많은 수의 게시물이 있을 수 있습니다. 이러한 게시물에는 복제해야 하는 모든 테이블과 데이터에 매핑되는 문서가 들어 있습니다. 게시물과 문서를 정의하는 방법에 따라 전체 또는 일부 데이터베이스를 복제할 수 있습니다. 

복제에서 구독자는 게시물의 모든 데이터와 트랜잭션을 받는 서버입니다. 각 게시물에 여러 복제가 있을 수 있습니다.

## <a name="transactional-replication-requirements"></a>트랜잭션 복제 요구 사항
[업데이트된 요구 사항 목록을 보려면 이 링크로 이동합니다.](https://msdn.microsoft.com/en-US/library/mt589530.aspx)
> [!IMPORTANT]
> Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 SQL Server Management Studio를 사용합니다. 이전 버전의 SQL Server Management Studio는 SQL 데이터베이스를 구독자로 설정할 수 없습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).
> 

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>트랜잭션 복제 워크플로를 사용하여 SQL Database로 마이그레이션

1. 배포 설정
   -  [SSMS(SQL Server Management Studio) 사용](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [TRANSACT-SQL 사용](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. 게시물 만들기
   -  [SSMS(SQL Server Management Studio) 사용](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [TRANSACT-SQL 사용](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. 구독 만들기
   -  [SSMS(SQL Server Management Studio) 사용](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [TRANSACT-SQL 사용](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

## <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>SQL Database로 마이그레이션하기 위한 몇 가지 팁과 차이점

1. 로컬 배포자 사용 
   - 이는 서버의 성능에 영향을 미칠 수 있습니다. 
   - 성능에 영향을 미치지 않아야 하면 다른 서버를 사용할 수 있지만 관리가 복잡해집니다.
2. 스냅숏 폴더를 선택할 때 선택하는 폴더가 복제하려는 모든 테이블의 BCP를 수용하기에 충분한지 확인합니다. 
3. 스냅숏 만들기는 연결된 테이블이 완료될 때까지 잠기기 때문에 스냅숏을 예약할 때는 이를 염두에 두어야 합니다. 
4. 푸시 구독만 Azure SQL Database에서 지원됩니다.
   - 온-프레미스 데이터베이스의 측면에서만 구독자를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)
* [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016 ](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>추가 리소스
* 트랜잭션 복제에 대한 자세한 내용은 [트랜잭션 복제](https://msdn.microsoft.com/library/mt589530.aspx)를 참조하세요.
* 전체 마이그레이션 프로세스 및 옵션에 대해 알아보려면 [클라우드에서 SQL Database로 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.



<!--HONumber=Dec16_HO2-->


