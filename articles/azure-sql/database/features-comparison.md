---
title: SQL Database 및 SQL Managed Instance의 데이터베이스 엔진 기능 비교
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 이 문서에서는 Azure SQL Database 및 Azure SQL의 데이터베이스 엔진 기능을 비교 Managed Instance
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
ms.date: 06/25/2020
ms.openlocfilehash: e48a027af70ec9d002ddcfbb2ee36ded4ca7875a
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983522"
---
# <a name="features-comparison-azure-sql-database-and-azure-sql-managed-instance"></a>기능 비교: Azure SQL Database 및 Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 SQL Managed Instance은 안정적인 최신 버전의 SQL Server를 사용 하 여 공통 코드 베이스를 공유 합니다. 대부분의 표준 SQL 언어, 쿼리 처리 및 데이터베이스 관리 기능은 동일 합니다. SQL Server와 SQL Database 또는 SQL Managed Instance 간의 일반적인 기능은 다음과 같습니다.

- 언어 기능- [흐름 언어 키워드](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow), [커서](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql), [데이터 형식](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql), [DML 문](https://docs.microsoft.com/sql/t-sql/queries/queries), [조건자](https://docs.microsoft.com/sql/t-sql/queries/predicates), [시퀀스 번호](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers), [저장 프로시저](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)및 [변수](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql)를 제어 합니다.
- 데이터베이스 기능- [자동 조정 (강제 적용)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning), [변경 내용 추적](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), [데이터베이스 데이터 정렬](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation), [포함 된 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases), [포함 된 사용자](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [데이터 압축](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression), [데이터베이스 구성 설정](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), [온라인 인덱스 작업](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online), [분할](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes)및 [임시 테이블](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) ([시작 가이드 참조](../temporal-tables.md))
- 보안 기능- [응용 프로그램 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles), [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ([시작 가이드 참조](dynamic-data-masking-overview.md)), [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)및 위협 검색- [SQL Database](threat-detection-configure.md) 및 [SQL Managed Instance](../managed-instance/threat-detection-configure.md)의 시작 가이드를 참조 하세요.
- 다중 모델 기능- [그래프 처리](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview), [JSON 데이터](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) ([시작 가이드 참조](json-features.md)), [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql), [공간](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server), [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)및 [XML 인덱스](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql)입니다.

Azure는 데이터베이스를 관리 하 고 고가용성을 보장 합니다. 고가용성에 영향을 주거나 PaaS 세계에서 사용할 수 없는 일부 기능에는 SQL Database 및 SQL Managed Instance에서 제한 된 기능이 있습니다. 이러한 기능은 아래 표에 설명 되어 있습니다. 차이점에 대 한 자세한 내용이 필요한 경우 [Azure SQL Database](../managed-instance/transact-sql-tsql-differences-sql-server.md) 또는 [Azure SQL Managed Instance](../managed-instance/transact-sql-tsql-differences-sql-server.md)에 대 한 별도의 페이지에서 찾을 수 있습니다.

## <a name="features-of-sql-database-and-sql-managed-instance"></a>SQL Database 및 SQL Managed Instance의 기능

다음 표에서는 SQL Server의 주요 기능을 나열 하 고 기능에 대 한 자세한 정보를 제공 하는 링크를 통해 Azure SQL Database 및 Azure SQL Managed Instance에서 기능을 부분적으로 지원 하는지 또는 완전히 지원 하는지에 대 한 정보를 제공 합니다.

| **기능** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 예 - [인증서 저장소](always-encrypted-certificate-store-configure.md) 및 [키 자격 증명 모음](always-encrypted-azure-key-vault-configure.md) 참조 | 예 - [인증서 저장소](always-encrypted-certificate-store-configure.md) 및 [키 자격 증명 모음](always-encrypted-azure-key-vault-configure.md) 참조 |
| [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [99.995%](high-availability-sla.md) 의 모든 데이터베이스에 대해 99.99%의 가용성을 보장 합니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명합니다. | [99.99.](high-availability-sla.md) 모든 데이터베이스에 대해 가용성을 보장 하 고 [사용자가 관리할 수 없습니다](../managed-instance/transact-sql-tsql-differences-sql-server.md#availability). 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명 합니다. [자동 장애 조치 (failover) 그룹](auto-failover-group-overview.md) 을 사용 하 여 다른 지역에서 보조 SQL Managed Instance를 구성 합니다. SQL Server 인스턴스와 SQL Database는 SQL Managed Instance 보조 항목으로 사용할 수 없습니다. |
| [데이터베이스 연결](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 아니요 | 아니요 |
| [감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [예](auditing-overview.md)| [예](../managed-instance/auditing-configure.md), 몇 가지 [차이점이](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing) 있습니다. |
| [Azure Active Directory (Azure AD) 인증](authentication-aad-overview.md) | 예. Azure AD 사용자만 | 예. 서버 수준 Azure AD 로그인을 포함 합니다. |
| [BACKUP 명령](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 아니요. 시스템에서 시작한 자동 백업만 - [자동 백업](automated-backups-overview.md) 참조 | 예, 사용자가 Azure Blob storage로 복사 전용 백업을 시작 했습니다 (사용자가 자동 시스템 백업을 시작할 수 없음). [백업 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) 참조 |
| [기본 제공 함수](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) 참조 |
| [BULK INSERT 문](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 예, 하지만 Azure Blob storage에서 원본으로만 가능 합니다. | 예, 하지만 원본으로 Azure Blob Storage 에서만 [차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)을 참조 하세요. |
| [인증서 및 비대칭 키](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 예 `BACKUP` . 및 작업에 대 한 파일 시스템에 액세스 하지 않습니다 `CREATE` . | 예. 및 작업용 파일 시스템에 대 한 액세스 권한이 없는 경우 `BACKUP` `CREATE` [인증서 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#certificates)을 참조 하세요. |
| [변경 데이터 캡처-CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 예 | 예 |
| [데이터 정렬 - 서버/인스턴스](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | 아니요, 기본 서버 데이터 정렬이 `SQL_Latin1_General_CP1_CI_AS` 항상 사용 됩니다. | 예. [인스턴스를 만들](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md) 때 설정할 수 있으며 나중에 업데이트할 수 없습니다. |
| [columnstore 인덱스](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 예- [프리미엄 계층, 표준 계층-S3 이상, 범용 계층, 중요 비즈니스용 및 HyperScale 계층](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |예 |
| [CLR (공용 언어 런타임)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 아니요 | 예, 하지만 문에서 파일 시스템에 액세스 하지 않습니다. `CREATE ASSEMBLY` [CLR 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#clr) 을 참조 하세요. |
| [자격 증명](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | 예, 하지만 [데이터베이스 범위 자격 증명만](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) | 예, 하지만 **Azure Key Vault** 만 `SHARED ACCESS SIGNATURE` 지원 됩니다. [자세한](../managed-instance/transact-sql-tsql-differences-sql-server.md#credential) 내용은 |
| [데이터베이스 간/세 부분으로 구성 되는 이름 쿼리](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](elastic-query-overview.md) 참조 | 예. 추가적으로 [탄력적 쿼리](elastic-query-overview.md) |
| [데이터베이스 간 트랜잭션](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 | 예, 인스턴스 내에 있습니다. 인스턴스 간 쿼리에 대 한 [연결 된 서버 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers) 을 참조 하세요. |
| [데이터베이스 메일-DbMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 예 | 예 |
| [데이터베이스 미러링](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 아니요 | [아니요](../managed-instance/transact-sql-tsql-differences-sql-server.md#database-mirroring) |
| [데이터베이스 스냅숏](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 아니요 | 아니요 |
| [DBCC 문](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 대부분 - 개별 문 참조 | 예 - [DBCC 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc) 참조 |
| [DDL 문](https://docs.microsoft.com/sql/t-sql/statements/statements) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [DDL 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 데이터베이스에만 해당 |  예 |
| [분산된 파티션 뷰](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 예 | 예 |
| [분산된 트랜잭션 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 아니요 - [탄력적 트랜잭션](elastic-transactions-overview.md) 참조 |  아니요- [연결 된 서버 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)을 참조 하세요. 마이그레이션하는 동안 여러 분산 SQL Server 인스턴스에서 데이터베이스를 하나의 SQL Managed Instance 통합 해 보세요. |
| [DML 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 대부분 - 개별 문 참조 |  예 |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 대부분 - 개별 DMV 참조 |  예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [이벤트 알림](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 아니요 - [경고](alerts-insights-configure-portal.md) 참조 | 아니요 |
| [식](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |예 | 예 |
| [확장 이벤트 (XEvent)](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 일부 - [SQL Database의 확장 이벤트](xevent-db-diff-from-svr.md) 참조 | 예 - [확장 이벤트 차이](../managed-instance/transact-sql-tsql-differences-sql-server.md#extended-events) 참조 |
| [확장된 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 아니요 | 아니요 |
| [파일 및 파일 그룹](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 기본 파일 그룹만 해당 | 예. 파일 경로는 자동으로 할당 되며 문에서 파일 위치를 지정할 수 없습니다 `ALTER DATABASE ADD FILE` [statement](../managed-instance/transact-sql-tsql-differences-sql-server.md#alter-database-statement).  |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 아니요 | [아니요](../managed-instance/transact-sql-tsql-differences-sql-server.md#filestream-and-filetable) |
| [전체 텍스트 검색 (FT)](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  예, 하지만 타사 단어 분리기는 지원 되지 않습니다. | 예, 하지만 [타사 단어 분리기는 지원 되지 않습니다](../managed-instance/transact-sql-tsql-differences-sql-server.md#full-text-semantic-search) . |
| [함수](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) 참조 |
| [메모리 내 최적화](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 예- [프리미엄 및 중요 비즈니스용 계층은](../in-memory-oltp-overview.md) 테이블 형식과 같은 비영구 메모리 내 개체만을 제한적으로 지원 합니다. | 예 - [중요 비즈니스용 계층만 해당](../managed-instance/sql-managed-instance-paas-overview.md) |
| [언어 요소](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 대부분 - 개별 요소 참조 |  예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [연결된 서버](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](elastic-query-horizontal-partitioning.md) 참조 | 예. 분산 트랜잭션이 없는 [SQL Server 및 SQL Database](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers) 에만 사용 됩니다. |
| 파일에서 읽은 [연결 된 서버](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) (CSV, Excel)| 아니요. CSV 형식 대신 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 또는 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 을 사용 합니다. | 아니요. CSV 형식 대신 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 또는 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 을 사용 합니다. [SQL Managed Instance 피드백 항목](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources) 에서 이러한 요청 추적|
| [로그 전달](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [고가용성](high-availability-sla.md)은 모든 데이터베이스에 포함됩니다. 재해 복구에 대해서는 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명 합니다. | Azure 데이터 마이그레이션 서비스 마이그레이션 프로세스의 일부로 기본적으로 제공 됩니다. 고가용성 솔루션으로는 사용할 수 없습니다 [. 다른 고가용성](high-availability-sla.md) 방법이 모든 데이터베이스에 포함 되어 있으므로 로그 전달을 HA 대체로 사용 하지 않는 것이 좋습니다. 재해 복구에 대해서는 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명 합니다. 데이터베이스 간 복제 메커니즘으로 사용할 수 없음- [중요 비즈니스용 계층](service-tier-business-critical.md), [자동 장애 조치 (failover) 그룹](auto-failover-group-overview.md)또는 [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) 에 보조 복제본을 사용 합니다. |
| [로그인 및 사용자](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | 예, 하지만 `CREATE` 및 `ALTER` 로그인 문은 모든 옵션을 제공 하지 않습니다 (Windows 및 서버 수준 Azure Active Directory 로그인). `EXECUTE AS LOGIN`지원 되지 않음-대신을 사용 `EXECUTE AS USER` 합니다.  | 예, 몇 가지 [차이점이](../managed-instance/transact-sql-tsql-differences-sql-server.md#logins-and-users)있습니다. Windows 로그인은 지원 되지 않으며 Azure Active Directory 로그인으로 바꾸어야 합니다. |
| [대량 가져오기에서 최소 로깅](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 아니요, 전체 복구 모델만 지원 됩니다. | 아니요, 전체 복구 모델만 지원 됩니다. |
| [시스템 데이터 수정](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 예 | 예 |
| [OLE Automation](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 아니요 | 아니요 |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|아니요|예, SQL Database, SQL Managed Instance 및 SQL Server에만 해당 합니다. [T-sql 차이점을](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 하세요.|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|아니요|예, SQL Database, SQL Managed Instance 및 SQL Server에만 해당 합니다. [T-sql 차이점을](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 하세요.|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|예, Azure Blob 저장소에서 가져옵니다. |예, SQL Database, SQL Managed Instance 및 SQL Server 뿐 아니라 Azure Blob 저장소에서 가져옵니다. [T-sql 차이점을](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 하세요.|
| [연산자](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 대부분 - 개별 연산자 참조 |예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 아니요. 함수를 사용 하 여 Azure Blob Storage에 배치 된 파일의 데이터를 쿼리할 수 있습니다 `OPENROWSET` . | 아니요. 함수를 사용 하 여 Azure Blob Storage에 배치 된 파일의 데이터를 쿼리할 수 있습니다 `OPENROWSET` . |
| [쿼리 알림](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | 예 | 예 |
| [Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)(_이전의 R Services_)| 예, [공개 미리 보기 상태](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | 아니요 |
| [복구 모델](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | 고가용성을 보장 하는 전체 복구만 지원 됩니다. 단순 및 대량 로그 복구 모델을 사용할 수 없습니다. | 고가용성을 보장 하는 전체 복구만 지원 됩니다. 단순 및 대량 로그 복구 모델을 사용할 수 없습니다. |
| [리소스 관리자](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 예 | 예 |
| [RESTORE 문](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 아니요 | 예, `FROM URL` Azure Blob Storage에 배치 된 백업 파일에 대 한 필수 옵션을 사용 합니다. [복원 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#restore-statement) 을 참조 하세요. |
| [백업에서 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 자동 백업에서만 - [SQL Database 복구](recovery-using-backups.md) 참조 | 자동화 된 백업에서 [SQL Database 복구](recovery-using-backups.md) 및 Azure Blob Storage에 배치 된 전체 백업에서 [백업 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) 참조 |
| [데이터베이스를 SQL Server으로 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 아니요. 네이티브 복원 대신 BACPAC 또는 BCP를 사용 합니다. | 아니요. SQL Managed Instance에서 사용 되는 SQL Server 데이터베이스 엔진의 버전은 온-프레미스에서 사용 되는 SQL Server RTM 버전 보다 높습니다. 대신 BACPAC, BCP 또는 트랜잭션 복제를 사용 하십시오. |
| [의미 체계 검색](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 아니요 | 아니요 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 아니요 | 예. 단, 인스턴스 내 에서만 가능 합니다. 원격 Service Broker 경로를 사용 하는 경우 마이그레이션 중에 여러 배포 된 SQL Server 인스턴스의 데이터베이스를 하나의 SQL Managed Instance로 통합 하 여 로컬 경로만 사용 하십시오. [Service Broker 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker) 을 참조 하세요. |
| [서버 구성 설정](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 아니요 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [Set 문](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조|
| [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 아니요 - [탄력적 작업](elastic-jobs-overview.md) 참조 | 예 - [SQL Server Agent 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent) 참조 |
| [SQL Server 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 아니요 - [SQL Database 감사](auditing-overview.md) 참조 | 예 - [감사 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing) 참조 |
| [시스템 저장 함수](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) 참조 |
| [시스템 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 일부 - 개별 저장 프로시저 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) 참조 |
| [시스템 테이블](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 일부 - 개별 테이블 참조 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [시스템 카탈로그 뷰](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 일부 - 개별 뷰 참조 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database) | 예. [32-모든 데이터베이스에 대해 코어 당 GB 크기](resource-limits-vcore-single-databases.md) | 예. [전체 GP 계층에 대 한 vCore 당 24 GB 크기 및 BC 계층의 인스턴스 크기로 제한](../managed-instance/resource-limits.md#service-tier-characteristics)  |
| [임시 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 로컬 및 데이터베이스 범위 전역 임시 테이블 | 로컬 및 인스턴스 범위 전역 임시 테이블 |
| 표준 시간대 선택 | 아니요 | [예](../managed-instance/timezones-overview.md), SQL Managed Instance를 만들 때 구성 해야 합니다. |
| [추적 플래그](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 아니요 | 예. 단, 제한 된 전역 추적 플래그 집합만 지원 됩니다. [DBCC 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc) 을 참조 하세요. |
| [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) | 예, [트랜잭션 및 스냅숏 복제 구독자만](migrate-to-database-from-sql-server.md) 해당 | 예, [공개 미리 보기](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)상태입니다. [여기](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)에서 제약 조건을 참조 하세요. |
| [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 예 - 범용 및 중요 비즈니스 서비스 계층만 해당| [예](transparent-data-encryption-tde-overview.md) |
| Windows 인증 | 아니요 | 아니요 |
| [Windows Server 장애 조치 (Failover) 클러스터링](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 아니요. [고가용성](high-availability-sla.md) 을 제공 하는 다른 기술은 모든 데이터베이스에 포함 되어 있습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명 합니다. | 아니요. [고가용성](high-availability-sla.md) 을 제공 하는 다른 기술은 모든 데이터베이스에 포함 되어 있습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명 합니다. |

## <a name="platform-capabilities"></a>플랫폼 기능

Azure 플랫폼은 표준 데이터베이스 기능에 추가 값으로 추가 된 다양 한 PaaS 기능을 제공 합니다. Azure SQL Database와 함께 사용할 수 있는 여러 외부 서비스가 있습니다.

| **플랫폼 기능** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| [활성 지리적 복제](active-geo-replication-overview.md) | 예-대규모 이외의 모든 서비스 계층 | 아니요, 대 안으로 [자동 장애 조치 (failover) 그룹](auto-failover-group-overview.md) 을 참조 하세요. |
| [자동 장애 조치 그룹](auto-failover-group-overview.md) | 예-대규모 이외의 모든 서비스 계층 | 예, [자동 장애 조치 그룹](auto-failover-group-overview.md) 을 참조 하세요.|
| 자동 크기 조정 | 예, 하지만 서버 리스 [모델](serverless-tier-overview.md)에서만 사용 됩니다. 서버를 사용 하지 않는 모델에서는 서비스 계층의 변경 (vCore, 저장소 또는 DTU 변경)이 빠르고 온라인 상태입니다. 서비스 계층을 변경 하려면 가동 중지 시간을 최소화 해야 합니다. | 아니요, 예약 된 계산 및 저장소를 선택 해야 합니다. 서비스 계층 (vCore 또는 max storage)의 변경은 온라인 상태 이며 가동 중지 시간을 최소화 하거나 거의 필요 하지 않습니다. |
| [자동 백업](automated-backups-overview.md) | 예. 전체 백업은 7 일, 차등 12 시간 및 로그 백업 마다 5-10 분 마다 수행 됩니다. | 예. 전체 백업은 7 일, 차등 12 시간 및 로그 백업 마다 5-10 분 마다 수행 됩니다. |
| [자동 조정(인덱스)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [예](automatic-tuning-overview.md)| 아니요 |
| [가용성 영역](/azure/availability-zones/az-overview) | 예 | 아니요 |
| [Azure Resource Health](/azure/service-health/resource-health-overview) | 예 | 아니요 |
| Backup 보존 | 예. 기본값은 7 일, 최대 35 일입니다. | 예. 기본값은 7 일, 최대 35 일입니다. |
| [DMS(데이터 마이그레이션 서비스)](https://docs.microsoft.com/sql/dma/dma-overview) | 예 | 예 |
| 파일 시스템 액세스 | 아니요. [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 또는 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) 을 사용 하 여 Azure Blob Storage에서 데이터에 액세스 하 고 해당 데이터를 로드 합니다. | 아니요. [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 또는 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) 을 사용 하 여 Azure Blob Storage에서 데이터에 액세스 하 고 해당 데이터를 로드 합니다. |
| [지역 복원](recovery-using-backups.md#geo-restore) | 예 | 예 |
| [하이퍼 확장 아키텍처](service-tier-hyperscale.md) | 예 | 아니요 |
| [장기 백업 보존-LTR](long-term-retention-overview.md) | 예, 자동으로 최대 10 년 동안 백업을 유지 합니다. | 아직은 연결할 수 없습니다. `COPY_ONLY`임시 해결 방법으로 [수동 백업을](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) 사용 합니다. |
| 일시 중지/다시 시작 | 예, [서버 리스 모델](serverless-tier-overview.md) 에서 | 아니요 |
| [정책 기반 관리](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 아니요 | 아니요 |
| 공용 IP 주소 | 예. 방화벽 또는 서비스 끝점을 사용 하 여 액세스를 제한할 수 있습니다.  | 예. 명시적으로 사용 하도록 설정 해야 하며, NSG 규칙에서 포트 3342를 사용 하도록 설정 해야 합니다. 필요한 경우 공용 IP를 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 [공용 끝점](../managed-instance/public-endpoint-overview.md) 을 참조 하세요. |
| [지정 시간 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 예-대규모 이외의 모든 서비스 계층- [SQL Database 복구](recovery-using-backups.md#point-in-time-restore) 참조 | 예 - [SQL Database 복구](recovery-using-backups.md#point-in-time-restore) 참조 |
| 리소스 풀 | 예, [탄력적 풀](elastic-pool-overview.md) 로 | 예. SQL Managed Instance의 단일 인스턴스에는 동일한 리소스 풀을 공유 하는 여러 데이터베이스가 있을 수 있습니다. 또한 리소스를 공유할 수 있는 [인스턴스 풀 (미리 보기)](../managed-instance/instance-pools-overview.md) 에 SQL Managed Instance의 여러 인스턴스를 배포할 수 있습니다. |
| 규모 확장 또는 축소 (온라인) | 예, 최소 가동 중지 시간으로 DTU 또는 예약 vCores 또는 max storage를 변경할 수 있습니다. | 예, 최소 가동 중지 시간으로 예약 된 vCores 또는 max storage를 변경할 수 있습니다. |
| [SQL 별칭](https://docs.microsoft.com/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) | 아니요, [DNS 별칭](dns-alias-overview.md) 을 사용 합니다. | 아니요, [Clicongf](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/Lesson-Learned-33-How-to-make-quot-cliconfg-quot-to-work-with/ba-p/369022) 를 사용 하 여 클라이언트 컴퓨터에서 별칭을 설정 합니다. |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | 예 | 예 |
| [SQL 데이터 동기화](sql-data-sync-sql-server-configure.md) | 예 | 아니요 |
| [SSAS(SQL Server Analysis Services)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 아니요, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 별도의 Azure 클라우드 서비스입니다. | 아니요, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 별도의 Azure 클라우드 서비스입니다. |
| [SSIS(SQL Server Integration Services)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 예, ADF(Azure Data Factory) 환경의 관리 SSIS를 사용합니다. 여기서 패키지는 Azure SQL Database에서 호스트되는 SSISDB에 저장되고 Azure SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. <br/><br/>SQL Database 및 SQL Managed Instance에서 SSIS 기능을 비교 하려면 [sql Managed Instance SQL Database 비교](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)를 참조 하세요. | 예, Azure Data Factory (ADF) 환경에서 관리 되는 SSIS를 사용 하는 경우 패키지가 SQL Managed Instance에서 호스트 되는 SSISDB에 저장 되 고 Azure SSIS Integration Runtime (IR)에서 실행 되는 경우 [adf에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조 하세요. <br/><br/>SQL Database 및 SQL Managed Instance에서 SSIS 기능을 비교 하려면 [sql Managed Instance SQL Database 비교](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)를 참조 하세요. |
| [SSRS(SQL Server Reporting Services)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 아니요 - [Power BI 참조](https://docs.microsoft.com/power-bi/) | 아니요 - [Power BI 참조](https://docs.microsoft.com/power-bi/) |
| [QPI (Query Performance Insights)](query-performance-insight-use.md) | 예 | 아니요. SQL Server Management Studio 및 Azure Data Studio에서 기본 제공 보고서를 사용 합니다. |
| [VNet](../../virtual-network/virtual-networks-overview.md) | 일부는 [VNet 끝점](vnet-service-endpoint-rule-overview.md) 을 사용 하 여 제한 된 액세스를 가능 하 게 합니다. | 예, SQL Managed Instance 고객의 VNet에 삽입 됩니다. [서브넷](../managed-instance/transact-sql-tsql-differences-sql-server.md#subnet) 및 [VNet](../managed-instance/transact-sql-tsql-differences-sql-server.md#vnet) 참조 |
| VNet 서비스 끝점 | [예](vnet-service-endpoint-rule-overview.md) | 아니요 |
| VNet 글로벌 피어 링 | 예, [개인 IP 및 서비스 끝점](vnet-service-endpoint-rule-overview.md) 사용 | 아니요. [VNet 글로벌 피어 링의 부하 분산 장치 제약 조건](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)으로 인해 [SQL Managed Instance 지원 되지](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 않습니다.

## <a name="tools"></a>도구

Azure SQL Database 및 Azure SQL Managed Instance는 데이터를 관리 하는 데 도움이 될 수 있는 다양 한 데이터 도구를 지원 합니다.

| **도구** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| Azure portal | 예 | 예 |
| Azure CLI | 예 | 예|
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | 예 | 예 |
| Azure Powershell | 예 | 예 |
| [BACPAC 파일(내보내기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 예 - [SQL Database 내보내기](database-export.md) 참조 | 예- [SQL Managed Instance 내보내기](database-export.md) 참조 |
| [BACPAC 파일(가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 예 - [SQL Database 가져오기](database-import.md) 참조 | 예- [SQL Managed Instance 가져오기](database-import.md) 참조 |
| [DQS(Data Quality Services)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 아니요 | 아니요 |
| [MDS(Master Data Services)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 아니요 | 아니요 |
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [예](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 예 [버전 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SSDT(SQL Server Data Tools)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | 예 | 예 |
| [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | 예 | 예 [버전 18.0 이상](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 예 | 예 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 아니요 - [확장 이벤트](xevent-db-diff-from-svr.md) 참조 | 예 |
| [System Center Operations Manager (SCOM)](https://docs.microsoft.com/system-center/scom/welcome) | [예](https://www.microsoft.com/download/details.aspx?id=38829) | 예, [미리 보기](https://www.microsoft.com/download/details.aspx?id=100306) |

## <a name="migration-methods"></a>마이그레이션 방법

서로 다른 마이그레이션 방법을 사용 하 여 SQL Server, Azure SQL Database 및 Azure SQL Managed Instance 간에 데이터를 이동할 수 있습니다. 일부 메서드는 **온라인** 상태 이며 마이그레이션을 실행 하는 동안 원본에 적용 된 모든 변경 내용을 선택 하지만, **오프 라인** 방법에서는 마이그레이션이 진행 되는 동안 원본에서 데이터를 수정 하는 작업을 중지 해야 합니다.

| **소스** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| SQL Server (온-프레미스, Add-azurevm, Amazon RDS) | **온라인:** [DMS (데이터 마이그레이션 서비스](https://docs.microsoft.com/sql/dma/dma-overview)), [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) <br/> **오프 라인:** [BACPAC 파일 (가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP | **온라인:** [DMS (데이터 마이그레이션 서비스](https://docs.microsoft.com/sql/dma/dma-overview)), [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) <br/> **오프 라인:** 네이티브 백업/복원, [BACPAC 파일 (가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP, [스냅숏 복제](../managed-instance/replication-transactional-overview.md) |
| 단일 데이터베이스 | **오프 라인:** [BACPAC 파일 (가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP | **오프 라인:** [BACPAC 파일 (가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP |
| SQL Managed Instance | **온라인:** [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) <br/> **오프 라인:** [BACPAC 파일 (가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP, [스냅숏 복제](../managed-instance/replication-transactional-overview.md) | **온라인:** [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) <br/> **오프 라인:** 인스턴스 간 지정 시간 복원 ([Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase?#examples) 또는 [Azure CLI](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Cross-instance-point-in-time-restore-in-Azure-SQL-Database/ba-p/386208)), [네이티브 백업/복원](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore), [BACPAC 파일 (가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP, [스냅숏 복제](../managed-instance/replication-transactional-overview.md) |

## <a name="next-steps"></a>다음 단계

Microsoft는 Azure SQL Database에 계속해서 기능을 추가하고 있습니다. Azure용 서비스 업데이트 웹 페이지에서 다음 필터를 사용하여 최신 업데이트를 확인하세요.

- [Azure SQL Database](https://azure.microsoft.com/updates/?service=sql-database)필터링 됩니다.
- SQL Database 기능의 [GA(General Availability) 공지](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 에 대해 필터링되었습니다.

Azure SQL Database 및 Azure SQL Managed Instance에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure SQL Database란?](sql-database-paas-overview.md)
- [Azure SQL Managed Instance 이란?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Azure SQL Managed Instance 풀 이란?](../managed-instance/instance-pools-overview.md)
