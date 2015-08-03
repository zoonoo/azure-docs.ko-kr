<properties
	pageTitle="Excel을 사용하여 Azure SQL 데이터베이스에 연결"
	description="보고 및 데이터 탐색을 위한 Azure SQL 데이터베이스에 대한 Excel 스프레드시트입니다."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="joseidz"
	editor="joseidz"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="joseidz"/>


# Excel을 사용하여 Azure SQL 데이터베이스에 연결
Excel을 Azure SQL 데이터베이스에 연결하고 데이터베이스의 데이터에 대해 보고서를 만듭니다.

## 필수 조건
- Azure SQL 데이터베이스가 프로비전되고 실행 중이어야 합니다. 새 SQL 데이터베이스를 만들려면 [Microsoft Azure SQL 데이터베이스 시작](sql-database-get-started.md)을 참조하세요.
- [Microsoft Excel 2013](https://products.office.com/en-US/)(또는 Microsoft Excel 2010)

## SQL 데이터베이스에 연결 및 보고서 만들기
1.	Excel을 엽니다.
2.	페이지 상단에 있는 메뉴 모음에서 **데이터**를 클릭합니다.
3.	**기타 원본**을 클릭하고 **SQL Server**를 클릭합니다. **데이터 연결 마법사**가 열립니다.

	![데이터 연결 마법사][1]
4.	**서버 이름** 상자에 Azure SQL 데이터베이스 서버 이름을 입력합니다. 예:

	 	adventureserver.database.windows.net
5.	**로그온 자격 증명** 섹션에서 **다음 사용자 이름과 암호 사용**을 선택한 후 SQL 데이터베이스 서버에 대해 적절한 자격 증명을 입력합니다. 그런 후 **Next**를 클릭합니다.

	참고: Excel에 대한 [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102)및[Power Query](https://www.microsoft.com/download/details.aspx?id=39379) 추가 기능의 환경은 매우 유사합니다.

6. **데이터베이스 및 테이블 선택** 대화 상자의 풀 다운 메뉴에서 **AdventureWorks** 데이터베이스를 선택하고 테이블 및 뷰 목록에서 **vGetAllCategories**를 선택한 후 **다음**을 클릭합니다.

	![데이터베이스 및 테이블 선택][5]
7. **데이터 연결 파일 저장 및 마침** 대화 상자에서 **마침**을 클릭합니다.
8. **데이터 가져오기** 대화 상자에서 **피벗 차트**를 선택하고 **확인**을 클릭합니다.

	![데이터 가져오기 선택][2]
9. **피벗 차트 필드** 대화 상자에서 다음 구성을 선택하여 범주별 제품 수에 대한 보고서를 만듭니다.

	![구성][3]
10.	성공 시 다음과 같이 표시됩니다.

	![성공][4]

## 다음 단계

SaaS(Software as a Service) 개발자인 경우 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)에 대해 알아보세요. [탄력적 데이터베이스 작업](sql-database-elastic-jobs-overview.md)을 사용하여 대규모 데이터베이스 컬렉션을 쉽게 관리할 수 있습니다.

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=July15_HO4-->