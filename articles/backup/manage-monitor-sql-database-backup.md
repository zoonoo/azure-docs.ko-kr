---
title: Azure Backup을 사용하여 백업한 Azure VM에서 SQL Server 데이터베이스 관리 및 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Backup으로 백업되는 Azure VM에서 실행되는 SQL Server 데이터베이스를 복원하는 방법을 설명합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430957"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>백업한 SQL Server 데이터베이스 관리 및 모니터링 


이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 Azure Backup Recovery Services 자격 증명 모음으로 백업된 SQL Server 데이터베이스(Azure VM에서 실행)를 관리하고 모니터링하기 위한 일반적인 작업을 설명합니다. 작업에는 작업 및 경고 모니터링, 데이터베이스 보호 중지 및 다시 시작, 백업 작업 실행, 백업에서 VM 등록 취소 등이 포함됩니다.


> [!NOTE]
> Azure Backup을 사용하여 Azure VM에서 실행되는 SQl Server 데이터베이스의 백업은 현재 공개 미리 보기로 제공됩니다.


SQL Server 데이터베이스에 대해 백업을 아직 구성하지 않은 경우 [이 문서](backup-azure-sql-database.md)의 지침을 따릅니다.

## <a name="monitor-backup-jobs"></a>백업 작업 모니터링

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>포털에서 임시 작업 모니터링

Azure Backup은 데이터베이스 검색 및 등록, 백업 및 복원 작업을 포함하여 **백업 작업** 포털에서 수동으로 트리거된 모든 작업을 표시합니다.

![백업 작업 포털](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> 예약된 백업 작업은 **백업 작업** 포털에 표시되지 않습니다. SQL Server Management Studio를 사용하면 다음 섹션에서 설명하는 대로 예약된 백업 작업을 모니터링할 수 있습니다.
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>SQL Server Management Studio를 사용하여 백업 작업 모니터링 

Azure Backup은 모든 백업 작업에 대해 SQL 네이티브 API를 사용합니다.

네이티브 API를 사용하여 msdb 데이터베이스의 [SQL backupset 테이블](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017)에서 모든 작업 정보를 페치합니다.

다음 예제는 **DB1**이라는 데이터베이스에 대한 모든 백업 작업을 페치하는 쿼리입니다. 고급 모니터링에 대한 쿼리를 사용자 지정합니다.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>백업 경고 보기

로그 백업은 15분마다 발생하기 때문에 백업 작업 모니터링이 번거로울 수 있습니다. Azure Backup은 이메일 경고를 사용하여 모니터링을 간소화합니다.

- 모든 백업 실패에 대해 경고가 트리거됩니다.
- 경고는 오류 코드별로 데이터베이스 수준에서 통합됩니다.
- 이메일 경고는 데이터베이스에 대한 첫 번째 백업 실패에 대해서만 전송됩니다. 

백업 경고를 모니터링하려면:

1. [Azure Portal](https://portal.azure.com)에서 Azure 구독에 로그인하여 데이터베이스 경고를 모니터링합니다.

2. 자격 증명 모음 대시보드에서 **경고 및 이벤트**를 선택합니다.

   ![경고 및 이벤트 선택](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. **경고 및 이벤트**에서 **Backup 경고**를 선택합니다.

   ![백업 경고 선택](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server 데이터베이스에 대한 보호 중지

다음과 같은 여러 가지 방법으로 SQL Server 데이터베이스 백업을 중지할 수 있습니다.

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
* 미래의 모든 백업 작업을 중지하지만 복구 지점을 원래 상태로 유지

다음 사항에 유의하세요.

복구 지점에서 나가면 해당 지점은 백업 정책에 따라 정리됩니다. 모든 복구 지점이 정리될 때까지 보호된 인스턴스 및 사용된 스토리지에 대해 요금이 부과됩니다. 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/backup/).
- 복구 지점을 원래 상태로 유지할 경우 보존 정책에 따라 만료되더라도 Azure Backup은 사용자가 백업 데이터를 명시적으로 삭제할 때까지 마지막 하나의 복구 지점을 항상 유지합니다.
- 백업을 중지하지 않고 데이터 원본을 삭제하면 새 백업이 시작되지 못합니다. 다시 말하지만 이전 복구 지점은 정책에 따라 만료되지만 사용자가 백업을 중지하고 데이터를 삭제할 때까지 마지막 1개의 복구 지점은 항상 유지됩니다.
- 자동 보호가 설정된 데이터베이스의 백업은 자동 보호를 해제할 때까지 중지할 수 없습니다.

데이터베이스에 대한 보호를 중지하려면:

1. 자격 증명 모음 대시보드의 **사용량** 아래에서 **백업 항목**을 선택합니다.

    ![백업 항목 메뉴 열기](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

2. **백업 관리 유형**에서 **Azure VM의 SQL**을 선택합니다.

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. 보호를 중지하려는 데이터베이스를 선택합니다.

    ![보호를 중지할 데이터베이스 선택](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. 데이터베이스 메뉴에서 **백업 중지**를 선택합니다.

    ![백업 중지 선택](./media/backup-azure-sql-database/stop-db-button.png)


6. **백업 중지** 메뉴에서 데이터를 보존할지 또는 삭제할지를 선택합니다. 필요에 따라 이유 또는 설명을 입력합니다.

    ![백업 중지 메뉴](./media/backup-azure-sql-database/stop-backup-button.png)

7. **백업 중지**를 클릭합니다.

  

### <a name="resume-protection-for-a-sql-database"></a>SQL 데이터베이스에 대한 보호 재개

SQL 데이터베이스에 대한 보호가 중지된 경우 **백업 데이터 보존** 옵션을 선택하면 보호를 다시 시작할 수 있습니다. 백업 데이터가 보존되지 않은 경우에는 보호를 다시 시작할 수 없습니다.

1. SQL 데이터베이스에 대한 보호를 다시 시작하려면 백업 항목을 열고 **백업 다시 시작**을 선택합니다.

    ![백업 다시 시작을 선택하여 데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/resume-backup-button.png)

2. **백업 정책** 메뉴에서 정책을 선택한 다음, **저장**을 클릭합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

다음과 같은 다양한 유형의 주문형 백업을 실행할 수 있습니다.

* 전체 백업
* 복사 전용 전체 백업
* 차등 백업
* 로그 백업

SQL Server 백업 유형에 대해 [자세히 알아봅니다](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>SQL Server 인스턴스 등록 취소

다음과 같이 보호를 사용하지 않도록 설정한 후 자격 증명 모음을 삭제하기 전에 SQL Server 인스턴스 등록을 취소합니다.

1. 자격 증명 모음 대시보드의 **관리** 아래에서 **백업 인프라**를 선택합니다.  

   ![백업 인프라 선택](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. **관리 서버**에서 **보호된 서버**를 선택합니다.

   ![보호된 서버 선택](./media/backup-azure-sql-database/protected-servers.png)


3. **보호된 서버**에서 등록을 취소할 서버를 선택합니다. 자격 증명 모음을 삭제하려면 모든 서버의 등록을 취소해야 합니다.

4. 보호된 서버를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.

   ![삭제 선택](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>다음 단계

SQL Server 데이터베이스 백업에 대한 문제 해결 정보를 [검토](backup-sql-server-azure-troubleshoot.md)합니다.
