---
title: 로그 재생 서비스를 사용 하 여 SQL Managed Instance로 데이터베이스 마이그레이션
description: 로그 재생 서비스를 사용 하 여 SQL Server에서 SQL Managed Instance로 데이터베이스를 마이그레이션하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/17/2021
ms.openlocfilehash: 7892b1fe0fcad77d1fde8b44f4a8745b5c7dd334
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654422"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>로그 재생 서비스를 사용 하 여 SQL Server에서 SQL Managed Instance로 데이터베이스 마이그레이션
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 LRS (로그 재생 서비스)를 사용 하 여 SQL Server 2008-2019에서 SQL Managed Instance로 데이터베이스 마이그레이션을 수동으로 구성 하는 방법을 설명 합니다. 이 서비스는 복구 안 함 모드의 SQL Server 로그 전달 기술을 기반으로 관리 되는 인스턴스에 대해 사용 하도록 설정 된 클라우드 서비스입니다. LRS는 DMS (데이터 마이그레이션 서비스)를 사용할 수 없는 경우, 추가 제어가 필요 하거나 가동 중지 시간에 대 한 허용 오차를 거의 없는 경우에 사용 해야 합니다.

## <a name="when-to-use-log-replay-service"></a>로그 재생 서비스를 사용 하는 경우

[AZURE DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) 를 마이그레이션에 사용할 수 없는 경우 POWERSHELL, CLI CMDLET 또는 API와 함께 LRS 클라우드 서비스를 직접 사용 하 여 데이터베이스 마이그레이션을 수동으로 빌드하고 SQL 관리 되는 인스턴스로 오케스트레이션 할 수 있습니다. 

다음과 같은 경우에 LRS 클라우드 서비스를 사용 하는 것을 고려할 수 있습니다.
- 데이터베이스 마이그레이션 프로젝트에 대 한 추가 제어가 필요 합니다.
- 마이그레이션하는 동안 가동 중지 시간에 대 한 제한이 거의 없습니다.
- 사용자 환경에 DMS 실행 파일을 설치할 수 없습니다.
- DMS 실행 파일에 데이터베이스 백업에 대 한 파일 액세스 권한이 없습니다.
- 호스트 OS에 액세스할 수 없거나 관리자 권한이 없습니다.

> [!NOTE]
> SQL Server에서 SQL Managed Instance로 데이터베이스를 마이그레이션하는 데 권장 되는 자동화 된 방법은 Azure DMS를 사용 하는 것입니다. 이 서비스는 백 엔드에서 로그 전달이 복구 안 함 모드로 동일한 LRS 클라우드 서비스를 사용 하 고 있습니다. Azure DMS가 시나리오를 완전히 지원 하지 않는 경우 LRS를 사용 하 여 마이그레이션을 오케스트레이션 하는 것이 좋습니다.

## <a name="how-does-it-work"></a>작동 원리

LRS를 사용 하 여 클라우드로 데이터베이스를 마이그레이션하는 사용자 지정 솔루션을 구축 하려면 다이어그램에 표시 되는 몇 가지 오케스트레이션 단계와 아래 표에 설명 되어 있습니다.

마이그레이션은 SQL Server에 전체 데이터베이스 백업을 만들고 백업 파일을 Azure Blob 저장소에 복사 합니다. LRS는 Azure Blob storage에서 SQL 관리 되는 인스턴스로 백업 파일을 복원 하는 데 사용 됩니다. Azure Blob storage는 SQL Server와 SQL Managed Instance 사이에 중간 저장소로 사용 됩니다.

LRS는 새 차등 또는 전체 백업이 복원 된 후 추가 되는 로그 백업에 대해 Azure Blob 저장소를 모니터링 하 고 추가 된 새 파일을 자동으로 복원 합니다. SQL 관리 되는 인스턴스에서 복원 되는 백업 파일의 진행률을 서비스를 사용 하 여 모니터링할 수 있으며, 필요한 경우 프로세스를 중단할 수도 있습니다. 마이그레이션 프로세스 중에 복원 되는 데이터베이스는 복원 모드가 되며 프로세스가 완료 될 때까지 읽거나 쓰는 데 사용할 수 없습니다.

LRS는 자동 완성 또는 연속 모드에서 시작할 수 있습니다. 자동 완성 모드로 시작 하는 경우 지정 된 마지막 백업 파일이 복원 되 면 마이그레이션이 자동으로 완료 됩니다. 연속 모드에서 시작 되는 경우 서비스는 추가 된 모든 새 백업 파일을 지속적으로 복원 하 고, 마이그레이션은 수동 시작에만 완료 됩니다. 마지막으로이 단계를 수행 하면 SQL Managed Instance에서 데이터베이스를 읽고 쓸 수 있습니다. 

  ![SQL Managed Instance에 대해 설명 된 로그 재생 서비스 오케스트레이션 단계](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| 작업 | 세부 정보 |
| :----------------------------- | :------------------------- |
| **1. SQL Server에서 Azure Blob storage로 데이터베이스 백업을 복사** 합니다. | - [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) 또는 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용 하 여 SQL Server에서 Azure Blob storage로 전체, 차등 및 로그 백업을 복사 합니다. <br />-여러 데이터베이스를 마이그레이션하는 경우 각 데이터베이스에 별도의 폴더가 필요 합니다. |
| **2. 클라우드에서 LRS 서비스를 시작** 합니다. | -다음과 같은 cmdlet을 선택 하 여 서비스를 시작할 수 있습니다. <br /> PowerShell [시작-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start cmdlet](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)입니다. <br /><br />-서비스는 시작 되 면 Azure Blob 저장소에서 백업을 수행 하 고 SQL Managed Instance에 대 한 복원을 시작 합니다. <br /> -처음에 업로드 된 모든 백업이 복원 되 면 서비스는 폴더에 업로드 된 새 파일을 감시 하 고 서비스가 중지 될 때까지 LSN 체인에 따라 로그를 지속적으로 적용 합니다. |
| **2.1. 작업 진행률을 모니터링 하십시오.** | -복원 작업의 진행 상황을 선택 하 여 모니터링할 수 있습니다. <br /> PowerShell [azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show cmdlet](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)입니다. |
| **2.2. Stop\abort 경우 작업을 중단** 합니다. | -마이그레이션 프로세스를 중단 해야 하는 경우에는 cmdlet을 선택 하 여 작업을 중지할 수 있습니다. <br /> PowerShell [중지-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) cmdlet입니다. <br /><br />-이로 인해 SQL Managed Instance에서 데이터베이스가 복원 됩니다. <br />-일단 중지 되 면 데이터베이스에 대해 LRS를 계속할 수 없습니다. 마이그레이션 프로세스를 처음부터 다시 시작 해야 합니다. |
| **3. 준비가 되 면 클라우드로 전환 합니다.** | -모든 백업이 SQL Managed Instance으로 복원 되 면 선택 된 API 호출 또는 cmdlet을 사용 하 여 LRS 완료 작업을 시작 하 여이 작업을 완료 합니다. <br />PowerShell [완료-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) cmdlet입니다. <br /><br />-이로 인해 LRS 서비스가 중지 되 고 Managed Instance 데이터베이스가 복구 됩니다. <br />-SQL Server에서 SQL Managed Instance로 응용 프로그램 연결 문자열을 가리킵니다. <br />-작업 완료 데이터베이스는 클라우드에서 R/W 작업에 사용할 수 있습니다. |

## <a name="requirements-for-getting-started"></a>시작 하기 위한 요구 사항

### <a name="sql-server-side"></a>SQL Server 쪽
- SQL Server 2008-2019
- 데이터베이스의 전체 백업 (하나 또는 여러 개 파일)
- 차등 백업 (하나 또는 여러 파일)
- 로그 백업 (트랜잭션 로그 파일에 대해 분할 안 함)
- **CHECKSUM** 은 필수로 설정 되어야 합니다.

### <a name="azure-side"></a>Azure 쪽
-   PowerShell Az. SQL 모듈 버전 2.16.0 이상 (Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/)[설치](https://www.powershellgallery.com/packages/Az.Sql/)또는 사용)
-   CLI 버전 2.19.0 이상 ([설치](https://docs.microsoft.com/cli/azure/install-azure-cli))
-   프로 비전 된 Azure Blob Storage
-   Blob 저장소에 대해 **읽기** 및 **나열** 전용 권한이 생성 된 SAS 보안 토큰

## <a name="best-practices"></a>모범 사례

다음은 모범 사례에서 매우 권장 되는 방법입니다.
- [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) 를 실행 하 여 데이터베이스의 유효성을 검사 하는 동안 SQL Managed Instance에 대 한 문제가 발생 하지 않는지 확인 합니다. 
- 전체 및 차등 백업을 단일 파일이 아닌 여러 파일로 분할 합니다.
- 백업 압축을 사용 하도록 설정 합니다.
- Cloud Shell를 사용 하 여 항상 릴리스된 최신 cmdlet으로 업데이트 될 때 스크립트를 실행 합니다.
- LRS 서비스가 시작 된 이후 47 시간 이내에 마이그레이션을 완료할 계획입니다.

> [!IMPORTANT]
> - 마이그레이션 프로세스가 완료 될 때까지 LRS를 사용 하 여 복원 되는 데이터베이스를 사용할 수 없습니다. 이는 기본 기술이 복구 안 함 모드에서 로그 전달 이기 때문입니다.
> - 로그 전달에 대 한 대기 모드는 SQL Managed Instance와 최신 시장 SQL Server 버전 간의 버전 차이로 인해 LRS에서 지원 되지 않습니다.

## <a name="steps-to-execute"></a>실행 단계

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>SQL Server에서 Azure Blob storage로 백업 복사

다음 두 가지 방법을 사용 하 여 LRS를 사용 하 여 데이터베이스를 Managed Instance으로 마이그레이션하는 blob 저장소에 백업을 복사할 수 있습니다.
- SQL Server 기본 백업을 사용 하 여 [URL 기능을](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 사용 합니다.
- [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)또는 [Azure Storage 탐색기](https://azure.microsoft.com/en-us/features/storage-explorer)를 사용 하 여 Blob 컨테이너에 백업을 복사 합니다. 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Azure Blob 및 SAS 인증 토큰 만들기

Azure Blob storage는 SQL Server와 SQL Managed Instance 간 백업 파일에 대 한 중간 저장소로 사용 됩니다. Azure Blob 저장소 컨테이너를 만들려면 다음 단계를 수행 합니다.

1. [스토리지 계정을 만드는](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. 저장소 계정 내에서 [blob 컨테이너를](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

Blob 컨테이너를 만든 후에는 다음 단계에 따라 읽기 및 나열 권한으로 SAS 인증 토큰을 생성 합니다.

1. Azure Portal를 사용 하 여 저장소 계정 액세스
2. Storage 탐색기 탐색
3. BLOB 컨테이너 확장
4. Blob 컨테이너를 마우스 오른쪽 단추로 클릭 합니다.
5. 공유 액세스 서명 가져오기를 선택 합니다.
6. 토큰 만료 기간을 선택 합니다. 토큰이 마이그레이션 기간 동안 유효한 지 확인 합니다.
7. 읽기 및 나열 권한만 선택 되었는지 확인
8. 만들기 클릭
9. 코드에서 사용할 URI에서 "sv ="로 시작 하는 토큰을 복사 합니다.

> [!IMPORTANT]
> Azure Blob storage의 SAS 토큰에 대 한 사용 권한은 읽기 및 목록만 가능 해야 합니다. SAS 인증 토큰에 대해 다른 사용 권한을 부여 하는 경우 LRS 서비스를 시작할 수 없습니다. 이러한 보안 요구 사항은 의도적으로 설계 되었습니다.

## <a name="log-in-to-azure-and-select-subscription"></a>Azure에 로그인 하 고 구독을 선택 합니다.

다음 PowerShell cmdlet을 사용 하 여 Azure에 로그인 합니다.

```powershell
Login-AzAccount
```

다음 PowerShell cmdlet을 사용 하 여 SQL Managed Instance 있는 적절 한 구독을 선택 합니다.

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>마이그레이션 시작

LRS 서비스를 시작 하 여 마이그레이션을 시작 합니다. 자동 완성 또는 연속 모드에서 서비스를 시작할 수 있습니다. 자동 완성 모드로 시작 하는 경우 지정 된 마지막 백업 파일이 복원 되 면 마이그레이션이 자동으로 완료 됩니다. 이 옵션을 사용 하려면 start 명령이 마지막 백업 파일의 파일 이름을 지정 해야 합니다. LRS가 연속 모드에서 시작 되 면 서비스는 추가 된 모든 새 백업 파일을 지속적으로 복원 하 고, 마이그레이션은 수동 시작에만 완료 됩니다. 

### <a name="start-lrs-in-autocomplete-mode"></a>자동 완성 모드로 LRS 시작

자동 완성 모드로 LRS 서비스를 시작 하려면 다음 PowerShell 또는 CLI 명령을 사용 합니다. -LastBackupName 매개 변수를 사용 하 여 마지막 백업 파일 이름을 지정 합니다. 지정 된 마지막 백업 파일 이름을 복원할 때 서비스가 자동으로 시작 됩니다.

자동 완성 모드로 LRS 시작-PowerShell 예제:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

자동 완성 모드에서 LRS 시작-CLI 예제:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>연속 모드에서 LRS 시작

연속 모드에서 LRS 시작-PowerShell 예제:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

연속 모드에서 LRS 시작-CLI 예제:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> LRS가 시작 되 면 시스템에서 관리 하는 소프트웨어 패치가 다음 47 시간 동안 중단 됩니다. 이 창을 전달할 때 다음 자동 소프트웨어 패치는 지속적인 LRS를 자동으로 중지 합니다. 이러한 경우 마이그레이션을 재개할 수 없으며, 마이그레이션을 처음부터 다시 시작 해야 합니다. 

## <a name="monitor-the-migration-progress"></a>마이그레이션 진행률 모니터링

마이그레이션 작업 진행률을 모니터링 하려면 다음 PowerShell 명령을 사용 합니다.

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

마이그레이션 작업 진행률을 모니터링 하려면 다음 CLI 명령을 사용 합니다.

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>마이그레이션 중지

마이그레이션을 중지 해야 하는 경우 다음 cmdlet을 사용 합니다. 마이그레이션을 중지 하면 마이그레이션을 다시 시작할 수 없기 때문에 SQL 관리 되는 인스턴스에서 복원 중인 데이터베이스가 삭제 됩니다.

마이그레이션 프로세스를 중지 하려면 다음 PowerShell 명령을 사용 합니다.

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

마이그레이션 프로세스를 중지 하려면 다음 CLI 명령을 사용 합니다.

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>마이그레이션 완료 (연속 모드)

LRS가 연속 모드에서 시작 되는 경우 모든 백업이 복원 되었는지 확인 한 후에는 시작을 시작 하면 마이그레이션이 완료 됩니다. 완료 되 면 데이터베이스가 마이그레이션되고 읽기 및 쓰기 권한이 준비 됩니다.

LRS 연속 모드에서 마이그레이션 프로세스를 완료 하려면 다음 PowerShell 명령을 사용 합니다.

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" -LastBackupName "last_backup.bak"
```

LRS 연속 모드에서 마이그레이션 프로세스를 완료 하려면 다음 CLI 명령을 사용 합니다.

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>다음 단계
- [SQL 관리 되는 인스턴스로 SQL Server 마이그레이션](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)에 대해 자세히 알아보세요.
- [SQL Server와 AZURE SQL Managed Instance의 차이점](transact-sql-tsql-differences-sql-server.md)에 대해 자세히 알아보세요.
- [Azure로 마이그레이션하는 작업을 비용 및 크기 조정 하는 모범 사례](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)에 대해 자세히 알아보세요.
