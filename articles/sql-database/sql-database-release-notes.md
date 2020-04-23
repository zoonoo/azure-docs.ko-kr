---
title: 릴리스 정보
description: Azure SQL Database 서비스 및 Azure SQL Database 설명서의 새로운 기능 및 향상 된 기능에 대해 알아봅니다.
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: sstein
ms.openlocfilehash: 27a62223970b0f697465ce9aa050f3fccbcae464
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106426"
---
# <a name="sql-database-release-notes"></a>SQL Database 릴리스 정보

이 문서에는 현재 공개 미리 보기로 제공 되는 SQL Database 기능이 나열 되어 있습니다. SQL Database 업데이트 및 개선 사항에 대해서는 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조 하세요. 다른 Azure 서비스에 대 한 업데이트 및 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조 하세요.

## <a name="features-in-public-preview"></a>공개 미리 보기의 기능

### <a name="single-database"></a>[단일 데이터베이스](#tab/single-database)

| 기능 | 세부 정보 |
| ---| --- |
| 새 Fsv2 시리즈 및 M 시리즈 하드웨어 생성| 자세한 내용은 [하드웨어 생성](sql-database-service-tiers-vcore.md#hardware-generations)을 참조 하세요.|
| 단일 데이터베이스 및 탄력적 풀을 사용한 가속화 된 데이터베이스 복구 | 자세한 내용은 [가속화 데이터베이스 복구](sql-database-accelerated-database-recovery.md)를 참조 하세요.|
|대략적인 Count Distinct|자세한 내용은 [대략적인 개수 Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)를 참조 하십시오.|
|Rowstore의 일괄 처리 모드 (호환성 수준 150)|자세한 내용은 [Rowstore의 일괄 처리 모드](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)를 참조 하세요.|
| 데이터 검색 및 분류  |자세한 내용은 [Azure SQL Database 및 SQL Data Warehouse 데이터 검색 & 분류](sql-database-data-discovery-and-classification.md)를 참조 하세요.|
| 탄력적 데이터베이스 작업 | 자세한 내용은 [탄력적 작업 만들기, 구성 및 관리](elastic-jobs-overview.md)를 참조 하세요. |
| 탄력적 쿼리 | 자세한 내용은 [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)를 참조 하세요. |
| 탄력적 트랜잭션 | [클라우드 데이터베이스 간 분산 트랜잭션](sql-database-elastic-transactions-overview.md). |
|메모리 부여 피드백 (행 모드) (호환성 수준 150)|자세한 내용은 [메모리 부여 피드백 (행 모드)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)을 참조 하세요.|
| Azure Portal의 쿼리 편집기 |자세한 내용은 [Azure Portal의 SQL 쿼리 편집기를 사용 하 여 데이터 연결 및 쿼리를](sql-database-connect-query-portal.md)참조 하세요.|
| 단일 데이터베이스 및 탄력적 풀을 사용 하 여 R services/기계 학습 |자세한 내용은 [Azure SQL Database Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)을 참조 하세요.|
|SQL 분석|자세한 내용은 [Azure SQL 분석](../azure-monitor/insights/azure-sql.md)를 참조 하세요.|
|테이블 변수 지연 컴파일 (호환성 수준 150 미만)|자세한 내용은 [테이블 변수 지연 컴파일](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)을 참조 하세요.|
| &nbsp; |

### <a name="managed-instance"></a>[Managed Instance](#tab/managed-instance)

| 기능 | 세부 정보 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">인스턴스 풀</a> | 더 작은 SQL 인스턴스를 클라우드로 마이그레이션하는 편리 하 고 비용 효율적인 방법입니다. |
| <a href="https://aka.ms/managed-instance-aadlogins">인스턴스 수준 Azure AD 서버 보안 주체 (로그인)</a> | <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> 문을 사용 하 여 서버 수준 로그인을 만듭니다. |
| [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md) | 테이블의 변경 내용을 관리 되는 인스턴스, 단일 데이터베이스 또는 SQL Server 인스턴스에 배치 된 다른 데이터베이스로 복제 하거나 다른 관리 되는 인스턴스 또는 SQL Server 인스턴스에서 일부 행이 변경 될 때 테이블을 업데이트 합니다. 자세한 내용은 [Azure SQL Database 관리 되는 인스턴스 데이터베이스에서 복제 구성](replication-with-sql-database-managed-instance.md)을 참조 하세요. |
| 위협 감지 |자세한 내용은 [Azure SQL Database 관리 되는 인스턴스에서 위협 감지 구성](sql-database-managed-instance-threat-detection.md)을 참조 하세요.|
| 장기 백업 보존 | 자세한 내용은 [Azure SQL Database 관리 되는 인스턴스에서 장기 백업 보존 구성](sql-database-managed-instance-long-term-backup-retention-configure.md)을 참조 하세요. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>관리 되는 인스턴스-새로운 기능 및 알려진 문제

### <a name="managed-instance-h2-2019-updates"></a>관리 되는 인스턴스 H2 2019 업데이트

- [서비스-기타 서브넷 구성](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) 관리 되는 인스턴스가 중단 되지 않는 관리 트래픽 흐름을 보장 하는 동안 데이터 트래픽을 제어 하는 서브넷 구성을 관리 하는 안전 하 고 편리한 방법
- [BYOK (투명 한 데이터 암호화) Bring Your Own Key를 사용](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) 하면 미사용 데이터 보호를 위한 byok (사용자-키) 시나리오를 사용할 수 있으며, 조직에서 키 및 데이터에 대 한 관리 의무를 구분할 수 있습니다.
- [자동 장애 조치 그룹](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) 을 사용 하면 주 인스턴스에서 다른 지역의 보조 인스턴스로 모든 데이터베이스를 복제할 수 있습니다.
- [전역 추적 플래그](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)를 사용 하 여 관리 되는 인스턴스 동작을 구성 합니다.

### <a name="managed-instance-h1-2019-updates"></a>관리 되는 인스턴스 H1 2019 업데이트

다음 기능은 H1 2019의 관리 되는 인스턴스 배포 모델에서 사용할 수 있습니다.
  - <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio 구독자를 위한 Azure 월간 크레딧을</a> 사용 하 여 구독에 대 한 지원을 제공 하 고 [지역별 제한을](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)늘립니다.
  - <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">SharePoint 2016 및 SharePoint 2019</a>와 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a> 지원
  - <a href="https://aka.ms/managed-instance-collation">서버 수준 데이터 정렬</a> 및 원하는 <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">표준 시간대</a> 를 사용 하 여 인스턴스를 만듭니다.
  - 이제 관리 되는 인스턴스가 <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">기본 제공 방화벽</a>으로 보호 됩니다.
  - [공용 끝점](sql-database-managed-instance-public-endpoint-configure.md)을 사용 하 여 프록시를 구성 하 고, [프록시를 재정의](sql-database-connectivity-architecture.md#connection-policy) 하 여 네트워크 성능을 향상 하거나, <a href="https://aka.ms/four-cores-sql-mi-update">Gen5 하드웨어 세대에서 4 개의 vcores</a> 를 사용 하거나, 지정 시간 복원의 경우 <a href="https://aka.ms/managed-instance-configurable-backup-retention">최대 35 일 동안 백업 보존을 구성</a> 합니다. 장기 백업 보존 (최대 10 년)은 아직 사용 되지 않으므로 <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">복사 전용 백업을</a> 대체 방법으로 사용할 수 있습니다.
  - 새 기능을 통해 <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell을 사용 하 여 데이터베이스를 다른 데이터 센터로 지역 복원</a>하 고, [데이터베이스 이름을 바꾸고](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [가상 클러스터를 삭제할](sql-database-managed-instance-delete-virtual-cluster.md)수 있습니다.
  - 새로운 기본 제공 [인스턴스 기여자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) 을 사용 하면 보안 원칙 및 엔터프라이즈 표준 준수와 관련 하 여 의무 (안 함) 규정 준수를 구분할 수 있습니다.
  - 관리 되는 인스턴스는 다음 Azure Government 지역에서 GA (US Gov 텍사스, US Gov 애리조나) 및 중국 북부 2와 중국 동부 2에서 사용할 수 있습니다. 또한 오스트레일리아 중부, 오스트레일리아 중부 2, 브라질 남부, 프랑스 남부, 아랍에미리트 중부, 아랍에미리트 북부, 남아프리카 공화국 북부, 남아프리카 공화국에서 사용할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

|문제  |검색 된 날짜  |Status  |해결 된 날짜  |
|---------|---------|---------|---------|
|[리소스 그룹에 대 한 사용 권한이 Managed Instance에 적용 되지 않음](#permissions-on-resource-group-not-applied-to-managed-instance)|2 월 2020|해결 방법||
|[장애 조치 (failover) 그룹용 포털을 통한 수동 장애 조치의 제한 사항](#limitation-of-manual-failover-via-portal-for-failover-groups)|1 월 2020|해결 방법||
|[SQL 에이전트 역할에는 sysadmin이 아닌 로그인에 대 한 명시적 실행 권한이 필요 합니다.](#in-memory-oltp-memory-limits-are-not-applied)|12 월 2019|해결 방법||
|[에이전트 프로세스를 다시 시작 하 여 SQL 에이전트 작업을 중단할 수 있습니다.](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|12 월 2019|해결 방법 없음|3 월 2020|
|[AAD 로그인 및 사용자는 SSDT에서 지원 되지 않습니다.](#aad-logins-and-users-are-not-supported-in-ssdt)|11 월 2019|해결 방법 없음||
|[메모리 내 OLTP 메모리 제한이 적용 되지 않습니다.](#in-memory-oltp-memory-limits-are-not-applied)|10 월 2019|해결 방법||
|[비어 있지 않은 파일을 제거 하는 동안 잘못 된 오류가 반환 되었습니다.](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|10 월 2019|해결 방법||
|[지속적인 데이터베이스 복원으로 서비스 계층 변경 및 인스턴스 만들기 작업 차단](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|9 월 2019|해결 방법||
|[장애 조치 (failover) 후 중요 비즈니스용 서비스 계층의 Resource Governor를 다시 구성 해야 할 수 있음](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|9 월 2019|해결 방법||
|[서비스 계층 업그레이드 후 데이터베이스 간 Service Broker 대화 상자를 다시 초기화 해야 함](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|8 월 2019|해결 방법||
|[Azure AD 로그인 유형의 Impersonification 지원 되지 않습니다.](#impersonification-of-azure-ad-login-types-is-not-supported)|7 월 2019|해결 방법 없음||
|[@querysp_send_db_mail에서 지원 되지 않는 매개 변수](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|해결 방법 없음||
|[지역 장애 조치 (failover) 후 트랜잭션 복제를 다시 구성 해야 합니다.](#transactional-replication-must-be-reconfigured-after-geo-failover)|3 월 2019|해결 방법 없음||
|[복원 작업 중에 임시 데이터베이스가 사용 됩니다.](#temporary-database-is-used-during-restore-operation)||해결 방법||
|[TEMPDB 구조와 콘텐츠를 다시 만들었습니다.](#tempdb-structure-and-content-is-re-created)||해결 방법 없음||
|[작은 데이터베이스 파일이 포함된 스토리지 공간 초과](#exceeding-storage-space-with-small-database-files)||해결 방법||
|[데이터베이스 이름 대신 표시 되는 GUID 값](#guid-values-shown-instead-of-database-names)||해결 방법||
|[오류 로그는 지속 되지 않습니다.](#error-logs-arent-persisted)||해결 방법 없음||
|[동일한 인스턴스 내에 있는 두 데이터베이스의 트랜잭션 범위는 지원 되지 않습니다.](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||해결 방법|2020년 3월|
|[CLR 모듈 및 연결 된 서버는 때때로 로컬 IP 주소를 참조할 수 없습니다.](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||해결 방법||
|Azure Blob Storage에서 데이터베이스를 복원한 후 DBCC CHECKDB를 사용 하 여 데이터베이스 일관성을 확인 하지 않았습니다.||해결됨|11 월 2019|
|원본 데이터베이스에 메모리 내 OLTP 개체가 포함 되어 있는 경우 중요 비즈니스용 계층에서 일반 용도의 계층으로의 지정 시간 데이터베이스 복원에 실패 합니다.||해결됨|10 월 2019|
|보안 연결을 사용 하는 외부 (비 Azure) 메일 서버와 데이터베이스 메일 기능||해결됨|10 월 2019|
|관리 되는 인스턴스에서 지원 되지 않는 포함 된 데이터베이스||해결됨|8 월 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>관리 되는 인스턴스에 적용 되지 않는 리소스 그룹에 대 한 사용 권한

리소스 그룹에 적용 될 때 (RG) Managed Instance 참가자 RBAC 역할은 Managed Instance에 적용 되지 않으며 효과가 없습니다.

**해결 방법**: 구독 수준에서 사용자의 참가자 역할을 Managed Instance 설정 합니다.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>장애 조치 (failover) 그룹용 포털을 통한 수동 장애 조치의 제한 사항

장애 조치 (failover) 그룹이 다른 Azure 구독 또는 리소스 그룹의 여러 인스턴스에 걸쳐 있으면 장애 조치 (failover) 그룹의 주 인스턴스에서 수동 장애 조치 (failover)를 시작할 수 없습니다.

**해결 방법**: 지역 보조 인스턴스에서 포털을 통해 장애 조치 (failover)를 시작 합니다.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL 에이전트 역할에는 sysadmin이 아닌 로그인에 대 한 명시적 실행 권한이 필요 합니다.

[SQL 에이전트 고정 데이터베이스 역할](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)중 하나에 sysadmin이 아닌 로그인을 추가 하면 이러한 로그인이 작동 하기 위해 마스터 저장 프로시저에 명시적 실행 권한을 부여 해야 하는 문제가 있습니다. 이 문제가 발생 하는 경우 개체에 대 한 EXECUTE 권한이 거부 되었습니다. 오류 메시지 <object_name> (Microsoft SQL Server, 오류: 229)가 표시 됩니다.

**해결 방법**: SQL 에이전트 고정 데이터베이스 역할 중 하나에 로그인을 추가 하면 (SQLAgentUserRole, SQLAgentReaderRole 또는 SQLAgentOperatorRole) 이러한 역할에 추가 된 각 로그인에 대해 아래 t-sql 스크립트를 실행 하 여 나열 된 저장 프로시저에 대 한 실행 권한을 명시적으로 부여 합니다.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>에이전트 프로세스를 다시 시작 하 여 SQL 에이전트 작업을 중단할 수 있습니다.

SQL 에이전트는 작업이 시작 될 때마다 새 세션을 만들고 메모리 소비량을 점차적으로 늘립니다. 예약 된 작업의 실행을 차단 하는 내부 메모리 제한에 도달 하지 않도록 하기 위해 메모리 소비가 임계값에 도달 하면 에이전트 프로세스가 다시 시작 됩니다. 이로 인해 다시 시작 될 때 실행 중인 작업의 실행이 중단 될 수 있습니다.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>메모리 내 OLTP 메모리 제한이 적용 되지 않습니다.

경우에 따라 중요 비즈니스용 서비스 계층은 [메모리 액세스에 최적화 된 개체에 대 한 최대 메모리 제한을](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) 올바르게 적용 하지 않습니다. 관리 되는 인스턴스를 사용 하면 메모리 내 OLTP 작업에 더 많은 메모리를 사용 하 여 작업을 수행할 수 있으며이 경우 인스턴스의 가용성과 안정성에 영향을 줄 수 있습니다. 제한에 도달 하는 메모리 내 OLTP 쿼리는 즉시 실패 하지 않을 수 있습니다. 이 문제는 곧 해결 될 예정입니다. 메모리 내 OLTP 메모리를 더 많이 사용 하는 쿼리는 [제한](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)에 도달 하면 더 빨리 장애 조치 (failover) 됩니다.

**해결 방법:** [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) 를 사용 하 여 [메모리 내 OLTP 저장소 사용량을 모니터링](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) 하 여 워크 로드가 사용 가능한 메모리를 초과 하지 않는지 확인 합니다. VCores 수에 따라 달라 지는 메모리 제한을 늘리거나 메모리를 더 사용 하도록 작업을 최적화 합니다.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>비어 있지 않은 파일을 제거 하는 동안 잘못 된 오류가 반환 되었습니다.

SQL Server/Managed Instance [사용자가 비어 있지 않은 파일을 삭제할 수](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)없습니다. 문을 사용 하 여 `ALTER DATABASE REMOVE FILE` 비어 있지 않은 데이터 파일을 제거 하려고 하면 오류가 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 즉시 반환 되지 않습니다. Managed Instance은 파일을 삭제 하려고 계속 시도 하 고, 30 분 후에 작업이 실패 `Internal server error`합니다.

**해결 방법**: 명령을 사용 하 여 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 파일의 내용을 제거 합니다. 파일 그룹의 유일한 파일인 경우 파일을 축소 하기 전에이 파일 그룹에 연결 된 테이블이 나 파티션에서 데이터를 삭제 하 고 필요에 따라이 데이터를 다른 테이블/파티션에 로드 해야 합니다.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>지속적인 데이터베이스 복원으로 서비스 계층 변경 및 인스턴스 만들기 작업 차단

진행 `RESTORE` 중인 문, 데이터 마이그레이션 서비스 마이그레이션 프로세스 및 기본 제공 시점 복원은 복원 프로세스가 완료 될 때까지 서비스 계층 업데이트 또는 기존 인스턴스의 크기 조정 및 새 인스턴스 만들기를 차단 합니다. 복원 프로세스는 복원 프로세스가 실행 되는 동일한 서브넷의 관리 되는 인스턴스 및 인스턴스 풀에서 이러한 작업을 차단 합니다. 인스턴스 풀의 인스턴스는 영향을 받지 않습니다. 서비스 계층 작업을 만들거나 변경 하면 실패 하거나 시간이 초과 되지 않습니다. 복원 프로세스가 완료 되거나 취소 되 면 계속 됩니다.

**해결 방법**: 복원 프로세스가 완료 될 때까지 기다리거나, 만들기 또는 업데이트 서비스 계층 작업의 우선 순위가 더 높은 경우 복원 프로세스를 취소 합니다.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>장애 조치 (failover) 후 중요 비즈니스용 서비스 계층의 Resource Governor를 다시 구성 해야 할 수 있음

사용자 작업에 할당 된 리소스를 제한할 수 있도록 하는 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 기능은 장애 조치 (failover) 또는 사용자가 시작한 서비스 계층 변경 (예: 최대 vcore 또는 최대 인스턴스 저장소 크기의 변경) 후에 일부 사용자 작업을 잘못 분류 하는 것일 수 있습니다.

**해결 방법**: `ALTER RESOURCE GOVERNOR RECONFIGURE` [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)를 사용 하는 경우 인스턴스가 시작 될 때 SQL 태스크를 실행 하는 sql 에이전트 작업의 일부로 정기적으로 또는를 실행 합니다.

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>서비스 계층 업그레이드 후 데이터베이스 간 Service Broker 대화 상자를 다시 초기화 해야 함

데이터베이스 간 Service Broker 대화 상자는 서비스 계층 변경 작업 후에 다른 데이터베이스의 서비스에 메시지를 배달 하지 않습니다. 메시지는 **손실 되지** 않으며 발신자 큐에서 찾을 수 있습니다. Managed Instance에서 vCores 또는 인스턴스 저장소 크기를 변경 하면 모든 데이터베이스에 `service_broke_guid` 대해 [sys. 데이터베이스](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 보기의 값이 변경 됩니다. 다른 `DIALOG` 데이터베이스의 Service broker를 참조 하는 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 문을 사용 하 여 만든 경우에는 대상 서비스에 대 한 메시지 배달이 중지 됩니다.

**해결 방법:** 서비스 계층을 업데이트 하기 전에 데이터베이스 간 Service Broker 대화 상자를 사용 하는 작업을 중지 하 고 이후에 다시 초기화 합니다. 서비스 계층 변경 후 배달 되지 않은 메시지가 남아 있으면 원본 큐에서 메시지를 읽고 대상 큐에 다시 보냅니다.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Azure AD 로그인 유형의 Impersonification 지원 되지 않습니다.

다음 AAD `EXECUTE AS USER` 보안 `EXECUTE AS LOGIN` 주체를 사용 하는 가장이 지원 되지 않습니다.
-    별칭이 지정 되는 AAD 사용자입니다. 이 경우 `15517`다음과 같은 오류가 반환 됩니다.
- Aad 응용 프로그램 또는 서비스 주체를 기반으로 하는 AAD 로그인 및 사용자입니다. 이 경우 `15517` 및 `15406`에는 다음과 같은 오류가 반환 됩니다.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@querysp_send_db_mail에서 지원 되지 않는 매개 변수

Sp_send_db_mail `@query` 프로시저의 매개 [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 변수가 작동 하지 않습니다.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>지역 장애 조치 (failover) 후 트랜잭션 복제를 다시 구성 해야 합니다.

자동 장애 조치 그룹의 데이터베이스에서 트랜잭션 복제를 사용 하는 경우 관리 되는 인스턴스 관리자는 이전 주 데이터베이스에서 모든 게시를 정리 하 고 다른 지역에 대 한 장애 조치 (failover)가 발생 한 후 새 주 데이터베이스에서 다시 구성 해야 합니다. 자세한 내용은 [복제](sql-database-managed-instance-transact-sql-information.md#replication) 를 참조 하세요.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>AAD 로그인 및 사용자는 SSDT에서 지원 되지 않습니다.

SQL Server Data Tools Azure Active directory 로그인 및 사용자를 완전히 지원 하지 않습니다.

### <a name="temporary-database-is-used-during-restore-operation"></a>복원 작업 중에 임시 데이터베이스가 사용 됩니다.

데이터베이스가 Managed Instance에서 복원 되는 경우 복원 서비스는 먼저 원하는 이름으로 빈 데이터베이스를 만들어 인스턴스에 이름을 할당 합니다. 잠시 후에이 데이터베이스가 삭제 되 고 실제 데이터베이스의 복원이 시작 됩니다. *복원* 중인 상태에 있는 데이터베이스에는 이름 대신 임의의 GUID 값이 있습니다. 복원 프로세스가 완료 되 면 임시 이름이 `RESTORE` 문에 지정 된 원하는 이름으로 변경 됩니다. 초기 단계에서 사용자는 빈 데이터베이스에 액세스 하 고이 데이터베이스에서 테이블을 만들거나 데이터를 로드할 수도 있습니다. 이 임시 데이터베이스는 복원 서비스가 두 번째 단계를 시작할 때 삭제 됩니다.

**해결 방법**: 복원이 완료 될 때까지 복원 하는 데이터베이스에 액세스 하지 마세요.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB 구조와 콘텐츠를 다시 만들었습니다.

데이터베이스 `tempdb` 는 항상 12 개의 데이터 파일로 분할 되며 파일 구조를 변경할 수 없습니다. 파일당 최대 크기를 변경할 수 없으며 새 파일을에 `tempdb`추가할 수 없습니다. `Tempdb`는 인스턴스가 시작 되거나 장애 조치 (failover) 될 때 항상 빈 데이터베이스로 다시 생성 되며에서 `tempdb` 변경한 내용은 유지 되지 않습니다.

### <a name="exceeding-storage-space-with-small-database-files"></a>작은 데이터베이스 파일이 포함된 스토리지 공간 초과

`CREATE DATABASE`인스턴스가 `ALTER DATABASE ADD FILE`Azure Storage 제한 `RESTORE DATABASE` 에 도달할 수 있으므로, 및 문이 실패할 수 있습니다.

범용으로 관리 되는 각 인스턴스에는 Azure Premium 디스크 공간 용으로 예약 된 저장소 최대 35 TB가 있습니다. 각 데이터베이스 파일은 별도의 실제 디스크에 배치 됩니다. 디스크 크기는 128GB, 256GB, 512GB, 1TB 또는 4TB일 수 있습니다. 디스크의 사용 되지 않는 공간은 청구 되지 않지만 Azure Premium 디스크 크기의 총 합계는 35 TB를 초과할 수 없습니다. 경우에 따라 총 8TB가 필요 없는 관리 되는 인스턴스는 내부 조각화로 인해 저장소 크기에 대 한 35 TB Azure 제한을 초과할 수 있습니다.

예를 들어 범용 관리 되는 인스턴스의 크기가 4tb 디스크에 배치 된 크기가 1.2 인 큰 파일이 있을 수 있습니다. 또한 각각 별도의 128 GB 디스크에 배치 되는 248 파일의 크기가 1gb입니다. 이 예제에 대한 설명:

- 전체 할당된 디스크 스토리지 크기는 1x4TB + 248x128GB = 35TB입니다.
- 인스턴스에서 데이터베이스에 대해 예약된 총 공간은 1x1.2TB + 248x1GB = 1.4TB입니다.

이 예에서는 특정 상황에서 파일의 특정 배포로 인해 관리 되는 인스턴스가 연결 된 Azure Premium 디스크에 대해 예약 된 35-TB 제한에 도달할 수 있음을 보여 줍니다.

이 예제에서 기존 데이터베이스는 계속 작동 하며 새 파일이 추가 되지 않는 한 문제 없이 커질 수 있습니다. 모든 데이터베이스의 총 크기가 인스턴스 크기 제한에 도달 하지 않더라도 새 디스크 드라이브에 충분 한 공간이 없기 때문에 새 데이터베이스를 만들거나 복원할 수 없습니다. 이 경우 반환 되는 오류는 명확 하지 않습니다.

시스템 뷰를 사용 하 여 [남은 파일 수를 식별할](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) 수 있습니다. 이 한도에 도달 하면 [DBCC SHRINKFILE 문을 사용 하 여 작은 파일 중 일부를 비우고 삭제](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) 하거나 [이 제한이 없는 중요 비즈니스용 계층](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)으로 전환 해 보세요.

### <a name="guid-values-shown-instead-of-database-names"></a>데이터베이스 이름 대신 표시 되는 GUID 값

몇 가지 시스템 뷰, 성능 카운터, 오류 메시지, XEvent 및 오류 로그 항목에는 실제 데이터베이스 이름 대신 GUID 데이터베이스 식별자가 표시됩니다. 이러한 GUID 식별자는 나중에 실제 데이터베이스 이름으로 대체 되기 때문에 사용 하지 마세요.

**해결 방법**: sys 데이터베이스 뷰를 사용 하 여 실제 데이터베이스 이름에서 GUID 데이터베이스 식별자 형식으로 지정 된 실제 데이터베이스 이름을 확인 합니다.

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>오류 로그는 지속 되지 않습니다.

관리 되는 인스턴스에서 사용할 수 있는 오류 로그는 지속 되지 않으며 크기는 최대 저장소 제한에 포함 되지 않습니다. 장애 조치 (failover)가 발생 하면 오류 로그가 자동으로 삭제 될 수 있습니다. 여러 가상 머신에서 Managed Instance 여러 번 이동 되었으므로 오류 로그 기록에 차이가 있을 수 있습니다.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>동일한 인스턴스 내에 있는 두 데이터베이스의 트랜잭션 범위는 지원 되지 않습니다.

**(3 월 2020 일에 해결 됨)** 동일한 `TransactionScope` 인스턴스 내에서 동일한 트랜잭션 범위에서 두 개의 쿼리를 두 개의 데이터베이스로 보내면 .net의 클래스가 작동 하지 않습니다.

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**해결 방법 (3 월 2020 일 이후 필요 없음):** 두 개의 연결을 사용 하는 대신 [SqlConnection 데이터베이스 (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) 를 사용 하 여 연결 컨텍스트에서 다른 데이터베이스를 사용 합니다.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 모듈 및 연결 된 서버는 때때로 로컬 IP 주소를 참조할 수 없습니다.

관리 되는 인스턴스 및 연결 된 서버 또는 현재 인스턴스를 참조 하는 분산 쿼리에 배치 된 CLR 모듈은 종종 로컬 인스턴스의 IP를 확인할 수 없습니다. 이 오류는 일시적인 문제입니다.

**해결 방법:** 가능 하면 CLR 모듈에서 컨텍스트 연결을 사용 합니다.

## <a name="updates"></a>업데이트

SQL Database 업데이트 및 개선 사항 목록은 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조 하세요.

모든 Azure 서비스에 대 한 업데이트 및 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조 하세요.

## <a name="contribute-to-content"></a>콘텐츠에 참여

Azure SQL Database 설명서에 기여 하려면 [Docs 참여자 가이드](https://docs.microsoft.com/contribute/)를 참조 하세요.
