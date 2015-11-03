<properties
    pageTitle="Power BI로 SQL 데이터 웨어하우스 데이터 시각화 | Microsoft Azure"
    description="Power BI로 SQL 데이터 웨어하우스 데이터 시각화"
    services="sql-data-warehouse"
    documentationCenter="NA"
    authors="lodipalm"
    manager="barbkess"
    editor=""/>

<tags
    ms.service="sql-data-warehouse"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="10/21/2015"
    ms.author="lodipalm"/>

# Power BI를 사용하여 데이터 시각화


> [AZURE.NOTE]이 자습서를 완료하려면 AdventureWorksDW 예제 데이터베이스가 미리 로그된 SQL 데이터 웨어하우스 데이터베이스가 필요합니다. [SQL 데이터 웨어하우스 만들기](sql-data-warehouse-get-started-provision.md)는 만드는 방법을 보여줍니다.
> 
> SQL 데이터 웨어하우스 데이터베이스는 있지만 샘플 데이터가 없는 경우 [샘플 데이터를 수동으로 로드][]할 수 있습니다.

이 자습서에서는 Power BI를 사용하여 SQL 데이터 웨어하우스에 연결하고 몇 가지 기본적인 시각화를 만드는 방법을 보여줍니다.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## AdventureWorksDW에 연결

Power BI를 열고 AdventureWorksDW 데이터베이스에 연결하려면

1. [Azure Preview 포털][]에 로그인합니다.
2. **SQL 데이터베이스**를 클릭하고 AdventureWorks SQL 데이터 웨어하우스 데이터베이스를 선택합니다. 

    ![데이터베이스 찾기][1]

3. 'Power BI에서 열기' 단추를 클릭합니다.

    ![Power BI 단추][2]

4. 이제 데이터베이스 웹 주소를 표시하는 SQL 데이터 웨어하우스 연결 페이지가 표시됩니다. 다음을 클릭합니다.

    ![Power BI 연결][3]

6. Azure SQL 서버 사용자 이름과 암호를 입력하면 SQL 데이터 웨어하우스 데이터베이스에 완벽하게 연결됩니다.

    ![Power BI 로그인][4]

1. Power BI에 로그인하면 왼쪽 블레이드에서 AdventureWorksDW 데이터 집합을 클릭합니다. 그러면 데이터베이스가 열립니다.

    ![Power BI AdventureWorksDW 열기][5]



## 샘플 데이터 분석을 위한 Power BI 보고서 만들기

이제 Power BI를 사용하여 AdventureWorksDW 샘플 데이터를 분석할 준비가 되었습니다. 분석을 수행하기 위해 AdventureWorksDW에는 AggregateSales라는 뷰가 포함되어 있습니다. 이 뷰는 회사의 판매를 분석하기 위한 주요 메트릭 중 일부를 포함합니다.

1. 우편 번호에 따라 판매액 지도를 만들려면 오른쪽 필드 창에서 AggregateSales 뷰를 클릭하여 확장합니다. PostalCode 및 SalesAmount 열을 클릭하여 선택합니다. 

    ![Power BI AggregateSales 선택][6]

    Power BI는 이를 지리적 데이터로 자동으로 인식하고 지도에 배치합니다.

    ![Power BI 맵][7]

2. 이 단계에서는 고객 수익당 매출액을 보여주는 막대 그래프를 만듭니다. 이 그래프를 만들려면 확장된 AggregateSales 보기로 이동합니다. SalesAmount 필드를 클릭합니다. 고객 수익 필드를 왼쪽으로 끌어 축에 놓습니다.
    
    ![Power BI 축 선택][8]

    가로 막대형 차트를 왼쪽으로 이동했습니다.

    ![Power BI 막대형][9]

3. 이 단계에서는 주문 날짜당 판매 금액을 보여주는 꺽은선형 차트를 만듭니다. 이 그래프를 만들려면 확장된 AggregateSales 보기로 이동합니다. SalesAmount 및 OrderDate를 클릭합니다. 시각화 열에서 왼쪽 차트 아이콘을 클릭합니다. 이 아이콘은 시각화에서 두 번째 열의 첫 번째 아이콘입니다.

	![Power BI 꺾은선형 차트 선택][10]

    이제 데이터를 다양한 시각화로 보여주는 보고서가 작성되었습니다.

    ![Power BI 꺾은선형][11]

언제든지 **파일**을 클릭하고 **저장**을 선택하여 진행 상황을 저장할 수 있습니다.

## 다음 단계
이제 샘플 데이터로 [개발][], [로드][] 또는 [마이그레이션][]할 준비 시간을 드리겠습니다.

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[마이그레이션]: ./sql-data-warehouse-overview-migrate.md
[개발]: ./sql-data-warehouse-overview-develop.md
[로드]: ./sql-data-warehouse-overview-load.md
[샘플 데이터를 수동으로 로드]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Azure Preview 포털]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: ./sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=Nov15_HO1-->