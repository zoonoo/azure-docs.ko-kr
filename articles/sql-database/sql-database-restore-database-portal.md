---
title: "Azure Portal: Azure SQL Database 복원 | Microsoft Docs"
description: "Azure SQL Database를 복원합니다(Azure Portal)."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 49800be1a61e45f64a77f8d2662ca9edeae29de9
ms.lasthandoff: 03/10/2017


---
# <a name="restore-an-azure-sql-database-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure SQL Database 복원

다음 단계에서는 특정 시점으로, 삭제된 데이터베이스에서, 지역 중복 백업에서 SQL Database를 복원하는 방법을 보여 줍니다.

## <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>이전 시점으로 Azure SQL Database를 복원합니다. 

> [!TIP]
> 자습서는 [데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery-portal.md)을 참조하세요.
>

Azure Portal에서 복원할 데이터베이스를 선택합니다.

1. [Azure 포털](https://portal.azure.com)을 엽니다.
2. 화면 왼쪽에서 **추가 서비스** > **SQL Database**를 선택합니다.
3. 복원할 데이터베이스를 클릭합니다.
4. 데이터베이스 페이지 위쪽에서 **복원**을 선택합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/restore.png)
5. **복원** 페이지에서 데이터베이스를 복원할 날짜 및 시간(UTC 시간)을 선택하고 **확인**을 클릭합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. 이전 단계에서 **확인**을 클릭한 후에는 페이지 오른쪽 위에 있는 알림 아이콘을 클릭하고 **SQL Database 복원** 알림을 클릭하여 세부 정보를 확인합니다.
   
    ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. 복원 상태에 대한 정보가 포함된 SQL Database 복원 페이지가 열립니다. 행 항목을 클릭하여 자세한 내용을 확인할 수 있습니다.
   
    ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/inprogress.png)


## <a name="restore-a-deleted-azure-sql-database-from-backups"></a>백업에서 삭제된 Azure SQL Database 복원
Azure Portal에서 삭제된 데이터베이스를 복원하려면:

1. [Azure Portal](https://portal.azure.com)에서 **추가 서비스** > **SQL 서버**를 클릭합니다.
2. 복원하려는 데이터베이스를 포함하는 서버를 선택합니다.
3. 서버 블레이드의 **작업** 섹션으로 스크롤하고 **삭제된 데이터베이스**를 선택합니다. ![Azure SQL 데이터베이스 복원](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. 복원할 데이터베이스를 선택합니다.
5. 데이터베이스 이름을 지정하고 **확인**을 클릭합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>지역 중복 백업에서 Azure SQL Database 복원

Azure Portal에서 데이터베이스를 지역 복원하려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 화면 왼쪽에서 **+새로 만들기** > **데이터베이스** > **SQL Database**를 선택합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 원본으로 **백업**을 선택한 다음 복원할 백업을 선택합니다. 데이터베이스 이름, 데이터베이스를 복원하려는 서버를 지정한 다음 **만들기**를 클릭합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-geo-restore-portal/geo-restore.png)

4. 페이지 오른쪽 위에 있는 알림 아이콘을 클릭하여 복원 작업의 상태를 모니터링합니다.

## <a name="next-steps"></a>다음 단계
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
* Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)
* 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)  
* 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)


