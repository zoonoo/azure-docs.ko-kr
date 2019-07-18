---
title: Azure Backup을 사용 하 여 SQL Server 데이터베이스 백업 문제 해결 | Microsoft Docs
description: Azure Backup을 사용하여 Azure VM에서 실행되는 SQL Server 데이터베이스를 백업하는 경우의 문제 해결 정보입니다.
services: backup
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: b2822a3c7dfa23065f2cbd35ef4e506efae026f2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704844"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Azure Backup을 사용 하 여 SQL Server 데이터베이스 백업 문제 해결

이 문서에서는 Azure virtual machines에서 실행 되는 SQL Server 데이터베이스에 대 한 문제 해결 정보를 제공 합니다.

백업 프로세스 및 제한 사항에 대 한 자세한 내용은 참조 하세요. [Azure Vm에서 SQL Server에 대 한 백업](backup-azure-sql-database.md#feature-consideration-and-limitations)합니다.

## <a name="sql-server-permissions"></a>SQL Server 사용 권한

가상 머신에서 SQL Server 데이터베이스에 대 한 보호를 구성 하려면 설치 해야 합니다 **AzureBackupWindowsWorkload** 가상 머신에서 확장 합니다. 오류를 받게 되 면 **UserErrorSQLNoSysadminMembership**, SQL Server 인스턴스에 필요한 백업 사용 권한이 없는 것을 의미 합니다. 이 오류를 해결 하려면의 단계를 따릅니다 [사용 권한 집합 VM](backup-azure-sql-database.md#set-vm-permissions)합니다.

## <a name="error-messages"></a>오류 메시지

### <a name="backup-type-unsupported"></a>백업 유형이 지원되지 않습니다

| Severity | Description | 가능한 원인 | 권장 작업 |
|---|---|---|---|
| 경고 | 이 데이터베이스에 대 한 현재 설정을 연결 된 정책에 있는 특정 백업 유형 지원 하지 않습니다. | <li>Master 데이터베이스의 전체 데이터베이스 백업 작업만 수행할 수 있습니다. 차등 백업과 트랜잭션 로그 백업을 모두 가능 합니다. </li> <li>단순 복구 모델에서 데이터베이스의 트랜잭션 로그 백업을 허용 하지 않습니다.</li> | 정책의 모든 백업 유형이 지원되도록 데이터베이스 설정을 수정합니다. 또는 지원 되는 백업 유형만 포함 하도록 현재 정책을 변경 합니다. 이 고, 그렇지 예약 된 백업 중 백업 지원 되지 않는 형식을 건너뜁니다 또는 임시 백업에 대 한 백업 작업이 실패 합니다.


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 이 SQL 데이터베이스는 요청된 백업 유형을 지원하지 않습니다. | 요청된 백업 유형을 데이터베이스 복구 모델에서 허용하지 않을 때 발생합니다. 이 오류는 다음과 같은 상황에서 발생할 수 있습니다. <br/><ul><li>단순 복구 모델을 사용 하는 데이터베이스에 로그 백업을 허용 하지 않습니다.</li><li>Master 데이터베이스에 대 한 차등 및 로그 백업은 허용 되지 않습니다.</li></ul>자세한 내용은 참조는 [SQL Server 복구 모델](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) 설명서. | 단순 복구 모델에서 데이터베이스에 대 한 로그 백업이 실패 하면 다음이 옵션 중 하나를 시도 합니다.<ul><li>데이터베이스가 단순 복구 모드인 경우 로그 백업을 사용하지 않도록 설정합니다.</li><li>사용 된 [SQL Server 설명서](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) 데이터베이스 복구 모델이 전체 또는 대량 로그 변경 합니다. </li><li> 여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있고 복구 모델을 변경하지 않으려는 경우에는 오류를 무시합니다. 전체 및 차등 백업은 일정에 따라 작동합니다. 로그 백업은 예상대로 건너뜁니다.</li></ul>Master 데이터베이스 로그 백업이 나 차등 구성한 경우 다음 단계 중 하나를 사용 합니다.<ul><li>Master 데이터베이스에 대 한 백업 정책 일정을 full로 변경 하려면 포털을 사용 합니다.</li><li>여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있는 경우에는 오류를 무시합니다. 전체 백업은 일정에 따라 작동합니다. 차등 또는 로그 백업은 예상대로 발생하지 않습니다.</li></ul> |
| 충돌하는 작업이 동일한 데이터베이스에서 이미 실행 중이기 때문에 작업이 취소되었습니다. | 참조 된 [백업 및 복원 제한 사항에 대 한 블로그 항목](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) 동시에 실행 되는 합니다.| [SQL Server Management Studio (SSMS)를 사용 하 여 백업 작업을 모니터링 하려면](manage-monitor-sql-database-backup.md)합니다. 충돌 하는 작업이 실패 한 후 작업을 다시 시작 합니다.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 데이터베이스가 없습니다. | 데이터베이스 삭제되었거나 이름이 변경되었습니다. | 데이터베이스가 실수로 삭제되었거나 이름이 변경되었는지 확인합니다.<br/><br/> 데이터베이스가 실수로 삭제된 경우 백업을 계속하려면 데이터베이스를 원래 위치로 복원합니다.<br/><br/> 데이터베이스를 삭제 하 고 필요한 Recovery Services 자격 증명 한 다음 향후 백업을 선택 하지 않은 경우 **백업 중지** 사용 하 여 **Backup 데이터 보관** 하거나 **Backup 데이터 삭제**합니다. 자세한 내용은 [백업 된 SQL Server 데이터베이스 관리 및 모니터링](manage-monitor-sql-database-backup.md)합니다.

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 로그 체인이 손상되었습니다. | 데이터베이스 또는 VM 로그 체인을 자릅니다 다른 백업 솔루션을 통해 백업 됩니다.|<ul><li>다른 백업 솔루션이나 스크립트를 사용 중인지 확인합니다. 이런 경우 백업 솔루션을 중지합니다. </li><li>백업 된 임시 로그 백업 인 경우 새 로그 체인을 시작 하려면 전체 백업을 트리거하십시오. 예약 된 로그 백업에 대 한 Azure Backup 서비스에서 자동으로이 문제를 해결 하려면 전체 백업을 트리거합니다 때문에 아무 작업도 필요 합니다.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup에서 SQL 인스턴스에 연결할 수 없습니다. | Azure Backup은 SQL Server 인스턴스에 연결할 수 없습니다. | Azure portal 오류 메뉴의 근본 원인을 좁힐 추가 세부 정보를 사용 합니다. 오류를 해결하려면 [SQL 백업 문제 해결](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)을 참조하세요.<br/><ul><li>기본 SQL 설정을 원격 연결 허용 안 함, 설정을 변경 합니다. 설정을 변경 하는 방법은 다음 문서를 참조 하세요.<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>로그인 문제가 있는 경우 시정 하기 위해 이러한 링크를 사용 합니다.<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 이 데이터 원본에 대한 첫 번째 전체 백업이 없습니다. | 데이터베이스에 대한 전체 백업이 누락되었습니다. 전체 백업에는 부모를 로그 및 차등 백업은, 로그 백업 또는 전체 백업을 차등를 트리거하기 전에 수행 해야 합니다. | 임시 전체 백업을 트리거하십시오.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터 원본의 트랜잭션 로그가 꽉 차서 백업을 수행할 수 없습니다. | 데이터베이스 트랜잭션 로그 공간이 꽉 찼습니다. | 이 문제를 해결 하려면 참조는 [SQL Server 설명서](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)합니다. |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상 위치에 이름이 같은 데이터베이스가 이미 있습니다. | 복원 대상에 이미 동일한 이름의 데이터베이스가 있습니다.  | <ul><li>대상 데이터베이스 이름을 변경 합니다.</li><li>또는 복원 페이지에서 강제 덮어쓰기 옵션을 사용 합니다.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터베이스를 오프라인 상태로 만들지 못해서 복원에 실패했습니다. | 복원을 수행 하는, 하는 동안 대상 데이터베이스를 오프 라인 상태로 만들 수 해야 합니다. Azure Backup에는이 데이터를 오프 라인 상태로 만들 수 없습니다. | Azure portal 오류 메뉴의 근본 원인을 좁힐 추가 세부 정보를 사용 합니다. 자세한 내용은 참조는 [SQL Server 설명서](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)합니다. |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상에서 지문이 포함된 서버 인증서를 찾을 수 없습니다. | 대상 인스턴스의 master 데이터베이스는 유효한 암호화 지문이 없습니다. | 원본 인스턴스에서 대상 인스턴스로 사용 되는 유효한 인증서 지문을 가져옵니다. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 복구에 사용되는 로그 백업에 대량 로그된 변경 내용이 포함되어 있습니다. SQL 지침에 따라 임의의 시점에서 중지하도록 사용할 수 없습니다. | 데이터베이스에 대량 로그 복구 모드에 있으면 다음 로그 트랜잭션 및 대량 로그 트랜잭션 간에 데이터를 복구할 수 없습니다. | 복구에 대 한 시간에 다른 시점을 선택 합니다. [자세히 알아보기](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 가용성 그룹의 일부 노드가 등록되지 않아서 SQL Always On 가용성 그룹에 대한 백업 기본 설정을 충족할 수 없습니다. | 백업을 수행하는 데 필요한 노드가 등록되지 않았거나 연결할 수 없습니다. | <ul><li>이 데이터베이스의 백업을 수행 하는 데 필요한 모든 노드가 등록 되어 있고 정상, 하는 작업을 다시 시도 확인 합니다.</li><li>SQL Server Always On 가용성 그룹에 대 한 백업 기본 설정을 변경 합니다.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 서버 VM이 종료되어 Azure Backup 서비스에 액세스할 수 없습니다. | VM 종료 됩니다. | SQL Server 인스턴스가 실행 되 고 있는지 확인 합니다. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup 서비스가 Azure VM 게스트 에이전트를 사용하여 백업을 수행하는 데 대상 서버에서 게스트 에이전트를 사용할 수 없습니다. | 게스트 에이전트는 가능 하지 않거나 올바른 상태가 아닙니다. | [VM 게스트 에이전트를 수동으로 설치](../virtual-machines/extensions/agent-windows.md)합니다. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 자동 보호 의도가 제거되었거나 더 이상 유효하지 않습니다. | SQL Server 인스턴스의 자동 보호를 사용 하도록 설정 하면 **백업 구성** 해당 인스턴스에 있는 모든 데이터베이스에 대 한 작업을 실행 합니다. 작업을 실행하는 동안 자동 보호를 사용하지 않도록 설정하는 경우 **In-Progress** 작업이 이 오류 코드로 취소됩니다. | 다시 보호 하기 위해 모든 나머지 데이터베이스 자동 보호를 사용 하도록 설정 합니다. |

## <a name="re-registration-failures"></a>다시 등록 실패

다시 등록 작업을 트리거하기 전에 같은 증상 중 하나 이상에 대해 확인 합니다.

* 모든 작업 (예: 백업, 복원 및 백업 구성) 다음 오류 코드 중 하나를 사용 하 여 VM에서 실패 하는: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* 합니다 **백업 상태** 백업 항목에 대 한 영역에 표시 됩니다 **불가능**합니다. 동일한 상태를 일으킬 수 있는 다른 모든 기호 규칙:

  * VM에 대 한 백업 관련 작업을 수행할 수 있는 권한이 부족  
  * VM 백업 하기 위해서는 하므로 종료
  * 네트워크 문제  

  ![VM를 다시 등록 하는 중에 "연결할 수 없음" 상태](./media/backup-azure-sql-database/re-register-vm.png)

* Always On 가용성 그룹의 경우 백업 된 백업 기본 설정을 변경한 후 또는 장애 조치 후 실패 하기 시작 합니다.

다음 이유 중 하나 이상에 대해 이러한 현상이 발생할 수 있습니다.

* 확장 삭제 되었거나 포털에서 제거 됩니다. 
* 확장 제거 **Control Panel** 에서 VM에 **제거 또는 변경 프로그램**합니다.
* 전체 디스크 복원을 통해 시간에 다시 VM 복원 되었습니다.
* VM 확장 구성에 만료 되므로 오랜된 시간에 대 한 종료 되었습니다.
* VM이 삭제 및 삭제 된 VM과 동일한 리소스 그룹에 동일한 이름의 다른 VM이 만들어진 합니다.
* 전체 백업 구성 중 가용성 그룹 노드에서 받지 못했습니다. 이 가용성 그룹에 새 노드를 추가할 때 또는 자격 증명 모음 등록 될 때 발생할 수 있습니다.

앞의 시나리오에서 VM에 다시 등록 작업을 트리거하는 것이 좋습니다. 이제이 옵션은 PowerShell을 통해서만 사용할 수 있습니다.

## <a name="size-limit-for-files"></a>파일 크기 제한

파일의 총 문자열 크기는 파일 수가 뿐만 아니라 해당 이름 및 경로에 따라 달라 집니다. 각 데이터베이스 파일의 논리적 파일 이름 및 실제 경로 가져옵니다. 이 SQL 쿼리를 사용할 수 있습니다.

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

이제 다음 형식으로 정렬 합니다.

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

예를 들면 다음과 같습니다.

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

콘텐츠의 문자열 크기를 20, 000 바이트를 초과 하면 데이터베이스 파일 다르게 저장 됩니다. 복구 하는 동안 복원에 대 한 대상 파일 경로 설정할 수 없습니다. SQL Server에서 제공 하는 기본 SQL 경로에 파일 복원 됩니다.

### <a name="override-the-default-target-restore-file-path"></a>기본 대상 복원 파일 경로 재정의 합니다.

대상 복원 경로에 데이터베이스 파일의 매핑을 포함 하는 JSON 파일을 배치 하 여 복원 작업 중 대상 복원 파일 경로 재정의할 수 있습니다. 만들기는 `database_name.json` 파일 및 위치에 배치 *C:\Program Files\Azure 워크 로드 Backup\bin\plugins\SQL*합니다.

파일의 콘텐츠이 형식 이어야 합니다.
```
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

예를 들면 다음과 같습니다.

```
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

이전 콘텐츠 전체에서 다음 SQL 쿼리를 사용 하 여 데이터베이스 파일의 논리적 이름을 가져올 수 있습니다.

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


복원 작업을 트리거하기 전에이 파일을 배치 되어야 합니다.

## <a name="next-steps"></a>다음 단계

SQL Server Vm (공개 미리 보기)에 대 한 Azure Backup에 대 한 자세한 내용은 참조 [SQL Vm 용 Azure Backup](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)합니다.
