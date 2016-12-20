---
title: "자동 백업에서 Azure SQL Database 복원(Azure Portal) | Microsoft Docs"
description: "자동 백업에서 Azure SQL Database 복원(Azure Portal)"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 62d94085-d7e7-4f08-bb83-404cf866a6c1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85f8f2473e372a72c66153cd4131ba46e1c8c741


---
# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Azure Portal을 사용하여 자동 백업에서 Azure SQL Database 복원
> [!div class="op_single_selector"]
> * [개요](sql-database-recovery-using-backups.md#geo-restore)
> * [지리적 복원: PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

이 문서에서는 Azure Portal에서 [지리적 복원](sql-database-recovery-using-backups.md#geo-restore)을 사용하여 [자동 백업](sql-database-automated-backups.md)에서 새 서버로 데이터베이스를 복원하는 방법을 보여 줍니다.

## <a name="select-a-database-to-restore"></a>복원할 데이터베이스 선택
Azure Portal에서 Azure SQL Database를 복원하려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 화면 왼쪽에서 **+새로 만들기** > **데이터베이스** > **SQL Database**를 선택합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 원본으로 **백업**을 선택한 다음 복원할 백업을 선택합니다. 데이터베이스 이름, 데이터베이스를 복원하려는 서버를 지정한 다음 **만들기**를 클릭합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-geo-restore-portal/geo-restore.png)

페이지 오른쪽 위에 있는 알림 아이콘을 클릭하여 복원 작업의 상태를 모니터링합니다.

## <a name="next-steps"></a>다음 단계
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
* Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)
* 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)  
* 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


