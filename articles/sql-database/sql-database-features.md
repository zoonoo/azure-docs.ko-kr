---
title: "Azure SQL Database 기능 비교 | Microsoft Docs"
description: "이 문서에서는 SQL Server 및 Azure SQL Database의 기능을 비교하고 차이점을 보여줍니다."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 02/08/2018
ms.author: carlrab
ms.openlocfilehash: dc9a7fa0a7fa0e029f71510cc516496ed12a6274
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2018
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>기능 비교: Azure SQL Database 및 SQL Server 

Azure SQL Database는 SQL Server와 공용 코드 베이스를 공유하며 데이터베이스 수준에서 대부분의 동일한 기능을 지원합니다. Azure SQL Database와 SQL Server 간의 주요 기능 차이점은 인스턴스 수준에 있습니다. 

Azure SQL Database에 기능은 추가 계속됩니다. Azure에 대한 서비스 업데이트 웹 페이지를 방문하고 해당 필터를 사용해 보세요.

* [SQL Database 서비스](https://azure.microsoft.com/updates/?service=sql-database)에 대해 필터링되었습니다.
* SQL Database 기능의 [GA(General Availability) 공지](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 에 대해 필터링되었습니다.

## <a name="sql-server-and-sql-database-feature-support"></a>SQL Server 및 SQL Database 기능 지원

다음 표에서는 SQL Server의 주요 기능을 나열하고 특정 기능을 지원하는지 여부에 대한 정보 및 기능에 대한 자세한 정보로 링크를 제공합니다. 기존 데이터베이스 솔루션을 마이그레이션할 때 고려해야 할 Transact-SQL 차이점은 [SQL Database에 대한 마이그레이션 중 Transact-SQL 차이점 해결](sql-database-transact-sql-information.md)을 참조하세요.


| **SQL Server 기능** | **Azure SQL Database에서 지원** | 
| --- | --- |  
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 예 - [인증서 저장소](sql-database-always-encrypted.md) 및 [키 자격 증명 모음](sql-database-always-encrypted-azure-key-vault.md) 참조|
| [AlwaysOn 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 고가용성은 모든 데이터베이스에 포함됩니다. [고가용성 및 Azure SQL Database](sql-database-high-availability.md)를 참조하세요. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-geo-replication-overview.md)에서 설명합니다. |
| [데이터베이스 연결](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 아니요 |
| [응용 프로그램 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | 예 |
|[감사](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [예](sql-database-auditing.md)|
| [자동 조정](/sql/relational-databases/automatic-tuning/automatic-tuning)| [예](sql-database-automatic-tuning.md)|
| [BACPAC 파일(내보내기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 예 - [SQL Database 내보내기](sql-database-export.md) 참조 |
| [BACPAC 파일(가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 예 - [SQL Database 가져오기](sql-database-import.md) 참조 |
| [BACKUP 명령](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 아니요 - [자동화된 백업](sql-database-automated-backups.md) 참조 |
| [기본 제공 함수](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 |
| [변경 데이터 캡처](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 아니요 |
| [변경 내용 추적](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | 예 |
| [Collation 문](https://docs.microsoft.com/sql/t-sql/statements/collations) | 예 |
| [Columnstore 인덱스](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 예 - [Premium Edition만 해당](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [CLR(공용 언어 런타임)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 아니요 |
| [포함된 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | 예 |
| [포함된 사용자](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | 예 |
| [흐름 제어 언어 키워드](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | 예 |
| [데이터베이스 간 쿼리](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | 부분 - [탄력적 쿼리](sql-database-elastic-query-overview.md) 참조 |
| [커서](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 예 | 
| [데이터 압축](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | 예 |
| [데이터베이스 메일](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 아니오 |
| [데이터베이스 미러링](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 아니요 |
| [데이터베이스 구성 설정](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | 예 |
| [DQS(Data Quality Services)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 아니요 |
| [데이터베이스 스냅숏](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 아니요 |
| [데이터 형식](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | 예 |  
| [DBCC 문](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 대부분 - 개별 문 참조 |
| [DDL 문](https://docs.microsoft.com/sql/t-sql/statements/statements) | 예 |
| [DDL 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 데이터베이스에만 해당 |
| [분산된 트랜잭션 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 아니요 - [탄력적 트랜잭션](sql-database-elastic-transactions-overview.md) 참조 |
| [DML 문](https://docs.microsoft.com/sql/t-sql/queries/queries) | 예 |
| [DML 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 대부분 - 개별 문 참조 | 
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 일부 - 개별 DMV 참조 |
|[동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)|[예](sql-database-dynamic-data-masking-get-started.md)|
| [이벤트 알림](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 아니요 - [경고](sql-database-insights-alerts-portal.md) 참조 |
| [식](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |예 |
| [확장 이벤트](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 일부 - [SQL Database의 확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 |
| [확장된 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 아니요 |
| [파일 및 파일 그룹](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 기본 파일 그룹만 해당 |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 아니오 |
| [전체 텍스트 검색](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | 지원되지 않는 타사 단어 분리기 |
| [함수](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 |
| [그래프 처리 중](/sql/relational-databases/graphs/sql-graph-overview) | 예 |
| [메모리 내 최적화](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 예 - [Premium Edition만 해당](sql-database-in-memory.md) |
| [JSON 데이터 지원](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | 예 |
| [언어 요소](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 대부분 - 개별 요소 참조 |  
| [연결된 서버](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](sql-database-elastic-query-horizontal-partitioning.md) 참조 |
| [로그 전달](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 고가용성은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-geo-replication-overview.md)에서 설명합니다. |
| [MDS(Master Data Services)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 아니요 |
| [대량 가져오기에서 최소 로깅](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 아니요 |
| [시스템 데이터 수정](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 아니요 |
| [온라인 인덱스 작업](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | 예 |
| [연산자](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 대부분 - 개별 연산자 참조 |
| [지정 시간 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 예 - [SQL Database 복구](sql-database-recovery-using-backups.md#point-in-time-restore) 참조 |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 아니오 |
| [정책 기반 관리](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 아니요 |
| [조건자](https://docs.microsoft.com/sql/t-sql/queries/predicates) | 예 |
| [R 서비스](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 미리 보기 릴리스, [기계 학습의 새로운 기능](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)을 참조하세요.  |
| [리소스 관리자](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 아니오 |
| [RESTORE 문](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 아니오 | 
| [백업에서 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 기본 제공 백업에서만 - [SQL Database 복구](sql-database-recovery-using-backups.md) 참조 |
| [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | 예 |
| [의미 체계 검색](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 아니요 |
| [시퀀스 번호](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | 예 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 아니요 |
| [서버 구성 설정](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 아니요 |
| [Set 문](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 대부분 - 개별 문 참조 
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | 예 |
| [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 아니요 - [탄력적 작업](sql-database-elastic-jobs-getting-started.md) 참조 |
| [SSAS(SQL Server Analysis Services)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 참조 |
| [SQL Server 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 아니요 - [SQL Database 감사](sql-database-auditing.md) 참조 |
| [SSIS(SQL Server Integration Services)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 예 - [SQL Server Integration Services 워크로드를 클라우드로 이동](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview) 참조 |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 예 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 아니요 - [확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 |
| [SQL Server 복제](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [트랜잭션 및 스냅숏 복제 구독자만 해당](sql-database-cloud-migrate.md) |
| [SSRS(SQL Server Reporting Services)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 아니오 |
| [저장 프로시저](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | 예 |
| [시스템 저장 함수](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 일부 - 개별 함수 참조 |
| [시스템 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 일부 - 개별 저장 프로시저 참조 |
| [시스템 테이블](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 일부 - 개별 테이블 참조 |
| [시스템 카탈로그 뷰](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 일부 - 개별 뷰 참조 |
| [테이블 분할](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 예 - 기본 파일 그룹만 해당 |
| [임시 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | 로컬 및 데이터베이스 범위 전역 임시 테이블만 해당 |
| [임시 테이블](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | 예 |
| [변수](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 예 | 
| [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 예 |
| [Windows Server 장애 조치(failover) 클러스터링](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 고가용성은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-geo-replication-overview.md)에서 설명합니다. |
| [XML 인덱스](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | 예 |

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요.
- Transact-SQL 지원 및 차이점에 대한 정보는 [SQL Database에 대한 마이그레이션 중 Transact-SQL 차이점 해결](sql-database-transact-sql-information.md)을 참조하세요.
