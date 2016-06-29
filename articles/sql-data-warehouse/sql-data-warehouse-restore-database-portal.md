<properties
   pageTitle="Azure SQL 데이터 웨어하우스 복원(포털) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스 복원을 위한 Azure 포털 작업."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/11/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스 복원(포털)

> [AZURE.SELECTOR]
- [개요][]
- [포털][]
- [PowerShell][]
- [REST (영문)][]

이 문서에서는 Azure 포털을 사용하여 Azure SQL 데이터 웨어하우스를 복원하는 방법을 배웁니다.

## 시작하기 전에

**DTU 용량을 확인합니다.** 각 SQL 데이터 웨어하우스는 SQL Server 논리 서버에 의해 호스팅됩니다. 이 논리 서버는 DTU로 측정된 용량 제한을 가집니다. SQL 데이터 웨어하우스를 복원하기 전에 데이터베이스를 호스팅하는 SQL Server 논리 서버에 복원 중인 데이터베이스에 대한 충분한 DTU 용량이 있는지 확인하십시오. [DTU 할당량을 보고 늘리는 방법][]에 대한 자세한 내용은 이 블로그 게시물을 참조하세요.


## 활성 또는 일시 중지된 데이터베이스 복원

데이터베이스를 복원하려면

1. [Azure 포털][]에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 데이터베이스**를 선택합니다.
3. 사용자의 데이터베이스로 이동한 후 선택합니다.
4. 데이터베이스 블레이드의 위쪽에서 **복원**을 클릭합니다.
5. 새 **데이터베이스 이름**을 지정하고 **복원 지점**을 선택한 다음 **만들기**를 클릭합니다.
6. 데이터베이스 복원 프로세스가 시작되며 **알림**을 사용하여 모니터링할 수 있습니다.

## 삭제된 데이터베이스 복원

삭제된 데이터베이스를 복원하려면:

1. [Azure 포털][]에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 서버**를 선택합니다.
3. 사용자의 서버로 이동한 후 선택합니다.
4. 서버 블레이드에서 작업까지 아래로 스크롤하여 **삭제된 데이터베이스** 타일을 클릭합니다.
5. 복원할 삭제된 데이터베이스를 선택합니다.
5. 새 **데이터베이스 이름**을 지정하고 **만들기**를 클릭합니다.
6. 데이터베이스 복원 프로세스가 시작되며 **알림**을 사용하여 모니터링할 수 있습니다.


## 다음 단계
Azure SQL 데이터베이스 버전의 무중단 업무 방식 기능에 대해 알아보려면 [Azure SQL 데이터베이스 무중단 업무 방식 개요][]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 무중단 업무 방식 개요]: ./sql-database-business-continuity.md
[개요]: ./sql-data-warehouse-restore-database-overview.md
[포털]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST (영문)]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->

<!--Blog references-->
[DTU 할당량을 보고 늘리는 방법]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure 포털]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->