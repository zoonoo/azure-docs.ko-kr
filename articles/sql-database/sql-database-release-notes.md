---
title: 릴리스 정보
description: Azure SQL Database 서비스 및 Azure SQL Database 설명서의 새로운 기능 및 개선 사항에 대해 알아봅니다.
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: sstein
ms.openlocfilehash: 7d922aa0727ad28054d050a29039951d3f04985f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383381"
---
# <a name="sql-database-release-notes"></a>SQL 데이터베이스 릴리스 정보

이 문서에서는 현재 공개 미리 보기중인 SQL Database 기능을 나열합니다. SQL 데이터베이스 업데이트 및 개선 사항은 [SQL Database 서비스 업데이트를](https://azure.microsoft.com/updates/?product=sql-database)참조하십시오. 다른 Azure 서비스에 대한 업데이트 및 개선 사항은 [서비스 업데이트를](https://azure.microsoft.com/updates)참조하십시오.

## <a name="features-in-public-preview"></a>공개 미리 보기의 기능

### <a name="single-database"></a>[단일 데이터베이스](#tab/single-database)

| 기능 | 세부 정보 |
| ---| --- |
| 새로운 Fsv2 시리즈 및 M 시리즈 하드웨어 세대| 자세한 내용은 [하드웨어 세대를](sql-database-service-tiers-vcore.md#hardware-generations)참조하십시오.|
| 단일 데이터베이스 및 탄력적 풀로 데이터베이스 복구 가속화 | 자세한 내용은 [가속 데이터베이스 복구](sql-database-accelerated-database-recovery.md)를 참조하십시오.|
|대략적인 Count Distinct|자세한 내용은 [대략적인 개수 구별을](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)참조하십시오.|
|Rowstore의 일괄 처리 모드(호환성 수준 150 미만)|자세한 내용은 [Rowstore의 일괄 처리 모드를](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)참조하십시오.|
| 데이터 검색 및 분류  |자세한 내용은 [Azure SQL 데이터베이스 및 SQL 데이터 웨어하우스 데이터 검색 & 분류를](sql-database-data-discovery-and-classification.md)참조하십시오.|
| 탄력적 데이터베이스 작업 | 자세한 내용은 [탄력적 작업 만들기, 구성 및 관리를](elastic-jobs-overview.md)참조하십시오. |
| 탄력적 쿼리 | 자세한 내용은 [탄력적 쿼리 개요를](sql-database-elastic-query-overview.md)참조하십시오. |
| 탄력적 트랜잭션 | [클라우드 데이터베이스에 분산된 트랜잭션](sql-database-elastic-transactions-overview.md). |
|메모리 부여 피드백(행 모드) (호환성 수준 150 미만)|자세한 내용은 [메모리 부여 피드백(행 모드)을](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)참조하십시오.|
| Azure 포털의 쿼리 편집기 |자세한 내용은 [Azure 포털의 SQL 쿼리 편집기 사용을 사용하여 데이터를 연결하고 쿼리합니다.](sql-database-connect-query-portal.md)|
| 단일 데이터베이스 및 탄력적 풀을 갖춘 R 서비스/기계 학습 |자세한 내용은 [Azure SQL 데이터베이스의 기계 학습 서비스를](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)참조하십시오.|
|SQL 분석|자세한 내용은 [Azure SQL 분석](../azure-monitor/insights/azure-sql.md)을 참조하십시오.|
|테이블 변수 지연 컴파일(호환성 수준 150 미만)|자세한 내용은 [테이블 변수 지연 컴파일](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)을 참조하십시오.|
| &nbsp; |

### <a name="managed-instance"></a>[관리되는 인스턴스](#tab/managed-instance)

| 기능 | 세부 정보 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">인스턴스 풀</a> | 더 작은 SQL 인스턴스를 클라우드로 마이그레이션하는 편리하고 비용 효율적인 방법입니다. |
| <a href="https://aka.ms/managed-instance-aadlogins">인스턴스 수준 Azure AD 서버 주체(로그인)</a> | <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">외부 공급자 문에서 로그인 만들기를</a> 사용하여 서버 수준 로그인을 만듭니다. |
| [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md) | 테이블의 변경 내용을 관리되는 인스턴스, 단일 데이터베이스 또는 SQL Server 인스턴스에 배치된 다른 데이터베이스로 복제하거나 다른 관리형 인스턴스 또는 SQL Server 인스턴스에서 일부 행이 변경될 때 테이블을 업데이트합니다. 자세한 내용은 [Azure SQL Database 관리 인스턴스 데이터베이스의 복제 구성을](replication-with-sql-database-managed-instance.md)참조하십시오. |
| 위협 감지 |자세한 내용은 [Azure SQL Database 관리 인스턴스에서 위협 검색 구성을](sql-database-managed-instance-threat-detection.md)참조하십시오.|
| 장기 백업 보존 | 자세한 내용은 [Azure SQL Database 관리 인스턴스에서 장기 백업 보존 구성을](sql-database-managed-instance-long-term-backup-retention-configure.md)참조하십시오. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>관리되는 인스턴스 - 새로운 기능 및 알려진 문제

### <a name="managed-instance-h2-2019-updates"></a>관리 인스턴스 H2 2019 업데이트

- [서비스 지원 서브넷 구성](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) 관리되는 인스턴스에서 데이터 트래픽을 제어하는 서브넷 구성을 관리하는 안전하고 편리한 방법으로 관리 트래픽의 중단 없는 흐름을 보장합니다.
- [BYOK(사용자 고유키 가져오기)를 사용한 투명 데이터 암호화(TDE)를](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) 사용하면 미사용 데이터 보호를 위한 BYOK(사용자 고유키) 시나리오를 사용할 수 있으며 조직은 키와 데이터에 대한 관리 업무를 분리할 수 있습니다.
- [자동 장애 조치 그룹을](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) 사용하면 기본 인스턴스에서 다른 지역의 보조 인스턴스로 모든 데이터베이스를 복제할 수 있습니다.
- [글로벌 추적 플래그를](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)통해 관리되는 인스턴스 동작을 구성합니다.

### <a name="managed-instance-h1-2019-updates"></a>관리 인스턴스 H1 2019 업데이트

H1 2019의 관리형 인스턴스 배포 모델에서 다음 기능을 사용할 수 있습니다.
  - <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio 구독자에 대한 Azure 월별 크레딧및</a> [지역 한도](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)가 증가한 구독에 대한 지원입니다.
  - <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">SharePoint 2016 및 SharePoint 2019</a>와 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a> 지원
  - <a href="https://aka.ms/managed-instance-collation">선택한 서버 수준 데이터 정렬</a> 및 표준 <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">시간대를</a> 사용하여 인스턴스를 만듭니다.
  - 관리되는 인스턴스는 이제 <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">기본 제공 방화벽으로</a>보호됩니다.
  - [공용 끝점을](sql-database-managed-instance-public-endpoint-configure.md)사용하도록 인스턴스 구성, [프록시 재정의](sql-database-connectivity-architecture.md#connection-policy) 연결으로 네트워크 성능 향상, <a href="https://aka.ms/four-cores-sql-mi-update">Gen5 하드웨어 생성시 4vCore s</a> 또는 Point-In-Time 복원을 위해 최대 <a href="https://aka.ms/managed-instance-configurable-backup-retention">35일의 백업 보존 구성.</a> 장기 백업 보존(최대 10년)은 여전히 활성화되어 있지 않으므로 <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">복사 전용 백업을</a> 대안으로 사용할 수 있습니다.
  - 새로운 기능을 사용하면 <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell을 사용하여 다른 데이터 센터로 데이터베이스를 지리적으로 복원하고</a> [데이터베이스 이름을 바꾸고](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) [가상 클러스터를 삭제할](sql-database-managed-instance-delete-virtual-cluster.md)수 있습니다.
  - 새로운 기본 제공 [인스턴스 기여자 역할은](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) SoD(의무) 보안 원칙을 분리하고 엔터프라이즈 표준을 준수할 수 있도록 합니다.
  - 관리형 인스턴스는 GA(미국 텍사스 주, 미국 정부 애리조나 주)와 중국 북부 2 및 중국 동부 2에 대한 다음 Azure 정부 지역에서 사용할 수 있습니다. 호주 중부, 오스트레일리아 중부 2, 브라질 남부, 프랑스 남부, UAE 중부, UAE 북부, 남아프리카 공화국 북부, 남아프리카 공화국 서부 등 공공 지역에서도 사용할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

|문제  |검색된 날짜  |상태  |해결된 날짜  |
|---------|---------|---------|---------|
|[관리되는 인스턴스에 적용되지 않는 리소스 그룹에 대한 사용 권한](#permissions-on-resource-group-not-applied-to-managed-instance)|2020년 2월|해결 방법을 가지고 있습니다.||
|[장애 조치 그룹에 대한 포털을 통한 수동 장애 조치 제한](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020년 1월|해결 방법을 가지고 있습니다.||
|[SQL 에이전트 역할에는 비시스템 관리자 로그인에 대한 명시적 EXECUTE 권한이 필요합니다.](#in-memory-oltp-memory-limits-are-not-applied)|2019년 12월|해결 방법을 가지고 있습니다.||
|[에이전트 프로세스 를 다시 시작하여 SQL 에이전트 작업을 중단할 수 있습니다.](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019년 12월|해결 안 됩니다.|2020년 3월|
|[AAD 로그인 및 사용자는 SSDT에서 지원되지 않습니다.](#aad-logins-and-users-are-not-supported-in-ssdt)|2019년 11월|해결 안 됩니다.||
|[메모리 내 OLTP 메모리 제한이 적용되지 않음](#in-memory-oltp-memory-limits-are-not-applied)|2019년 10월|해결 방법을 가지고 있습니다.||
|[비어 있지 않은 파일을 제거하는 동안 잘못된 오류가 반환되었습니다.](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019년 10월|해결 방법을 가지고 있습니다.||
|[서비스 계층을 변경하고 진행 중인 데이터베이스 복원에 의해 인스턴스 작업을 생성하지 못하도록 차단됩니다.](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019년 9월|해결 방법을 가지고 있습니다.||
|[비즈니스 중요 서비스 계층의 리소스 관리자를 장애 조치 후 다시 구성해야 할 수 있습니다.](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019년 9월|해결 방법을 가지고 있습니다.||
|[서비스 계층 업그레이드 후 데이터베이스 간 서비스 브로커 대화 상자를 다시 초기화해야 합니다.](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|2019년 8월|해결 방법을 가지고 있습니다.||
|[Azure AD 로그인 형식의 비personification이 지원되지 않습니다.](#impersonification-of-azure-ad-login-types-is-not-supported)|2019년 7월|해결 안 됩니다.||
|[@querysp_send_db_mail 지원되지 않는 매개 변수](#-parameter-not-supported-in-sp_send_db_mail)|2019년 4월|해결 안 됩니다.||
|[지리적 장애 조치 후 트랜잭션 복제를 다시 구성해야 합니다.](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019년 3월|해결 안 됩니다.||
|[RESTORE 작업 중에 임시 데이터베이스가 사용됩니다.](#temporary-database-is-used-during-restore-operation)||해결 방법을 가지고 있습니다.||
|[TEMPDB 구조 및 콘텐츠가 다시 생성됨](#tempdb-structure-and-content-is-re-created)||해결 안 됩니다.||
|[작은 데이터베이스 파일이 포함된 스토리지 공간 초과](#exceeding-storage-space-with-small-database-files)||해결 방법을 가지고 있습니다.||
|[데이터베이스 이름 대신 표시되는 GUID 값](#guid-values-shown-instead-of-database-names)||해결 방법을 가지고 있습니다.||
|[오류 로그가 유지되지 않습니다.](#error-logs-arent-persisted)||해결 안 됩니다.||
|[동일한 인스턴스 내의 두 데이터베이스의 트랜잭션 범위가 지원되지 않음](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||해결 방법을 가지고 있습니다.||
|[CLR 모듈 및 연결된 서버가 로컬 IP 주소를 참조할 수 없는 경우가 있습니다.](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||해결 방법을 가지고 있습니다.||
|Azure Blob 저장소에서 데이터베이스를 복원한 후 DBCC CHECKDB를 사용하여 데이터베이스 일관성을 확인하지 못했습니다.||해결됨|2019년 11월|
|원본 데이터베이스에 메모리 내 OLTP 개체가 포함된 경우 비즈니스 중요 계층에서 범용 계층으로 의 한 시점 데이터베이스 복원이 성공하지 못합니다.||해결됨|2019년 10월|
|보안 연결을 사용하는 외부(Azure가 아닌) 메일 서버가 있는 데이터베이스 메일 기능||해결됨|2019년 10월|
|관리되는 인스턴스에서 지원되지 않는 포함된 데이터베이스||해결됨|2019년 8월|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>관리되는 인스턴스에 적용되지 않는 리소스 그룹에 대한 사용 권한

RG(리소스 그룹)에 적용될 때 관리되는 인스턴스 기여자 RBAC 역할은 관리되는 인스턴스에 적용되지 않으며 영향을 주지 않습니다.

**해결:** 구독 수준에서 사용자에 대 한 관리 인스턴스 기여자 역할을 설정 합니다.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>장애 조치 그룹에 대한 포털을 통한 수동 장애 조치 제한

장애 조치 그룹이 다른 Azure 구독 또는 리소스 그룹의 인스턴스에 걸쳐 있는 경우 장애 조치 그룹의 기본 인스턴스에서 수동 장애 복구를 시작할 수 없습니다.

**해결:** 지역 보조 인스턴스에서 포털을 통해 장애 조치(failover)를 시작합니다.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL 에이전트 역할에는 비시스템 관리자 로그인에 대한 명시적 EXECUTE 권한이 필요합니다.

[SQL Agent 고정 데이터베이스 역할에](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)비시스템 지정 로그인이 추가되는 경우 이러한 로그인이 작동하려면 마스터 저장 프로시저에 명시적 EXECUTE 권한을 부여해야 하는 문제가 있습니다. 이 문제가 발생하면 "object_name>(Microsoft SQL Server, Error: 229)<개체에서 EXECUTE 권한이 거부되었습니다."라는 오류 메시지가 표시됩니다.

**해결 방법**: SQL Agent 고정 데이터베이스 역할 중 하나에 로그인을 추가하면 SQLAgentUserRole, SQLAgentReaderRole 또는 SQLAgentOperatorRole, 이러한 역할에 추가된 각 로그인에 대해 아래 T-SQL 스크립트를 실행하여 나열된 저장된 프로시저에 EXECUTE 권한을 명시적으로 부여합니다.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>에이전트 프로세스 를 다시 시작하여 SQL 에이전트 작업을 중단할 수 있습니다.

SQL Agent는 작업이 시작될 때마다 새 세션을 만들어 메모리 소비를 점차 증가시게 합니다. 예약된 작업의 실행을 차단하는 내부 메모리 제한에 도달하지 않으려면 메모리 소비가 임계값에 도달하면 에이전트 프로세스가 다시 시작됩니다. 다시 시작할 때 실행 중인 작업의 실행이 중단될 수 있습니다.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>메모리 내 OLTP 메모리 제한이 적용되지 않음

비즈니스 크리티컬 서비스 계층은 경우에 따라 [메모리에 최적화된 개체에 대해 최대 메모리 제한을](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) 올바르게 적용하지 않습니다. 관리되는 인스턴스를 사용하면 워크로드가 인메모리 OLTP 작업에 더 많은 메모리를 사용할 수 있으며, 이는 인스턴스의 가용성과 안정성에 영향을 줄 수 있습니다. 제한에 도달하는 메모리 내 OLTP 쿼리는 즉시 실패하지 않을 수 있습니다. 이 문제는 곧 해결될 예정입니다. 메모리 내 OLTP 메모리를 더 많이 사용하는 쿼리는 [제한에](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)도달하면 더 빨리 실패합니다.

**해결 방법:** [SQL Server 관리 스튜디오를](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) 사용하여 [인메모리 OLTP 저장소 사용량을 모니터링하여](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) 워크로드가 사용 가능한 메모리 이상을 사용하지 않는지 확인합니다. vCore 수에 따라 메모리 제한을 늘리거나 워크로드를 최적화하여 메모리를 적게 사용합니다.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>비어 있지 않은 파일을 제거하는 동안 잘못된 오류가 반환되었습니다.

SQL [Server/관리인스턴스는 사용자가 비어 있지 않은 파일을 삭제할 수 없습니다.](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites) 문을 사용하여 `ALTER DATABASE REMOVE FILE` 비어 있지 않은 데이터 파일을 제거하려고 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 하면 오류가 즉시 반환되지 않습니다. 관리되는 인스턴스는 파일을 삭제하려고 계속 시도하며 `Internal server error`에서 30분 후에 작업이 실패합니다.

**해결 :** 명령을 사용하여 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 파일의 내용을 제거합니다. 파일 그룹의 유일한 파일인 경우 파일을 축소하기 전에 이 파일 그룹에 연결된 테이블 또는 파티션에서 데이터를 삭제하고 선택적으로 이 데이터를 다른 테이블/파티션에 로드해야 합니다.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>서비스 계층을 변경하고 진행 중인 데이터베이스 복원에 의해 인스턴스 작업을 생성하지 못하도록 차단됩니다.

진행 `RESTORE` 중인 문, 데이터 마이그레이션 서비스 마이그레이션 프로세스 및 기본 제공 시점 복원은 서비스 계층 업데이트 또는 기존 인스턴스의 크기 조정을 차단하고 복원 프로세스가 완료될 때까지 새 인스턴스를 만듭니다. 복원 프로세스는 복원 프로세스가 실행 중인 동일한 서브넷에서 관리되는 인스턴스 및 인스턴스 풀에서 이러한 작업을 차단합니다. 인스턴스 풀의 인스턴스는 영향을 받지 않습니다. 서비스 계층 작업을 만들거나 변경하면 실패하거나 시간 시간이 변경되지 않으며 복원 프로세스가 완료되거나 취소되면 작업이 진행됩니다.

**해결 방법**: 복원 프로세스가 완료될 때까지 기다리거나 서비스 계층 작업 생성 또는 업데이트 우선 순위가 높은 경우 복원 프로세스를 취소합니다.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>비즈니스 중요 서비스 계층의 리소스 관리자를 장애 조치 후 다시 구성해야 할 수 있습니다.

사용자 워크로드에 할당된 리소스를 제한할 수 있는 [리소스 거지](/sql/relational-databases/resource-governor/resource-governor) 기능으로 장애 조치 또는 사용자가 시작한 서비스 계층 변경(예: 최대 vCore 또는 최대 인스턴스 저장소 크기 변경) 후 일부 사용자 워크로드를 잘못 분류할 수 있습니다.

**해결:** 리소스 `ALTER RESOURCE GOVERNOR RECONFIGURE` [지사를](/sql/relational-databases/resource-governor/resource-governor)사용하는 경우 인스턴스가 시작될 때 SQL 작업을 실행하는 SQL 에이전트 작업의 일부로 주기적으로 실행합니다.

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>서비스 계층 업그레이드 후 데이터베이스 간 서비스 브로커 대화 상자를 다시 초기화해야 합니다.

데이터베이스 간 서비스 브로커 대화 상자는 서비스 계층 을 변경한 후 다른 데이터베이스의 서비스에 메시지를 배달하지 않습니다. 메시지가 **손실되지 않으며** 보낸 자 큐에서 찾을 수 있습니다. 관리되는 인스턴스에서 vCores 또는 인스턴스 저장소 `service_broke_guid` 크기를 변경하면 [sys.database](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 보기의 값이 모든 데이터베이스에 대해 변경됩니다. 다른 `DIALOG` 데이터베이스의 서비스 브로커를 참조하는 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 문을 사용하여 만든 모든 것은 대상 서비스에 메시지를 배달하지 않습니다.

**해결 해결:** 서비스 계층을 업데이트하기 전에 데이터베이스 간 서비스 브로커 대화 상자 대화를 사용하는 모든 활동을 중지하고 그 후에 다시 초기화합니다. 서비스 계층 을 변경한 후 배달되지 않은 나머지 메시지가 있는 경우 원본 큐에서 메시지를 읽고 대상 큐로 다시 보냅니다.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Azure AD 로그인 형식의 비personification이 지원되지 않습니다.

다음 AAD `EXECUTE AS LOGIN` 보안 주체를 사용하거나 `EXECUTE AS USER` 사용하는 사칭은 지원되지 않습니다.
-    별칭 AAD 사용자. 이 경우 `15517`다음 오류가 반환됩니다.
- AAD 응용 프로그램 또는 서비스 주체를 기반으로 하는 AAD 로그인 및 사용자입니다. 이 경우 `15517` 다음 오류가 반환됩니다. `15406`

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@querysp_send_db_mail 지원되지 않는 매개 변수

`@query` [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 프로시저의 매개 변수가 작동하지 않습니다.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>지리적 장애 조치 후 트랜잭션 복제를 다시 구성해야 합니다.

자동 장애 조치 그룹의 데이터베이스에서 트랜잭션 복제를 사용하도록 설정된 경우 관리되는 인스턴스 관리자는 이전 주 데이터베이스의 모든 게시를 정리하고 다른 지역으로의 장애 조치가 발생한 후 새 주 복제본에서 다시 구성해야 합니다. 자세한 내용은 [복제를](sql-database-managed-instance-transact-sql-information.md#replication) 참조하십시오.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>AAD 로그인 및 사용자는 SSDT에서 지원되지 않습니다.

SQL Server 데이터 도구는 Azure Active 디렉터리 로그인 및 사용자를 완전히 지원하지 않습니다.

### <a name="temporary-database-is-used-during-restore-operation"></a>RESTORE 작업 중에 임시 데이터베이스가 사용됩니다.

데이터베이스가 관리되는 인스턴스에서 복원되는 경우 복원 서비스는 먼저 원하는 이름으로 빈 데이터베이스를 만들어 인스턴스에 이름을 할당합니다. 잠시 후 이 데이터베이스가 삭제되고 실제 데이터베이스의 복원이 시작됩니다. *복원* 상태에 있는 데이터베이스에는 이름 대신 임의의 GUID 값이 일시적으로 있습니다. 복원 프로세스가 완료되면 임시 이름이 명령문에 `RESTORE` 지정된 원하는 이름으로 변경됩니다. 초기 단계에서 사용자는 빈 데이터베이스에 액세스하고 이 데이터베이스에서 테이블을 만들거나 데이터를 로드할 수도 있습니다. 이 임시 데이터베이스는 복원 서비스가 두 번째 단계를 시작할 때 삭제됩니다.

**해결 방법**: 복원이 완료될 때까지 복원중인 데이터베이스에 액세스하지 마십시오.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB 구조 및 콘텐츠가 다시 생성됨

데이터베이스는 `tempdb` 항상 12개의 데이터 파일로 분할되며 파일 구조를 변경할 수 없습니다. 파일당 최대 크기는 변경할 수 없으며 새 파일을 `tempdb`에 추가할 수 없습니다. `Tempdb`인스턴스가 시작되거나 장애 복구될 때 항상 빈 데이터베이스로 다시 `tempdb` 만들어지며 변경 된 내용은 유지되지 않습니다.

### <a name="exceeding-storage-space-with-small-database-files"></a>작은 데이터베이스 파일이 포함된 스토리지 공간 초과

`CREATE DATABASE`및 `ALTER DATABASE ADD FILE` `RESTORE DATABASE` 문은 인스턴스가 Azure Storage 제한에 도달할 수 있기 때문에 실패할 수 있습니다.

각 범용 관리 형 인스턴스에는 Azure Premium 디스크 공간에 대해 예약된 최대 35TB의 저장소가 있습니다. 각 데이터베이스 파일은 별도의 물리적 디스크에 배치됩니다. 디스크 크기는 128GB, 256GB, 512GB, 1TB 또는 4TB일 수 있습니다. 디스크의 사용되지 않는 공간은 청구되지 않지만 Azure Premium 디스크 크기의 총 합계는 35TB를 초과할 수 없습니다. 경우에 따라 총 8TB가 필요하지 않은 관리되는 인스턴스는 내부 조각화로 인해 저장소 크기에 대한 35TB Azure 제한을 초과할 수 있습니다.

예를 들어 범용 관리형 인스턴스에는 4TB 디스크에 배치된 크기가 1.2TB인 하나의 큰 파일이 있을 수 있습니다. 또한 별도의 128GB 디스크에 배치되는 각각 1GB 크기의 248개의 파일이 있을 수 있습니다. 이 예제에 대한 설명:

- 전체 할당된 디스크 스토리지 크기는 1x4TB + 248x128GB = 35TB입니다.
- 인스턴스에서 데이터베이스에 대해 예약된 총 공간은 1x1.2TB + 248x1GB = 1.4TB입니다.

이 예제에서는 특정 상황에서 파일의 특정 배포로 인해 관리되는 인스턴스가 예상하지 못할 때 연결된 Azure Premium 디스크에 대해 예약된 35TB 제한에 도달할 수 있음을 보여 줍니다.

이 예제에서는 기존 데이터베이스가 계속 작동하며 새 파일이 추가되지 않는 한 아무문제 없이 증가할 수 있습니다. 모든 데이터베이스의 총 크기가 인스턴스 크기 제한에 도달하지 않더라도 새 디스크 드라이브를 위한 공간이 충분하지 않아 새 데이터베이스를 만들거나 복원할 수 없습니다. 이 경우 반환되는 오류는 명확하지 않습니다.

시스템 [뷰를](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) 사용하여 나머지 파일 수를 식별할 수 있습니다. 이 제한에 도달하면 [DBCC SHRINKFILE 문을 사용하여 일부 작은 파일을 비우고 삭제하거나](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) [이 제한이 없는 비즈니스 중요 계층으로](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)전환합니다.

### <a name="guid-values-shown-instead-of-database-names"></a>데이터베이스 이름 대신 표시되는 GUID 값

몇 가지 시스템 뷰, 성능 카운터, 오류 메시지, XEvent 및 오류 로그 항목에는 실제 데이터베이스 이름 대신 GUID 데이터베이스 식별자가 표시됩니다. 이러한 GUID 식별자는 나중에 실제 데이터베이스 이름으로 대체되므로 이러한 GUID 식별자에 의존하지 마십시오.

**해결:** sys.database view를 사용하여 GUID 데이터베이스 식별자 형태로 지정된 실제 데이터베이스 이름에서 실제 데이터베이스 이름을 확인합니다.

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>오류 로그가 유지되지 않습니다.

관리되는 인스턴스에서 사용할 수 있는 오류 로그는 유지되지 않으며 해당 크기는 최대 저장소 제한에 포함되지 않습니다. 장애 조치(failover)가 발생하면 오류 로그가 자동으로 지워질 수 있습니다. 관리되는 인스턴스가 여러 가상 컴퓨터에서 여러 번 이동되었기 때문에 오류 로그 기록에 차이가 있을 수 있습니다.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>동일한 인스턴스 내의 두 데이터베이스의 트랜잭션 범위가 지원되지 않음

동일한 `TransactionScope` 트랜잭션 범위에서 동일한 인스턴스 내에서 두 개의 쿼리가 동일한 인스턴스 내에 있는 두 개의 데이터베이스로 전송되는 경우 .NET의 클래스가 작동하지 않습니다.

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

이 코드는 동일한 인스턴스 내의 데이터와 함께 작동하지만 MSDTC가 필요했습니다.

**해결 해결:** [SqlConnection.ChangeDatabase(문자열)를](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) 사용하여 두 개의 연결을 사용하는 대신 연결 컨텍스트에서 다른 데이터베이스를 사용합니다.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 모듈 및 연결된 서버가 로컬 IP 주소를 참조할 수 없는 경우가 있습니다.

관리되는 인스턴스에 배치된 CLR 모듈과 연결된 서버 또는 현재 인스턴스를 참조하는 분산 쿼리가 로컬 인스턴스의 IP를 확인할 수 없는 경우가 있습니다. 이 오류는 일시적인 문제입니다.

**해결 해결:** 가능하면 CLR 모듈에서 컨텍스트 연결을 사용합니다.

## <a name="updates"></a>업데이트

SQL Database 업데이트 및 개선 사항 목록은 [SQL Database 서비스 업데이트를](https://azure.microsoft.com/updates/?product=sql-database)참조하십시오.

모든 Azure 서비스에 대한 업데이트 및 개선 사항은 [서비스 업데이트를](https://azure.microsoft.com/updates)참조하십시오.

## <a name="contribute-to-content"></a>콘텐츠에 참여

Azure SQL Database 설명서에 기여하려면 [문서 기고자 가이드를](https://docs.microsoft.com/contribute/)참조하십시오.
