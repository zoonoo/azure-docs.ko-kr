---
title: 삭제된 Azure SQL 데이터베이스 복원(Azure 포털) | Microsoft Docs
description: 삭제된 Azure SQL 데이터베이스를 복원합니다(Azure 포털).
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/09/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Azure 포털을 사용하여 삭제된 Azure SQL 데이터베이스 복원
> [!div class="op_single_selector"]
> * [개요](sql-database-recovery-using-backups.md)
> * [삭제된 데이터베이스 복원: PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## 복원할 데이터베이스를 선택합니다.
Azure 포털에서 Azure SQL 데이터베이스를 복원하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com)을 엽니다.
2. 화면 왼쪽에서 **찾아보기** > **SQL 서버**를 선택합니다.
3. 복원하려는 삭제된 데이터베이스를 사용하여 서버로 이동하고 서버를 선택합니다.
4. 서버 블레이드의 **작업** 섹션으로 스크롤하고 **삭제된 데이터베이스**를 선택합니다. ![Azure SQL 데이터베이스 복원](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5. 복원할 삭제된 데이터베이스를 선택합니다.
6. 데이터베이스 이름을 지정하고 확인을 클릭합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## 다음 단계
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
* Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
* 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.
* 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)를 참조하세요.

<!---HONumber=AcomDC_0727_2016-->