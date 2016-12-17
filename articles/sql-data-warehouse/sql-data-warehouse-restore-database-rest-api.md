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
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eed4442c5bd7a955e8d380bcb84cd62cd07eca05


---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Azure SQL 데이터 웨어하우스 복원(REST API)
> [!div class="op_single_selector"]
> * [개요][개요]
> * [포털][포털]
> * [PowerShell][PowerShell]
> * [REST (영문)][REST (영문)]
> 
> 

이 문서에서는 REST API를 사용하여 Azure SQL 데이터 웨어하우스를 복원하는 방법을 배웁니다.

## <a name="before-you-begin"></a>시작하기 전에
**DTU 용량을 확인합니다.**  각 SQL 데이터 웨어하우스는 기본 DTU 할당량이 있는 SQL server (예: myserver.database.windows.net)에 의해 호스팅됩니다.  SQL 데이터 웨어하우스를 복원하기 전에 SQL 서버에 복원 중인 데이터베이스에 대해 충분한 DTU 할당량이 남아 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청][DTU 할당량 변경 요청]을 참조하세요.

## <a name="restore-an-active-or-paused-database"></a>활성 또는 일시 중지된 데이터베이스 복원
데이터베이스를 복원하려면

1. 데이터베이스 복원 지점 가져오기 작업을 사용하여 데이터베이스 복원 지점 목록을 가져옵니다.
2. [데이터베이스 복원 요청 만들기][데이터베이스 복원 요청 만들기] 작업을 사용하여 복원을 시작합니다.
3. [데이터베이스 작업 상태][데이터베이스 작업 상태] 작업을 사용하여 복원 상태를 추적합니다.

> [!NOTE]
> 복원이 완료된 후 [복구 후 데이터베이스 구성][복구 후 데이터베이스 구성]에 따라 복구된 데이터베이스를 구성할 수 있습니다.
> 
> 

## <a name="restore-a-deleted-database"></a>삭제된 데이터베이스 복원
삭제된 데이터베이스를 복원하려면:

1. [복원 가능한 삭제된 데이터베이스 나열][복원 가능한 삭제된 데이터베이스 나열] 작업을 사용하여 복원 가능한 모든 삭제된 데이터베이스를 나열합니다.
2. [복원 가능한 삭제된 데이터베이스 가져오기][복원 가능한 삭제된 데이터베이스 가져오기] 작업을 사용하여 복원하려는 삭제된 데이터베이스에 대한 세부 정보를 가져옵니다.
3. [데이터베이스 복원 요청 만들기][데이터베이스 복원 요청 만들기] 작업을 사용하여 복원을 시작합니다.
4. [데이터베이스 작업 상태][데이터베이스 작업 상태] 작업을 사용하여 복원 상태를 추적합니다.

> [!NOTE]
> 복원이 완료된 후에 데이터베이스를 구성하려면 [복구 후 데이터베이스 구성][복구 후 데이터베이스 구성]을 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
Azure SQL Database 버전의 무중단 업무 방식 기능에 대해 알아보려면 [Azure SQL 데이터베이스 무중단 업무 방식 개요][Azure SQL 데이터베이스 무중단 업무 방식 개요]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 무중단 업무 방식 개요]: ../sql-database/sql-database-business-continuity.md
[DTU 할당량 변경 요청]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[복구 후 데이터베이스 구성]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Azure PowerShell 설치 및 구성 방법]: ./powershell-install-configure.md
[개요]: ./sql-data-warehouse-restore-database-overview.md
[포털]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST (영문)]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[데이터베이스 복원 요청 만들기]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[데이터베이스 작업 상태]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[복원 가능한 삭제된 데이터베이스 가져오기]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[복원 가능한 삭제된 데이터베이스 나열]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure 앱 서비스에서 웹앱]: https://portal.azure.com/
[Microsoft 웹 플랫폼 설치 관리자]: https://aka.ms/webpi-azps



<!--HONumber=Nov16_HO3-->


