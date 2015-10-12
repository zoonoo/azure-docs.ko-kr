<properties
   pageTitle="샘플 데이터를 SQL 데이터 웨어하우스에 로드 | Microsoft Azure"
   description="샘플 데이터를 SQL 데이터 웨어하우스에 로드"
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
   ms.date="09/23/2015"
   ms.author="lodipalm;barbkess"/>

#SQL 데이터 웨어하우스로 샘플 데이터를 로드

SQL 데이터 웨어하우스 인스턴스를 만드는 동안 일부 샘플 데이터를 쉽게 로드할 수 있습니다. 프로비전하는 동안 이 단계를 놓친 경우 [샘플 데이터를 수동으로 로드][]할 수도 있습니다.

다음은 AdventureWorksDW를 데이터베이스에 로드할 수 방법의 간략한 보기를 제공합니다. 이 데이터 집합은 회사에 대한 판매 및 고객을 나타내는 데이터가 있는 AdventureWorks라는 가상 회사의 샘플 데이터 웨어하우스 구조를 모델링합니다.

## 생성 중 샘플 데이터 추가
다음 단계를 수행하여 배포하는 동안 샘플 데이터가 SQL 데이터 웨어하우스에 로드되는지 확인할 수 있습니다.

1. '+ 새로 만들기', ‘데이터 및 저장소'를 차례로 클릭하거나 Marketplace에서 'SQL 데이터 웨어하우스'를 검색하여 [Azure 포털][]에서 SQL 데이터 웨어하우스를 찾아 생성 프로세스를 시작합니다. 
 
2. 프로세스가 시작되면 '원본 선택' 옵션을 클릭하고 '샘플'로 설정합니다. 새 서버를 만들지 않은 경우 만드는데 사용하고 있는 서버에 대한 로그인을 제공하라는 메시지가 표시됩니다.


> [AZURE.NOTE]샘플 데이터를 인스턴스에 로드하기 위해 Azure 서비스를 활성화하여 서버에 액세스해야 합니다(새 서버를 만들 때 기본으로 해야 함). 이렇게 하지 않으면 로드가 실패하지만 여전히 [샘플 데이터를 수동으로 로드][]할 수 있습니다.


##Power BI를 사용하여 Adventureworks 분석

샘플 데이터 집합을 사용하는 것은 Power BI를 시작하기 위한 좋은 방법이 될 수 있습니다. 샘플 데이터를 로드한 후 Azure 포털에서 ‘Power BI에서 열기’ 단추를 클릭하거나 [Power BI][]로 이동하고 [SQL 데이터 웨어하우스에 연결][]하여 SQL 데이터 웨어하우스에 대한 연결을 열 수 있습니다. 연결 후 데이터 웨어하우스와 동일한 이름을 가진 새 데이터 집합을 만들어야 합니다. 분석을 쉽게 수행할 수 있도록 회사의 판매 분석에 핵심적인 매트릭 중 몇 가지가 있는 'AggregateSales'라는 보기를 만들었습니다. 이 보기의 이름을 클릭하여 확장하고 포함하는 열을 볼 수 있으며 다음 단계를 수행하여 일부 빠른 시각화를 만들 수 있습니다.

1. 시작하려면 'PostalCode' 및 'SalesAmount' 열을 클릭하여 모든 판매의 지도를 쉽게 만들 수 있습니다. Power BI는 이를 지리적 데이터로 자동으로 인식하고 지도에 배치합니다. 

2. 이제 판매 막대 그래프를 만들고자 하는 경우 'SalesAmount' 열을 클릭하면 Power BI는 자동으로 판매 막대 그래프를 만듭니다. 'CustomerIncome' 차트를 'AggregateSales'의 왼쪽 '축' 영역으로 끌어 고객 수입 대괄호로 판매를 표시하여 추가 깊이를 추가할 수 있습니다.

3. 마지막으로 판매 타임라인을 만들려는 경우 'SalesAmount', 'OrderDate' 및 '꺾은선형 차트'('시각화' 아래 둘째 줄에서 첫 번째 아이콘)를 클릭하면 됩니다.

왼쪽 위 모서리에 있는 ‘저장’ 단추를 클릭하고 시각화를 보고서로 저장하여 언제든지 진행률을 저장할 수 있습니다.

## 샘플을 연결하고 쿼리하기

전통적인 방법으로 샘플 데이터를 분석할 수도 있습니다. [연결 및 쿼리][] 설명서에서 설명한대로 Visual Studio의 SQL Server 데이터 도구를 사용하여 데이터베이스에 연결할 수 있습니다. 이제 SQL 데이터 웨어하우스 내의 일부 샘플 데이터를 로드했고, 시작하기 위해 몇 가지 쿼리를 신속하게 실행할 수 있습니다.

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
[샘플 데이터를 수동으로 로드]: https://azure.microsoft.com/ko-kr/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Azure 포털]: https://portal.azure.com
[Power BI]: http://www.powerbi.com
[SQL 데이터 웨어하우스에 연결]: https://azure.microsoft.com/ko-kr/documentation/articles/sql-data-warehouse-integrate-power-bi/

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/ko-kr/download/details.aspx?id=36433

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=Oct15_HO1-->