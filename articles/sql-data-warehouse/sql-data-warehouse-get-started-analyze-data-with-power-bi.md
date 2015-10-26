<properties
    pageTitle="Power BI로 SQL 데이터 웨어하우스 데이터 분석 | Microsoft Azure"
    description="Power BI로 SQL 데이터 웨어하우스 데이터 분석"
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
    ms.date="10/07/2015"
    ms.author="lodipalm"/>

# Power BI를 사용하여 데이터 분석
이 자습서에서는 Power BI를 사용하여 SQL 데이터 웨어하우스에 연결하고 몇 가지 기본적인 시각화를 만드는 방법을 보여줍니다.

> [AZURE.NOTE]이 자습서를 완료하려면 AdventureWorksDW 예제 데이터베이스가 미리 로그된 SQL 데이터 웨어하우스 데이터베이스가 필요합니다. [SQL 데이터 웨어하우스 만들기](sql-data-warehouse-get-started-provision.md)는 만드는 방법을 보여줍니다.
> 
> SQL 데이터 웨어하우스 데이터베이스는 있지만 샘플 데이터가 없는 경우 [샘플 데이터를 수동으로 로드][]할 수 있습니다.


## SQL 데이터 웨어하우스 데이터베이스에 연결

Power BI를 열고 AdventureWorksDW 데이터베이스에 연결하려면

1. [Azure Preview 포털][]에 로그인합니다.
2. **SQL 데이터베이스**를 클릭하고 AdventureWorks SQL 데이터 웨어하우스 데이터베이스를 선택합니다. 

    ![데이터베이스 찾기][1]

3. 'Power BI에서 열기' 단추를 클릭합니다.

    ![Power BI 단추][2]

4. 이제 데이터베이스 정보를 표시하는 SQL 데이터 웨어하우스 연결 페이지가 표시됩니다. 암호를 입력하면 SQL 데이터 웨어하우스 데이터베이스에 완벽하게 연결됩니다.


## Power BI를 사용하여 데이터 분석

이제 Power BI를 사용하여 AdventureWorksDW 샘플 데이터를 분석할 준비가 되었습니다. 분석을 수행하기 위해 AdventureWorksDW에는 AggregateSales라는 뷰가 포함되어 있습니다. 이 뷰는 회사의 판매를 분석하기 위한 주요 메트릭 중 일부를 포함합니다.

1. 우편 번호에 따라 판매액 지도를 만들려면 AggregateSales 뷰를 클릭하여 확장한 후 PostalCode 및 SalesAmount 열을 클릭합니다. Power BI는 이를 지리적 데이터로 자동으로 인식하고 지도에 배치합니다.

    ![Power BI 맵][3]

2. 판매량에 대한 막대 그래프를 만들려면 SalesAmount 열을 클릭하기만 하면 됩니다. 세부 정보를 추가하려면 CustomerIncome 차트를 'AggregateSale'의 왼쪽 Axis 필드로 끌어 고객 수입별 판매량을 표시합니다.

    ![Power BI 막대형][4]

3. 판매 타임라인을 만들려는 경우 SalesAmount, OrderDate 및 '꺾은선형 차트'(시각화 아래 둘째 줄에서 첫 번째 아이콘)를 클릭하면 됩니다.

    ![Power BI 꺾은선형][5]

왼쪽 위 모서리에 있는 **저장** 단추를 클릭하고 시각화를 보고서로 저장하여 언제든지 진행률을 저장할 수 있습니다.

## 다음 단계
이제 샘플 데이터로 [개발][], [로드][] 또는 [마이그레이션][]할 준비 시간을 드리겠습니다.

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png

<!--Article references-->
[마이그레이션]: sql-data-warehouse-overview-migrate.md
[개발]: sql-data-warehouse-overview-develop.md
[로드]: sql-data-warehouse-overview-load.md
[샘플 데이터를 수동으로 로드]: sql-data-warehouse-get-started-manually-load-samples.md
[Azure Preview 포털]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO3-->