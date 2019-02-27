---
title: Azure Backup을 사용하여 Azure VM에서 백업한 SQL Server 데이터베이스 복원 | Microsoft Docs
description: 이 문서에서는 Azure Backup으로 백업되는 Azure VM에서 실행되는 SQL Server 데이터베이스를 복원하는 방법을 설명합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 9b741f8562ae2e81d297357afd3b0e0e3976a248
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445443"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Azure VM에서 SQL Server 데이터베이스 복원 


이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 Azure Backup Recovery Services 자격 증명 모음으로 백업된 SQL Server 데이터베이스(Azure VM에서 실행)를 복원하는 방법을 설명합니다.


> [!NOTE]
> Azure Backup을 사용하여 Azure VM에서 실행되는 SQl Server 데이터베이스의 백업은 현재 공개 미리 보기로 제공됩니다.


이 문서에서는 SQL Server 데이터베이스를 복원하는 방법을 설명합니다. 데이터베이스에 대해 백업을 구성하지 않은 경우 [이 문서](backup-azure-sql-database.md)의 지침을 따릅니다.



## <a name="about-restoring-databases"></a>데이터베이스 복원 정보

Azure Backup에서는 다음과 같이 Azure VM에서 실행되는 SQL Server 데이터베이스를 복원할 수 있습니다.

- 트랜잭션 로그 백업을 사용하여 특정 날짜 또는 시간(초)으로 복원합니다. Azure Backup은 선택한 시간을 기준으로 복원해야 하는 해당 전체 차등 백업 및 로그 백업 체인을 자동으로 결정합니다.
- 특정 시간보다는 특정 복구 지점으로 복원하려면 특정 전체 또는 차등 백업을 복원합니다.


### <a name="prerequisites"></a>필수 조건

데이터베이스를 복원하기 전에 다음에 유의하세요.

- 동일한 Azure 지역의 SQL Server 인스턴스에 데이터베이스를 복원할 수 있습니다.
- 대상 서버를 원본과 동일한 자격 증명 모음에 등록해야 합니다.
- TDE 암호화된 데이터베이스를 다른 SQL Server로 복원하려면, 먼저 [대상 서버로 인증서를 복원](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)해야 합니다.
- “마스터” 데이터베이스의 복원을 트리거하기 전에 시작 옵션 **-m AzureWorkloadBackup**을 사용하여 단일 사용자 모드로 SQL Server 인스턴스를 시작합니다.
    - **-m** 값은 클라이언트의 이름입니다.
    - 지정된 클라이언트 이름으로만 연결을 열 수 있습니다.
- 모든 시스템 데이터베이스(모델, 마스터, msdb)에 대해 복원을 트리거하기 전에 SQL 에이전트 서비스를 중지합니다.
- 이러한 데이터베이스에 대한 연결을 도용하려고 하는 애플리케이션을 닫습니다.

## <a name="restore-a-database"></a>데이터베이스 복원

복원하려면 다음 권한이 필요합니다.

* 복원을 수행할 자격 증명 모음에 대한 **백업 운영자** 권한
* 백업되는 원본 VM에 대한 **참가자(쓰기)** 액세스 권한
* 대상 VM에 대한 **참가자(쓰기)** 액세스 권한
    - 동일한 VM으로 복원하는 경우 원본 VM입니다.
    - 대체 위치로 복원하는 경우 새 대상 VM입니다. 

다음과 같이 복원합니다.
1. SQL Server VM이 등록된 자격 증명 모음을 엽니다.
2. 자격 증명 모음 대시보드의 **사용 현황** 아래에서 **백업 항목**을 선택합니다.

    ![백업 항목 메뉴 열기](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. **백업 항목**의 **백업 관리 유형**에서 **Azure VM의 SQL**을 선택합니다.

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 복원할 데이터베이스를 선택합니다.

    ![복원할 데이터베이스를 선택합니다.](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 데이터베이스 메뉴를 검토합니다. 다음과 같은 데이터베이스 백업에 대한 정보를 제공합니다. 

    * 가장 오래된 복원 지점 및 최신 복원 지점
    * 트랜잭션 로그 백업으로 구성된 경우 전체 및 대량 로그된 복구 모델의 데이터베이스에 대한 지난 24시간 동안의 로그 백업 상태

6. **복원 DB**를 클릭합니다. 

    ![복원 DB 선택](./media/backup-azure-sql-database/restore-db-button.png)

7. **복원 구성**에서 데이터를 복원할 위치를 지정합니다.
    - **대체 위치**: 대체 위치에 데이터베이스를 복원하고 원래 원본 데이터베이스를 유지합니다.
    - **DB 덮어쓰기**: 원래 원본과 동일한 SQL Server 인스턴스에 데이터를 복원합니다. 이 옵션의 효과는 원래 데이터베이스를 덮어쓰는 것입니다.

    > [!Important]
    > 선택한 데이터베이스가 Always On 가용성 그룹에 속하면 SQL Server에서 데이터베이스를 덮어쓸 수 없습니다. **대체 위치**만 사용할 수 있습니다.
    >

    ![복원 구성 메뉴](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

1. **복원 구성** 메뉴의 **복원할 위치** 아래에서 **대체 위치**를 선택합니다.
2. 데이터베이스를 복원하려는 SQL Server 이름 및 인스턴스를 선택합니다.
3. **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.
4. 해당되는 경우 **선택한 SQL 인스턴스에 이름이 같은 DB가 있으면 덮어쓰기**를 선택합니다.
5. **확인**을 클릭합니다.

    ![복원 구성 메뉴에 대한 값 제공](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. **복원 지점 선택**에서 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)할지 또는 [특정 복구 지점](#restore-to-a-specific-restore-point)으로 복원할지를 선택합니다.

    > [!NOTE]
    > 특정 시점 복원은 전체 및 대량 로그된 복구 모델을 사용하는 데이터베이스의 로그 백업에만 사용할 수 있습니다. 


### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

1. **복원 구성** 메뉴의 **복원할 위치** 아래에서 **DB 덮어쓰기** > **확인**을 선택합니다.

    ![DB 덮어쓰기 선택](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. **복원 지점 선택**에서 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)하려면 **로그(시점)을 선택하고, **특정 복구 지점**으로 복원하려면 [전체 및 차등](#restore-to-a-specific-restore-point)을 선택합니다.

    > [!NOTE]
    > 특정 시점 복원은 전체 및 대량 로그된 복구 모델을 사용하는 데이터베이스의 로그 백업에만 사용할 수 있습니다. 




    
### <a name="restore-to-a-specific-point-in-time"></a>특정 시점으로 복원

복원 유형으로 **로그(시점)** 을 선택한 경우 다음을 수행합니다.

1.  **복원 날짜/시간** 아래에서 미니 달력을 선택합니다. **달력**에서 굵게 표시된 날짜에 복구 지점이 포함되고 현재 날짜는 강조 표시됩니다.
2. 복구 지점이 있는 날짜를 선택합니다. 복구 지점이 없는 날짜는 선택할 수 없습니다.

    ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. 날짜를 선택한 후에는 타임라인 그래프에 사용 가능한 복구 지점이 연속적인 범위로 표시됩니다.
4. 타임라인 그래프를 사용하여 복구 시간을 지정하거나 시간을 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. **고급 구성** 메뉴에서 복원 후 데이터베이스를 비작동 상태로 유지하려면 **NORECOVERY로 복원**을 사용하도록 설정합니다.
5. 대상 서버에서 복원 위치를 변경하려면 새 대상 경로를 입력합니다.
6. **확인**을 클릭합니다.

    ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.
8. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **복원 작업**을 선택합니다.

    ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>특정 복원 지점으로 복원

복원 유형으로 **전체 및 차등**을 선택한 경우 다음을 수행합니다.


1. 목록에서 복구 지점을 선택하고 **확인**을 클릭하여 복원 지점 절차를 완료합니다.

    ![전체 복구 지점 선택](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. **고급 구성** 메뉴에서 복원 후 데이터베이스를 비작동 상태로 유지하려면 **NORECOVERY로 복원**을 사용하도록 설정합니다.
3. 대상 서버에서 복원 위치를 변경하려면 새 대상 경로를 입력합니다. 
4. **확인**을 클릭합니다.

    ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.
8. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **복원 작업**을 선택합니다.

    ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>다음 단계

Azure Backup에서 백업된 SQL Server 데이터베이스를 [관리 및 모니터링](manage-monitor-sql-database-backup.md)합니다.
