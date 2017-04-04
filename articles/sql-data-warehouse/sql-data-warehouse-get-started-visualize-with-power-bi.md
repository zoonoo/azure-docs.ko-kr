---
title: "Power BI Microsoft Azure로 SQL 데이터 웨어하우스 데이터 시각화"
description: "Power BI로 SQL 데이터 웨어하우스 데이터 시각화"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: d7fb89d1-da1d-4788-a111-68d0e3fda799
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 8790bacecac0fa824189b5c212c2d803092ff4ed
ms.lasthandoff: 01/30/2017


---
# <a name="visualize-data-with-power-bi"></a>Power BI를 사용하여 데이터 시각화
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 기계 학습](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

이 자습서에서는 Power BI를 사용하여 SQL 데이터 웨어하우스에 연결하고 몇 가지 기본적인 시각화를 만드는 방법을 보여줍니다.

> [!비디오 https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>필수 조건
이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

* AdventureWorksDW 데이터베이스로 미리 로드된 SQL 데이터 웨어하우스. 프로비전하려면 [SQL Data Warehouse 만들기][Create a SQL Data Warehouse]를 참조하고 샘플 데이터 로드를 선택합니다. 데이터 웨어하우스는 있지만 샘플 데이터가 없는 경우 [샘플 데이터를 수동으로 로드][load sample data manually]할 수 있습니다.

## <a name="1-connect-to-your-database"></a>1. 데이터베이스 연결
Power BI를 열고 AdventureWorksDW 데이터베이스에 연결하려면

1. [Azure Portal][Azure portal]에 로그인합니다.
2. **SQL 데이터베이스** 를 클릭하고 AdventureWorks SQL 데이터 웨어하우스 데이터베이스를 선택합니다.
   
    ![데이터베이스 찾기][1]
3. 'Power BI에서 열기' 단추를 클릭합니다.
   
    ![Power BI 단추][2]
4. 이제 데이터베이스 웹 주소를 표시하는 SQL 데이터 웨어하우스 연결 페이지가 표시됩니다. 다음을 클릭합니다.
   
    ![Power BI 연결][3]
5. Azure SQL 서버 사용자 이름과 암호를 입력하면 SQL 데이터 웨어하우스 데이터베이스에 완벽하게 연결됩니다.
   
    ![Power BI 로그인][4]
6. Power BI에 로그인하면 왼쪽 블레이드에서 AdventureWorksDW 데이터 집합을 클릭합니다. 그러면 데이터베이스가 열립니다.
   
    ![Power BI AdventureWorksDW 열기][5]

## <a name="2-create-a-report"></a>2. 보고서 만들기
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

## <a name="next-steps"></a>다음 단계
이제 샘플 데이터로 [개발][develop], [로드][load] 또는 [마이그레이션][migrate]할 준비 시간을 드리겠습니다. 또는 [Power BI 웹 사이트][Power BI website]를 살펴보세요.

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure portal]: https://portal.azure.com/
[Power BI website]: http://www.powerbi.com/

