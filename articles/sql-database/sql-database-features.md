---
title: 기능 비교
description: 이 문서에서는 다양한 Azure SQL Database 버전에서 제공하는 SQL Server 기능을 비교합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
ms.date: 05/10/2019
ms.openlocfilehash: 33ecef4dde3787546afd28e5f5b31e8dd535fc7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646367"
---
# <a name="azure-sql-database-features"></a>Azure SQL 데이터베이스 기능

Azure SQL Database 서비스는 공통 코드 베이스를 최신 안정 버전의 SQL Server와 공유합니다. 대부분의 표준 SQL 언어, 쿼리 처리 및 데이터베이스 관리 기능은 SQL Server 및 Azure SQL Database에서 동일합니다. SQL Server에서 일반적인 기능과 Azure SQL Database의 모든 맛은 다음과 같습니다.
  - 언어 기능 - [흐름 언어 키워드,](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) [커서,](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) [데이터 유형,](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) [DML 문,](https://docs.microsoft.com/sql/t-sql/queries/queries) [조건자,](https://docs.microsoft.com/sql/t-sql/queries/predicates) [시퀀스 번호,](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) [저장 프로시저](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)및 [변수의](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql)제어 .
  - 데이터베이스 기능 - [자동 튜닝 (계획 강제)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning), [변경 추적,](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) [데이터베이스 데이터 정렬,](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) [포함 된 데이터베이스,](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) [포함 된 사용자,](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) [데이터 압축,](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) [데이터베이스 구성 설정,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) [온라인 인덱스 작업,](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) [분할](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes)및 [임시 테이블](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) [(시작 가이드 참조).](sql-database-temporal-tables.md)
  - 보안 기능 - [응용 프로그램 역할,](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) [(시작 가이드 참조),](sql-database-dynamic-data-masking-get-started.md) [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)및 위협 탐지 - 단일 데이터베이스 및 [탄력적 풀 및](sql-database-threat-detection.md) [관리되는 인스턴스에](sql-database-managed-instance-threat-detection.md)대한 시작 가이드를 참조하십시오.
  - 다중 모델 기능 - [그래프 처리,](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) [JSON 데이터](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) [(시작 가이드 참조),](sql-database-json-features.md) [OPENXML,](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql) [공간,](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)및 [XML 인덱스](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql).

Azure SQL Database는 데이터베이스를 관리하고 고가용성을 보장합니다. PaaS 세계에서 고가용성에 영향을 주거나 사용할 수 없는 일부 기능은 Azure SQL Database의 기능이 제한되어 있습니다. 또한 일부 데이터베이스 기능은 만드는 Azure SQL Database의 유형에 따라 달라집니다. 이러한 기능은 아래 표에 설명되어 있습니다. Azure SQL Database를 사용하면 데이터베이스를 [Managed Instance](sql-database-managed-instance.md) 또는 탄력적 풀의 일부분으로 만들거나 단일 데이터베이스로 만들 수 있습니다. 차이점에 대한 자세한 정보가 필요한 경우 [단일 데이터베이스 및 탄력적 풀](sql-database-transact-sql-information.md) 또는 [관리인스턴스에](sql-database-managed-instance-transact-sql-information.md)대한 별도의 페이지에서 찾을 수 있습니다.

## <a name="sql-features"></a>SQL 기능

다음 표에서는 SQL Server의 주요 기능을 나열하고 관리되는 인스턴스 또는 단일 데이터베이스 및 탄력적 풀에서 기능이 부분적으로 또는 완전히 지원되는지 여부에 대한 정보를 제공하며 기능에 대한 자세한 정보에 대한 링크를 제공합니다.

| **SQL 기능** | **단일 데이터베이스 및 탄력적 풀** | **관리되는 인스턴스 및 인스턴스 풀** |
| --- | --- | --- |
| [항상 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 예 - [인증서 저장소](sql-database-always-encrypted.md) 및 [키 자격 증명 모음](sql-database-always-encrypted-azure-key-vault.md) 참조 | 예 - [인증서 저장소](sql-database-always-encrypted.md) 및 [키 자격 증명 모음](sql-database-always-encrypted-azure-key-vault.md) 참조 |
| [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [99.99-99.995% 가용성은](sql-database-high-availability.md) 모든 데이터베이스에 대해 보장됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. | [99.99.% 가용성은](sql-database-high-availability.md) 모든 데이터베이스에 대해 보장되며 [사용자가 관리할 수 없습니다.](sql-database-managed-instance-transact-sql-information.md#availability) 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요에서](sql-database-business-continuity.md)설명합니다. [자동 장애 조치 그룹을](sql-database-auto-failover-group.md) 사용하여 다른 리전에서 관리되는 인스턴스에서 보조 를 구성합니다. 다른 SQL Server 인스턴스 및 단일 데이터베이스는 관리되는 인스턴스의 보조 데이터베이스로 사용할 수 없습니다. |
| [데이터베이스 연결](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 예 | 예 |
| [감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [예](sql-database-auditing.md)| [예](sql-database-managed-instance-auditing.md), 몇 가지 [차이점이 있습니다.](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [AAD(Azure Active Directory) 인증](sql-database-aad-authentication.md) | 예. AAD 사용자만. | 예. 서버 수준 AAD 로그인 포함. |
| [백업 명령](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 아니요. 시스템에서 시작한 자동 백업만 - [자동 백업](sql-database-automated-backups.md) 참조 | 예. 사용자가 Azure Blob Storage에 대한 복사 전용 백업을 시작했습니다(사용자가 자동 시스템 백업을 시작할 수 없음) 백업 [차이점](sql-database-managed-instance-transact-sql-information.md#backup) 참조 |
| [내장 기능](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) 참조 | 
| [벌크 삽입 문](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 예, 하지만 Azure Blob 저장소에서 소스로. | 예. 하지만 Azure Blob 저장소에서 소스로 만 - [차이점을](sql-database-managed-instance-transact-sql-information.md#bulk-insert--openrowset)참조하십시오. |
| [인증서 및 비대칭 키](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 예, 파일 시스템 `BACKUP` 및 `CREATE` 작업에 액세스하지 않습니다. | 예, 파일 시스템에 `BACKUP` 액세스하지 `CREATE` 않고 및 작업에 대한 - [인증서 차이를](sql-database-managed-instance-transact-sql-information.md#certificates)참조하십시오 . | 
| [데이터 캡처 변경 - CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 예 | yes |
| [데이터 정렬 - 서버/인스턴스](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | 아니요, 기본 논리 `SQL_Latin1_General_CP1_CI_AS` 서버 데이터 정렬은 항상 사용됩니다. | 예. [인스턴스가 생성될](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) 때 설정할 수 있으며 나중에 업데이트할 수 없습니다. |
| [열 저장소 인덱스](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 예 - [프리미엄 계층, 표준 계층 - S3 이상, 범용 계층, 비즈니스 중요 계층 및 하이퍼스케일 계층](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |yes |
| [공통 언어 런타임 - CLR](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 예 | 예, 하지만 문에서 `CREATE ASSEMBLY` 파일 시스템에 액세스하지 않고 - [CLR 차이를](sql-database-managed-instance-transact-sql-information.md#clr) 참조하십시오 |
| [자격 증명](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | 예, 하지만 [데이터베이스 범위 자격 증명](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql). | 예, 하지만 Azure 키 `SHARED ACCESS SIGNATURE` **볼트만** 지원되며 세부 [정보가](sql-database-managed-instance-transact-sql-information.md#credential) 표시됩니다. |
| [데이터베이스 간/세 부분으로 구성된 이름 쿼리](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](sql-database-elastic-query-overview.md) 참조 | 예. 추가적으로 [탄력적 쿼리](sql-database-elastic-query-overview.md) |
| [데이터베이스 간 트랜잭션](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 예 | 예, 인스턴스 내에서. 크로스 인스턴스 쿼리에 대한 [연결된 서버 차이점을](sql-database-managed-instance-transact-sql-information.md#linked-servers) 참조하십시오. |
| [데이터베이스 메일 - DbMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 예 | yes |
| [데이터베이스 미러링](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 예 | [아니요](sql-database-managed-instance-transact-sql-information.md#database-mirroring) |
| [데이터베이스 스냅샷](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 예 | 예 |
| [DBCC 문](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 대부분 - 개별 문 참조 | 예 - [DBCC 차이점](sql-database-managed-instance-transact-sql-information.md#dbcc) 참조 |
| [DDL 문](https://docs.microsoft.com/sql/t-sql/statements/statements) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [DDL 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 데이터베이스에만 해당 |  yes |
| [분산된 파티션 뷰](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 예 | yes |
| [분산된 트랜잭션 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 아니요 - [탄력적 트랜잭션](sql-database-elastic-transactions-overview.md) 참조 |  아니오 - [연결된 서버 차이점을](sql-database-managed-instance-transact-sql-information.md#linked-servers)참조하십시오. 마이그레이션 하는 동안 여러 분산 된 SQL Server 인스턴스에서 하나의 관리 되는 인스턴스에 데이터베이스를 통합 하려고 합니다. |
| [DML 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 대부분 - 개별 문 참조 |  yes |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 대부분 - 개별 DMV 참조 |  예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [이벤트 알림](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 아니요 - [경고](sql-database-insights-alerts-portal.md) 참조 | 예 |
| [식](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |yes | yes |
| [확장 이벤트(XEvent)](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 일부 - [SQL Database의 확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 | 예 - [확장 이벤트 차이](sql-database-managed-instance-transact-sql-information.md#extended-events) 참조 |
| [확장된 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 예 | 예 |
| [파일 및 파일 그룹](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 기본 파일 그룹만 해당 | 예. 파일 경로가 자동으로 할당되고 파일 위치를 `ALTER DATABASE ADD FILE` [문으로](sql-database-managed-instance-transact-sql-information.md#alter-database-statement)지정할 수 없습니다.  |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 예 | [아니요](sql-database-managed-instance-transact-sql-information.md#filestream-and-filetable) |
| [전체 텍스트 검색(FTS)](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  예, 하지만 타사 단어 차단기는 지원 되지 않습니다. | 예, 하지만 [타사 단어 차단기는 지원 되지 않습니다.](sql-database-managed-instance-transact-sql-information.md#full-text-semantic-search) |
| [함수](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) 참조 |
| [메모리 내 최적화](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 예 - [프리미엄 및 비즈니스 크리티컬 계층은](sql-database-in-memory.md) 테이블 유형과 같은 비영구 메모리 내 개체에 대해서만 제한된 지원 | 예 - [중요 비즈니스용 계층만 해당](sql-database-managed-instance.md) |
| [언어 요소](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 대부분 - 개별 요소 참조 |  예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [연결된 서버](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](sql-database-elastic-query-horizontal-partitioning.md) 참조 | 예. 분산 트랜잭션이 없는 [SQL Server 및 SQL 데이터베이스에만](sql-database-managed-instance-transact-sql-information.md#linked-servers) 적용됩니다. |
| 파일에서 읽은 연결된 서버(CSV, Excel) [Linked servers](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine)| 아니요. CSV 형식의 대안으로 [벌크 삽입](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 또는 [OPENROWSET을](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 사용합니다. | 아니요. CSV 형식의 대안으로 [벌크 삽입](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 또는 [OPENROWSET을](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 사용합니다. [관리되는 인스턴스 피드백 항목에서](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources) 이 요청 추적|
| [로그 배송](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [고가용성](sql-database-high-availability.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. | 기본적으로 DMS 마이그레이션 프로세스의 일부로 내장되어 있습니다. 다른 고가용성 방법은 모든 데이터베이스에 포함되어 있으므로 [고가용성](sql-database-high-availability.md) 솔루션으로 사용할 수 없으며 로그 배송을 HA 대안으로 사용하지 않는 것이 좋습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요에서](sql-database-business-continuity.md)설명합니다. 데이터베이스 간의 복제 메커니즘으로 사용할 수 없음 - [비즈니스 중요 계층,](sql-database-service-tier-business-critical.md) [자동 장애 조치 그룹](sql-database-auto-failover-group.md)또는 트랜잭션 복제에서 보조 [복제본을](sql-database-managed-instance-transactional-replication.md) 대안으로 사용합니다. |
| [로그인 및 사용자](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | 예. `CREATE` 그러나 `ALTER` 로그인 문은 모든 옵션을 제공하지 않습니다(Windows 및 서버 수준 Azure Active Directory 로그인 없음). `EXECUTE AS LOGIN`대신 사용할 수 `EXECUTE AS USER` 없습니다.  | 예, 몇 가지 [차이점](sql-database-managed-instance-transact-sql-information.md#logins-and-users). Windows 로그인은 지원되지 않으며 Azure Active Directory 로그인으로 대체해야 합니다. |
| [대량 가져오기에서 최소 로깅](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 아니요, 전체 복구 모델만 지원됩니다. | 아니요, 전체 복구 모델만 지원됩니다. |
| [시스템 데이터 수정](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 예 | yes |
| [OLE 자동화](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 예 | 예 |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|예|예. 다른 Azure SQL 데이터베이스 및 SQL 서버에만 있습니다. [T-SQL 차이](sql-database-managed-instance-transact-sql-information.md) 참조|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|예|예. 다른 Azure SQL 데이터베이스 및 SQL 서버에만 있습니다. [T-SQL 차이](sql-database-managed-instance-transact-sql-information.md) 참조|
| [Openrowset](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|예. Azure Blob 저장소에서 가져올 때만 가능합니다. |예. 다른 Azure SQL 데이터베이스 및 SQL 서버로만 가져오고 Azure Blob 저장소에서 가져올 수 있습니다. [T-SQL 차이](sql-database-managed-instance-transact-sql-information.md) 참조|
| [연산자](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 대부분 - 개별 연산자 참조 |예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [폴리베이스](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 아니요. 함수를 사용하여 `OPENROWSET` Azure Blob Storage에 배치된 파일의 데이터를 쿼리할 수 있습니다. | 아니요. 함수를 사용하여 `OPENROWSET` Azure Blob Storage에 배치된 파일의 데이터를 쿼리할 수 있습니다. |
| [쿼리 알림](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | 예 | yes |
| [머신 러닝 서비스](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)_(이전 R 서비스)_| 예, [공개 미리 보기 상태](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | 예 |
| [복구 모델](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | 고가용성을 보장하는 전체 복구만 지원됩니다. 단순 및 대량 기록 복구 모델은 사용할 수 없습니다. | 고가용성을 보장하는 전체 복구만 지원됩니다. 단순 및 대량 기록 복구 모델은 사용할 수 없습니다. | 
| [리소스 관리자](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 예 | yes |
| [RESTORE 문](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 예 | 예. Azure `FROM URL` Blob 저장소에 배치된 백업 파일에 대한 필수 옵션이 있습니다. [차이점 복원](sql-database-managed-instance-transact-sql-information.md#restore-statement) 참조 |
| [백업에서 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 자동 백업에서만 - [SQL Database 복구](sql-database-recovery-using-backups.md) 참조 | 자동화된 백업에서 - [SQL 데이터베이스 복구](sql-database-recovery-using-backups.md) 및 Azure Blob 저장소에 배치된 전체 백업에서 참조 - 백업 [차이점](sql-database-managed-instance-transact-sql-information.md#backup) 참조 |
| [데이터베이스를 SQL 서버로 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 아니요. 기본 복원 대신 BACPAC 또는 BCP를 사용합니다. | 아니요, 관리되는 인스턴스에 사용되는 SQL Server 데이터베이스 엔진은 온-프레미스에서 사용되는 모든 RTM 버전의 SQL Server 버전보다 버전이 더 높습니다. 대신 BACPAC, BCP 또는 트랜잭션 복제를 사용합니다. |
| [의미 체계 검색](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 예 | 예 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 예 | 예, 하지만 인스턴스 내에서만 가능합니다. 원격 서비스 브로커 경로를 사용하는 경우 여러 분산 SQL Server 인스턴스의 데이터베이스를 마이그레이션 중에 관리되는 하나의 인스턴스로 통합하고 로컬 경로만 사용하십시오. [서비스 브로커 차이](sql-database-managed-instance-transact-sql-information.md#service-broker) 참조 |
| [서버 구성 설정](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 예 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [Set 문](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조|
| [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 아니요 - [탄력적 작업](elastic-jobs-overview.md) 참조 | 예 - [SQL Server Agent 차이점](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) 참조 |
| [SQL Server 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 아니요 - [SQL Database 감사](sql-database-auditing.md) 참조 | 예 - [감사 차이점](sql-database-managed-instance-transact-sql-information.md#auditing) 참조 |
| [시스템 저장 함수](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) 참조 |
| [시스템 저장 절차](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 일부 - 개별 저장 프로시저 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) 참조 |
| [시스템 테이블](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 일부 - 개별 테이블 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [시스템 카탈로그 뷰](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 일부 - 개별 뷰 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [Tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database) | 예. [모든 데이터베이스에 대한 코어 당 32GB 크기.](sql-database-vcore-resource-limits-single-databases.md) | 예. [전체 GP 계층에 대해 vCore당 24GB 크기, BC 계층의 인스턴스 크기로 제한](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)  |
| [임시 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 로컬 및 데이터베이스 범위 전역 임시 테이블 | 로컬 및 인스턴스 범위 전역 임시 테이블 |
| 표준 시간대 선택 | 예 | [예,](sql-database-managed-instance-timezone.md)관리되는 인스턴스를 만들 때 구성해야 합니다. |
| [추적 플래그](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 예 | 예. 하지만 전역 추적 플래그집합만 제한됩니다. [DBCC 차이점](sql-database-managed-instance-transact-sql-information.md#dbcc) 보기 |
| [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md) | 예. [트랜잭션 및 스냅숏 복제 구독자만](sql-database-single-database-migrate.md) | 예, [공개 미리 보기에서](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance). [제약 조건은 여기를](sql-database-managed-instance-transact-sql-information.md#replication)참조하십시오. |
| [투명 데이터 암호화(TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 예 - 범용 및 중요 비즈니스 서비스 계층만 해당| [예](transparent-data-encryption-azure-sql.md) |
| Windows 인증 | 예 | 예 |
| [Windows Server 장애 조치(failover) 클러스터링](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 아니요. [고가용성을](sql-database-high-availability.md) 제공하는 다른 기술은 모든 데이터베이스에 포함되어 있습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. | 아니요. [고가용성을](sql-database-high-availability.md) 제공하는 다른 기술은 모든 데이터베이스에 포함되어 있습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. |

## <a name="platform-capabilities"></a>플랫폼 기능

Azure 플랫폼은 표준 데이터베이스 기능에 추가 값으로 추가되는 여러 PaaS 기능을 제공합니다. Azure SQL Database 서비스와 함께 사용할 수 있는 여러 외부 서비스가 있습니다. 

| **플랫폼 기능** | **단일 데이터베이스 및 탄력적 풀** | **관리되는 인스턴스 및 인스턴스 풀** |
| --- | --- | --- |
| [활성 지역 복제](sql-database-active-geo-replication.md) | 예 - 하이퍼스케일을 제외한 모든 서비스 계층 | 아니요, [자동 장애 조치 그룹이](sql-database-auto-failover-group.md) 대안으로 표시됩니다. |
| [자동 장애 조치 그룹](sql-database-auto-failover-group.md) | 예 - 하이퍼스케일을 제외한 모든 서비스 계층 | 예, [자동 장애 조치 그룹](sql-database-auto-failover-group.md) 참조|
| 자동 크기 조정 | 예, 하지만 [서버리스 모델에서만](sql-database-serverless.md). 서버가 없는 모델에서는 서비스 계층(vCore, 저장소 또는 DTU 변경)의 변경이 빠르고 온라인상태입니다. 서비스 계층 을 변경하려면 가동 중지 시간을 최소화하거나 전혀 필요하지 않습니다. | 아니요, 예약된 계산 및 저장소를 선택해야 합니다. 서비스 계층(vCore 또는 최대 저장소)의 변경은 온라인 이며 가동 중지 시간을 최소화 하거나 전혀 필요 합니다. |
| [자동 백업](sql-database-automated-backups.md) | 예. 전체 백업은 7일마다, 차등 12시간마다 수행되며, 5-10분마다 로그 백업이 수행됩니다. | 예. 전체 백업은 7일마다, 차등 12시간마다 수행되며, 5-10분마다 로그 백업이 수행됩니다. |
| [자동 조정(인덱스)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [예](sql-database-automatic-tuning.md)| 예 |
| [가용성 영역](/azure/availability-zones/az-overview) | yes | 예 |
| [Azure Resource Health](/azure/service-health/resource-health-overview) | yes | 예 |
| Backup 보존 | 예. 7일 기본값, 최대 35일. | 예. 7일 기본값, 최대 35일. |
| [DMS(데이터 마이그레이션 서비스)](https://docs.microsoft.com/sql/dma/dma-overview) | yes | yes |
| 파일 시스템 액세스 | 아니요. [벌크 삽입](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 또는 [OPENROWSET을](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) 사용하여 Azure Blob Storage의 데이터에 액세스하고 로드하는 대신 | 아니요. [벌크 삽입](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 또는 [OPENROWSET을](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) 사용하여 Azure Blob Storage의 데이터에 액세스하고 로드하는 대신 |
| [지오 복원](sql-database-recovery-using-backups.md#geo-restore) | 예 - 하이퍼스케일을 제외한 모든 서비스 계층 | 예 - 하이퍼스케일을 제외한 모든 서비스 계층 |
| [하이퍼스케일 아키텍처](sql-database-service-tier-hyperscale.md) | yes | 예 |
| [장기 백업 보존 - LTR](sql-database-long-term-retention.md) | 예. 최대 10년까지 자동으로 백업을 수행합니다. | 아직 없습니다. 임시 `COPY_ONLY` 해결 해결 로 [수동 백업을](sql-database-managed-instance-transact-sql-information.md#backup) 사용 합니다. |
| 일시 중지/다시 시작 | 예, [서버리스 모델](sql-database-serverless.md) | 예 | 
| [정책 기반 관리](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 예 | 예 |
| 공용 IP 주소 | 예. 방화벽 또는 서비스 끝점을 사용하여 액세스를 제한할 수 있습니다.  | 예. 명시적으로 활성화해야 하며 NSG 규칙에서 포트 3342를 사용하도록 설정해야 합니다. 필요한 경우 공용 IP를 사용하지 않도록 선택할 수 있습니다. 자세한 내용은 [공용 끝점을](sql-database-managed-instance-public-endpoint-securely.md) 참조하십시오. | 
| [지정 시간 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 예 - 하이퍼스케일이외의 모든 서비스 계층 - [SQL 데이터베이스 복구](sql-database-recovery-using-backups.md#point-in-time-restore) 참조 | 예 - [SQL Database 복구](sql-database-recovery-using-backups.md#point-in-time-restore) 참조 |
| 리소스 풀 | 예, [탄력적 풀로](sql-database-elastic-pool.md) | 예. 단일 관리인스턴스에는 동일한 리소스 풀을 공유하는 여러 데이터베이스가 있을 수 있습니다. 또한 리소스를 공유할 수 있는 [인스턴스 풀(미리 보기)에](sql-database-instance-pools.md) 여러 관리되는 인스턴스를 배포할 수 있습니다. |
| 확장 또는 축소(온라인) | 예. 최소한의 가동 중지 시간으로 DTU 또는 예약 vCores 또는 최대 스토리지를 변경할 수 있습니다. | 예. 최소한의 가동 중지 시간으로 예약된 vCores 또는 최대 저장소를 변경할 수 있습니다. |
| [SQL 별칭](https://docs.microsoft.com/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) | 아니요, [DNS 별칭](dns-alias-overview.md) 사용 | 아니요, [Clicongf를](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/Lesson-Learned-33-How-to-make-quot-cliconfg-quot-to-work-with/ba-p/369022) 사용하여 클라이언트 컴퓨터에서 별칭을 설정합니다. |
| [SQL 분석](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | yes | yes |
| [SQL 데이터 동기화](sql-database-get-started-sql-data-sync.md) | yes | 예 |
| [SSAS(SQL Server Analysis Services)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 아니요, [Azure 분석 서비스는](https://azure.microsoft.com/services/analysis-services/) 별도의 Azure 클라우드 서비스입니다. | 아니요, [Azure 분석 서비스는](https://azure.microsoft.com/services/analysis-services/) 별도의 Azure 클라우드 서비스입니다. |
| [SQL 서버 통합 서비스(SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 예, ADF(Azure Data Factory) 환경의 관리 SSIS를 사용합니다. 여기서 패키지는 Azure SQL Database에서 호스트되는 SSISDB에 저장되고 Azure SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. <br/><br/>SQL Database 서버 및 관리 인스턴스의 SSIS 기능을 비교하려면 [Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리되는 인스턴스 비교를](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)참조하십시오. | 예, ADF(Azure Data Factory) 환경의 관리 SSIS를 사용합니다. 여기서 패키지는 Managed Instance에서 호스트되는 SSISDB에 저장되고 Azure SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. <br/><br/>SQL 데이터베이스 및 관리되는 인스턴스의 SSIS 기능을 비교하려면 [Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리되는 인스턴스 비교를](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)참조하십시오. |
| [SSRS(SQL Server Reporting Services)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 아니요 - [Power BI 참조](https://docs.microsoft.com/power-bi/) | 아니요 - [Power BI 참조](https://docs.microsoft.com/power-bi/) |
| [쿼리 성능 인사이트(QPI)](sql-database-query-performance.md) | yes | 아니요. SQL 서버 관리 스튜디오 및 Azure 데이터 스튜디오에서 기본 제공 보고서를 사용합니다. |
| [VNet (주)V넷](../virtual-network/virtual-networks-overview.md) | 부분, 그것은 [VNet 끝점을](sql-database-vnet-service-endpoint-rule-overview.md) 사용하여 제한된 액세스를 가능하게 | 예. 관리되는 인스턴스는 고객의 VNet에 주입됩니다. [서브넷](sql-database-managed-instance-transact-sql-information.md#subnet) 및 [VNet](sql-database-managed-instance-transact-sql-information.md#vnet) 보기 |
| VNet 서비스 끝점 | [예](sql-database-vnet-service-endpoint-rule-overview.md) | 예 |
| VNet 글로벌 피어링 | 예. [개인 IP 및 서비스 끝점을](sql-database-vnet-service-endpoint-rule-overview.md) 사용 하 여 | 아니요, [관리되는 인스턴스는](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) [VNet 전역 피어링의 로드 밸런서 제약 조건으로](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)인해 지원되지 않습니다.

## <a name="tools"></a>도구
Azure SQL 데이터베이스는 데이터를 관리하는 데 도움이 되는 다양한 데이터 도구를 지원합니다.

| **도구** | **단일 데이터베이스 및 탄력적 풀** | **관리되는 인스턴스 및 인스턴스 풀** |
| --- | --- | --- |
| Azure portal | yes | yes |
| Azure CLI | yes | yes|
| [Azure 데이터 스튜디오](https://docs.microsoft.com/sql/azure-data-studio/what-is) | yes | yes |
| Azure Powershell | yes | yes |
| [BACPAC 파일(내보내기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 예 - [SQL Database 내보내기](sql-database-export.md) 참조 | 예 - [SQL Database 내보내기](sql-database-export.md) 참조 |
| [BACPAC 파일(가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 예 - [SQL Database 가져오기](sql-database-import.md) 참조 | 예 - [SQL Database 가져오기](sql-database-import.md) 참조 |
| [DQS(Data Quality Services)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 예 | 예 |
| [MDS(Master Data Services)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 예 | 예 |
| [Smo](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [예](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 예 [버전 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SQL 서버 데이터 도구(SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | yes | yes |
| [SQL 서버 관리 스튜디오(SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | yes | 예 [버전 18.0 이상](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | yes | yes |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 아니요 - [확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 | yes |
| [시스템 센터 운영 관리자(SCOM)](https://docs.microsoft.com/system-center/scom/welcome) | [예](https://www.microsoft.com/download/details.aspx?id=38829) | 예, [미리 보기](https://www.microsoft.com/download/details.aspx?id=100306) |

## <a name="migration-methods"></a>마이그레이션 방법

다른 마이그레이션 방법을 사용하여 SQL Server, 단일 데이터베이스 및 관리되는 인스턴스 데이터베이스 간에 데이터를 이동할 수 있습니다. 일부 메서드는 **온라인** 및 마이그레이션을 실행 하는 동안 원본에서 만든 모든 변경 내용을 선택, **오프라인** 메서드에서 마이그레이션이 진행 되는 동안 원본에 데이터를 수정 하는 작업 부하를 중지 해야 합니다.

| **원본** | **단일 데이터베이스 및 탄력적 풀** | **관리되는 인스턴스 및 인스턴스 풀** |
| --- | --- | --- |
| SQL 서버(온프레미, AzureVM, 아마존 RDS) | **온라인:** [데이터 마이그레이션 서비스(DMS)](https://docs.microsoft.com/sql/dma/dma-overview), 트랜잭션 [복제](sql-database-managed-instance-transactional-replication.md) <br/> **오프라인:** [BACPAC 파일(가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP | **온라인:** [데이터 마이그레이션 서비스(DMS)](https://docs.microsoft.com/sql/dma/dma-overview), 트랜잭션 [복제](sql-database-managed-instance-transactional-replication.md) <br/> **오프라인:** 네이티브 백업/복원, [BACPAC 파일(가져오기),](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)BCP, [스냅샷 복제](sql-database-managed-instance-transactional-replication.md) |
| 단일 데이터베이스 | **오프라인:** [BACPAC 파일(가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP | **오프라인:** [BACPAC 파일(가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP |
| 관리되는 인스턴스 | **온라인:** [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md) <br/> **오프라인:** [BACPAC 파일(가져오기),](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)BCP, [스냅샷 복제](sql-database-managed-instance-transactional-replication.md) | **온라인:** [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md) <br/> **오프라인:** 교차 인스턴스 시점 복원[(Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase?#examples) 또는 [Azure CLI),](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Cross-instance-point-in-time-restore-in-Azure-SQL-Database/ba-p/386208) [네이티브 백업/복원,](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) [BACPAC 파일(가져오기),](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)BCP, [스냅숏 복제](sql-database-managed-instance-transactional-replication.md) |

## <a name="next-steps"></a>다음 단계

Microsoft는 Azure SQL Database에 계속해서 기능을 추가하고 있습니다. Azure용 서비스 업데이트 웹 페이지에서 다음 필터를 사용하여 최신 업데이트를 확인하세요.

- [SQL Database 서비스](https://azure.microsoft.com/updates/?service=sql-database)에 대해 필터링되었습니다.
- SQL Database 기능의 [GA(General Availability) 공지](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 에 대해 필터링되었습니다.

Azure SQL Database 맛에 대한 자세한 내용은 다음을 참조하십시오.
- [SQL Database 정의](sql-database-technical-overview.md)
- [Managed Instance란?](sql-database-managed-instance.md)
- [관리되는 인스턴스 풀이란 무엇입니까?](sql-database-instance-pools.md)
