---
title: "Azure SQL Data Warehouse 복원(REST API) | Microsoft Docs"
description: "Azure SQL 데이터 웨어하우스 복원을 위한 REST API 작업."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a957a70be915459baa8c687c92e251c6011b6172
ms.openlocfilehash: 7bd71144cd2c96fcfb6636ca8d24fc354f86584d
ms.lasthandoff: 12/06/2016


---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Azure SQL 데이터 웨어하우스 복원(REST API)
> [!div class="op_single_selector"]
> * [개요][Overview]
> * [포털][Portal]
> * [PowerShell][PowerShell]
> * [REST (영문)][REST]
> 
> 

이 문서에서는 REST API를 사용하여 Azure SQL 데이터 웨어하우스를 복원하는 방법을 배웁니다.

## <a name="before-you-begin"></a>시작하기 전에
**DTU 용량을 확인합니다.** 각 SQL 데이터 웨어하우스는 기본 DTU 할당량이 있는 SQL server (예: myserver.database.windows.net)에 의해 호스팅됩니다.  SQL 데이터 웨어하우스를 복원하기 전에 SQL 서버에 복원 중인 데이터베이스에 대해 충분한 DTU 할당량이 남아 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청][Request a DTU quota change]을 참조합니다.

## <a name="restore-an-active-or-paused-database"></a>활성 또는 일시 중지된 데이터베이스 복원
데이터베이스를 복원하려면

1. 데이터베이스 복원 지점 가져오기 작업을 사용하여 데이터베이스 복원 지점 목록을 가져옵니다.
2. [데이터베이스 복원 요청 만들기][Create database restore request] 작업을 사용하여 복원을 시작합니다.
3. [데이터베이스 작업 상태][Database operation status] 작업을 사용하여 복원 상태를 추적합니다.

> [!NOTE]
> 복원이 완료된 후 [복구 후 데이터베이스 구성][Configure your database after recovery]에 따라 복구된 데이터베이스를 구성할 수 있습니다.
> 
> 

## <a name="restore-a-deleted-database"></a>삭제된 데이터베이스 복원
삭제된 데이터베이스를 복원하려면:

1. [복원 가능한 삭제된 데이터베이스 나열][List restorable dropped databases] 작업을 사용하여 복원 가능한 모든 삭제된 데이터베이스를 나열합니다.
2. [복원 가능한 삭제된 데이터베이스 가져오기][Get restorable dropped database] 작업을 사용하여 복원하려는 삭제된 데이터베이스에 대한 세부 정보를 가져옵니다.
3. [데이터베이스 복원 요청 만들기][Create database restore request] 작업을 사용하여 복원을 시작합니다.
4. [데이터베이스 작업 상태][Database operation status] 작업을 사용하여 복원 상태를 추적합니다.

> [!NOTE]
> 복원이 완료된 후에 데이터베이스를 구성하려면 [복구 후 데이터베이스 구성][Configure your database after recovery]을 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
Azure SQL Database 버전의 무중단 업무 방식 기능에 대해 알아보려면 [Azure SQL Database 무중단 업무 방식 개요][Azure SQL Database business continuity overview]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

