---
title: 로그 재생 서비스를 사용 하 여 SQL Managed Instance로 데이터베이스 마이그레이션
description: 로그 재생 서비스를 사용 하 여 SQL Server에서 SQL Managed Instance로 데이터베이스를 마이그레이션하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 0bc00aea67fa2f71599ee62e657e1ca1b0627681
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199852"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>로그 재생 서비스 (미리 보기)를 사용 하 여 SQL Server에서 SQL Managed Instance로 데이터베이스 마이그레이션
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 현재 공개 미리 보기로 제공 되는 LRS (로그 재생 서비스)를 사용 하 여 SQL Server 2008-2019에서 Azure SQL Managed Instance로 데이터베이스 마이그레이션을 수동으로 구성 하는 방법을 설명 합니다. LRS는 SQL Managed Instance를 사용 하도록 설정 되 고 SQL Server 로그 전달 기술을 기반으로 하는 클라우드 서비스입니다. 

[Azure Database Migration Service](/azure/dms/tutorial-sql-server-to-managed-instance) 및 LRS는 동일한 기본 마이그레이션 기술과 동일한 api를 사용 합니다. LRS을 해제 하 여 온-프레미스 SQL Server와 SQL Managed Instance 간에 복잡 한 사용자 지정 마이그레이션과 하이브리드 아키텍처를 추가로 사용할 수 있습니다.

## <a name="when-to-use-log-replay-service"></a>로그 재생 서비스를 사용 하는 경우

마이그레이션을 위해 Azure Database Migration Service를 사용할 수 없는 경우 PowerShell, Azure CLI cmdlet 또는 Api에서 직접 LRS를 사용 하 여 데이터베이스 마이그레이션을 수동으로 빌드하고 SQL Managed Instance에 오케스트레이션 할 수 있습니다. 

다음과 같은 경우 LRS를 사용 하는 것을 고려할 수 있습니다.
- 데이터베이스 마이그레이션 프로젝트에 대 한 추가 제어가 필요 합니다.
- 마이그레이션의 경우 가동 중지 시간에 대 한 제한이 거의 없습니다.
- Database Migration Service 실행 파일은 사용자 환경에 설치할 수 없습니다.
- Database Migration Service 실행 파일에 데이터베이스 백업에 대 한 파일 액세스 권한이 없습니다.
- 사용할 수 있는 호스트 OS에 대 한 액세스 권한이 없거나 관리자 권한이 없는 경우
- 사용자 환경에서 Azure로 네트워크 포트를 열 수 없습니다.
- 백업은 옵션을 통해 Azure Blob Storage에 직접 저장 됩니다 `TO URL` .
- 차등 백업을 사용 해야 합니다.

> [!NOTE]
> Database Migration Service를 사용 하 여 SQL Server에서 SQL Managed Instance로 데이터베이스 마이그레이션을 자동화 하는 것이 좋습니다. 이 서비스는 백 엔드에서 로그 전달 모드를 사용 하 여 동일한 LRS 클라우드 서비스를 사용 `NORECOVERY` 합니다. Database Migration Service에서 시나리오를 완전히 지원 하지 않을 때 마이그레이션을 오케스트레이션 하는 데 수동으로 LRS를 사용 하는 것이 좋습니다.

## <a name="how-it-works"></a>작동 방법

LRS를 사용 하 여 클라우드로 데이터베이스를 마이그레이션하는 사용자 지정 솔루션을 빌드하려면이 단원의 뒷부분에 나오는 다이어그램 및 표에 표시 된 것 처럼 몇 가지 오케스트레이션 단계가 필요 합니다.

마이그레이션은 사용 하도록 설정 된 SQL Server에서 전체 데이터베이스 백업을 수행 `CHECKSUM` 하 고 Azure Blob Storage 백업 파일을 복사 하는 것으로 구성 됩니다. LRS는 Blob Storage에서 SQL Managed Instance 백업 파일을 복원 하는 데 사용 됩니다. Blob Storage은 SQL Server와 SQL Managed Instance 사이의 중간 저장소입니다.

LRS는 전체 백업이 복원 된 후 추가 된 새로운 차등 또는 로그 백업에 대 한 Blob Storage를 모니터링 합니다. 그런 다음 LRS는 이러한 새 파일을 자동으로 복원 합니다. 서비스를 사용 하 여 SQL Managed Instance에서 복원 되는 백업 파일의 진행률을 모니터링 하 고 필요한 경우 프로세스를 중지할 수 있습니다.

LRS에는 백업 파일에 대 한 특정 명명 규칙이 필요 하지 않습니다. Blob Storage에 배치 된 모든 파일을 검색 하 고 파일 헤더만 읽을 때만 백업 체인을 생성 합니다. 데이터베이스는 마이그레이션 프로세스 중에 "복원 중" 상태로 있습니다. 데이터베이스는 [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) 모드로 복원 되므로 마이그레이션 프로세스가 완료 될 때까지 읽거나 쓰는 데 사용할 수 없습니다. 

여러 데이터베이스를 마이그레이션하는 경우 다음을 수행 해야 합니다.
 
- 각 데이터베이스에 대 한 백업을 Blob Storage 별도의 폴더에 저장 합니다.
- 각 데이터베이스에 대해 개별적으로 LRS을 시작 합니다.
- 다른 경로를 지정 하 Blob Storage 폴더를 구분 합니다. 

*자동 완성* 또는 *연속* 모드에서 LRS를 시작할 수 있습니다. 자동 완성 모드로 시작 하는 경우 지정 된 백업 파일이 마지막으로 복원 되 면 마이그레이션이 자동으로 완료 됩니다. 연속 모드에서 LRS를 시작 하는 경우 서비스는 추가 된 모든 새 백업 파일을 계속 복원 하며, 마이그레이션은 수동 종료에만 완료 됩니다. 

최종 로그 비상 백업을 수행 하 고 SQL Managed Instance에 복원 된 것으로 표시 한 후 수동으로 잘라내는 것이 좋습니다. 마지막 단계를 수행 하면 데이터베이스가 온라인 상태가 되 고 SQL Managed Instance에서 읽기 및 쓰기가 가능 하 게 됩니다.

자동 완성을 통해 자동으로 또는 LRS을 통해 수동으로 중지 한 후에는 SQL Managed Instance에서 온라인 상태로 전환 된 데이터베이스에 대 한 복원 프로세스를 다시 시작할 수 없습니다. 자동 완성 또는 종료를 통해 마이그레이션이 완료 된 후 추가 백업 파일을 복원 하려면 데이터베이스를 삭제 해야 합니다. 또한 LRS를 다시 시작 하 여 전체 백업 체인을 처음부터 복원 해야 합니다.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="SQL Managed Instance에 대 한 로그 재생 서비스 오케스트레이션 단계를 설명 하는 다이어그램입니다." border="false":::
    
| 작업(Operation) | 세부 정보 |
| :----------------------------- | :------------------------- |
| **1. SQL Server에서 Blob Storage으로 데이터베이스 백업을 복사** 합니다. | [Azcopy](/azure/storage/common/storage-use-azcopy-v10) 또는 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용 하 여 SQL Server에서 Blob Storage 컨테이너로 전체, 차등 및 로그 백업을 복사 합니다. <br /><br />모든 파일 이름을 사용 합니다. LRS에는 특정 파일 명명 규칙이 필요 하지 않습니다.<br /><br />여러 데이터베이스를 마이그레이션하는 경우 각 데이터베이스에 대해 별도의 폴더가 필요 합니다. |
| **2. 클라우드에서 LRS을 시작** 합니다. | PowerShell ([azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) 또는 Azure CLI ([az_sql_midb_log_replay_start cmdlet](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)) cmdlet을 선택 하 여 서비스를 다시 시작할 수 있습니다. <br /><br /> Blob Storage의 백업 폴더를 가리키는 각 데이터베이스에 대해 개별적으로 LRS을 시작 합니다. <br /><br /> 서비스를 시작한 후에는 Blob Storage 컨테이너에서 백업을 수행 하 고 SQL Managed Instance에서 복원을 시작 합니다.<br /><br /> 연속 모드에서 LRS를 시작한 경우 처음 업로드 된 모든 백업이 복원 된 후 서비스는 폴더에 업로드 된 새 파일을 감시 합니다. 서비스는 중지 될 때까지 LSN (로그 시퀀스 번호) 체인을 기반으로 로그를 지속적으로 적용 합니다. |
| **2.1. 작업의 진행 상태를 모니터링 합니다.** | PowerShell ([azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) 또는 Azure CLI ([az_sql_midb_log_replay_show cmdlet](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)) cmdlet을 선택 하 여 복원 작업의 진행률을 모니터링할 수 있습니다. |
| **2.2. 필요한 경우 작업을 중지 합니다.** | 마이그레이션 프로세스를 중지 해야 하는 경우 PowerShell ([azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) 또는 Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)) cmdlet 중에서 선택할 수 있습니다. <br /><br /> 작업을 중지 하면 SQL Managed Instance에서 복원 하는 데이터베이스가 삭제 됩니다. 작업을 중지 한 후에는 데이터베이스에 대해 LRS를 다시 시작할 수 없습니다. 마이그레이션 프로세스를 처음부터 다시 시작 해야 합니다. |
| **3. 준비가 되 면 클라우드로 이동 합니다.** | 응용 프로그램 및 작업을 중지 합니다. 마지막 로그 비상 백업을 수행 하 고 Azure Blob Storage에 업로드 합니다.<br /><br /> `complete`PowerShell ([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) 또는 Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete)CMDLET을 선택 하 여 LRS 작업을 시작 하 여이 작업을 완료 합니다. 이 작업을 수행 하면 LRS이 중지 되 고 데이터베이스가 온라인 상태가 되어 SQL Managed Instance에서 읽기 및 쓰기가 사용 됩니다.<br /><br /> SQL Server에서 SQL Managed Instance로 응용 프로그램 연결 문자열을 다시 가리킵니다. |

## <a name="requirements-for-getting-started"></a>시작 하기 위한 요구 사항

### <a name="sql-server-side"></a>SQL Server 쪽
- SQL Server 2008-2019
- 데이터베이스의 전체 백업 (하나 또는 여러 개 파일)
- 차등 백업 (하나 또는 여러 파일)
- 로그 백업 (트랜잭션 로그 파일에 대해 분할 안 함)
- `CHECKSUM` 백업 사용 (필수)

### <a name="azure-side"></a>Azure 쪽
- PowerShell Az. SQL 모듈 버전 2.16.0 이상 ( [Azure Cloud Shell](/azure/cloud-shell/)을 통해[설치](https://www.powershellgallery.com/packages/Az.Sql/) 또는 액세스)
- Azure CLI 버전 2.19.0 이상 ([설치 됨](/cli/azure/install-azure-cli))
- 프로 비전 된 Azure Blob Storage 컨테이너
- Blob Storage 컨테이너에 대해 읽기 및 나열 권한이 생성 된 SAS (공유 액세스 서명) 보안 토큰

### <a name="migration-of-multiple-databases"></a>여러 데이터베이스 마이그레이션
서로 다른 데이터베이스에 대 한 백업 파일을 Blob Storage 별도의 폴더에 두어야 합니다.

Blob Storage에서 적절 한 폴더를 가리켜 각 데이터베이스에 대해 개별적으로 LRS을 시작 합니다. LRS는 단일 관리 되는 인스턴스당 최대 100 개의 동시 복원 프로세스를 지원할 수 있습니다.

### <a name="azure-rbac-permissions"></a>Azure RBAC 권한
제공 된 클라이언트를 통해 LRS을 실행 하려면 다음 Azure 역할 중 하나가 필요 합니다.
- 구독 소유자 역할
- [Managed Instance 참가자](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 역할
- 다음 권한이 있는 사용자 지정 역할: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>모범 사례

다음과 같은 최선의 구현 방법을 권장합니다.
- [Data Migration Assistant](/sql/dma/dma-overview) 를 실행 하 여 데이터베이스를 SQL Managed Instance 마이그레이션할 준비가 되었는지 확인 합니다. 
- 단일 파일을 사용 하는 대신 전체 및 차등 백업을 여러 파일로 분할 합니다.
- 백업 압축을 사용 하도록 설정 합니다.
- 스크립트를 실행 하려면 Cloud Shell를 사용 합니다 .이 cmdlet은 항상 릴리스된 최신 cmdlet으로 업데이트 되기 때문입니다.
- LRS을 시작한 후 47 시간 이내에 마이그레이션을 완료할 계획입니다. 이는 시스템 관리 소프트웨어 패치를 설치할 수 없도록 하는 유예 기간입니다.

> [!IMPORTANT]
> - 마이그레이션 프로세스가 완료 될 때까지 LRS를 통해 복원 되는 데이터베이스를 사용할 수 없습니다. 
> - LRS는 마이그레이션하는 동안 데이터베이스에 대 한 읽기 전용 액세스를 지원 하지 않습니다.
> - 마이그레이션이 완료 되 면 LRS 복원 프로세스를 다시 시작 하는 것을 지원 하지 않으므로 마이그레이션 프로세스가 종료 됩니다.

## <a name="steps-to-execute"></a>실행 단계

### <a name="make-backups-of-sql-server"></a>SQL Server의 백업 만들기

다음 옵션 중 하나를 사용 하 여 SQL Server의 백업을 수행할 수 있습니다.

- 사용자 환경에서 Blob Storage에 대 한 직접 백업을 제한 하는 경우 로컬 디스크 저장소에 백업한 다음 Azure Blob Storage에 파일을 업로드 합니다.
- `TO URL`환경 및 보안 프로시저에서 허용 하는 경우 t-sql의 옵션을 사용 하 여 Blob Storage에 직접 백업 합니다. 

로그 백업을 허용 하기 위해 마이그레이션할 데이터베이스를 전체 복구 모드로 설정 합니다.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

로컬 저장소에 데이터베이스의 전체, 차등 및 로그 백업을 수동으로 만들려면 다음 샘플 T-sql 스크립트를 사용 합니다. `CHECKSUM`LRS에 필수 이므로 옵션을 사용 하도록 설정 해야 합니다.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

Azure Blob Storage은 SQL Server와 SQL Managed Instance 간의 백업 파일에 대 한 중간 저장소로 사용 됩니다. 저장소 계정 내에 새 저장소 계정 및 blob 컨테이너를 만들려면 다음 단계를 수행 합니다.

1. [스토리지 계정 만들기](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. 저장소 계정 내에서 [blob 컨테이너를 만듭니다](../../storage/blobs/storage-quickstart-blobs-portal.md) .

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>SQL Server에서 Blob Storage 백업 복사

LRS를 사용 하 여 데이터베이스를 관리 되는 인스턴스로 마이그레이션하는 경우 다음 방법을 사용 하 여 Blob Storage에 백업을 업로드할 수 있습니다.
- SQL Server 기본 [백업에서 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 기능 사용
- [Azcopy](/azure/storage/common/storage-use-azcopy-v10) 또는 [Azure Storage 탐색기](https://azure.microsoft.com/en-us/features/storage-explorer) 를 사용 하 여 blob 컨테이너에 백업 업로드
- Azure Portal에서 Storage 탐색기 사용

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>SQL Server에서 Blob Storage로 직접 백업 만들기
회사 및 네트워크 정책에서이를 허용 하는 경우에는 SQL Server 네이티브 [백업 URL 옵션을](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 사용 하 여 SQL Server에서 Blob Storage로 직접 백업할 수 있습니다. 이 옵션을 사용할 수 있는 경우 로컬 저장소에 백업을 만들고 Blob Storage에 업로드할 필요가 없습니다.

첫 번째 단계로이 작업을 수행 하려면 Blob Storage에 대 한 SAS 인증 토큰을 생성 한 다음 SQL Server 토큰을 가져와야 합니다. 두 번째 단계는 T-sql의 옵션을 사용 하 여 백업을 수행 하는 것입니다 `TO URL` . 옵션을 사용 하도록 설정 하 여 모든 백업이 수행 되었는지 확인 `CHEKSUM` 합니다.

참조를 위해 다음 샘플 코드는 Blob Storage에 대 한 백업을 수행 합니다. 이 예제에는 SAS 토큰을 가져오는 방법에 대 한 지침이 포함 되어 있지 않습니다. SQL Server에 대 한 SAS 토큰을 생성 하 고 가져오는 방법을 포함 하 여 자세한 지침을 찾을 수 있습니다. 자습서의 자습서에서는 [SQL Server을 사용](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)하 여 Azure Blob Storage를 사용 합니다. 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>LRS에 대 한 Blob Storage SAS 인증 토큰 생성

Azure Blob Storage은 SQL Server와 SQL Managed Instance 간의 백업 파일에 대 한 중간 저장소로 사용 됩니다. LRS에 대 한 목록 및 읽기 권한만 있는 SAS 인증 토큰을 생성 해야 합니다. 토큰은 LRS가 Blob Storage에 액세스 하 고 백업 파일을 사용 하 여 SQL Managed Instance에서 복원할 수 있도록 합니다. 

토큰을 생성 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Storage 탐색기를 엽니다.
2. **Blob 컨테이너** 를 확장 합니다.
3. Blob 컨테이너를 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기** 를 선택 합니다.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="S 인증 토큰을 생성 하기 위한 선택 항목을 보여 주는 스크린샷":::

4. 토큰 만료 기간을 선택 합니다. 토큰이 마이그레이션 기간 동안 유효한 지 확인 합니다.
5. 토큰에 대 한 표준 시간대 (UTC 또는 현지 시간)를 선택 합니다.
    
   > [!IMPORTANT]
   > 토큰 및 관리 되는 인스턴스의 표준 시간대가 일치 하지 않을 수 있습니다. 표준 시간대를 고려 하 여 SAS 토큰에 적절 한 유효 기간이 있는지 확인 합니다. 가능 하면 계획 된 마이그레이션 기간 이전 및 이후 시간으로 표준 시간대를 설정 합니다.
6. **읽기** 및 **목록** 사용 권한만을 선택 합니다.

   > [!IMPORTANT]
   > 다른 사용 권한을 선택 하지 마십시오. 이렇게 하면 LRS이 시작 되지 않습니다. 이 보안 요구 사항은 의도적으로 설계 되었습니다.
7. **만들기** 를 선택합니다.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="만들기 단추와 함께 s 토큰 만료, 표준 시간대 및 사용 권한에 대 한 선택 항목을 보여 주는 스크린샷":::

지정 된 시간 동안 SAS 인증이 생성 됩니다. 다음 스크린샷에 표시 된 것 처럼 토큰의 URI 버전이 필요 합니다.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="S A S 토큰의 U R I 버전 예를 보여 주는 스크린샷":::

### <a name="copy-parameters-from-the-sas-token"></a>SAS 토큰에서 매개 변수 복사

SAS 토큰을 사용 하 여 LRS를 시작 하기 전에 해당 구조를 이해 해야 합니다. 생성 된 SAS 토큰의 URI는 다음 예와 같이 물음표 ()로 구분 된 두 부분으로 구성 됩니다 `?` .

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="로그 재생 서비스용 S 토큰을 생성 하는 U R I의 예입니다." border="false":::

첫 번째 부분은 물음표 ()로 시작 하 여 `https://` `?` LRS에 대 한 `StorageContainerURI` 입력으로 공급 되는 매개 변수에 사용 됩니다. 데이터베이스 백업 파일이 저장 된 폴더에 대 한 LRS 정보를 제공 합니다.

물음표 () 다음에 시작 하 `?` 고 문자열의 끝까지 모든 방법으로 이동 하는 두 번째 부분은 `StorageContainerSasToken` 매개 변수입니다. 지정 된 시간 동안 유효한 실제 서명 된 인증 토큰입니다. 예제에 표시 된 것 처럼이 파트는를 반드시 시작 해야 하는 것은 아닙니다 `sp=` . 사용자의 사례는 다를 수 있습니다.

다음과 같이 매개 변수를 복사 합니다.

1. `https://`물음표 ()까지 모든 방향에서 시작 하 여 토큰의 첫 번째 부분을 복사 합니다 `?` . `StorageContainerUri`PowerShell에서 매개 변수로 사용 하거나 LRS를 시작 하는 Azure CLI을 사용 합니다.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="토큰의 첫 번째 부분을 복사 하는 과정을 보여 주는 스크린샷":::

2. 문자열의 끝까지 물음표 ()에서 시작 하 여 토큰의 두 번째 부분을 복사 `?` 합니다. `StorageContainerSasToken`PowerShell에서 매개 변수로 사용 하거나 LRS를 시작 하는 Azure CLI을 사용 합니다.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="토큰의 두 번째 부분을 복사 하는 방법을 보여 주는 스크린샷":::
   
> [!NOTE]
> 토큰의 일부를 복사할 때 물음표를 포함 하지 않습니다.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Azure에 로그인 하 고 구독을 선택 합니다.

다음 PowerShell cmdlet을 사용 하 여 Azure에 로그인 합니다.

```powershell
Login-AzAccount
```

다음 PowerShell cmdlet을 사용 하 여 관리 되는 인스턴스가 있는 적절 한 구독을 선택 합니다.

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>마이그레이션 시작

LRS를 시작 하 여 마이그레이션을 시작 합니다. 자동 완성 또는 연속 모드에서 서비스를 시작할 수 있습니다. 

자동 완성 모드를 사용 하는 경우 지정 된 백업 파일이 마지막으로 복원 되 면 마이그레이션이 자동으로 완료 됩니다. 이 옵션을 사용 하려면 start 명령이 마지막 백업 파일의 파일 이름을 지정 해야 합니다. 

연속 모드를 사용 하는 경우 서비스는 추가 된 새 백업 파일을 계속 복원 합니다. 마이그레이션은 수동의 경우에만 완료 됩니다. 

### <a name="start-lrs-in-autocomplete-mode"></a>자동 완성 모드로 LRS 시작

자동 완성 모드로 LRS을 시작 하려면 다음 PowerShell 또는 Azure CLI 명령을 사용 합니다. 매개 변수를 사용 하 여 마지막 백업 파일 이름을 지정 합니다 `-LastBackupName` . 지정 된 백업 파일의 마지막을 복원 하면 서비스가 자동으로 시작 됩니다.

PowerShell을 사용 하 여 자동 완성 모드로 LRS을 시작 하는 예제는 다음과 같습니다.

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Azure CLI를 사용 하 여 자동 완성 모드로 LRS을 시작 하는 예는 다음과 같습니다.

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>연속 모드에서 LRS 시작

PowerShell을 사용 하 여 연속 모드에서 LRS를 시작 하는 예는 다음과 같습니다.

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Azure CLI를 사용 하 여 연속 모드에서 LRS를 시작 하는 예는 다음과 같습니다.

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

연속 모드에서 LRS를 시작 하기 위한 PowerShell 및 CLI 클라이언트는 동기식입니다. 즉, 클라이언트는 API 응답에서 작업을 시작 하는 데 성공 또는 실패를 보고할 때까지 대기 합니다. 

이 대기 중에 명령이 명령 프롬프트에 컨트롤을 반환 하지 않습니다. 마이그레이션 환경을 스크립팅 하는 경우 스크립트의 나머지 부분을 계속 하기 위해 LRS start 명령이 즉시 다시 제공 되도록 하려면 스위치를 사용 하 여 PowerShell을 백그라운드 작업으로 실행할 수 있습니다 `-AsJob` . 다음은 그 예입니다. 

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

백그라운드 작업을 시작 하면 작업을 완료 하는 데 오랜 시간이 소요 되는 경우에도 작업 개체가 즉시 반환 됩니다. 작업이 실행되는 동안 중단 없이 세션에서 작업을 계속할 수 있습니다. 백그라운드 작업으로 PowerShell을 실행 하는 방법에 대 한 자세한 내용은 [Powershell 시작-작업](/powershell/module/microsoft.powershell.core/start-job#description) 설명서를 참조 하세요.

마찬가지로, Linux에서 Azure CLI 명령을 백그라운드 프로세스로 시작 하려면 `&` LRS start 명령의 끝에 앰퍼샌드 ()를 사용 합니다.

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> LRS를 시작 하면 시스템에서 관리 하는 모든 소프트웨어 패치가 47 시간 동안 중지 됩니다. 이 창 후에는 다음 자동화 된 소프트웨어 패치가 자동으로 LRS을 중지 합니다. 이런 경우 마이그레이션을 다시 시작 하 고 처음부터 다시 시작 해야 합니다. 

## <a name="monitor-the-migration-progress"></a>마이그레이션 진행률 모니터링

PowerShell을 통해 마이그레이션의 진행 상황을 모니터링 하려면 다음 명령을 사용 합니다.

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Azure CLI를 통해 마이그레이션의 진행률을 모니터링 하려면 다음 명령을 사용 합니다.

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>마이그레이션 중지

마이그레이션을 중지 해야 하는 경우 다음 cmdlet을 사용 합니다. 마이그레이션을 중지 하면 SQL Managed Instance에서 복원 중인 데이터베이스가 삭제 되므로 마이그레이션을 다시 시작할 수 없습니다.

PowerShell을 통해 마이그레이션 프로세스를 중지 하려면 다음 명령을 사용 합니다.

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Azure CLI를 통해 마이그레이션 프로세스를 중지 하려면 다음 명령을 사용 합니다.

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>마이그레이션 완료 (연속 모드)

연속 모드에서 LRS를 시작한 경우 모든 백업이 복원 되었는지 확인 한 후에는 시작을 시작 하면 마이그레이션이 완료 됩니다. 이를 통해 데이터베이스를 마이그레이션하고 읽기 및 쓰기 권한이 준비 됩니다.

PowerShell을 통해 LRS 연속 모드의 마이그레이션 프로세스를 완료 하려면 다음 명령을 사용 합니다.

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Azure CLI를 통해 LRS 연속 모드의 마이그레이션 프로세스를 완료 하려면 다음 명령을 사용 합니다.

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>기능 제한 사항

LRS의 기능 제한 사항은 다음과 같습니다.
- 복원 하는 데이터베이스는 마이그레이션 프로세스 중에 읽기 전용 액세스에 사용할 수 없습니다.
- LRS를 시작한 후에는 시스템 관리 소프트웨어 패치가 47 시간 동안 차단 됩니다. 이 기간이 만료 되 면 다음 소프트웨어 업데이트는 LRS을 중지 합니다. 그런 다음 LRS를 처음부터 다시 시작 해야 합니다.
- LRS를 사용 하려면 옵션을 사용 하도록 설정 하 여 SQL Server의 데이터베이스를 백업 해야 `CHECKSUM` 합니다.
- LRS에서 사용할 SAS 토큰은 전체 Azure Blob Storage 컨테이너에 대해 생성 되어야 하며 읽기 및 나열 권한만 있어야 합니다.
- 서로 다른 데이터베이스에 대 한 백업 파일은 Blob Storage에서 별도의 폴더에 배치 해야 합니다.
- Blob Storage에서 백업 파일을 사용 하 여 별도의 폴더를 가리키는 각 데이터베이스에 대해 LRS을 별도로 시작 해야 합니다.
- LRS는 단일 관리 되는 인스턴스당 최대 100 개의 동시 복원 프로세스를 지원할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

LRS를 시작한 후에는 모니터링 cmdlet ( `get-azsqlinstancedatabaselogreplay` 또는)을 사용 `az_sql_midb_log_replay_show` 하 여 작업 상태를 확인 합니다. 잠시 후 LRS가 시작 되지 않고 오류가 발생 하는 경우 가장 일반적인 문제를 확인 합니다.

- SQL Managed Instance의 기존 데이터베이스가 SQL Server 마이그레이션하는 것과 동일한 이름을 갖고 있나요? 데이터베이스 중 하나의 이름을 바꿔서이 충돌을 해결 합니다.
- 옵션을 통해 SQL Server 데이터베이스 백업이 수행 `CHECKSUM` 되었습니까?
- SAS 토큰에 대 한 사용 권한은 LRS에 대 한 읽기 및 목록만 표시 되나요?
- 물음표 () 다음에 LRS에 대 한 SAS 토큰을 복사 했습니까 `?` ? 콘텐츠는 다음과 같이 표시 됩니다. `sv=2020-02-10...` 
- 마이그레이션을 시작 하 고 완료 하는 기간에 해당 하는 SAS 토큰 유효 시간입니다. SQL Managed Instance 및 SAS 토큰에 사용 되는 서로 다른 표준 시간대로 인해 불일치가 발생할 수 있습니다. SAS 토큰을 다시 생성 하 고 현재 날짜 전과 후의 시간 창의 토큰 유효성을 확장 합니다.
- 데이터베이스 이름, 리소스 그룹 이름 및 관리 되는 인스턴스 이름의 철자가 정확한 지 확인 합니다.
- 자동 완성 모드로 LRS을 시작한 경우는 지정 된 마지막 백업 파일의 올바른 파일 이름 입니까?

## <a name="next-steps"></a>다음 단계
- [SQL 관리 되는 인스턴스로 SQL Server를 마이그레이션하는](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)방법에 대해 자세히 알아보세요.
- [SQL Server와 SQL Managed Instance의 차이점](transact-sql-tsql-differences-sql-server.md)에 대해 자세히 알아보세요.
- [Azure로 마이그레이션하는 작업을 비용 및 크기 조정 하는 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)에 대해 자세히 알아보세요.
