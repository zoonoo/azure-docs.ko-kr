---
title: SQL Server 데이터베이스 백업 문제 해결
description: Azure Backup을 사용하여 Azure VM에서 실행되는 SQL Server 데이터베이스를 백업하는 경우의 문제 해결 정보입니다.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 8d49adb0ab741903ccb2989cfeb4ceaef2e8a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408619"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Azure 백업을 사용하여 SQL Server 데이터베이스 백업 문제 해결

이 문서에서는 Azure 가상 컴퓨터에서 실행되는 SQL Server 데이터베이스에 대한 문제 해결 정보를 제공합니다.

백업 프로세스 및 제한 사항에 대한 자세한 내용은 [Azure VM의 SQL Server 백업 정보를](sql-support-matrix.md#feature-consideration-and-limitations)참조하십시오.

## <a name="sql-server-permissions"></a>SQL Server 사용 권한

가상 컴퓨터에서 SQL Server 데이터베이스에 대한 보호를 구성하려면 해당 가상 컴퓨터에 **AzureBackupWindowsWorkload** 확장을 설치해야 합니다. **사용자ErrorSQLNoSysadminMembership**오류가 발생하면 SQL Server 인스턴스에 필요한 백업 권한이 없다는 의미입니다. 이 오류를 해결하려면 [VM 사용 권한 설정의 단계를 따릅니다.](backup-azure-sql-database.md#set-vm-permissions)

## <a name="troubleshoot-discover-and-configure-issues"></a>문제 발견 및 구성 문제 해결

복구 서비스 자격 증명 모음을 만들고 구성한 후 데이터베이스를 검색하고 백업을 구성하는 것은 2단계 프로세스입니다.<br>

![sql](./media/backup-azure-sql-database/sql.png)

백업 구성 중에 SQL VM 및 해당 인스턴스가 **VM의 검색 B에** 표시되지 않는 경우 **백업 구성(위** 이미지 참조)이 다음과 같은지 확인합니다.

### <a name="step-1-discovery-dbs-in-vms"></a>1단계: VM의 디스커버리 DB

- VM이 검색된 VM 목록에 나열되지 않고 다른 자격 증명 모음에서 SQL 백업에 등록되지 않은 경우 [검색 SQL Server 백업](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases) 단계를 따릅니다.

### <a name="step-2-configure-backup"></a>2단계: 백업 구성

- SQL VM이 데이터베이스를 보호하는 데 사용된 동일한 자격 증명 모음에 등록된 경우 [백업 구성](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup) 단계를 따릅니다.

SQL VM을 새 볼트에 등록해야 하는 경우 이전 자격 증명 모음에서 등록취소해야 합니다.  볼트에서 SQL VM을 등록 취소하려면 보호된 모든 데이터 원본을 중지한 다음 백업된 데이터를 삭제할 수 있습니다. 백업된 데이터를 삭제하는 것은 파괴적인 작업입니다.  SQL VM 등록을 취소하기 위한 모든 예방 조치를 검토하고 수행한 후 이 동일한 VM을 새 볼트에 등록하고 백업 작업을 다시 시도합니다.

## <a name="troubleshoot-backup-and-recovery-issues"></a>백업 및 복구 문제 해결  

경우에 따라 백업 및 복원 작업에서 임의 오류가 발생할 수 있거나 이러한 작업이 중단될 수 있습니다. 이는 VM의 바이러스 백신 프로그램 때문일 수 있습니다. 모범 사례로 다음 단계를 제안합니다.

1. 바이러스 백신 검색에서 다음 폴더를 제외합니다.

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    `C:\` *SystemDrive의*문자로 바꿉입니다.

1. 바이러스 백신 검색에서 VM 내에서 실행되는 다음 세 프로세스를 제외합니다.

    - IaasWLPluginSvc.exe
    - 이아스워크로드코르디나어서비스.exe
    - 트리거 확장작업.exe

1. SQL은 바이러스 백신 프로그램과 함께 작업하는 방법에 대한 몇 가지 지침도 제공합니다. 자세한 내용은 [이 문서를](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) 참조하십시오.

## <a name="error-messages"></a>오류 메시지

### <a name="backup-type-unsupported"></a>지원되지 않는 백업 유형

| 심각도 | 설명 | 가능한 원인 | 권장 작업 |
|---|---|---|---|
| Warning | 이 데이터베이스에 대한 현재 설정은 연결된 정책에 있는 특정 백업 유형을 지원하지 않습니다. | <li>마스터 데이터베이스에서는 전체 데이터베이스 백업 작업만 수행할 수 있습니다. 차등 백업이나 트랜잭션 로그 백업은 불가능합니다. </li> <li>단순 복구 모델의 모든 데이터베이스는 트랜잭션 로그의 백업을 허용하지 않습니다.</li> | 정책의 모든 백업 유형이 지원되도록 데이터베이스 설정을 수정합니다. 또는 지원되는 백업 유형만 포함하도록 현재 정책을 변경합니다. 그렇지 않으면 지원되지 않는 백업 유형이 예약된 백업 중에 건너뛰거나 온디맨드 백업에 대해 백업 작업이 실패합니다.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 이 SQL 데이터베이스는 요청된 백업 유형을 지원하지 않습니다. | 요청된 백업 유형을 데이터베이스 복구 모델에서 허용하지 않을 때 발생합니다. 이 오류는 다음과 같은 상황에서 발생할 수 있습니다. <br/><ul><li>간단한 복구 모델을 사용하는 데이터베이스는 로그 백업을 허용하지 않습니다.</li><li>마스터 데이터베이스에 는 차동 및 로그 백업이 허용되지 않습니다.</li></ul>자세한 내용은 SQL [Server 복구 모델](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) 설명서를 참조하십시오. | 간단한 복구 모델에서 데이터베이스에 대한 로그 백업이 실패하면 다음 옵션 중 하나를 시도해 보십시오.<ul><li>데이터베이스가 단순 복구 모드인 경우 로그 백업을 사용하지 않도록 설정합니다.</li><li>SQL [Server 설명서를](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) 사용하여 데이터베이스 복구 모델을 전체 또는 대량 로깅으로 변경합니다. </li><li> 여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있고 복구 모델을 변경하지 않으려는 경우에는 오류를 무시합니다. 전체 및 차등 백업은 일정에 따라 작동합니다. 로그 백업은 예상대로 건너뜁니다.</li></ul>마스터 데이터베이스이고 차동 또는 로그 백업을 구성한 경우 다음 단계 중 하나를 사용합니다.<ul><li>포털을 사용하여 마스터 데이터베이스의 백업 정책 일정을 전체로 변경합니다.</li><li>여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있는 경우에는 오류를 무시합니다. 전체 백업은 일정에 따라 작동합니다. 차등 또는 로그 백업은 예상대로 발생하지 않습니다.</li></ul> |
| 충돌하는 작업이 동일한 데이터베이스에서 이미 실행 중이기 때문에 작업이 취소되었습니다. | 동시에 실행되는 [백업 및 복원 제한에 대한 블로그 항목을](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) 참조하십시오.| [SQL Server 관리 스튜디오(SSMS)를 사용하여 백업 작업을 모니터링합니다.](manage-monitor-sql-database-backup.md) 충돌하는 작업이 실패하면 작업을 다시 시작합니다.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 데이터베이스가 없습니다. | 데이터베이스 삭제되었거나 이름이 변경되었습니다. | 데이터베이스가 실수로 삭제되었거나 이름이 변경되었는지 확인합니다.<br/><br/> 데이터베이스가 실수로 삭제된 경우 백업을 계속하려면 데이터베이스를 원래 위치로 복원합니다.<br/><br/> 데이터베이스를 삭제하고 향후 백업이 필요하지 않은 경우 복구 서비스 자격 증명 모음에서 **백업 데이터 유지를** 사용하여 **백업 중지를** 선택하거나 **백업 데이터 삭제를**선택합니다. 자세한 내용은 [백업된 SQL Server 데이터베이스 관리 및 모니터링을](manage-monitor-sql-database-backup.md)참조하십시오.

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 로그 체인이 손상되었습니다. | 데이터베이스 또는 VM은 로그 체인을 트러깅하는 다른 백업 솔루션을 통해 백업됩니다.|<ul><li>다른 백업 솔루션이나 스크립트를 사용 중인지 확인합니다. 이런 경우 백업 솔루션을 중지합니다. </li><li>백업이 주문형 로그 백업인 경우 전체 백업을 트리거하여 새 로그 체인을 시작합니다. 예약된 로그 백업의 경우 Azure Backup 서비스가 이 문제를 해결하기 위해 전체 백업을 자동으로 트리거하므로 아무 작업도 필요하지 않습니다.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup에서 SQL 인스턴스에 연결할 수 없습니다. | Azure 백업은 SQL Server 인스턴스에 연결할 수 없습니다. | Azure 포털 오류 메뉴의 추가 세부 정보를 사용하여 근본 원인을 좁힙습니다. 오류를 해결하려면 [SQL 백업 문제 해결](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)을 참조하세요.<br/><ul><li>기본 SQL 설정에서 원격 연결을 허용하지 않는 경우 설정을 변경합니다. 설정 변경에 대한 자세한 내용은 다음 문서를 참조하십시오.<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>로그인 문제가 있는 경우 다음 링크를 사용하여 문제를 해결합니다.<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 이 데이터 원본에 대한 첫 번째 전체 백업이 없습니다. | 데이터베이스에 대한 전체 백업이 누락되었습니다. 로그 및 차등 백업은 전체 백업의 부모이므로 차동 또는 로그 백업을 트리거하기 전에 전체 백업을 수행해야 합니다. | 주문형 전체 백업을 트리거합니다.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터 원본의 트랜잭션 로그가 꽉 차서 백업을 수행할 수 없습니다. | 데이터베이스 트랜잭션 로그 공간이 꽉 찼습니다. | 이 문제를 해결하려면 [SQL Server 설명서를](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)참조하십시오. |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상 위치에 이름이 같은 데이터베이스가 이미 있습니다. | 대상 복원 대상에는 이미 이름이 같은 데이터베이스가 있습니다.  | <ul><li>대상 데이터베이스 이름을 변경합니다.</li><li>또는 복원 페이지에서 강제 덮어쓰기 옵션을 사용합니다.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터베이스를 오프라인 상태로 만들지 못해서 복원에 실패했습니다. | 복원을 수행하는 동안 대상 데이터베이스를 오프라인으로 만들어야 합니다. Azure Backup은 이 데이터를 오프라인상태로 만들 수 없습니다. | Azure 포털 오류 메뉴의 추가 세부 정보를 사용하여 근본 원인을 좁힙습니다. 자세한 내용은 [SQL Server 설명서](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)를 참조하세요. |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상에서 지문이 포함된 서버 인증서를 찾을 수 없습니다. | 대상 인스턴스의 마스터 데이터베이스에 유효한 암호화 지문이 없습니다. | 원본 인스턴스에 사용된 유효한 인증서 지문을 대상 인스턴스로 가져옵니다. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 복구에 사용되는 로그 백업에 대량 로그된 변경 내용이 포함되어 있습니다. SQL 지침에 따라 임의의 시점에서 중지하도록 사용할 수 없습니다. | 데이터베이스가 대량 로깅 된 복구 모드에 있는 경우 대량 로깅 된 트랜잭션과 다음 로그 트랜잭션 간의 데이터를 복구할 수 없습니다. | 복구를 위해 다른 시기를 선택합니다. [자세히 알아봅니다](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 가용성 그룹의 일부 노드가 등록되지 않아서 SQL Always On 가용성 그룹에 대한 백업 기본 설정을 충족할 수 없습니다. | 백업을 수행하는 데 필요한 노드가 등록되지 않았거나 연결할 수 없습니다. | <ul><li>이 데이터베이스의 백업을 수행하는 데 필요한 모든 노드가 등록되고 정상인지 확인하고 작업을 다시 시도합니다.</li><li>SQL Server Always 켜기 가용성 그룹에 대한 백업 기본 설정을 변경합니다.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 서버 VM이 종료되어 Azure Backup 서비스에 액세스할 수 없습니다. | VM이 종료되었습니다. | SQL Server 인스턴스가 실행되고 있는지 확인합니다. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup 서비스가 Azure VM 게스트 에이전트를 사용하여 백업을 수행하는 데 대상 서버에서 게스트 에이전트를 사용할 수 없습니다. | 게스트 에이전트가 활성화되지 않았거나 비정상입니다. | [VM 게스트 에이전트를 수동으로 설치](../virtual-machines/extensions/agent-windows.md)합니다. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 자동 보호 의도가 제거되었거나 더 이상 유효하지 않습니다. | SQL Server 인스턴스에서 자동 보호를 사용하도록 설정하면 해당 인스턴스의 모든 데이터베이스에 대해 **백업 구성** 작업이 실행됩니다. 작업을 실행하는 동안 자동 보호를 사용하지 않도록 설정하는 경우 **In-Progress** 작업이 이 오류 코드로 취소됩니다. | 다시 한 번 자동 보호를 활성화하여 나머지 모든 데이터베이스를 보호할 수 있습니다. |

### <a name="clouddosabsolutelimitreached"></a>클라우드도스절대제한 도달

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
24시간 동안 허용되는 작업 수 제한에 도달하면 작업이 차단됩니다. | 24시간 동안 작업에 대한 최대 허용 한도에 도달하면 이 오류가 발생합니다. <br> 예를 들어 하루에 트리거할 수 있는 구성 백업 작업 수에 대한 제한에 도달한 경우 새 항목에 대한 백업을 구성하려고 하면 이 오류가 표시됩니다. | 일반적으로 24시간 후에 작업을 다시 시도하면 이 문제가 해결됩니다. 그러나 문제가 지속되면 Microsoft 지원팀에 문의하여 도움을 청할 수 있습니다.

### <a name="clouddosabsolutelimitreachedwithretry"></a>클라우드도스절대적도달리리리

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
볼트가 24시간 동안 허용되는 이러한 작업에 대한 최대 제한에 도달하면 작업이 차단됩니다. | 24시간 동안 작업에 대한 최대 허용 한도에 도달하면 이 오류가 발생합니다. 이 오류는 일반적으로 수정 정책 또는 자동 보호와 같은 대규모 작업이 있을 때 발생합니다. CloudDosAbsoluteLimitReached의 경우와 달리 이 상태를 해결하기 위해 수행할 수 있는 작업은 많지 않으며 실제로 Azure Backup 서비스는 문제의 모든 항목에 대해 내부적으로 작업을 다시 시도합니다.<br> 예를 들어 정책으로 보호되는 많은 수의 데이터 원본이 있고 해당 정책을 수정하려고 하면 보호된 각 항목에 대해 보호 작업을 구성하게 되고 경우에 따라 해당 작업에 대해 허용되는 최대 한도에 도달할 수 있습니다.| Azure 백업 서비스는 24시간 후에 이 작업을 자동으로 다시 시도합니다.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
VM은 인터넷 연결 문제로 인해 Azure Backup 서비스에 문의할 수 없습니다. | VM은 Azure 백업 서비스, Azure 저장소 또는 Azure Active Directory 서비스에 대한 아웃바운드 연결이 필요합니다.| - NSG를 사용하여 연결을 제한하는 경우 AzureBackup 서비스 태그를 사용하여 Azure Backup 서비스, Azure 저장소 또는 Azure Active Directory 서비스에 대한 아웃바운드 액세스를 허용해야 합니다. 다음 [단계에](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) 따라 액세스 권한을 부여합니다.<br>- DNS가 Azure 끝점을 해결하고 있는지 확인합니다.<br>- VM이 인터넷 액세스를 차단하는 로드 밸러서 뒤에 있는지 확인합니다. VM에 공용 IP를 할당하면 검색이 작동합니다.<br>- 위의 세 대상 서비스에 대한 호출을 차단하는 방화벽 / 바이러스 백신 / 프록시가 없는지 확인합니다.

## <a name="re-registration-failures"></a>재등록 실패

다시 등록 작업을 트리거하기 전에 다음 증상 중 하나 이상을 확인합니다.

* 모든 작업(예: 백업, 복원 및 백업 구성)이 다음 오류 코드 중 하나를 사용 하 여 VM에서 실패: **워크로드ExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **워크로드ExtensionNotPresent**, **워크로드ExtensionDidntDequeueMsg**.
* 백업 항목의 **백업 상태** 영역에 **연결할 수 없는**상태가 표시되면 동일한 상태가 될 수 있는 다른 모든 원인을 제외합니다.

  * VM에서 백업 관련 작업을 수행할 수 있는 권한이 없습니다.
  * VM을 종료하므로 백업을 수행 할 수 없습니다.
  * 네트워크 문제.

   ![VM 재등록](./media/backup-azure-sql-database/re-register-vm.png)



* Always On 가용성 그룹의 경우 백업 기본 설정을 변경하거나 장애 조치 후 백업이 실패하기 시작했습니다.

이러한 증상은 다음 이유 중 하나 이상 발생할 수 있습니다.

* 확장이 포털에서 삭제되었거나 제거되었습니다.
* **프로그램 제거 또는 변경**중 VM의 **제어판에서** 확장이 제거되었습니다.
* VM은 현재 디스크 복원을 통해 복원되었습니다.
* VM이 오랜 기간 동안 종료되었기 때문에 VM의 확장 구성이 만료되었습니다.
* VM이 삭제되고 다른 VM이 동일한 이름으로 삭제된 VM과 동일한 리소스 그룹에서 만들어졌습니다.
* 가용성 그룹 노드 중 하나가 전체 백업 구성을 받지 못했습니다. 가용성 그룹이 볼트에 등록되거나 새 노드가 추가될 때 발생할 수 있습니다.

앞의 시나리오에서는 VM에서 다시 등록 작업을 트리거하는 것이 좋습니다. PowerShell에서 이 작업을 수행하는 방법에 대한 지침은 [여기를](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) 참조하십시오.

## <a name="size-limit-for-files"></a>파일의 크기 제한

파일의 총 문자열 크기는 파일 수뿐만 아니라 이름과 경로에 따라 달라집니다. 각 데이터베이스 파일에 대해 논리 파일 이름과 물리적 경로를 가져옵니다. 이 SQL 쿼리를 사용할 수 있습니다.

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

콘텐츠의 문자열 크기가 20,000바이트를 초과하면 데이터베이스 파일이 다르게 저장됩니다. 복구 하는 동안 복원에 대 한 대상 파일 경로 설정할 수 없습니다. 파일은 SQL Server에서 제공하는 기본 SQL 경로로 복원됩니다.

### <a name="override-the-default-target-restore-file-path"></a>기본 대상 복원 파일 경로 재정의

데이터베이스 파일의 매핑을 대상 복원 경로에 포함하는 JSON 파일을 배치하여 복원 작업 중에 대상 복원 파일 경로를 재정의할 수 있습니다. `database_name.json` 파일을 만들고 *C:\프로그램 파일\Azure 워크로드 백업\bin\SQL 위치에 배치합니다.*

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

앞의 내용에서 다음 SQL 쿼리를 사용하여 데이터베이스 파일의 논리적 이름을 얻을 수 있습니다.

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

이 파일은 복원 작업을 트리거하기 전에 배치해야 합니다.

## <a name="next-steps"></a>다음 단계

SQL Server VM에 대한 Azure 백업(공개 미리 보기)에 대한 자세한 내용은 [SQL VM에 대한 Azure 백업을](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)참조하십시오.
