<properties
   pageTitle="샘플 데이터를 SQL 데이터 웨어하우스에 로드 | Microsoft Azure"
   description="앰플 데이터를 SQL 데이터 웨어하우스에 로드"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/05/2015"
   ms.author="lodipalm;barbkess"/>

#SQL 데이터 웨어하우스로 샘플 데이터를 로드

이제 SQL 데이터 웨어하우스 인스턴스를 설치하였음으로, 일부 데이터를 쉽게 로드할 수 있습니다. 다음은 데이터베이스에 AdventureWorksPDW2012라는 데이터 집합을 만드는데 도움이 됩니다. 이 데이터 집합은 AdventureWorks라는 가상 회사의 샘플 데이터 웨어하우스 구조를 모델링합니다. 아래의 단계에 따라 BCP를 설치해야합니다. 현재 BCP가 설치되어있지 않은경우, [SQL Server용 Microsoft 명령줄 유틸리티][]를 설치하십시오.

1. [샘플 데이터 스크립트][]를 다운로드하도록 클릭하여 시작합니다.

2. 파일을 다운로드한 후, AdventureWorksPDW2012.zip 파일의 내용을 추출하고 새 AdventureWorksPDW2012 폴더를 엽니다.

3. Aw\_create.bat 파일을 편집하고 파일의 위에 다음 값을 설정합니다. **서버**: b에 있는 SQL 데이터 웨어하우스의 완벽히 조건에 충족된 서버 이름입니다. **사용자**: 위 서버 c의 사용자입니다. **암호**: d에 로그인하는 제공된 서버의 암호입니다. **데이터베이스**: 데이터를 로드하려는 SQL 데이터 웨어하우스 인스턴스의 이름입니다.

4. 데이터베이스가 위치한 디렉토리에서 aw\_create.bat를 실행합니다. 이것은 스키마를 만들고 BCP를 사용하여 모든 테이블안에 데이터를 로드합니다.


## 샘플을 연결하고 쿼리하기

[연결 및 쿼리][] 설명서에서 설명한대로 Visual Studio와 SSDT를 사용하여 데이터베이스에 연결할 수 있습니다. 이제 SQL 데이터 웨어하우스 내의 일부 샘플 데이터를 로드했고, 시작하기위해 몇 가지 쿼리를 신속하게 실행할 수 있습니다.

직원의 모든 정보를 가져오는 간단한 select 문을 실행할 수 있습니다.

	SELECT * FROM DimEmployee;

각 날짜의 모든 판매 금액을 살펴보기 위해 GROUP BY 같은 구문을 사용하여 더 복잡한 쿼리를 실행할 수도 있습니다.

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

특정 날짜 이전의 주문을 필터링 할 WHERE 구문을 사용할 수도 있습니다.

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

사실, SQL 데이터 웨어하우스는 SQL Server에서 사용되는 대부분의 T-SQL 구문을 지원하며, [마이그레이션 코드][] 설명서에 일부 차이점을 찾을 수 있습니다.

## 다음 단계
이제 샘플 데이터로 [개발][],[로드][] 또는 [마이그레이션][]할 준비 시간을 드리겠습니다.

<!--Image references-->

<!--Article references-->
[마이그레이션]: https://azure.microsoft.com/ko-kr/documentation/articles/sql-data-warehouse-overview-migrate/
[개발]: https://azure.microsoft.com/ko-kr/documentation/articles/sql-data-warehouse-overview-develop/
[로드]: https://azure.microsoft.com/ko-kr/documentation/articles/sql-data-warehouse-overview-load/
[연결 및 쿼리]: https://azure.microsoft.com/ko-kr/documentation/articles/sql-data-warehouse-get-started-connect-query/
[마이그레이션 코드]: https://azure.microsoft.com/ko-kr/documentation/articles/sql-data-warehouse-migrate-code/

<!--MSDN references-->
[SQL Server용 Microsoft 명령줄 유틸리티]: http://www.microsoft.com/ko-kr/download/details.aspx?id=36433

<!--Other Web references-->
[샘플 데이터 스크립트]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=August15_HO6-->