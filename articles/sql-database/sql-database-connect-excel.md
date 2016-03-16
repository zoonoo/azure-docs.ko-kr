<properties
	pageTitle="SQL 데이터베이스에 Excel 연결 | Microsoft Azure"
	description="Microsoft Excel을 클라우드의 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다. 보고 및 데이터 탐색을 위해 Excel로 데이터를 가져옵니다."
	services="sql-database"
	keywords="SQL에 Excel 연결, Excel로 데이터 가져오기"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/04/2016"
	ms.author="joseidz"/>


# Azure SQL 데이터베이스에 Excel 연결 및 보고서 만들기 

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

데이터를 Excel로 가져오기 위해 Excel을 SQL 데이터베이스에 연결하는 방법을 알아봅니다. 그런 다음, 데이터에 대한 보고서를 만듭니다.

먼저 SQL 데이터베이스가 필요합니다. SQL 데이터베이스가 없다면 [첫 번째 SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하여 몇 분 내에 샘플 데이터와 함께 실행되는 데이터베이스를 가져옵니다. 이 문서에서는 해당 문서에서 샘플 데이터를 Excel에 가져오지만 고유의 데이터에서 비슷한 단계를 따를 수 있습니다.

또한 Excel의 사본이 필요합니다. 이 문서는 [Microsoft Excel 2016](https://products.office.com/ko-KR/)를 사용합니다.

## SQL 데이터베이스에 Excel 연결 및 보고서 만들기

1.	Excel을 SQL 데이터베이스에 연결하려면 Excel을 연 다음 새 통합 문서를 만듭니다. 또는 SQL 데이터베이스에 연결하려는 기존 Excel 통합 문서를 엽니다.

2.	페이지 위쪽에 있는 메뉴 모음에서 **데이터**, **기타 원본에서** 및 **SQL Server에서**를 차례로 클릭합니다.

	![데이터 원본 선택: SQL 데이터베이스에 Excel을 연결합니다.](./media/sql-database-connect-excel/excel_data_source.png)

	데이터 연결 마법사가 열립니다.

3.	**데이터베이스 서버에 연결** 대화 상자에서 **<*servername*>.database.windows.net** 형식에서 연결하려는 논리 서버를 호스팅하는 **서버 이름**을 입력합니다. 예를 들어 **adventureserver.database.windows.net**입니다.

4.	**로그온 자격 증명** 섹션에서 **다음 사용자 이름 및 암호 사용**을 클릭하고 SQL 데이터베이스 서버를 만들 때 설정한 **사용자 이름** 및 **암호**를 입력한 후 **다음**을 클릭합니다.

	> [AZURE.TIP] Excel에 대한 [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102)및[파워 쿼리](https://www.microsoft.com/download/details.aspx?id=39379) 추가 기능의 환경은 유사합니다.

5. **데이터베이스 및 테이블 선택** 대화 상자의 풀 다운 메뉴에서 **AdventureWorks** 데이터베이스를 선택하고 테이블 및 뷰 목록에서 **vGetAllCategories**를 선택한 후 **다음**을 클릭합니다.

	![데이터베이스 및 테이블 선택][5]

6. **데이터 연결 파일 저장 및 마침** 대화 상자에서 **마침**을 클릭합니다.

7. **데이터 가져오기** 대화 상자에서 **PivotChart**를 선택하고 **확인**을 클릭합니다.

	![Excel로 데이터 가져오기: 데이터 대화 상자에서 PivotChart를 선택합니다.][2]

8. **PivotChart 필드** 대화 상자에서 다음 구성을 선택하여 범주별 제품 수에 대한 보고서를 만듭니다.

	![데이터베이스 보고서를 구성합니다.][3]

	성공 시 다음과 같이 표시됩니다.

	![성공: SQL 데이터베이스에 Excel을 연결합니다.][4]

## 다음 단계

SaaS(Software as a Service) 개발자인 경우 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)에 대해 알아보세요. [탄력적 데이터베이스 작업](sql-database-elastic-jobs-overview.md)을 사용하여 대규모 데이터베이스 컬렉션을 쉽게 관리할 수 있습니다.

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=AcomDC_0204_2016-->