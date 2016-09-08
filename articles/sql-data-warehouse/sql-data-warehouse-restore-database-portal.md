<properties
   pageTitle="Azure SQL 데이터 웨어하우스 복원(포털) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스 복원을 위한 Azure 포털 작업."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스 복원(포털)

> [AZURE.SELECTOR]
- [개요][]
- [포털][]
- [PowerShell][]
- [REST (영문)][]

이 문서에서는 Azure 포털을 사용하여 Azure SQL 데이터 웨어하우스를 복원하는 방법을 배웁니다.

## 시작하기 전에

**DTU 용량을 확인합니다.** 각 SQL 데이터 웨어하우스는 기본 DTU 할당량이 있는 SQL server (예: myserver.database.windows.net)에 의해 호스팅됩니다. SQL 데이터 웨어하우스를 복원하기 전에 SQL 서버에 복원 중인 데이터베이스에 대해 충분한 DTU 할당량이 남아 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청][]을 참조합니다.


## 활성 또는 일시 중지된 데이터베이스 복원

데이터베이스를 복원하려면

1. [Azure 포털][]에 로그인
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 서버** 선택
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. 서버로 이동한 후 선택
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. 복원하려는 SQL 데이터 웨어하우스를 찾아서 선택
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. 데이터 웨어하우스 블레이드의 위쪽에서 **복원** 클릭
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. 새 **데이터베이스 이름** 지정
7. 최신 **복원 지점** 선택
    1. 최신 복원 지점을 선택했는지 확인합니다. 복원 지점은 UTC로 표시되므로 표시된 기본 옵션이 최신 복원 지점이 아닐 때도 있습니다.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. **확인**을 클릭합니다.
9. 데이터베이스 복원 프로세스가 시작되며 **알림**을 사용하여 모니터링할 수 있습니다.

>[AZURE.NOTE] 복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.


## 삭제된 데이터베이스 복원

삭제된 데이터베이스를 복원하려면:

1. [Azure 포털][]에 로그인
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 서버** 선택
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. 서버로 이동한 후 선택
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. 서버 블레이드의 작업 섹션까지 아래로 스크롤
5. **삭제된 데이터베이스** 타일 클릭
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. 복원할 삭제된 데이터베이스 선택
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. 새 **데이터베이스 이름** 지정
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. **확인**을 클릭합니다.
9. 데이터베이스 복원 프로세스가 시작되며 **알림**을 사용하여 모니터링할 수 있습니다.

>[AZURE.NOTE] 복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.


## 다음 단계
Azure SQL 데이터베이스 버전의 무중단 업무 방식 기능에 대해 알아보려면 [Azure SQL 데이터베이스 무중단 업무 방식 개요][]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 무중단 업무 방식 개요]: ./sql-database-business-continuity.md
[개요]: ./sql-data-warehouse-restore-database-overview.md
[포털]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST (영문)]: ./sql-data-warehouse-restore-database-rest-api.md
[복구된 데이터베이스 마무리]: ./sql-database-recovered-finalize.md
[DTU 할당량 변경 요청]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure 포털]: https://portal.azure.com/

<!---HONumber=AcomDC_0824_2016-->