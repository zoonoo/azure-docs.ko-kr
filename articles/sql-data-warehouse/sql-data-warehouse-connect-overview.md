<properties
   pageTitle="Azure SQL 데이터 웨어하우스에 연결 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스에 연결에 대한 연결 개요"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Azure SQL 데이터 웨어하우스에 연결

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-connect-overview.md)
- [인증](sql-data-warehouse-authentication.md)
- [드라이버](sql-data-warehouse-connection-strings.md)

Azure SQL 데이터 웨어하우스에 연결의 개요.

## 포털에서 연결 문자열 검색

SQL 데이터 웨어하우스는 Azure SQL Server와 연결됩니다. 연결하려면 서버의 정규화된 이름이 필요합니다. 예를 들어, **myserver**.database.windows.net입니다.

정규화된 서버 이름을 찾으려면:

1. [Azure 포털][]로 이동합니다.
2. **SQL 데이터베이스**를 클릭하고 연결하려는 데이터베이스를 클릭합니다. 이 예제에서는 AdventureWorksDW 샘플 데이터베이스를 사용합니다.
3. 전체 서버 이름을 찾습니다.

    ![전체 서버 이름][1]

## 연결 설정

SQL 데이터 웨어하우스는 연결 및 개체 생성 중에 몇 가지 설정을 표준화합니다. 이는 재정의할 수 없습니다.

| 데이터베이스 설정 | 값 |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | 켜기 |
| [QUOTED\_IDENTIFIERS][] | 켜기 |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## 연결 및 쿼리 모니터링

연결이 설정되고 세션이 설정되면 SQL 데이터 웨어하우스에 대한 쿼리를 작성하고 제출할 준비가 되었습니다. 세션 및 쿼리를 모니터링하는 방법을 알아보려면 [DMV를 사용하여 워크로드 모니터링][]을 참조하세요.

## 다음 단계

Visual Studio 및 다른 응용 프로그램으로 데이터 웨어하우스 쿼리를 시작하려면 [Visual Studio를 사용하여 쿼리][]를 참조하세요.

<!--Articles-->
[Visual Studio를 사용하여 쿼리]: ./sql-data-warehouse-query-visual-studio.md
[DMV를 사용하여 워크로드 모니터링]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure 포털]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0831_2016-->