---
title: "삭제된 Azure SQL 데이터베이스 복원(Azure Portal) | Microsoft Docs"
description: "삭제된 Azure SQL 데이터베이스를 복원합니다(Azure Portal)."
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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5a3f40ff8811c08d130c2a3e0a7d61aed9457d1


---
# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Azure 포털을 사용하여 삭제된 Azure SQL 데이터베이스 복원
> [!div class="op_single_selector"]
> * [개요](sql-database-recovery-using-backups.md)
> * [**삭제된 DB 복원: 포털**](sql-database-restore-deleted-database-portal.md)
> * [삭제된 DB 복원: PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## <a name="select-the-database-to-restore"></a>복원할 데이터베이스를 선택합니다.
Azure Portal에서 삭제된 데이터베이스를 복원하려면:

1. [Azure Portal](https://portal.azure.com)에서 **추가 서비스** > **SQL 서버**를 클릭합니다.
2. 복원하려는 데이터베이스를 포함하는 서버를 선택합니다.
3. 서버 블레이드의 **작업** 섹션으로 스크롤하고 **삭제된 데이터베이스**를 선택합니다. ![Azure SQL 데이터베이스 복원](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. 복원할 데이터베이스를 선택합니다.
5. 데이터베이스 이름을 지정하고 **확인**을 클릭합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="next-steps"></a>다음 단계
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
* Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)
* 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)  
* 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


