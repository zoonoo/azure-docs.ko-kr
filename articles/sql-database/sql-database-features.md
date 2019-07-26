---
title: Azure SQL Database 기능 비교 | Microsoft Docs
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
manager: craigg
ms.date: 05/10/2019
ms.openlocfilehash: 5bdbd9bebfb819ae18de884a014c574e12c53ebf
ms.sourcegitcommit: 83a89c45253b0d432ce8dcd70084c18e9930b1fd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371716"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>기능 비교: Azure SQL Database 및 SQL Server

Azure SQL Database는 SQL Server와 공용 코드 베이스를 공유합니다. Azure SQL Database에서 지원하는 SQL Server의 기능은 사용자가 만드는 Azure SQL 데이터베이스 형식에 따라 달라집니다. Azure SQL Database를 사용하면 데이터베이스를 [Managed Instance](sql-database-managed-instance.md) 또는 탄력적 풀의 일부분으로 만들거나 단일 데이터베이스로 만들 수 있습니다.

Microsoft는 Azure SQL Database에 계속해서 기능을 추가하고 있습니다. Azure용 서비스 업데이트 웹 페이지에서 다음 필터를 사용하여 최신 업데이트를 확인하세요.

- [SQL Database 서비스](https://azure.microsoft.com/updates/?service=sql-database)에 대해 필터링되었습니다.
- SQL Database 기능의 [GA(General Availability) 공지](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 에 대해 필터링되었습니다.

차이점에 대 한 자세한 내용이 필요한 경우 [단일 데이터베이스 및 탄력적 풀](sql-database-transact-sql-information.md) 에 대 한 별도의 페이지 또는 [Managed Instance](sql-database-managed-instance-transact-sql-information.md)에서 찾을 수 있습니다.

## <a name="sql-features"></a>SQL 기능

다음 표에서는 SQL Server의 주요 기능을 나열 하 고 기능에 대 한 자세한 정보를 제공 하는 링크를 통해 Managed Instance 또는 Single Database 및 탄력적 풀에서 기능을 부분적으로 지원 하는지 또는 완전히 지원 하는지에 대 한 정보를 제공 합니다.

| **SQL 기능** | **단일 데이터베이스 및 탄력적 풀** | **관리 되는 인스턴스** |
| --- | --- | --- |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 예 - [인증서 저장소](sql-database-always-encrypted.md) 및 [키 자격 증명 모음](sql-database-always-encrypted-azure-key-vault.md) 참조 | 예 - [인증서 저장소](sql-database-always-encrypted.md) 및 [키 자격 증명 모음](sql-database-always-encrypted-azure-key-vault.md) 참조 |
| [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [고가용성](sql-database-high-availability.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. | [고가용성](sql-database-high-availability.md) 은 모든 데이터베이스에 포함 되며 [사용자가 관리할 수 없습니다](sql-database-managed-instance-transact-sql-information.md#always-on-availability). 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. |
| [데이터베이스 연결](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 아니요 | 아니요 |
| [애플리케이션 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | 예 | 예 |
| [감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [예](sql-database-auditing.md)| [예](sql-database-managed-instance-auditing.md), 몇 가지 [차이점이](sql-database-managed-instance-transact-sql-information.md#auditing) 있습니다. |
| [자동 백업](sql-database-automated-backups.md) | 예. 전체 백업은 7 일, 차등 12 시간 및 로그 백업 마다 5-10 분 마다 수행 됩니다. | 예. 전체 백업은 7 일, 차등 12 시간 및 로그 백업 마다 5-10 분 마다 수행 됩니다. |
| [자동 조정(강제 진행 계획)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [예](sql-database-automatic-tuning.md)| [예](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [자동 조정(인덱스)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [예](sql-database-automatic-tuning.md)| 아니요 |
| [BACKUP 명령](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 아니요. 시스템에서 시작한 자동 백업만 - [자동 백업](sql-database-automated-backups.md) 참조 | 예, 사용자가 Azure Blob Storage에 대 한 복사 전용 백업을 시작 했습니다. 사용자가 자동 시스템 백업을 시작할 수 없습니다. [백업 차이점](sql-database-managed-instance-transact-sql-information.md#backup) 을 참조 하세요. |
| [기본 제공 함수](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) 참조 | 
| [BULK INSERT 문](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 예, 하지만 Azure Blob storage에서 원본으로만 가능 합니다. | 예, 하지만 원본으로 Azure Blob Storage 에서만 [차이점](sql-database-managed-instance-transact-sql-information.md#bulk-insert--openrowset)을 참조 하세요. |
| [인증서 및 비대칭 키](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 예. 및 `BACKUP` `CREATE` 작업에 대 한 파일 시스템에 액세스 하지 않습니다. | 예. 및 `CREATE` 작업용 파일 시스템에 대 `BACKUP` 한 액세스 권한이 없는 경우 [인증서 차이점](sql-database-managed-instance-transact-sql-information.md#certificates)을 참조 하세요. | 
| [변경 데이터 캡처-CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 아니요 | 예 |
| [Change tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | 예 |예 |
| [데이터 정렬 - 데이터베이스](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | 예 | 예 |
| [데이터 정렬 - 서버/인스턴스](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | 아니요, 기본 논리 서버 데이터 `SQL_Latin1_General_CP1_CI_AS` 정렬이 항상 사용 됩니다. | 예. [인스턴스를 만들](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) 때 설정할 수 있으며 나중에 업데이트할 수 없습니다. |
| [Columnstore 인덱스](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 예 - [프리미엄 계층, 표준 계층 - S3 이상, 범용 계층 및 중요 비즈니스 계층](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |예 |
| [CLR (공용 언어 런타임)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 아니요 | 예, 하지만 `CREATE ASSEMBLY` 문에서 파일 시스템에 액세스 하지 않습니다. [CLR 차이점](sql-database-managed-instance-transact-sql-information.md#clr) 을 참조 하세요. |
| [포함된 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | 예 | 현재 지정 [시간 복원을 비롯 한 복원의 결함으로 인 한](sql-database-managed-instance-transact-sql-information.md#cant-restore-contained-database)것이 아닙니다. 곧 수정 될 오류입니다. |
| [포함된 사용자](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | 예 | 예 |
| [흐름 제어 언어 키워드](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | 예 | 예 |
| [자격 증명](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | 예, 하지만 [데이터베이스 범위 자격 증명만](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) | 예, 하지만 **Azure Key Vault** `SHARED ACCESS SIGNATURE` 만 지원 됩니다. [자세한](sql-database-managed-instance-transact-sql-information.md#credential) 내용은 |
| [데이터베이스 간/세 부분으로 구성 되는 이름 쿼리](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](sql-database-elastic-query-overview.md) 참조 | 예. 추가적으로 [탄력적 쿼리](sql-database-elastic-query-overview.md) |
| [데이터베이스 간 트랜잭션](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 | 예, 인스턴스 내에 있습니다. 인스턴스 간 쿼리에 대 한 [연결 된 서버 차이점](sql-database-managed-instance-transact-sql-information.md#linked-servers) 을 참조 하세요. |
| [커서](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 예 |예 |
| [데이터 압축](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | 예 |예 |
| [데이터베이스 메일-DbMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 아니요 | 예 |
| [데이터베이스 미러링](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 아니요 | [아니오](sql-database-managed-instance-transact-sql-information.md#database-mirroring) |
| [데이터베이스 구성 설정](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | 예 | 예 |
| [데이터베이스 스냅숏](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 아니요 | 아니요 |
| [데이터 형식](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | 예 |예 |
| [DBCC 문](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 대부분 - 개별 문 참조 | 예 - [DBCC 차이점](sql-database-managed-instance-transact-sql-information.md#dbcc) 참조 |
| [DDL 문](https://docs.microsoft.com/sql/t-sql/statements/statements) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [DDL 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 데이터베이스만 |  예 |
| [분산된 파티션 뷰](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 아니요 | 예 |
| [분산된 트랜잭션 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 아니요 - [탄력적 트랜잭션](sql-database-elastic-transactions-overview.md) 참조 |  아니요- [연결 된 서버 차이점](sql-database-managed-instance-transact-sql-information.md#linked-servers) 참조 |
| [DML 문](https://docs.microsoft.com/sql/t-sql/queries/queries) | 예 | 예 |
| [DML 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 대부분 - 개별 문 참조 |  예 |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 대부분 - 개별 DMV 참조 |  예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[예](sql-database-dynamic-data-masking-get-started.md)| [예](sql-database-dynamic-data-masking-get-started.md) |
| [이벤트 알림](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 아니요 - [경고](sql-database-insights-alerts-portal.md) 참조 | 아니요 |
| [식](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |예 | 예 |
| [확장 이벤트 (XEvent)](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 일부 - [SQL Database의 확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 | 예 - [확장 이벤트 차이](sql-database-managed-instance-transact-sql-information.md#extended-events) 참조 |
| [확장 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 아니요 | 아니요 |
| [파일 및 파일 그룹](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 기본 파일 그룹만 해당 | 예. 파일 경로는 자동으로 할당 되며 `ALTER DATABASE ADD FILE` [문에서](sql-database-managed-instance-transact-sql-information.md#alter-database-statement)파일 위치를 지정할 수 없습니다.  |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 아니요 | [아니오](sql-database-managed-instance-transact-sql-information.md#filestream-and-filetable) |
| [전체 텍스트 검색 (FT)](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  예, 하지만 타사 단어 분리기는 지원 되지 않습니다. | 예, 하지만 [타사 단어 분리기는 지원 되지 않습니다](sql-database-managed-instance-transact-sql-information.md#full-text-semantic-search) . |
| [함수](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) 참조 |
| [그래프 처리 중](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | 예 | 예 |
| [메모리 내 최적화](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 예- [프리미엄 계층 및 중요 비즈니스 계층만](sql-database-in-memory.md) | 예 - [중요 비즈니스용 계층만 해당](sql-database-managed-instance.md) |
| [JSON 데이터 지원](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [예](sql-database-json-features.md) | [예](sql-database-json-features.md) |
| [언어 요소](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 대부분 - 개별 요소 참조 |  예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [연결된 서버](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](sql-database-elastic-query-horizontal-partitioning.md) 참조 | 예. 분산 트랜잭션이 없는 [SQL Server 및 SQL Database](sql-database-managed-instance-transact-sql-information.md#linked-servers) 에만 사용 됩니다. |
| [로그 전달](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [고가용성](sql-database-high-availability.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. | DMS 마이그레이션 프로세스의 일부로 기본적으로 제공 됩니다. 고가용성 솔루션으로는 사용할 수 없습니다 [. 다른 고가용성](sql-database-high-availability.md) 방법이 모든 데이터베이스에 포함 되어 있으므로 로그 전달을 HA 대체로 사용 하지 않는 것이 좋습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명 합니다. 데이터베이스 간 복제 메커니즘으로 사용할 수 없음- [중요 비즈니스용 계층](sql-database-service-tier-business-critical.md), [자동 장애 조치 (failover) 그룹](sql-database-auto-failover-group.md)또는 [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md) 에 보조 복제본을 사용 합니다. |
| [로그인 및 사용자](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | 예, 하지만 `CREATE` 및 `ALTER` 로그인 문은 모든 옵션을 제공 하지 않습니다 (Windows 및 서버 수준 Azure Active Directory 로그인). `EXECUTE AS LOGIN`지원 되지 않음-대신 `EXECUTE AS USER` 을 사용 합니다.  | 예, 몇 가지 [차이점이](sql-database-managed-instance-transact-sql-information.md#logins-and-users)있습니다. Windows 로그인은 지원 되지 않으며 Azure Active Directory 로그인으로 바꾸어야 합니다. |
| [대량 가져오기에서 최소 로깅](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 아니요 | 아니요 |
| [시스템 데이터 수정](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 아니요 | 예 |
| [OLE 자동화](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 아니요 | 아니요 |
| [온라인 인덱스 작업](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | 예 | 예 |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|아니요|예, 다른 Azure SQL Database 및 SQL Server에만 해당 합니다. [T-sql 차이점을](sql-database-managed-instance-transact-sql-information.md) 참조 하세요.|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|예|예|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|아니요|예, 다른 Azure SQL Database 및 SQL Server에만 해당 합니다. [T-sql 차이점을](sql-database-managed-instance-transact-sql-information.md) 참조 하세요.|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|예, Azure Blob 저장소에서 가져옵니다. |예, 다른 Azure SQL Database 및 SQL Server에만 해당 하 고 Azure Blob 저장소에서 가져옵니다. [T-sql 차이점을](sql-database-managed-instance-transact-sql-information.md) 참조 하세요.|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|예|예|
| [연산자](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 대부분 - 개별 연산자 참조 |예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [분할](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 예 | 예 |
| 공용 IP 주소 | 예. 방화벽 또는 서비스 끝점을 사용 하 여 액세스를 제한할 수 있습니다.  | 예. 명시적으로 사용 하도록 설정 해야 하며, NSG 규칙에서 포트 3342를 사용 하도록 설정 해야 합니다. 필요한 경우 공용 IP를 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 [공용 끝점](sql-database-managed-instance-public-endpoint-securely.md) 을 참조 하세요. | 
| [지정 시간 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 예-대규모 이외의 모든 서비스 계층- [SQL Database 복구](sql-database-recovery-using-backups.md#point-in-time-restore) 참조 | 예 - [SQL Database 복구](sql-database-recovery-using-backups.md#point-in-time-restore) 참조 |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 아니요. 함수를 사용 하 여 `OPENROWSET` Azure Blob Storage에 배치 된 파일의 데이터를 쿼리할 수 있습니다. | 아니요. 함수를 사용 하 여 `OPENROWSET` Azure Blob Storage에 배치 된 파일의 데이터를 쿼리할 수 있습니다. |
| [조건자](https://docs.microsoft.com/sql/t-sql/queries/predicates) | 예 | 예 |
| [쿼리 알림](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | 아니요 | 예 |
| [R 서비스](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 예, [공개 미리 보기 상태](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | 아니요 |
| [리소스 관리자](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 아니요 | 예 |
| [RESTORE 문](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 아니요 | 예, Azure Blob Storage에 `FROM URL` 배치 된 백업 파일에 대 한 필수 옵션을 사용 합니다. [복원 차이점](sql-database-managed-instance-transact-sql-information.md#restore-statement) 을 참조 하세요. |
| [백업에서 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 자동 백업에서만 - [SQL Database 복구](sql-database-recovery-using-backups.md) 참조 | 자동화 된 백업에서 [SQL Database 복구](sql-database-recovery-using-backups.md) 및 Azure Blob Storage에 배치 된 전체 백업에서 [백업 차이점](sql-database-managed-instance-transact-sql-information.md#backup) 참조 |
| [데이터베이스를 SQL Server으로 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 아니요. 네이티브 복원 대신 BACPAC 또는 BCP를 사용 합니다. | 아니요. Managed Instance에서 사용 되는 SQL Server 데이터베이스 엔진은 온-프레미스에서 사용 되는 SQL Server의 RTM 버전 보다 더 높습니다. 대신 BACPAC, BCP 또는 트랜잭션 복제를 사용 하십시오. |
| [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | 예 | 예 |
| [의미 체계 검색](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 아니요 | 아니요 |
| [시퀀스 번호](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | 예 | 예 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 아니요 | 예. 단, 인스턴스 내 에서만 가능 합니다. [Service Broker 차이점](sql-database-managed-instance-transact-sql-information.md#service-broker) 을 참조 하세요. |
| [서버 구성 설정](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 아니요 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [Set 문](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조|
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | 예 | 예 |
| [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 아니요 - [탄력적 작업](elastic-jobs-overview.md) 참조 | 예 - [SQL Server Agent 차이점](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) 참조 |
| [SQL Server 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 아니요 - [SQL Database 감사](sql-database-auditing.md) 참조 | 예 - [감사 차이점](sql-database-managed-instance-transact-sql-information.md#auditing) 참조 |
| [저장 프로시저](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | 예 | 예 |
| [시스템 저장 함수](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) 참조 |
| [시스템 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 일부 - 개별 저장 프로시저 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) 참조 |
| [시스템 테이블](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 일부 - 개별 테이블 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [시스템 카탈로그 뷰](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 일부 - 개별 뷰 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database) | 예. [모든 데이터베이스에 대해 코어 당 32gb 크기](sql-database-vcore-resource-limits-single-databases.md) | 예. [전체 GP 계층에 대 한 vCore 당 24GB 크기 및 BC 계층의 인스턴스 크기로 제한 됨](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)  |
| [임시 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 로컬 및 데이터베이스 범위 전역 임시 테이블 | 로컬 및 인스턴스 범위 전역 임시 테이블 |
| [임시 테이블](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [예](sql-database-temporal-tables.md) | [예](sql-database-temporal-tables.md) |
| 표준 시간대 선택 | 아니요 | [예](sql-database-managed-instance-timezone.md), Managed Instance 만들어질 때 구성 해야 합니다. |
| 위협 검색|  [예](sql-database-threat-detection.md)|[예](sql-database-managed-instance-threat-detection.md)|
| [추적 플래그](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 아니요 | 아니요 |
| [트랜잭션 복제자](sql-database-managed-instance-transactional-replication.md) | 예, [트랜잭션 및 스냅숏 복제 구독자만](sql-database-single-database-migrate.md) 해당 | 예, [공개 미리 보기](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)상태입니다. [여기](sql-database-managed-instance-transact-sql-information.md#replication)에서 제약 조건을 참조 하세요. |
| [변수](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 예 | 예 |
| [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 예 - 범용 및 중요 비즈니스 서비스 계층만 해당| [예](transparent-data-encryption-azure-sql.md) |
| [Windows Server 장애 조치(failover) 클러스터링](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 아니요. [고가용성](sql-database-high-availability.md) 을 제공 하는 다른 기술은 모든 데이터베이스에 포함 되어 있습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. | 아니요. [고가용성](sql-database-high-availability.md) 을 제공 하는 다른 기술은 모든 데이터베이스에 포함 되어 있습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. |
| [XML 인덱스](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | 예 | 예 |

## <a name="platform-capabilities"></a>플랫폼 기능

Azure platform은 표준 데이터베이스 기능에 추가 값으로 추가 된 다양 한 PaaS 기능을 제공 합니다. Azure SQL Database 서비스와 함께 사용할 수 있는 여러 외부 서비스가 있습니다. 

| **플랫폼 기능** | **단일 데이터베이스 및 탄력적 풀** | **관리 되는 인스턴스** |
| --- | --- | --- |
| [활성 지역 복제](sql-database-active-geo-replication.md) | 예-대규모 이외의 모든 서비스 계층 | 아니요, 대 안으로 [자동 장애 조치 그룹 (미리 보기)](sql-database-auto-failover-group.md) 을 참조 하세요. |
| [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md) | 예-대규모 이외의 모든 서비스 계층 | 예, [공개 미리 보기 상태](sql-database-auto-failover-group.md)|
| [Azure Resource Health](/azure/service-health/resource-health-overview) | 예 | 아니요 |
| [DMS(데이터 마이그레이션 서비스)](https://docs.microsoft.com/sql/dma/dma-overview) | 예 | 예 |
| [지역 복원](sql-database-recovery-using-backups.md#geo-restore) | 예-대규모 이외의 모든 서비스 계층 | 예- [Azure PowerShell](https://medium.com/azure-sqldb-managed-instance/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa)를 사용 합니다. |
| [하이퍼 확장 아키텍처](sql-database-service-tier-hyperscale.md) | 예 | 아니요 |
| [장기 백업 보존-LTR](sql-database-long-term-retention.md) | 예, 자동으로 최대 10 년 동안 백업을 유지 합니다. | 아직 없습니다. 임시 `COPY_ONLY` 해결 방법으로 [수동 백업을](sql-database-managed-instance-transact-sql-information.md#backup) 사용 합니다. |
| [정책 기반 관리](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 아니요 | 아니요 |
| 리소스 풀 | 예, [탄력적 풀](sql-database-elastic-pool.md) 로 | 기본 제공 - 단일 Managed Instance는 동일한 리소스 풀을 공유하는 여러 데이터베이스를 유지할 수 있습니다. |
| 규모 확장 또는 축소 (온라인) | 예, 최소 가동 중지 시간으로 DTU 또는 예약 vCores 또는 max storage를 변경할 수 있습니다. | 예, 최소 가동 중지 시간으로 예약 된 vCores 또는 max storage를 변경할 수 있습니다. | 
| 자동 크기 조정 | 예, [서버 리스 모델](sql-database-serverless.md) 에서 | 아니요, 예약 된 계산 및 저장소를 선택 해야 합니다. |
| 일시 중지/다시 시작 | 예, [서버 리스 모델](sql-database-serverless.md) 에서 | 아니요 | 
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [예](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 예 [버전 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | 예 | 예 |
| [SQL 데이터 동기화](sql-database-get-started-sql-data-sync.md) | 예 | 아니요 |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 예 | 예 |
| [SSAS(SQL Server Analysis Services)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 아니요, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 별도의 Azure 클라우드 서비스입니다. | 아니요, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 별도의 Azure 클라우드 서비스입니다. |
| [SSIS(SQL Server Integration Services)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 예, ADF(Azure Data Factory) 환경의 관리 SSIS를 사용합니다. 여기서 패키지는 Azure SQL Database에서 호스트되는 SSISDB에 저장되고 Azure SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. <br/><br/>SQL Database 서버 및 Managed Instance의 SSIS 기능을 비교하려면 [Azure SQL Database 단일 데이터베이스/탄력적 풀 및 Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)를 참조하세요. | 예, ADF(Azure Data Factory) 환경의 관리 SSIS를 사용합니다. 여기서 패키지는 Managed Instance에서 호스트되는 SSISDB에 저장되고 Azure SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. <br/><br/>SQL Database 및 Managed Instance의 SSIS 기능을 비교하려면 [Azure SQL Database 단일 데이터베이스/탄력적 풀 및 Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)를 참조하세요. |
| [SSRS(SQL Server Reporting Services)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 아니요 - [Power BI 참조](https://docs.microsoft.com/power-bi/) | 아니요 - [Power BI 참조](https://docs.microsoft.com/power-bi/) |
| [QPI (Query Performance Insights)](sql-database-query-performance.md) | 예 | 아니요. SQL Server Management Studio 및 Azure Data Studio에서 기본 제공 보고서를 사용 합니다. |
| [VNet](../virtual-network/virtual-networks-overview.md) | 일부는 [VNet 끝점](sql-database-vnet-service-endpoint-rule-overview.md) 을 사용 하 여 제한 된 액세스를 가능 하 게 합니다. | 예, Managed Instance 고객의 VNet에 삽입 됩니다. [서브넷](sql-database-managed-instance-transact-sql-information.md#subnet) 및 [VNet](sql-database-managed-instance-transact-sql-information.md#vnet) 참조 |

## <a name="tools"></a>Tools
Azure SQL database는 데이터를 관리 하는 데 사용할 수 있는 다양 한 데이터 도구를 지원 합니다.

| **SQL 도구** | **단일 데이터베이스 및 탄력적 풀** | **관리 되는 인스턴스** |
| --- | --- | --- |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | 예 | 예 |
| [BACPAC 파일(내보내기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 예 - [SQL Database 내보내기](sql-database-export.md) 참조 | 예 - [SQL Database 내보내기](sql-database-export.md) 참조 |
| [BACPAC 파일(가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 예 - [SQL Database 가져오기](sql-database-import.md) 참조 | 예 - [SQL Database 가져오기](sql-database-import.md) 참조 |
| [DQS(Data Quality Services)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 아니요 | 아니요 |
| [MDS(Master Data Services)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 아니요 | 아니요 |
| [SSDT(SQL Server Data Tools)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | 예 | 예 |
| [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | 예 | 예 [버전 18.0 이상](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server 프로파일러](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 아니요 - [확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 | 예 |
| [System Center Operations Manager-SCOM](https://docs.microsoft.com/system-center/scom/welcome) | [예](https://www.microsoft.com/download/details.aspx?id=38829) | 아니요 |

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요.
- Managed Instance에 대한 자세한 내용은 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
