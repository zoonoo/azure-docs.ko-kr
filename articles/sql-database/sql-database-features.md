---
title: "Azure SQL Database 기능 개요 | Microsoft Docs"
description: "이 페이지에서는 Azure SQL Database 논리 서버 및 데이터베이스에 대한 개요를 제공하며 나열된 각 기능에 대한 링크가 포함된 기능 지원 매트릭스를 포함합니다."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: features
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 9daf8be93216aefbcf02e3fcba44d048ab95b43d
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---
# <a name="azure-sql-database-features"></a>Azure SQL Database 기능

다음 표에서는 Azure SQL Database의 주요 기능 및 이에 해당하는 SQL Server의 기능을 나열하고 특정 기능을 지원하는지 여부에 대한 정보 및 각 플랫폼의 기능에 대한 자세한 정보로 링크를 제공합니다. 기존 데이터베이스 솔루션을 마이그레이션할 때 고려해야 할 Transact-SQL 차이점은 [SQL Database에 대한 마이그레이션 중 Transact-SQL 차이점 해결](sql-database-transact-sql-information.md)을 참조하세요.

Azure SQL Database에 기능은 추가 계속됩니다. Azure에 대한 서비스 업데이트 웹 페이지를 방문하고 해당 필터를 사용해 보세요.

* [SQL 데이터베이스 서비스](https://azure.microsoft.com/updates/?service=sql-database)에 대해 필터링되었습니다.
* SQL 데이터베이스 기능의 [GA(General Availability) 공지](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 에 대해 필터링되었습니다.

| **기능** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| 활성 지역 복제 | 지원되지 않음 - [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) 참조 | [지원됨](sql-database-geo-replication-overview.md)
| 상시 암호화 | [지원됨](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 지원됨 - [인증서 저장소](sql-database-always-encrypted.md) 및 [키 자격 증명 모음](sql-database-always-encrypted-azure-key-vault.md) 참조|
| AlwaysOn 가용성 그룹 | [지원됨](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 지원되지 않음 - [활성 지역 복제](sql-database-geo-replication-overview.md) 참조 |
| 데이터베이스 연결 | [지원됨](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 지원되지 않음 |
| 응용 프로그램 역할 | [지원됨](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [지원됨](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| 자동 크기 조정 | 지원되지 않음 | 지원됨 - [서비스 계층](sql-database-service-tiers.md) 참조 |
| Azure Active Directory | 지원되지 않음 | [지원됨](sql-database-aad-authentication.md) |
| Azure 데이터 팩터리 | [지원됨](../data-factory/data-factory-introduction.md) | [지원됨](../data-factory/data-factory-introduction.md) |
| 감사 | [지원됨](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [지원됨](sql-database-auditing.md) |
| BACPAC 파일(내보내기) | [지원됨](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [지원됨](sql-database-export.md) |
| BACPAC 파일(가져오기) | [지원됨](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [지원됨](sql-database-import.md) |
| BACKUP | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 지원되지 않음 |
| 기본 제공 함수 | [지원됨](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - [개별 함수](https://docs.microsoft.com/sql/t-sql/functions/functions) 참조 |
| 변경 데이터 캡처 | [지원됨](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 지원되지 않음 |
| 변경 내용 추적 | [지원됨](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [지원됨](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| Collation 문 | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/collations) | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| Columnstore 인덱스 | [지원됨](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Premium Edition만 해당](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| CLR(Common Language Runtime) | [지원됨](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 지원되지 않음 |
| 포함된 데이터베이스 | [지원됨](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [지원됨](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| 포함된 사용자 | [지원됨](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [지원됨](sql-database-manage-logins.md#non-administrator-users) |
| 흐름 제어 언어 키워드 | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| 데이터베이스 간 쿼리 | [지원됨](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | 부분 - [탄력적 쿼리](sql-database-elastic-query-overview.md) 참조 |
| 커서 | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| 데이터 압축 | [지원됨](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [지원됨](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| 데이터베이스 백업 | [관리되는 사용자](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [SQL Database 서비스에서 관리됨](sql-database-automated-backups.md) |
| 데이터베이스 메일 | [지원됨](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 지원되지 않음 |
| 데이터베이스 미러링 | [지원됨](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 지원되지 않음 |
| 데이터베이스 구성 설정 | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services(DQS) | [지원됨](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 지원되지 않음 |
| 데이터베이스 스냅숏 | [지원됨](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 지원되지 않음 |
| 데이터 형식 | [지원됨](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [지원됨](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| DBCC 문 | [지원됨](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 대부분 - [개별 문](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) 참조 |
| DDL 문 | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/statements) | 대부분 - [개별 문](https://docs.microsoft.com/sql/t-sql/statements/statements) 참조
| DDL 트리거 | [지원됨](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [데이터베이스에만 해당](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| 분산 트랜잭션 | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 제한된 내부 SQL Database 시나리오만 해당 |
| DML 문 | [지원됨](https://docs.microsoft.com/sql/t-sql/queries/queries) | 대부분 - [개별 문]](https://docs.microsoft.com/sql/t-sql/queries/queries) 참조 |
| DML 트리거 | [지원됨](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [지원됨](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| Dmv | [모두](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 일부 - [개별 DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) 참조 |
| 탄력적 풀 | 지원되지 않음 | [지원됨](sql-database-elastic-pool.md) |
| 탄력적 작업 | 지원되지 않음 - [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) 참조 | [지원됨](sql-database-elastic-jobs-getting-started.md) | 
| 탄력적 쿼리 | 지원되지 않음 - [데이터베이스 간 쿼리](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) 참조 | [지원됨](sql-database-elastic-query-overview.md) |
| 이벤트 알림 | [지원됨](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [지원됨](sql-database-insights-alerts-portal.md) |
| 식 | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| 확장 이벤트 | [지원됨](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 일부 - [개별 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 |
| 확장된 저장 프로시저 | [지원됨](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 지원되지 않음 |
| 파일 및 파일 그룹 | [지원됨](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [기본만 해당](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| 파일스트림 | [지원됨](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 지원되지 않음 |
| 전체 텍스트 검색 | [지원됨](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [지원되지 않는 타사 단어 분리기](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| 함수 | [지원됨](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - [개별 함수](https://docs.microsoft.com/sql/t-sql/functions/functions) 참조 |
| 메모리 내 최적화 | [지원됨](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Premium Edition만 해당](sql-database-in-memory.md) |
| 작업 | [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) 참조 | [탄력적 작업](sql-database-elastic-jobs-getting-started.md) 참조 |
| JSON 데이터 지원 | [지원됨](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [지원됨](sql-database-json-features.md) |
| 언어 요소 | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 대부분 - [개별 요소](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) 참조 |  
| 연결된 서버 | [지원됨](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 지원되지 않음 - [탄력적 쿼리](sql-database-elastic-query-horizontal-partitioning.md) 참조 |
| 로그 전달 | [지원됨](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 지원되지 않음 - [활성 지역 복제](sql-database-geo-replication-overview.md) 참조 |
| Master Data Services(MDS) | [지원됨](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 지원되지 않음 |
| 대량 가져오기에서 최소 로깅 | [지원됨](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 지원되지 않음 |
| 시스템 데이터 수정 | [지원됨](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 지원되지 않음 |
| 온라인 인덱스 작업 | [지원됨](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [지원됨 - 서비스 계층에 의해 제한된 트랜잭션 크기](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| 연산자 | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 대부분 - [개별 연산자](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) 참조 |
| 지정 시간 데이터베이스 복원 | [지원됨](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [지원됨](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [지원됨](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 지원되지 않음
| 정책 기반 관리 | [지원됨](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 지원되지 않음 |
| 조건자 | [지원됨](https://docs.microsoft.com/sql/t-sql/queries/predicates) | 대부분 - [개별 조건자](https://docs.microsoft.com/sql/t-sql/queries/predicates) 참조
| R 서비스 | [지원됨](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| 리소스 관리자 | [지원됨](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 지원되지 않음 |
| RESTORE 문 | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 지원되지 않음 | 
| 백업에서 데이터베이스 복원 | [지원됨](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [기본 제공 백업에서만](sql-database-recovery-using-backups.md) |
| 행 수준 보안 | [지원됨](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [지원됨](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| 의미 체계 검색 | [지원됨](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 지원되지 않음 |
| 시퀀스 번호 | [지원됨](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [지원됨](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Service Broker | [지원됨](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 지원되지 않음 |
| 서버 구성 설정 | [지원됨](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 지원되지 않음 - [데이터베이스 구성 옵션](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) 참조 |
| Set 문 | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 대부분 - [개별 문](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) 참조 
| 공간 | [지원됨](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [지원됨](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| SQL Server 에이전트 | [지원됨](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 지원되지 않음 - [탄력적 작업](sql-database-elastic-jobs-getting-started.md) 참조 |
| SQL Server Analysis Services(SSAS) | [지원됨](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 지원되지 않음 - [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 참조 |
| SQL Server 통합 서비스(SSIS) | [지원됨](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 지원되지 않음 - [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 참조 |
| SQL Server PowerShell | [지원됨](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [지원됨](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| SQL Server Profiler | [지원됨](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 지원되지 않음 - [확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 |
| SQL Server 복제 | [지원됨](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [트랜잭션 및 스냅숏 복제 구독자만 해당](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services(SSRS) | [지원됨](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 지원되지 않음 |
| 저장 프로시저 | [지원됨](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [지원됨](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| 시스템 저장 함수 | [지원됨](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 일부 - [개별 함수](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) 참조 |
| 시스템 저장 프로시저 | [지원됨](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 일부 - [개별 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) 참조 |
| 시스템 테이블 | [지원됨](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 일부 - [개별 테이블](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) 참조 |
| 시스템 카탈로그 뷰 | [지원됨](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 일부 - [개별 뷰](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) 참조
| 테이블 분할 | [지원됨](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 지원됨 - [기본 파일 그룹만 해당](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| 임시 테이블 | [로컬 및 전역](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [로컬만 해당](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| 임시 테이블 | [지원됨](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [지원됨](sql-database-temporal-tables.md) |
| 트랜잭션 | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| 변수 | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [지원됨](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| 투명한 데이터 암호화(TDE)  | [지원됨](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [지원됨](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Windows Server 장애 조치 클러스터링 | [지원됨](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 지원되지 않음 - [활성 지역 복제](sql-database-geo-replication-overview.md) 참조 |
| XML 인덱스 | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [지원됨](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요.
- Transact-SQL 지원 및 차이점에 대한 정보는 [SQL Database에 대한 마이그레이션 중 Transact-SQL 차이점 해결](sql-database-transact-sql-information.md)을 참조하세요.

