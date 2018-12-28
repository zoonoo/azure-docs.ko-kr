---
title: SQL Server VM용 Azure Backup 문제 해결 가이드 | Microsoft Docs
description: SQL Server VM을 Azure로 백업하는 문제 해결 정보입니다.
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/19/2018
ms.author: anuragm
ms.custom: ''
ms.openlocfilehash: 89344b6e06dbc62fe56c0aebc30a049aebf5c097
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339521"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Azure에 SQL Server 백업 문제 해결

이 문서에서는 Azure에서 SQL Server VM을 보호하는 작업에 대한 문제 해결 정보를 제공합니다(미리 보기).

## <a name="public-preview-limitations"></a>공개 미리 보기 제한 사항

공개 미리 보기 제한 사항을 확인하려면 [Azure에 SQL Server 데이터베이스 백업](backup-azure-sql-database.md#public-preview-limitations) 문서를 참조하세요.

## <a name="sql-server-permissions"></a>SQL Server 사용 권한

가상 머신에서 SQL Server 데이터베이스에 대한 보호를 구성하려면 가상 머신에 **AzureBackupWindowsWorkload** 확장이 설치되어 있어야 합니다. **UserErrorSQLNoSysadminMembership** 오류가 발생하면 SQL 인스턴스에서 필요한 백업 권한이 없다는 의미입니다. 이 오류를 해결하려면 [마켓플레이스 SQL VM이 아닌 VM에 대한 사용 권한 설정](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)의 단계를 수행하세요.

## <a name="troubleshooting-errors"></a>오류 문제 해결

SQL Server를 Azure로 보호하는 동안 발생한 문제와 오류를 해결하려면 다음 표의 정보를 사용하십시오.

## <a name="backup-failures"></a>백업 실패

다음 표는 오류 코드별로 구성되어 있습니다.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 이 SQL 데이터베이스는 요청된 백업 유형을 지원하지 않습니다. | 요청된 백업 유형을 데이터베이스 복구 모델에서 허용하지 않을 때 발생합니다. 이 오류는 다음과 같은 상황에서 발생할 수 있습니다. <br/><ul><li>단순 복구 모델을 사용하는 데이터베이스에 로그 백업이 허용되지 않습니다.</li><li>마스터 데이터베이스에 차등 및 로그 백업이 허용되지 않습니다.</li></ul>자세한 내용은 [SQL 복구 모델](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) 설명서를 참조하세요. | 단순 복구 모델에서 DB에 대한 로그 백업이 실패하면 다음 옵션 중 하나를 시도합니다.<ul><li>데이터베이스가 단순 복구 모드인 경우 로그 백업을 사용하지 않도록 설정합니다.</li><li>[SQL 설명서](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)를 사용하여 데이터베이스 복구 모델을 전체 또는 대량 기록으로 변경합니다. </li><li> 여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있고 복구 모델을 변경하지 않으려는 경우에는 오류를 무시합니다. 전체 및 차등 백업은 일정에 따라 작동합니다. 로그 백업은 예상대로 건너뜁니다.</li></ul>마스터 데이터베이스를 사용하면서 차등 백업이나 로그 백업을 구성한 경우 다음 중 원하는 단계를 사용합니다.<ul><li>포털을 사용하여 마스터 데이터베이스의 백업 정책 일정을 전체로 변경합니다.</li><li>여러 데이터베이스를 백업하는 변경할 수 없는 표준 정책이 있는 경우에는 오류를 무시합니다. 전체 백업은 일정에 따라 작동합니다. 차등 또는 로그 백업은 예상대로 발생하지 않습니다.</li></ul> |
| 충돌하는 작업이 동일한 데이터베이스에서 이미 실행 중이기 때문에 작업이 취소되었습니다. | 동시에 실행되는 [백업 및 복원 제한 사항에 대한 블로그 항목](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)을 참조하세요.| [SSMS(SQL Server Management Studio)를 사용하여 백업 작업을 모니터링합니다.](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) 충돌하는 작업이 실패하면 작업을 다시 시작합니다.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 데이터베이스가 없습니다. | 데이터베이스 삭제되었거나 이름이 변경되었습니다. | <ul><li>데이터베이스가 실수로 삭제되었거나 이름이 변경되었는지 확인합니다.</li><li>데이터베이스가 실수로 삭제된 경우 백업을 계속하려면 데이터베이스를 원래 위치로 복원합니다.</li><li>데이터베이스를 삭제했고 향후 백업이 필요하지 않은 경우에는 Recovery Services 자격 증명 모음에서 ["데이터 삭제/보관"을 통해 백업 중지](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms)를 클릭합니다.</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 로그 체인이 손상되었습니다. | 데이터베이스 또는 VM은 로그 체인을 자르는 다른 백업 솔루션을 사용하여 백업됩니다.|<ul><li>다른 백업 솔루션이나 스크립트를 사용 중인지 확인합니다. 이런 경우 백업 솔루션을 중지합니다. </li><li>백업이 임시 로그 백업인 경우, 전체 백업을 트리거하여 새 로그 체인을 시작합니다. 예약된 로그 백업의 경우, Azure Backup 서비스가 자동으로 전체 백업을 트리거하여 이 문제가 해결되기 때문에 아무 작업도 필요하지 않습니다.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup에서 SQL 인스턴스에 연결할 수 없습니다. | Azure Backup은 SQL 인스턴스에 연결할 수 없습니다. | Azure Portal 오류 메뉴의 추가 세부 정보를 사용하여 근본 원인의 범위를 좁힙니다. 오류를 해결하려면 [SQL 백업 문제 해결](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)을 참조하세요.<br/><ul><li>기본 SQL 설정이 원격 연결을 허용하지 않는 경우에는 설정을 변경합니다. 설정 변경은 아래 링크를 참조하세요.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>로그인 문제가 있는 경우 아래 링크를 참조하여 문제를 해결합니다.<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 이 데이터 원본에 대한 첫 번째 전체 백업이 없습니다. | 데이터베이스에 대한 전체 백업이 누락되었습니다. 로그 및 차등 백업은 전체 백업을 상위 백업으로 사용하기 때문에 차등 백업 또는 로그 백업을 트리거하기 전에 전체 백업을 수행해야 합니다. | 임시 전체 백업을 트리거합니다.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터 원본의 트랜잭션 로그가 꽉 차서 백업을 수행할 수 없습니다. | 데이터베이스 트랜잭션 로그 공간이 꽉 찼습니다. | 이 문제를 해결하려면 [SQL 설명서](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)를 참조하세요. |
| 이 SQL 데이터베이스는 요청된 백업 유형을 지원하지 않습니다. | Always On AG 보조 복제본은 전체 및 차등 백업을 지원하지 않습니다. | <ul><li>임시 백업을 트리거하는 경우에는 주 노드에서 백업을 트리거합니다.</li><li>백업이 정책에 따라 예약된 경우 주 노드가 등록되어 있는지 확인합니다. 노드를 등록하려면 [ SQL Server 데이터베이스를 검색하는 단계를 수행하십시오](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> |

## <a name="restore-failures"></a>복원 실패

다음과 같은 오류 코드는 복원 작업이 실패하는 경우 표시됩니다.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상 위치에 이름이 같은 데이터베이스가 이미 있습니다. | 대상 복원 대상에 이름이 같은 데이터베이스가 이미 있습니다.  | <ul><li>대상 데이터베이스 이름을 변경합니다.</li><li>또는 복원 페이지에서 강제 덮어쓰기 옵션을 사용합니다.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 데이터베이스를 오프라인 상태로 만들지 못해서 복원에 실패했습니다. | 복원 작업을 수행하는 동안 대상 데이터베이스를 오프라인 상태로 만들어야 합니다. Azure Backup에서 이 데이터를 오프라인 상태로 만들 수 없습니다. | Azure Portal 오류 메뉴의 추가 세부 정보를 사용하여 근본 원인의 범위를 좁힙니다. 자세한 내용은 [SQL 설명서](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)를 참조하십시오. |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 대상에서 지문이 포함된 서버 인증서를 찾을 수 없습니다. | 대상 인스턴스의 마스터 데이터베이스에 유효한 암호화 지문이 없습니다. | 원본 인스턴스에서 사용되는 유효한 인증서 지문을 대상 인스턴스로 가져옵니다. |

## <a name="registration-failures"></a>등록 오류

다음 오류 코드는 등록 실패에 해당합니다.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 가용성 그룹의 일부 노드가 등록되지 않아서 SQL Always On 가용성 그룹에 대한 백업 기본 설정을 충족할 수 없습니다. | 백업을 수행하는 데 필요한 노드가 등록되지 않았거나 연결할 수 없습니다. | <ul><li>이 데이터베이스의 백업을 수행하는 데 필요한 모든 노드가 등록되어 있고 정상 상태인지 확인하고 작업을 다시 시도합니다.</li><li>SQL Always On 가용성 그룹 백업 기본 설정을 변경합니다.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| SQL 서버 VM이 종료되어 Azure Backup 서비스에 액세스할 수 없습니다. | VM이 종료되었습니다. | SQL Server가 실행 중인지 확인합니다. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| Azure Backup 서비스가 Azure VM 게스트 에이전트를 사용하여 백업을 수행하는 데 대상 서버에서 게스트 에이전트를 사용할 수 없습니다. | 게스트 에이전트가 활성화되지 않았거나 정상 상태가 아닙니다. | [VM 게스트 에이전트를 수동으로 설치](../virtual-machines/extensions/agent-windows.md)합니다. |

## <a name="configure-backup-failures"></a>백업 실패 구성

다음 오류 코드는 백업 실패 구성을 위한 것입니다.

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 오류 메시지 | 가능한 원인 | 권장 작업 |
|---|---|---|
| 자동 보호 의도가 제거되었거나 더 이상 유효하지 않습니다. | SQL 인스턴스에서 자동 보호를 사용하도록 설정하면 **백업 구성** 작업이 해당 인스턴스에 있는 모든 데이터베이스에 대해 실행됩니다. 작업을 실행하는 동안 자동 보호를 사용하지 않도록 설정하는 경우 **In-Progress** 작업이 이 오류 코드로 취소됩니다. | 나머지 모든 데이터베이스를 보호하기 위해 다시 한 번 자동 보호를 사용하도록 설정합니다. |

## <a name="next-steps"></a>다음 단계

SQL Server VM용 Azure Backup(공개 미리 보기)에 대한 자세한 내용은 [SQL VM용 Azure Backup(공개 미리 보기)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)을 참조하세요.
