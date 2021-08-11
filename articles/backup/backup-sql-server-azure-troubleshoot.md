---
title: SQL Server 데이터베이스 백업 문제 해결
description: Azure Backup을 사용하여 Azure VM에서 실행되는 SQL Server 데이터베이스를 백업하는 경우의 문제 해결 정보입니다.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: bc53494ec8dad7cab4a1cf267e9ad838b9d34a29
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108277429"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Azure Backup을 사용하여 SQL Server 데이터베이스 백업 문제 해결

이 문서에서는 Azure 가상 머신에서 실행되는 SQL Server 데이터베이스의 문제 해결 정보를 제공합니다.

백업 프로세스 및 제한에 대한 자세한 내용은 [Azure VM의 SQL Server 백업 정보](sql-support-matrix.md#feature-considerations-and-limitations)를 참조하세요.

## <a name="sql-server-permissions"></a>SQL Server 사용 권한

가상 머신에서 SQL Server 데이터베이스에 대한 보호를 구성하려면 가상 머신에 **AzureBackupWindowsWorkload** 확장을 설치해야 합니다. **UserErrorSQLNoSysadminMembership** 오류가 발생하면 SQL Server 인스턴스에 필요한 백업 권한이 없다는 의미입니다. 이 오류를 해결하려면 [VM 권한 설정](backup-azure-sql-database.md#set-vm-permissions)의 단계를 수행합니다.

## <a name="troubleshoot-discover-and-configure-issues"></a>검색 및 구성 문제 해결

Recovery Services 자격 증명 모음을 만들고 구성한 후에는 2단계 프로세스를 통해 데이터베이스를 검색하고 백업을 구성합니다.<br>

![백업 목표 - Azure VM의 SQL Server](./media/backup-azure-sql-database/sql.png)

백업 구성 중에 SQL VM 및 해당 인스턴스가 **VM에서 DB 검색** 및 **백업 구성** 에 표시되지 않으면(위의 이미지 참조) 다음을 확인합니다.

### <a name="step-1-discovery-dbs-in-vms"></a>1단계: VM에서 DB 검색

- VM이 검색된 VM 목록에 나열되지 않고 다른 자격 증명 모음의 SQL 백업에도 등록되지 않은 경우 [SQL Server 백업 검색](./backup-sql-server-database-azure-vms.md#discover-sql-server-databases) 단계를 따릅니다.

### <a name="step-2-configure-backup"></a>2단계: 백업 구성

- SQL VM이 데이터베이스 보호에 사용되는 것과 동일한 자격 증명 모음에 등록된 자격 증명 모음인 경우 [백업 구성](./backup-sql-server-database-azure-vms.md#configure-backup) 단계를 따릅니다.

SQL VM을 새 자격 증명 모음에 등록해야 하는 경우 이전 자격 증명 모음에서 등록을 취소해야 합니다.  자격 증명 모음에서 SQL VM을 등록 취소하려면 보호된 모든 데이터 원본의 보호를 중지해야 합니다. 그 후 백업된 데이터를 삭제할 수 있습니다. 백업된 데이터 삭제는 파괴적 작업입니다.  SQL VM 등록 취소와 관련된 모든 예방 조치를 검토하고 수행했으면 이 동일한 VM을 새 자격 증명 모음에 등록하고 백업 작업을 다시 시도합니다.

## <a name="troubleshoot-backup-and-recovery-issues"></a>백업 및 복구 문제 해결  

때때로 백업 및 복원 작업에서 임의의 오류가 발생하거나 이러한 작업이 중단될 수 있습니다. VM의 바이러스 백신 프로그램이 그 원인일 수 있습니다. 모범 사례로써 다음 단계를 따르는 것이 좋습니다.

1. 바이러스 스캔에서 다음 폴더를 제외합니다.

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.AzureBackupWindowsWorkload`

    `C:\`를 해당하는 *SystemDrive* 문자로 바꿉니다.

1. VM 내에서 실행되는 다음 세 가지 프로세스를 바이러스 백신 스캔에서 제외합니다.

    - IaasWLPluginSvc.exe
    - IaaSWorkloadCoordinatorService.exe
    - TriggerExtensionJob.exe

1. 또한 SQL은 바이러스 백신 프로그램 사용에 대한 몇 가지 지침을 제공합니다. 자세한 내용은 [이 아티클](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server)을 참조하세요.

## <a name="faulty-instance-in-a-vm-with-multiple-sql-server-instances"></a>여러 SQL Server 인스턴스를 사용하는 VM의 잘못된 인스턴스

VM 내에서 실행되는 모든 SQL 인스턴스가 정상 상태로 보고되는 경우에만 SQL VM으로 복원할 수 있습니다. 하나 이상의 인스턴스에 "문제"가 있는 경우 VM이 복원 대상으로 표시되지 않습니다. 이로 인해 복원 작업 중에 다중 인스턴스 VM이 "서버" 드롭다운 목록에 표시되지 않을 수 있습니다.

**백업 구성** 에서 VM에 있는 모든 SQL 인스턴스의 "백업 준비 상태"를 확인할 수 있습니다.

![백업 준비 상태 확인](./media/backup-sql-server-azure-troubleshoot/backup-readiness.png)

정상적인 SQL 인스턴스에 복원을 트리거하려면 다음 단계를 수행합니다.

1. SQL VM에 로그인하고 `C:\Program Files\Azure Workload Backup\bin`으로 이동합니다.
1. `ExtensionSettingsOverrides.json`이라는 JSON 파일을 만듭니다(아직 없는 경우). 이 파일이 VM에 이미 있는 경우 해당 파일을 계속 사용합니다.
1. JSON 파일에 다음 콘텐츠를 추가하고 파일을 저장합니다.

    ```json
    {
                  "<ExistingKey1>":"<ExistingValue1>",
                    …………………………………………………… ,
              "whitelistedInstancesForInquiry": "FaultyInstance_1,FaultyInstance_2"
            }
            
            Sample content:        
            { 
              "whitelistedInstancesForInquiry": "CRPPA,CRPPB "
            }

    ```

1. Azure Portal에서(백업 준비 상태를 확인할 수 있는 위치와 동일) 영향을 받는 서버에 대한 **DB 다시 검색** 작업을 트리거합니다. VM이 복원 작업의 대상으로 표시되기 시작합니다.

    ![DB 다시 검색](./media/backup-sql-server-azure-troubleshoot/rediscover-dbs.png)

1. 복원 작업이 완료되면 ExtensionSettingsOverrides.json 파일에서 *whitelistedInstancesForInquiry* 항목을 제거합니다.

## <a name="error-messages"></a>오류 메시지

### <a name="backup-type-unsupported"></a>백업 형식이 지원되지 않습니다

| 심각도 | 설명 | 가능한 원인 | 권장 조치 |
|---|---|---|---|
| Warning | 이 데이터베이스의 현재 설정은 연결된 정책에 있는 특정 백업 형식을 지원하지 않습니다. | <li>master 데이터베이스에서는 전체 데이터베이스 백업 작업만 수행할 수 있습니다. 차등 백업 및 트랜잭션 로그 백업은 불가능합니다. </li> <li>단순 복구 모델의 데이터베이스는 트랜잭션 로그 백업을 허용하지 않습니다.</li> | 정책의 모든 백업 형식이 지원되도록 데이터베이스 설정을 수정합니다. 또는 지원되는 백업 형식만 포함하도록 현재 정책을 변경합니다. 이렇게 하지 않으면 예약된 백업 중에 지원되지 않는 백업 형식을 건너뛰거나, 주문형 백업의 경우에는 백업 작업이 실패합니다.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 이 SQL 데이터베이스는 요청된 백업 유형을 지원하지 않습니다. | 요청된 백업 유형을 데이터베이스 복구 모델에서 허용하지 않을 때 발생합니다. 이 오류는 다음과 같은 상황에서 발생할 수 있습니다. <br/><ul><li>단순 복구 모델을 사용하는 데이터베이스는 로그 백업을 허용하지 않습니다.</li><li>master 데이터베이스는 차등 백업 및 로그 백업을 허용하지 않습니다.</li></ul>자세한 내용은 [SQL Server 복구 모델](/sql/relational-databases/backup-restore/recovery-models-sql-server) 설명서를 참조하세요. | 단순 복구 모델에서 데이터베이스에 대한 로그 백업이 실패하면 다음 옵션 중 하나를 시도합니다.<ul><li>데이터베이스가 단순 복구 모드인 경우 로그 백업을 사용하지 않도록 설정합니다.</li><li>[SQL Server 설명서](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)를 사용하여 데이터베이스 복구 모델을 전체 또는 대량 기록으로 변경합니다. </li><li> 여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있고 복구 모델을 변경하지 않으려는 경우에는 오류를 무시합니다. 전체 및 차등 백업은 일정에 따라 작동합니다. 로그 백업은 예상대로 건너뜁니다.</li></ul>master 데이터베이스를 사용하면서 차등 백업이나 로그 백업을 구성한 경우 다음 중 원하는 단계를 사용합니다.<ul><li>포털을 사용하여 master 데이터베이스의 백업 정책 일정을 전체로 변경합니다.</li><li>여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있는 경우에는 오류를 무시합니다. 전체 백업은 일정에 따라 작동합니다. 차등 또는 로그 백업은 예상대로 발생하지 않습니다.</li></ul> |

### <a name="operationcancelledbecauseconflictingoperationrunningusererror"></a>OperationCancelledBecauseConflictingOperationRunningUserError

| 오류 메시지 | 가능한 원인 | 권장 조치 |
|---|---|---|
| 충돌하는 작업이 동일한 데이터베이스에서 이미 실행 중이기 때문에 작업이 취소되었습니다. | 다음은 이 오류 코드가 나타날 수 있는 원인입니다.<br><ul><li>백업이 진행되는 동안 데이터베이스에 파일을 추가하거나 삭제합니다.</li><li>데이터베이스 백업이 진행 중인 동안 파일을 축소합니다.</li><li>데이터베이스에 대해 구성된 다른 백업 제품이 데이터베이스 백업을 진행 중인 동안 Azure Backup 확장이 백업 작업을 트리거합니다.</li></ul>| 다른 백업 제품을 사용하지 않도록 설정하여 문제를 해결합니다.


### <a name="usererrorfilemanipulationisnotallowedduringbackup"></a>UserErrorFileManipulationIsNotAllowedDuringBackup

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터베이스에서의 백업, 파일 조작 작업(예: ALTER DATABASE ADD FILE) 및 암호화 변경 사항은 직렬화되어야 합니다. | 주문형으로 트리거되거나 예약된 백업 작업이 동일한 데이터베이스의 Azure Backup 확장에 의해 트리거되어 이미 실행 중인 백업 작업과 충돌하는 경우 이 오류가 발생할 수 있습니다.<br> 이 오류 코드가 표시될 수 있는 시나리오는 다음과 같습니다.<br><ul><li>데이터베이스에서 전체 백업이 실행 중인 동안 또 다른 전체 백업이 트리거됩니다.</li><li>데이터베이스에서 차등 백업이 실행 중인 동안 또 다른 차등 백업이 트리거됩니다.</li><li>데이터베이스에서 로그 백업이 실행 중인 동안 또 다른 로그 백업이 트리거됩니다.</li></ul>| 충돌하는 작업이 실패한 후 작업을 다시 시작합니다.


### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 데이터베이스가 없습니다. | 데이터베이스 삭제되었거나 이름이 변경되었습니다. | 데이터베이스가 실수로 삭제되었거나 이름이 변경되었는지 확인합니다.<br/><br/> 데이터베이스가 실수로 삭제된 경우 백업을 계속하려면 데이터베이스를 원래 위치로 복원합니다.<br/><br/> 데이터베이스를 삭제하고 향후 백업이 필요하지 않은 경우 Recovery Services 자격 증명 모음에서 **백업 데이터 유지** 또는 **백업 데이터 삭제** 와 함께 **백업 중지** 를 선택합니다. 자세한 내용은 [백업한 SQL Server 데이터베이스 관리 및 모니터링](manage-monitor-sql-database-backup.md)을 참조하세요.

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 로그 체인이 손상되었습니다. | 로그 체인을 자르는 또 다른 백업 솔루션을 통해 데이터베이스 또는 VM이 백업됩니다.|<ul><li>다른 백업 솔루션이나 스크립트를 사용 중인지 확인합니다. 이런 경우 백업 솔루션을 중지합니다. </li><li>백업이 주문형 로그 백업인 경우 전체 백업을 트리거하여 새 로그 체인을 시작합니다. 예약된 로그 백업인 경우 Azure Backup 서비스가 자동으로 전체 백업을 트리거하여 이 문제가 해결되기 때문에 아무 것도 할 필요가 없습니다.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup에서 SQL 인스턴스에 연결할 수 없습니다. | Azure Backup이 SQL Server 인스턴스에 연결할 수 없습니다. | Azure Portal 오류 메뉴의 추가 세부 정보를 사용하여 근본 원인의 범위를 좁힙니다. 오류를 해결하려면 [SQL 백업 문제 해결](/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)을 참조하세요.<br/><ul><li>기본 SQL 설정이 원격 연결을 허용하지 않는 경우에는 설정을 변경합니다. 설정 변경에 대한 자세한 내용은 다음 문서를 참조하세요.<ul><li>[MSSQLSERVER_-1](/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>로그인 문제가 있는 경우 다음 링크를 사용하여 문제를 해결합니다.<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 이 데이터 원본에 대한 첫 번째 전체 백업이 없습니다. | 데이터베이스에 대한 전체 백업이 누락되었습니다. 로그 백업 및 차등 백업은 전체 백업의 상위 백업이므로, 차등 백업 또는 로그 백업을 트리거하기 전에 전체 백업을 수행해야 합니다. | 주문형 전체 백업을 트리거합니다.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터 원본의 트랜잭션 로그가 꽉 차서 백업을 수행할 수 없습니다. | 데이터베이스 트랜잭션 로그 공간이 꽉 찼습니다. | 이 문제를 해결하려면 [SQL Server 설명서](/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)를 참조하세요. |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상 위치에 이름이 같은 데이터베이스가 이미 있습니다. | 타겟 복원 대상에 이름이 같은 데이터베이스가 이미 있습니다.  | <ul><li>대상 데이터베이스 이름을 변경합니다.</li><li>또는 복원 페이지에서 강제 덮어쓰기 옵션을 사용합니다.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터베이스를 오프라인 상태로 만들지 못해서 복원에 실패했습니다. | 복원 작업을 수행하는 동안 대상 데이터베이스를 오프라인으로 전환해야 합니다. Azure Backup은 이 데이터를 오프라인으로 전환할 수 없습니다. | Azure Portal 오류 메뉴의 추가 세부 정보를 사용하여 근본 원인의 범위를 좁힙니다. 자세한 내용은 [SQL Server 설명서](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)를 참조하세요. |

### <a name="wlextgenericiofaultusererror"></a>WlExtGenericIOFaultUserError

|오류 메시지 |가능한 원인  |권장 작업  |
|---------|---------|---------|
|작업을 수행하는 동안 입출력 오류가 발생했습니다. 가상 머신에서 일반적인 IO 오류를 확인합니다.   |   대상에 대한 액세스 권한 또는 공간 제약 조건       |  가상 머신에서 일반적인 IO 오류를 확인합니다. 머신의 대상 드라이브/네트워크 공유가 다음 조건을 충족하는지 확인합니다. <li> 머신의 NT AUTHORITY\SYSTEM에 대한 읽기/쓰기 권한이 있습니다. <li> 작업을 완료하는 데 필요한 공간이 충분합니다.<br> 자세한 내용은 [파일로 복원](restore-sql-database-azure-vm.md#restore-as-files)을 참조하세요.
       |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상에서 지문이 포함된 서버 인증서를 찾을 수 없습니다. | 대상 인스턴스의 master 데이터베이스에 유효한 암호화 지문이 없습니다. | 원본 인스턴스에서 사용되는 유효한 인증서 지문을 대상 인스턴스로 가져옵니다. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 복구에 사용되는 로그 백업에 대량 로그된 변경 내용이 포함되어 있습니다. SQL 지침에 따라 임의의 시점에서 중지하도록 사용할 수 없습니다. | 데이터베이스가 대량 로그 복구 모드인 경우 대량 로그 트랜잭션과 다음 로그 트랜잭션 간에 데이터를 복구할 수 없습니다. | 다른 복구 시점을 선택합니다. [자세한 정보를 알아보세요](/sql/relational-databases/backup-restore/recovery-models-sql-server).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 가용성 그룹의 일부 노드가 등록되지 않아서 SQL Always On 가용성 그룹에 대한 백업 기본 설정을 충족할 수 없습니다. | 백업을 수행하는 데 필요한 노드가 등록되지 않았거나 연결할 수 없습니다. | <ul><li>이 데이터베이스의 백업을 수행하는 데 필요한 모든 노드가 등록되어 있고 정상 상태인지 확인하고 작업을 다시 시도합니다.</li><li>SQL Server Always On 가용성 그룹의 백업 기본 설정을 변경합니다.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 서버 VM이 종료되어 Azure Backup 서비스에 액세스할 수 없습니다. | VM이 종료되었습니다. | SQL Server 인스턴스가 실행 중인지 확인합니다. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup 서비스가 Azure VM 게스트 에이전트를 사용하여 백업을 수행하는 데 대상 서버에서 게스트 에이전트를 사용할 수 없습니다. | 게스트 에이전트가 활성화되지 않았거나 비정상 상태입니다. | [VM 게스트 에이전트를 수동으로 설치](../virtual-machines/extensions/agent-windows.md)합니다. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 자동 보호 의도가 제거되었거나 더 이상 유효하지 않습니다. | SQL Server 인스턴스에서 자동 보호를 사용하도록 설정하면 **백업 구성** 작업이 해당 인스턴스에 있는 모든 데이터베이스에 대해 실행됩니다. 작업을 실행하는 동안 자동 보호를 사용하지 않도록 설정하는 경우 **In-Progress** 작업이 이 오류 코드로 취소됩니다. | 나머지 데이터베이스를 모두 보호하기 위해 다시 한 번 자동 보호를 사용하도록 설정합니다. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
24시간 동안 허용되는 작업 수 제한에 도달하여 작업이 차단되었습니다. | 24시간 동안 허용되는 최대 작업 제한에 도달하면 이 오류가 발생합니다. <br> 예를 들어 매일 트리거할 수 있는 백업 구성 작업 수 제한에 도달했는데 새 항목에서 백업을 구성하려고 시도하면 이 오류가 발생합니다. | 일반적으로 24시간 후에 작업을 다시 시도하면 이 문제가 해결됩니다. 이렇게 해도 문제가 계속되면 Microsoft 지원에 문의하여 도움을 받을 수 있습니다.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
자격 증명 모음이 24시간 동안 허용되는 최대 작업 제한에 도달하여 작업이 차단되었습니다. | 24시간 동안 허용되는 최대 작업 제한에 도달하면 이 오류가 발생합니다. 이 오류는 일반적으로 정책 수정 또는 자동 보호와 같은 대규모 작업을 수행할 때 발생합니다. CloudDosAbsoluteLimitReached와 달리, 이 상태를 해결하기 위해 할 수 있는 일이 많지 않습니다. 사실, Azure Backup 서비스는 의심스러운 모든 항목에 대해 내부적으로 작업을 다시 시도합니다.<br> 예를 들어 정책으로 보호되는 여러 데이터 원본이 있는데 이 정책을 수정하려고 하면 보호된 각 항목에 대한 보호 구성 작업이 트리거되고 경우에 따라 매일 이러한 작업에 허용되는 최대 제한에 도달할 수 있습니다.| Azure Backup 서비스는 24시간 후에 자동으로 이 작업을 다시 시도합니다.

### <a name="workloadextensionnotreachable"></a>WorkloadExtensionNotReachable

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
AzureBackup 워크로드 확장 작업이 실패했습니다. | VM이 종료되거나 인터넷 연결 문제로 인해 VM이 Azure Backup 서비스에 연결할 수 없습니다.| <li> VM이 실행 중이고 인터넷에 연결되어 있는지 확인합니다.<li> [SQL Server VM에서 확장을 다시 등록](manage-monitor-sql-database-backup.md#re-register-extension-on-the-sql-server-vm)합니다.


### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
VM이 인터넷 연결 문제로 인해 Azure Backup 서비스에 연결할 수 없습니다. | VM에서 Azure Backup 서비스, Azure Storage 또는 Azure Active Directory 서비스에 대한 아웃바운드 연결이 필요합니다.| <li> NSG를 사용하여 연결을 제한하는 경우 *AzureBackup* 서비스 태그를 사용하여 Azure Backup Service에 대한 아웃바운드 액세스를 허용해야 하며, Azure AD(*AzureActiveDirectory*) 및 Azure Storage(*Storage*) 서비스 역시 마찬가지입니다. 액세스 권한을 부여하려면 다음 [단계](./backup-sql-server-database-azure-vms.md#nsg-tags)를 수행합니다. <li> DNS가 Azure 엔드포인트를 확인하는지 확인합니다. <li> VM이 인터넷 액세스를 차단하는 부하 분산 장치 뒤에 있는지 확인합니다. VM에 공용 IP를 할당하면 검색이 작동합니다. <li> 위의 세 가지 대상 서비스에 대한 호출을 차단하는 방화벽/바이러스 백신/프록시가 없는지 확인합니다.

## <a name="re-registration-failures"></a>다시 등록 오류

다시 등록 작업을 트리거하기 전에 하나 이상의 다음 증상을 확인하세요.

- VM에서 백업, 복원, 백업 구성 등의 모든 작업이 실패하고 **[WorkloadExtensionNotReachable](#workloadextensionnotreachable)** , **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg** 오류 코드 중 하나가 표시됩니다.
- 백업 항목에 대한 **백업 상태** 영역에 **연결할 수 없음** 이 표시되면 동일한 상태를 유발하는 다른 모든 원인을 제외합니다.

  - VM에서 백업 관련 작업을 수행할 수 있는 권한이 없습니다.
  - VM이 종료되어 백업을 수행할 수 없습니다.
  - [네트워크 문제](#usererrorvminternetconnectivityissue)

   ![VM 다시 등록](./media/backup-azure-sql-database/re-register-vm.png)

- Always On 가용성 그룹의 경우 백업 기본 설정을 변경한 후 또는 장애 조치(failover) 후에 백업이 실패하기 시작했습니다.

다음 이유 중 하나 이상으로 인해 이러한 현상이 발생할 수 있습니다.

- 확장이 삭제되었거나 포털에서 제거되었습니다.
- VM의 **제어판** > **프로그램 제거 또는 변경** 에서 확장이 제거되었습니다.
- VM이 내부 디스크 복원을 통해 다시 복원되었습니다.
- 오랜 기간 동안 VM이 종료되었으므로 확장 구성이 만료되었습니다.
- VM이 삭제되고 삭제된 VM과 이름이 같고 동일한 리소스 그룹에 있는 다른 VM이 생성되었습니다.
- 가용성 그룹 노드 중 하나가 전체 백업 구성을 받지 못했습니다. 이 문제는 가용성 그룹이 자격 증명 모음에 등록되거나 새 노드가 추가될 때 발생할 수 있습니다.

위의 시나리오에서 VM에 대한 다시 등록 작업을 트리거하는 것이 좋습니다. PowerShell에서 이 작업을 수행하는 방법에 대한 지침은 [여기](./backup-azure-sql-automation.md#enable-backup)를 참조하세요.

## <a name="size-limit-for-files"></a>파일의 크기 제한

파일의 총 문자열 크기는 파일 수뿐만 아니라 파일 이름과 경로에 따라 달라집니다. 각 데이터베이스 파일의 논리적 파일 이름과 실제 경로를 가져옵니다. 다음 SQL 쿼리를 사용하면 됩니다.

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

이제 다음 형식으로 정렬합니다.

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

예를 들면 다음과 같습니다.

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

콘텐츠의 문자열 크기가 20,000바이트를 초과하는 경우 데이터베이스 파일이 다르게 저장됩니다. 복구 중에는 복원의 대상 파일 경로를 설정할 수 없습니다. 파일은 SQL Server에서 제공하는 기본 SQL 경로에 복원됩니다.

### <a name="override-the-default-target-restore-file-path"></a>기본 대상 복원 파일 경로 재정의

복원 작업 중에 데이터베이스 파일의 매핑이 포함된 JSON 파일을 대상 복원 경로에 배치하여 대상 복원 파일 경로를 재정의할 수 있습니다. `database_name.json` 파일을 만들어서 `C:\Program Files\Azure Workload Backup\bin\plugins\SQL*` 위치에 배치합니다.

파일의 내용은 다음 형식이어야 합니다.

```json
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

```json
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

위의 콘텐츠에서 다음 SQL 쿼리를 사용하여 데이터베이스 파일의 논리적 이름을 가져올 수 있습니다.

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
```

이 파일은 복원 작업을 트리거하기 전에 배치해야 합니다.

## <a name="next-steps"></a>다음 단계

SQL Server VM용 Azure Backup(공개 미리 보기)에 대한 자세한 내용은 [SQL VM용 Azure Backup](../azure-sql/virtual-machines/windows/backup-restore.md#azbackup)을 참조하세요.
