---
title: Azure Backup을 사용한 SQL Server 데이터베이스 백업 문제 해결 | Microsoft Docs
description: Azure Backup을 사용하여 Azure VM에서 실행되는 SQL Server 데이터베이스를 백업하는 경우의 문제 해결 정보입니다.
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 05/27/2019
ms.author: anuragm
ms.openlocfilehash: 8459bb451c4ff462ee816b986cafdbf776603917
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306964"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Azure에 SQL Server 백업 문제 해결

이 문서에서는 Azure에서 SQL Server Vm을 보호 하기 위한 문제 해결 정보를 제공 합니다.

## <a name="feature-consideration-and-limitations"></a>기능 고려 사항 및 제한 사항

기능 고려 사항 보기, 문서를 참조 [Azure Vm에서 SQL Server에 대 한 백업](backup-azure-sql-database.md#feature-consideration-and-limitations)합니다.

## <a name="sql-server-permissions"></a>SQL Server 사용 권한

가상 머신에서 SQL Server 데이터베이스에 대한 보호를 구성하려면 가상 머신에 **AzureBackupWindowsWorkload** 확장이 설치되어 있어야 합니다. **UserErrorSQLNoSysadminMembership** 오류가 발생하면 SQL 인스턴스에서 필요한 백업 권한이 없다는 의미입니다. 이 오류를 해결하려면 [마켓플레이스 SQL VM이 아닌 VM에 대한 사용 권한 설정](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)의 단계를 수행하세요.


## <a name="backup-type-unsupported"></a>백업 유형이 지원되지 않습니다

| Severity | 설명 | 가능한 원인 | 권장 작업 |
|---|---|---|---|
| Warning | 이 데이터베이스에 대 한 현재 설정을 특정 유형의 연결 된 정책에 있는 백업 유형 지원 하지 않습니다. | <li>**master DB**: Master 데이터베이스에서 전체 데이터베이스 백업 작업만 수행할 수 있습니다. 모두 **차등** 백업과 트랜잭션 **로그** 백업 불가능 합니다. </li> <li>**단순 복구 모델**의 모든 데이터베이스에서는 트랜잭션 **로그** 백업을 수행할 수 없습니다.</li> | 정책의 모든 백업 유형이 지원되도록 데이터베이스 설정을 수정합니다. 또는 지원되는 백업 유형만 포함하도록 현재 정책을 변경합니다. 이 고, 그렇지 예약 된 백업 중 백업 지원 되지 않는 형식을 건너뜁니다 또는 임시 백업에 대 한 백업 작업이 실패 합니다.


## <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 이 SQL 데이터베이스는 요청된 백업 유형을 지원하지 않습니다. | 요청된 백업 유형을 데이터베이스 복구 모델에서 허용하지 않을 때 발생합니다. 이 오류는 다음과 같은 상황에서 발생할 수 있습니다. <br/><ul><li>단순 복구 모델을 사용하는 데이터베이스에 로그 백업이 허용되지 않습니다.</li><li>마스터 데이터베이스에 차등 및 로그 백업이 허용되지 않습니다.</li></ul>자세한 내용은 [SQL 복구 모델](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) 설명서를 참조하세요. | 단순 복구 모델에서 DB에 대한 로그 백업이 실패하면 다음 옵션 중 하나를 시도합니다.<ul><li>데이터베이스가 단순 복구 모드인 경우 로그 백업을 사용하지 않도록 설정합니다.</li><li>[SQL 설명서](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)를 사용하여 데이터베이스 복구 모델을 전체 또는 대량 기록으로 변경합니다. </li><li> 여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있고 복구 모델을 변경하지 않으려는 경우에는 오류를 무시합니다. 전체 및 차등 백업은 일정에 따라 작동합니다. 로그 백업은 예상대로 건너뜁니다.</li></ul>마스터 데이터베이스를 사용하면서 차등 백업이나 로그 백업을 구성한 경우 다음 중 원하는 단계를 사용합니다.<ul><li>포털을 사용하여 마스터 데이터베이스의 백업 정책 일정을 전체로 변경합니다.</li><li>여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있는 경우에는 오류를 무시합니다. 전체 백업은 일정에 따라 작동합니다. 차등 또는 로그 백업은 예상대로 발생하지 않습니다.</li></ul> |
| 충돌하는 작업이 동일한 데이터베이스에서 이미 실행 중이기 때문에 작업이 취소되었습니다. | 동시에 실행되는 [백업 및 복원 제한 사항에 대한 블로그 항목](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)을 참조하세요.| [SSMS(SQL Server Management Studio)를 사용하여 백업 작업을 모니터링합니다.](manage-monitor-sql-database-backup.md) 충돌하는 작업이 실패하면 작업을 다시 시작합니다.|

## <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 데이터베이스가 없습니다. | 데이터베이스 삭제되었거나 이름이 변경되었습니다. | 데이터베이스가 실수로 삭제되었거나 이름이 변경되었는지 확인합니다.<br/><br/> 데이터베이스가 실수로 삭제된 경우 백업을 계속하려면 데이터베이스를 원래 위치로 복원합니다.<br/><br/> 데이터베이스를 삭제했고 향후 백업이 필요하지 않은 경우에는 Recovery Services 자격 증명 모음에서 ["데이터 삭제/보관"을 통해 백업 중지](manage-monitor-sql-database-backup.md)를 클릭합니다.

## <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 로그 체인이 손상되었습니다. | 데이터베이스 또는 VM은 로그 체인을 자르는 다른 백업 솔루션을 사용하여 백업됩니다.|<ul><li>다른 백업 솔루션이나 스크립트를 사용 중인지 확인합니다. 이런 경우 백업 솔루션을 중지합니다. </li><li>백업 된 임시 로그 백업 인 경우 새 로그 체인을 시작 하려면 전체 백업을 트리거하십시오. 예약된 로그 백업의 경우, Azure Backup 서비스가 자동으로 전체 백업을 트리거하여 이 문제가 해결되기 때문에 아무 작업도 필요하지 않습니다.</li>|

## <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup에서 SQL 인스턴스에 연결할 수 없습니다. | Azure Backup은 SQL 인스턴스에 연결할 수 없습니다. | Azure Portal 오류 메뉴의 추가 세부 정보를 사용하여 근본 원인의 범위를 좁힙니다. 오류를 해결하려면 [SQL 백업 문제 해결](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)을 참조하세요.<br/><ul><li>기본 SQL 설정이 원격 연결을 허용하지 않는 경우에는 설정을 변경합니다. 설정을 변경 하는 방법은 다음 문서를 참조 하세요.<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>로그인 문제가 있는 경우 아래 링크를 참조하여 문제를 해결합니다.<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

## <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 이 데이터 원본에 대한 첫 번째 전체 백업이 없습니다. | 데이터베이스에 대한 전체 백업이 누락되었습니다. 로그 및 차등 백업은 전체 백업을 상위 백업으로 사용하기 때문에 차등 백업 또는 로그 백업을 트리거하기 전에 전체 백업을 수행해야 합니다. | 임시 전체 백업을 트리거하십시오.   |

## <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터 원본의 트랜잭션 로그가 꽉 차서 백업을 수행할 수 없습니다. | 데이터베이스 트랜잭션 로그 공간이 꽉 찼습니다. | 이 문제를 해결하려면 [SQL 설명서](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)를 참조하세요. |

## <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상 위치에 이름이 같은 데이터베이스가 이미 있습니다. | 대상 복원 대상에 이름이 같은 데이터베이스가 이미 있습니다.  | <ul><li>대상 데이터베이스 이름을 변경합니다.</li><li>또는 복원 페이지에서 강제 덮어쓰기 옵션을 사용합니다.</li> |

## <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터베이스를 오프라인 상태로 만들지 못해서 복원에 실패했습니다. | 복원 작업을 수행하는 동안 대상 데이터베이스를 오프라인 상태로 만들어야 합니다. Azure Backup에서 이 데이터를 오프라인 상태로 만들 수 없습니다. | Azure Portal 오류 메뉴의 추가 세부 정보를 사용하여 근본 원인의 범위를 좁힙니다. 자세한 내용은 [SQL 설명서](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)를 참조하십시오. |

##  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상에서 지문이 포함된 서버 인증서를 찾을 수 없습니다. | 대상 인스턴스의 마스터 데이터베이스에 유효한 암호화 지문이 없습니다. | 원본 인스턴스에서 사용되는 유효한 인증서 지문을 대상 인스턴스로 가져옵니다. |

## <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 복구에 사용 되는 로그 백업에 대량 로그 변경 내용이 포함 되어 있습니다. SQL 지침에 따라 시간 임의의 지점에서 중지를 사용할 수 없습니다. | 데이터베이스를 대량 로그 복구 모드의 경우 대량 로그 트랜잭션 및 다음 로그 트랜잭션 간에 데이터를 복구할 수 없습니다. | 복구에 대 한 시간에 다른 시점을 선택 합니다. [자세히 알아보기](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


## <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 가용성 그룹의 일부 노드가 등록되지 않아서 SQL Always On 가용성 그룹에 대한 백업 기본 설정을 충족할 수 없습니다. | 백업을 수행하는 데 필요한 노드가 등록되지 않았거나 연결할 수 없습니다. | <ul><li>이 데이터베이스의 백업을 수행하는 데 필요한 모든 노드가 등록되어 있고 정상 상태인지 확인하고 작업을 다시 시도합니다.</li><li>SQL Always On 가용성 그룹 백업 기본 설정을 변경합니다.</li></ul> |

## <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 서버 VM이 종료되어 Azure Backup 서비스에 액세스할 수 없습니다. | VM이 종료되었습니다. | SQL Server가 실행 중인지 확인합니다. |

## <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup 서비스가 Azure VM 게스트 에이전트를 사용하여 백업을 수행하는 데 대상 서버에서 게스트 에이전트를 사용할 수 없습니다. | 게스트 에이전트가 활성화되지 않았거나 정상 상태가 아닙니다. | [VM 게스트 에이전트를 수동으로 설치](../virtual-machines/extensions/agent-windows.md)합니다. |

## <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 자동 보호 의도가 제거되었거나 더 이상 유효하지 않습니다. | SQL 인스턴스에서 자동 보호를 사용하도록 설정하면 **백업 구성** 작업이 해당 인스턴스에 있는 모든 데이터베이스에 대해 실행됩니다. 작업을 실행하는 동안 자동 보호를 사용하지 않도록 설정하는 경우 **In-Progress** 작업이 이 오류 코드로 취소됩니다. | 나머지 모든 데이터베이스를 보호하기 위해 다시 한 번 자동 보호를 사용하도록 설정합니다. |

## <a name="re-registration-failures"></a>다시 등록 실패

하나 이상의 확인 합니다 [증상](#symptoms) 다시 등록 작업을 트리거하기 전에 합니다.

### <a name="symptoms"></a>증상

* 백업과 같은 모든 작업 복원 및 백업 구성에 다음 오류 코드 중 하나를 사용 하 여 VM에서 실패 하는: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**
* 합니다 **백업 상태** 백업에 대 한 항목이 표시 됩니다 **불가능**합니다. 하지만 동일한 상태에도 발생할 수 있는 다른 모든 원인 배제 해야 합니다.

  * VM의 백업 관련된 작업을 수행할 수 있는 권한이 부족  
  * VM 종료 되었습니다는으로 인해 백업을 실행할 수 없습니다.
  * 네트워크 문제  

    ![VM을 다시 등록 합니다.](./media/backup-azure-sql-database/re-register-vm.png)

* Always on 가용성 그룹의 경우 백업이 실패 하기 시작할 때 장애 조치 또는 된 백업 기본 설정을 변경한 후

### <a name="causes"></a>원인
이러한 현상은 다음 이유 중 하나 이상으로 인해 발생할 수 있습니다.

  * 확장 삭제 되었거나 포털에서 제거 
  * 확장에서 제거 합니다 **Control Panel** 에서 VM의 **제거 또는 변경 프로그램** UI
  * 시간에 전체 디스크 복원을 사용 하 여 복원 된 VM
  * 확장 구성에 원인이 되 만료 연장 된 기간에 대 한 VM은 종료
  * VM이 삭제 및 삭제 된 VM과 동일한 리소스 그룹에 동일한 이름의 다른 VM이 만들어진
  * 전체 백업 구성을 받지 AG 노드 중 하나, 자격 증명 모음에 가용성 그룹 등록 시 하나 또는 새 노드를 추가 하면이 발생할 수 있습니다.  <br>
    위의 시나리오에서 VM에 다시 등록 작업을 트리거하는 것이 좋습니다. 이 옵션만 PowerShell을 통해 사용할 수 있으며도 Azure portal에서 제공 될 예정입니다.

## <a name="files-size-limit-beyond-which-restore-happens-to-default-path"></a>기본 경로에 파일의 크기 제한을 초과 복원 수행

뿐만 아니라 파일의 총 문자열 크기를 파일의 수에 있지만 해당 이름 및 경로에 따라 달라 집니다. 각 데이터베이스 파일의 논리적 파일 이름 및 실제 경로 가져옵니다.
아래 제공 된 SQL 쿼리를 사용할 수 있습니다.

  ```
  SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
                 INNER JOIN sys.databases db ON db.database_id = mf.database_id
                 WHERE db.name = N'<Database Name>'"
 ```

이제 아래 제공 된 형식으로 정렬 합니다.

  ```[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
  ```

예제:

  ```[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
  ```

위에 제공 된 콘텐츠의 문자열 크기를 20, 000 바이트를 초과 하면 데이터베이스 파일 다르게 저장 됩니다 하 고 복구 하는 동안 됩니다 복원에 대 한 대상 파일 경로 설정할 수 있습니다. SQL Server에서 제공 하는 SQL 기본 경로 파일 복원 됩니다.

### <a name="override-the-default-target-restore-file-path"></a>기본 대상 복원 파일 경로 재정의 합니다.

대상 복원 경로에 데이터베이스 파일의 매핑을 포함 하는 JSON 파일을 배치 하 여 복원 작업 중 대상 복원 파일 경로 재정의할 수 있습니다. 이 파일을 만듭니다 `database_name.json` 위치에 배치 *C:\Program Files\Azure 워크 로드 Backup\bin\plugins\SQL*합니다.

파일의 내용을 아래에 지정 된 형식 이어야 합니다.
  ```[
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

예제:

  ```[
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

 
위 내용에서 아래에 지정 된 SQL 쿼리를 사용 하 여 데이터베이스 파일의 논리적 이름을 가져올 수 있습니다.

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


복원 작업을 트리거하기 전에이 파일을 배치 되어야 합니다.

## <a name="next-steps"></a>다음 단계

SQL Server VM용 Azure Backup(공개 미리 보기)에 대한 자세한 내용은 [SQL VM용 Azure Backup(공개 미리 보기)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)을 참조하세요.
