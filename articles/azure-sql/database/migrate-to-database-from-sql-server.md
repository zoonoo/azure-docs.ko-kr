---
title: Azure SQL Database로 SQL Server 데이터베이스 마이그레이션
description: Azure SQL Database로 SQL Server 데이터베이스 마이그레이션에 대해 알아봅니다.
keywords: 데이터베이스 마이그레이션, SQL Server 데이터베이스 마이그레이션, 데이터베이스 마이그레이션 도구, 데이터베이스 마이그레이션, SQL 데이터베이스 마이그레이션
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/11/2019
ms.openlocfilehash: 06763624231fde344990da6d0a4639bcccdedf00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448872"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Azure SQL Database로 SQL Server 데이터베이스 마이그레이션
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 SQL Server 2005 이상 데이터베이스를 Azure SQL Database으로 마이그레이션하는 기본 방법에 대해 알아봅니다. Azure SQL Managed Instance로 마이그레이션하는 방법에 대 한 자세한 내용은 [AZURE sql Managed Instance SQL Server 인스턴스 마이그레이션](../managed-instance/migrate-to-instance-from-sql-server.md)을 참조 하세요. 다른 플랫폼에서 마이그레이션하는 방법에 대한 마이그레이션 정보는 [Azure Database 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조하세요.

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션

SQL Server 2005 이상의 데이터베이스를 Azure SQL Database으로 마이그레이션하는 데는 두 가지 기본 방법이 있습니다. 첫 번째 방법이 더 간단하지만, 마이그레이션하는 동안 다소 상당한 가동 중지 시간이 있을 수 있습니다. 두 번째 방법은 좀 복잡하지만, 마이그레이션하는 동안 가동 중지 시간이 대폭 줄어듭니다.

두 경우 모두 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 사용하여 원본 데이터베이스가 Azure SQL Database와 호환되는지 확인해야 합니다. SQL Database는 서버 수준 및 데이터베이스 간 작업과 관련 된 문제를 제외 하 고 SQL Server의 [기능 패리티](features-comparison.md) 에 근접 하 고 있습니다. SQL Server 데이터베이스를 마이그레이션하려면 [부분적으로 지원되거나 지원되지 않는 기능](transact-sql-tsql-differences-sql-server.md)을 사용하는 데이터베이스 및 애플리케이션을 [어느 정도 다시 엔지니어링하여 이러한 비호환성 문제를 해결](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues)해야 합니다.

> [!NOTE]
> Microsoft Access, Sybase, MySQL Oracle, DB2를 비롯한 비-SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션해야 할 경우 [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/)를 참조하세요.

## <a name="method-1-migration-with-downtime-during-the-migration"></a>방법 1: 마이그레이션하는 동안 가동 중지 시간을 사용한 마이그레이션

 일부 가동 중지 시간이 발생 하거나 나중에 마이그레이션할 수 있도록 프로덕션 데이터베이스의 테스트 마이그레이션을 수행 하는 경우이 메서드를 사용 하 여 단일 또는 풀링된 데이터베이스로 마이그레이션합니다. 자습서를 보려면 [SQL Server 데이터베이스 마이그레이션](../../dms/tutorial-sql-server-to-azure-sql.md)을 참조하세요.

다음 목록에는 이 방법을 사용한 단일 또는 풀링된 데이터베이스의 SQL Server 데이터베이스 마이그레이션에 대한 일반적인 워크플로가 포함되어 있습니다. SQL Managed Instance로 마이그레이션하려면 [sql Managed Instance로 마이그레이션](../managed-instance/migrate-to-instance-from-sql-server.md)을 참조 하세요.

  ![VSSSDT 마이그레이션 다이어그램](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)의 최신 버전을 사용하여 데이터베이스 호환성에 대해 [평가](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)합니다.
2. Transact-SQL 스크립트와 같은 필요한 수정 프로그램을 준비합니다.
3. 마이그레이션 진행되는 동안 원본 데이터베이스에 대해 일관된 트랜잭션의 복사본을 만들거나, 원본 데이터베이스에서 새 트랜잭션이 발생하지 않도록 합니다. 이 두 번째 방식을 진행하는 방법에는 클라이언트 연결을 사용하지 않도록 설정하거나 [데이터베이스 스냅샷](https://msdn.microsoft.com/library/ms175876.aspx)을 만드는 경우가 포함됩니다. 마이그레이션 후에 트랜잭션 복제를 사용하여 마이그레이션된 데이터베이스를 마이그레이션 구분 지점 이후에 발생한 변경 내용으로 업데이트할 수 있습니다. [트랜잭션 마이그레이션을 사용하여 마이그레이션](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication)을 참조하세요.  
4. Transact-SQL 스크립트를 배포하여 데이터베이스 복사본에는 수정 내용을 적용합니다.
5. Data Migration Assistant를 사용 하 여 Azure SQL Database의 새 데이터베이스로 데이터베이스 복사본을 [마이그레이션합니다](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) .

> [!NOTE]
> DMA를 사용하는 대신, BACPAC 파일을 사용할 수도 있습니다. [Azure SQL Database에서 BACPAC 파일을 새 데이터베이스로 가져오기](database-import.md)를 참조 하세요.

### <a name="optimizing-data-transfer-performance-during-migration"></a>마이그레이션하는 동안 데이터 전송 성능 최적화

다음 목록에는 가져오기 프로세스 도중 최상의 성능을 위한 권장 사항을 포함합니다.

- 전송 성능을 최대화할 수 있도록 예산 범위 내에서 가장 높은 서비스 계층과 컴퓨팅 크기를 선택합니다. 마이그레이션을 완료한 후 규모를 축소하여 비용을 절감할 수 있습니다.
- BACPAC 파일과 대상 데이터 센터 간의 거리를 최소화합니다.
- 마이그레이션하는 동안 autostatistics 사용 안 함
- 파티션 테이블 및 인덱스
- 인덱싱된 뷰를 삭제하고 완료된 후 다시 만들기
- 거의 쿼리하지 않는 기록 데이터를 다른 데이터베이스로 제거 하 고이 기록 데이터를 Azure SQL Database의 개별 데이터베이스로 마이그레이션합니다. 그러면 [탄력적 쿼리](elastic-query-overview.md)를 사용하여 이 기록 데이터를 쿼리할 수 있습니다.

### <a name="optimize-performance-after-the-migration-completes"></a>마이그레이션이 완료된 후 성능 최적화

마이그레이션이 완료된 후 전체 검색으로 [통계를 업데이트합니다](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="method-2-use-transactional-replication"></a>방법 2 트랜잭션 복제 사용

마이그레이션이 진행 되는 동안 프로덕션에서 SQL Server 데이터베이스를 제거 하는 것을 감당할 수 없는 경우 마이그레이션 솔루션으로 트랜잭션 복제를 SQL Server 사용할 수 있습니다. 이 방법을 사용하려면 원본 데이터베이스가 [트랜잭션 복제의 요구 사항](https://msdn.microsoft.com/library/mt589530.aspx)을 충족해야 하고 Azure SQL Database와 호환되어야 합니다. AlwaysOn을 사용한 SQL 복제에 대한 자세한 내용은 [AlwaysOn 가용성 그룹에 대한 복제 구성(SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)을 참조하세요.

이 솔루션을 사용 하려면 Azure SQL Database에서 마이그레이션할 SQL Server 인스턴스의 구독자로 데이터베이스를 구성 합니다. 트랜잭션 복제 배포자는 새 트랜잭션이 계속 발생하는 동안 데이터베이스의 데이터를 동기화합니다(게시자).

트랜잭션 복제를 사용 하면 데이터 또는 스키마에 대 한 모든 변경 내용이 Azure SQL Database의 데이터베이스에 표시 됩니다. 동기화가 완료 되 고 마이그레이션할 준비가 되 면 응용 프로그램의 연결 문자열을 변경 하 여 데이터베이스를 가리키도록 합니다. 트랜잭션 복제를 통해 원본 데이터베이스에 남아 있는 모든 변경 사항을 비우고 모든 애플리케이션이 Azure DB를 가리키면 트랜잭션 복제를 제거할 수 있습니다. 이제 Azure SQL Database의 데이터베이스가 프로덕션 시스템입니다.

 ![SeedCloudTR 다이어그램](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> 또한 트랜잭션 복제를 사용하여 데이터베이스의 하위 집합을 마이그레이션할 수 있습니다. 사용자가 Azure SQL Database로 복제하는 게시물을 복제되는 데이터베이스의 테이블 하위 집합으로 제한할 수 있습니다. 복제되는 각 테이블에 대해 데이터를 행의 하위 집합 및/또는 열의 하위 집합으로 제한할 수 있습니다.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>트랜잭션 복제 워크플로를 사용하여 SQL Database로 마이그레이션

> [!IMPORTANT]
> 최신 버전의 SQL Server Management Studio을 사용 하 여 Azure 및 SQL Database 업데이트와 동기화 상태를 유지 합니다. 이전 버전의 SQL Server Management Studio는 SQL Database를 구독자로 설정할 수 없습니다. [SQL Server Management Studio를 업데이트합니다](/sql/ssms/download-sql-server-management-studio-ssms).

1. 배포 설정
   - [SSMS(SQL Server Management Studio) 사용](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Transact-SQL 사용](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. 게시물 만들기
   - [SSMS(SQL Server Management Studio) 사용](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Transact-SQL 사용](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. 구독 만들기
   - [SSMS(SQL Server Management Studio) 사용](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Transact-SQL 사용](/sql/relational-databases/replication/create-a-push-subscription/)

SQL Database로 마이그레이션하기 위한 몇 가지 팁과 차이점

- 로컬 배포자 사용
  - 이렇게 하면 서버의 성능에 영향을 미칠 수 있습니다.
  - 성능에 영향을 미치지 않아야 하면 다른 서버를 사용할 수 있지만 관리가 복잡해집니다.
- 스냅샷 폴더를 선택할 때 선택하는 폴더가 복제하려는 모든 테이블의 BCP를 수용하기에 충분한지 확인합니다.
- 스냅숏 만들기는 완료 될 때까지 연결 된 테이블을 잠그고 스냅숏을 적절 하 게 예약 합니다.
- 푸시 구독만 Azure SQL Database에서 지원됩니다. 원본 데이터베이스에서만 구독자를 추가할 수 있습니다.

## <a name="resolving-database-migration-compatibility-issues"></a>데이터베이스 마이그레이션 호환성 문제 해결

원본 데이터베이스에 있는 SQL Server 버전과 마이그레이션하는 데이터베이스의 복잡성에 따라 발생할 수 있는 다양 한 호환성 문제가 있습니다. 이전 버전의 SQL Server에는 보다 많은 호환성 문제가 있습니다. 사용자가 선택한 검색 엔진을 사용하는 대상이 지정된 인터넷 검색 외에도 다음 리소스를 사용해 보세요.

- [Azure SQL Database에서 지원되지 않는 하는 SQL Server 데이터베이스 기능](transact-sql-tsql-differences-sql-server.md)
- [SQL Server 2016에서 지원되지 않는 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [SQL Server 2014에서 지원되지 않는 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [SQL Server 2012에서 지원되지 않는 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [SQL Server 2008 R2에서 사용이 중단된 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [SQL Server 2005에서 사용이 중단된 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

인터넷을 검색하고 이러한 리소스를 사용하는 것 외에도 [Azure SQL Database에 대한 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-sql-database.html) 또는 [StackOverflow](https://stackoverflow.com/)를 사용합니다.

> [!IMPORTANT]
> Azure SQL Managed Instance를 사용 하면 최소한의 호환성 문제 없이 기존 SQL Server 인스턴스와 해당 데이터베이스를 마이그레이션할 수 있습니다. 관리 되는 [인스턴스](../managed-instance/sql-managed-instance-paas-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Azure SQL EMEA 엔지니어 블로그의 스크립트를 사용하여 [마이그레이션하는 동안 tempdb 사용을 모니터링합니다](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Azure SQL EMEA 엔지니어 블로그의 스크립트를 사용하여 [마이그레이션이 진행되는 동안 데이터베이스의 트랜잭션 로그 공간을 모니터링합니다](https://docs.microsoft.com/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.
- 마이그레이션 후 UTC 시간 연동에 대한 내용은 [현지 표준 시간대에 맞게 기본 표준 시간대 수정](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/)을 참조하세요.
- 마이그레이션 후 데이터베이스의 기본 언어를 변경하는 방법에 대한 내용은 [Azure SQL Database의 기본 언어 변경 방법](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/)을 참조하세요.
 