---
title: Synapse 링크로 Azure Cosmos DB 데이터를 분석 하기 위해 Power BI 및 서버 리스 Synapse SQL 풀
description: Azure Cosmos DB에 대 한 서버를 Power BI 사용 하지 않는 Synapse SQL 풀 데이터베이스 및 뷰를 빌드하는 방법에 대해 알아봅니다.
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2020
ms.author: acomet
ms.openlocfilehash: 6e77746d21d63cf1460b9e460e470a3bd12ce656
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480040"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link-preview"></a>Power BI 및 서버를 사용 하지 않는 Synapse SQL 풀을 사용 하 여 Synapse 링크로 Azure Cosmos DB 데이터 분석 (미리 보기) 

이 문서에서는 서버를 사용 하지 않는 Synapse SQL 풀 (이전에는 **SQL 주문형**) 데이터베이스 및 뷰를 사용 하 여 Azure Cosmos DB에 대 한 Synapse 링크를 빌드하는 방법에 대해 알아봅니다. Azure Cosmos 컨테이너를 쿼리 한 다음 해당 쿼리를 반영 하기 위해 이러한 보기에 대해 Power BI를 사용 하 여 모델을 작성 합니다.

이 시나리오에서는 파트너 소매점의 Surface 제품 판매에 대 한 더미 데이터를 사용 합니다. 큰 명인 가구의 근접성 및 특정 주에 대 한 광고의 영향을 기준으로 매장 당 수익을 분석 합니다. 이 문서에서는 **RetailSales** 및 파일 **인구 통계** 와 둘 간의 쿼리 라는 두 개의 뷰를 만듭니다. 이 [GitHub](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) 리포지토리에서 샘플 제품 데이터를 가져올 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 다음 리소스를 만들어야 합니다.

* [SQL (core) 또는 MongoDB 종류의 Azure Cosmos DB 계정을 만듭니다.](create-cosmosdb-resources-portal.md)

* [Azure Cosmos 계정](configure-synapse-link.md#enable-synapse-link) 에 대해 Azure Synapse 링크를 사용 하도록 설정

* Azure Cosmos 계정 내에서 데이터베이스를 만들고 [분석 저장소가 사용 하도록 설정](configure-synapse-link.md#create-analytical-ttl) 된 두 개의 컨테이너를 만듭니다.

* 이 [batch 데이터](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) 수집 노트북에 설명 된 대로 제품 데이터를 Azure Cosmos 컨테이너에 로드 합니다.

* **SynapseLinkBI**라는 [Synapse 작업 영역을 만듭니다](../synapse-analytics/quickstart-create-workspace.md) .

* [Azure Cosmos 데이터베이스를 Synapse 작업 영역에 연결](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)합니다.

## <a name="create-a-database-and-views"></a>데이터베이스 및 뷰 만들기

Synapse 작업 영역에서 **개발** 탭으로 이동 하 여 **+** 아이콘을 선택 하 고 **SQL 스크립트**를 선택 합니다.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Synapse Analytics 작업 영역에 SQL 스크립트 추가":::

모든 작업 영역에는 서버를 사용 하지 않는 SQL 끝점이 제공 됩니다. SQL 스크립트를 만든 후 맨 위에 있는 도구 모음에서 **요청 시 sql**에 연결 합니다.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Synapse Analytics 작업 영역에 SQL 스크립트 추가":::

**RetailCosmosDB**라는 새 데이터베이스와 Synapse 링크를 사용 하도록 설정 된 컨테이너에 대 한 SQL 뷰를 만듭니다. 다음 명령은 데이터베이스를 만드는 방법을 보여 줍니다.

```sql
-- Create database
Create database RetailCosmosDB
```

다음으로, 다른 Synapse Link enabled Azure Cosmos 컨테이너에서 여러 뷰를 만듭니다. 뷰를 사용 하면 T-sql을 사용 하 여 서로 다른 컨테이너에 있는 Azure Cosmos DB 데이터를 조인 하 고 쿼리할 수 있습니다.  뷰를 만들 때 **RetailCosmosDB** 데이터베이스를 선택 해야 합니다.

다음 스크립트는 각 컨테이너에 대 한 뷰를 만드는 방법을 보여 줍니다. 편의상 Synapse Link enabled 컨테이너에서 서버를 사용 하지 않는 Synapse SQL의 [자동 스키마 유추](analytical-store-introduction.md#analytical-schema) 기능을 사용 하겠습니다.


### <a name="retailsales-view"></a>RetailSales 보기:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

이전 SQL 스크립트에 Azure Cosmos DB 지역 및 기본 키를 삽입 해야 합니다. 영역 이름에 있는 모든 문자는 공백 없이 소문자 여야 합니다. 명령의 다른 매개 변수와 달리 `OPENROWSET` container name 매개 변수는 따옴표로 묶지 않고 지정 해야 합니다.

### <a name="storedemographics-view"></a>작업 인구 통계 보기:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

이제 **실행** 명령을 선택 하 여 SQL 스크립트를 실행 합니다.

## <a name="query-the-views"></a>뷰 쿼리

이제 두 뷰를 만들었으므로 다음과 같이 두 뷰를 조인 하는 쿼리를 정의 하겠습니다.

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

다음 테이블을 결과로 제공 하는 **실행** 을 선택 합니다.

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Synapse Analytics 작업 영역에 SQL 스크립트 추가":::

## <a name="model-views-over-containers-with-power-bi"></a>Power BI를 사용 하 여 컨테이너에 대 한 모델 뷰

다음 단계를 사용 하 여 Power BI desktop을 열고 서버를 사용 하지 않는 SQL 끝점에 연결 합니다.

1. Power BI Desktop 응용 프로그램을 엽니다. **데이터 가져오기** 를 선택 하 고 **자세히**를 선택 합니다.

1. 연결 옵션 목록에서 **Azure Synapse Analytics (SQL DW)** 를 선택 합니다.

1. 데이터베이스가 있는 SQL 끝점의 이름을 입력 합니다. `SynapseLinkBI-ondemand.sql.azuresynapse.net` **서버** 필드 내에를 입력 합니다. 이 예제에서  **SynapseLinkBI** 는 작업 영역의 이름입니다. 작업 영역에 다른 이름을 지정한 경우이를 대체 합니다. 데이터 연결 모드에 대 한 **직접 쿼리** 를 선택 하 고 **확인**을 선택 합니다.

1. Azure AD와 같은 기본 인증 방법을 선택 합니다.

1. **RetailCosmosDB** 데이터베이스와 **RetailSales**, 데이터 **인구 통계** 보기를 선택 합니다.

1. **부하** 를 선택 하 여 두 뷰를 직접 쿼리 모드로 로드 합니다.

1. **모델** 을 선택 하 여 **storeId** 열을 통해 두 뷰 간의 관계를 만듭니다.

1. **RetailSales** 뷰에서 **StoreId** 열을 **StoreId** 열로 끌어다 놓습니다. **StoreDemographics**

1. **RetailSales** 보기에 동일한 저장소 ID를 가진 여러 행이 있으므로 다 대 일 (*: 1) 관계를 선택 합니다. 저장소 **인구 통계** 에는 하나의 저장소 ID 행만 있습니다 (차원 테이블).

이제 **보고서** 창으로 이동 하 고 LargeHH 인덱스의 분산 된 표현에 따라 가사 크기의 상대적 중요도와 매장 당 평균 수익을 비교 하는 보고서를 만듭니다.

1. **분산형 차트**를 선택 합니다.

1. **LargeHH** 을 [기능 **통계** ] 보기에서 X 축으로 끌어 놓습니다.

1. **RetailSales** View에서 Y 축으로 **수익** 을 끌어서 놓습니다. **평균** 을 선택 하 여 매장 당 및 주당 평균 판매량을 가져옵니다.

1. **RetailSales** 보기의 **productCode** 를 범례에 끌어서 놓고 특정 제품 라인을 선택 합니다.
이러한 옵션을 선택 하면 다음 스크린샷 처럼 그래프가 표시 됩니다.

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Synapse Analytics 작업 영역에 SQL 스크립트 추가":::

## <a name="next-steps"></a>다음 단계

[T-sql을 사용 하 여 Azure Synapse 링크를 사용 하 여 Azure Cosmos DB 데이터 쿼리](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

서버를 사용 하지 않는 Synapse SQL 풀을 사용 하 여 azure [Open 데이터 집합 분석 및 Azure Synapse Studio에서 결과 시각화](../synapse-analytics/sql/tutorial-data-analyst.md)
