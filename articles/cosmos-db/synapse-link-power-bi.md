---
title: Power BI 및 서버리스 SQL 풀에서 Synapse Link로 Azure Cosmos DB 데이터 분석
description: Azure Cosmos DB의 Synapse 링크를 통해 서버리스 SQL 풀 데이터베이스와 보기를 빌드하고, Azure Cosmos DB 컨테이너를 쿼리한 다음 해당 보기를 통해 Power BI가 있는 모델을 작성하는 방법을 알아보세요.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: synapse-cosmos-db
ms.openlocfilehash: d84508a7629481a7138f1080c86f4a203d35894d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626251"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Power BI 및 서버리스 Synapse SQL 풀을 사용하여 Synapse Link를 통해 Azure Cosmos DB 데이터 분석 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

이 문서에서는 Azure Cosmos DB의 Synapse Link를 통해 서버리스 SQL 풀 데이터베이스와 보기를 빌드하는 방법에 대해 알아봅니다. Azure Cosmos DB 컨테이너를 쿼리한 다음 해당 쿼리를 반영하기 위해 관련 보기를 통해 Power BI를 사용하여 모델을 작성합니다.

이 시나리오에서는 파트너 소매점의 Surface 제품 판매에 대한 더미 데이터를 사용합니다. 대규모 가구 수에 대한 근접성 및 특정 주의 광고 영향을 기준으로 매장 당 수익을 분석합니다. 이 문서에서는 **RetailSales** 와 **StoreDemographics** 라는 두 개의 보기와 그 둘 사이의 쿼리를 만듭니다. 이 [GitHub](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) 리포지토리에서 샘플 제품 데이터를 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 리소스를 만들어야 합니다.

* [SQL(core) 또는 MongoDB 종류의 Azure Cosmos DB 계정을 만듭니다.](create-cosmosdb-resources-portal.md)

* [Azure Cosmos 계정](configure-synapse-link.md#enable-synapse-link)에 Azure Synapse Link를 사용하도록 설정

* Azure Cosmos 계정 내에서 데이터베이스를 만들고 [분석 저장소가 활성화된](configure-synapse-link.md#create-analytical-ttl) 두 개의 컨테이너를 만듭니다.

* 이 [Batch 데이터 수집](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) 노트북에 설명된 대로 제품 데이터를 Azure Cosmos 컨테이너에 로드합니다.

* **SynapseLinkBI** 라는 [Synapse 작업 영역을 만듭니다](../synapse-analytics/quickstart-create-workspace.md).

* [Synapse 작업 영역에 Azure Cosmos 데이터베이스를 연결합니다](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>데이터베이스 및 보기 만들기

Synapse 작업 영역에서 **개발** 탭으로 이동하여 **+** 아이콘을 선택하고 **SQL 스크립트** 를 선택합니다.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Synapse Analytics 작업 영역에 SQL 스크립트 추가":::

모든 작업 영역이 서버리스 SQL 엔드포인트와 함께 제공됩니다. SQL 스크립트를 만든 후에는 맨 위의 도구 모음에서 **기본 제공** 에 연결합니다.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="작업 영역에서 서버리스 SQL 엔드포인트를 사용하도록 SQL 스크립트를 설정합니다.":::

**Master** 또는 **기본** 데이터베이스에 보기를 만드는 것은 권장되거나 지원되지 않습니다. **RetailCosmosDB** 라는 새 데이터베이스와 Synapse 링크를 지원하는 컨테이너에 대한 SQL 보기를 만듭니다. 다음 명령에서는 데이터베이스를 만드는 방법을 보여줍니다.

```sql
-- Create database
Create database RetailCosmosDB
```

다음으로, 다른 Synapse Link 지원 Azure Cosmos 컨테이너에서 여러 보기를 만듭니다. 보기를 사용하면 T-SQL을 사용하여 서로 다른 컨테이너에 있는 Azure Cosmos DB 데이터를 조인하고 쿼리할 수 있습니다.  보기를 만들 때 **RetailCosmosDB** 데이터베이스를 선택해야 합니다.

다음 스크립트는 각 컨테이너에 대한 보기를 만드는 방법을 보여줍니다. 편의상 Synapse Link 지원 컨테이너를 통한 서버리스 SQL 풀의 [자동 스키마 유추](analytical-store-introduction.md#analytical-schema) 기능을 사용하겠습니다.


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

이전 SQL 스크립트에 Azure Cosmos DB 지역 및 기본 키를 삽입해야 합니다. 지역 이름에 있는 모든 문자는 공백 없이 소문자여야 합니다. `OPENROWSET` 명령의 다른 매개 변수와 달리 컨테이너 이름 매개 변수는 따옴표로 묶지 않고 지정해야 합니다.

### <a name="storedemographics-view"></a>StoreDemographics 보기:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

이제 **실행** 명령을 선택하여 SQL 스크립트를 실행합니다.

## <a name="query-the-views"></a>보기 쿼리

이제 두 개의 보기를 만들었으므로 다음과 같이 두 보기를 조인하는 쿼리를 정의하겠습니다.

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

다음 테이블을 결과로 제공하는 **실행** 을 선택합니다.

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text=" StoreDemographics와 RetailSales 보기를 조인한 후의 쿼리 결과":::

## <a name="model-views-over-containers-with-power-bi"></a>Power BI가 지원되는 컨테이너에 대한 모델 보기

그리고 다음 단계를 사용하여 Power BI Desktop을 열고 서버리스 SQL 엔드포인트에 연결합니다.

1. Power BI Desktop 애플리케이션을 엽니다. **데이터 가져오기** 를 선택하고 **자세히** 를 선택합니다.

1. 연결 옵션 목록에서 **Azure Synapse Analytics(SQL DW)** 를 선택합니다.

1. 데이터베이스가 있는 SQL 엔드포인트의 이름을 입력합니다. **서버** 필드 내에 `SynapseLinkBI-ondemand.sql.azuresynapse.net`을 입력합니다. 이 예제에서 **SynapseLinkBI** 는 작업 영역의 이름입니다. 작업 영역에 다른 이름을 지정한 경우 이를 대체합니다. 데이터 연결 모드에 **직접 쿼리** 를 선택하고 **확인** 을 선택합니다.

1. Azure AD와 같은 기본 인증 방법을 선택합니다.

1. **RetailCosmosDB** 데이터베이스와 **RetailSales**, **StoreDemographics** 보기를 선택합니다.

1. **로드** 를 선택하여 두 보기를 직접 쿼리 모드로 로드합니다.

1. **모델** 을 선택하여 **storeId** 열을 통해 두 보기 간의 관계를 만듭니다.

1. **RetailSales** 보기에서 **StoreId** 열을 **StoreDemographics** 보기의 **StoreId** 열로 끌어다 놓습니다.

1. **RetailSales** 보기에 동일한 저장소 ID를 사용하는 행이 여러 개 있으므로 다대일(*: 1) 관계를 선택합니다. **StoreDemographics** 에는 하나의 저장소 ID 행만 있습니다(차원 테이블).

이제 **보고서** 창으로 이동하여 수익과 LargeHH 인덱스의 분산된 표시를 기준으로 매장별 평균 수익 대비 상대적 가구 규모 중요도를 비교하는 보고서를 만듭니다.

1. **분산형 차트** 를 선택합니다.

1. **LargeHH** 를 **StoreDemographics** 보기에서 X-축으로 끌어 놓습니다.

1. **RetailSales** 보기에서 Y-축으로 **수익** 을 끌어서 놓습니다. **평균** 을 선택하여 매장당 및 주당 평균 판매량을 가져옵니다.

1. **RetailSales** 보기의 **productCode** 를 범례에 끌어서 놓고 특정 제품 라인을 선택합니다.
해당 옵션을 선택하면 다음 스크린샷처럼 그래프가 표시됩니다.

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="매장당 평균 수익 대비 가구 규모의 상대적 중요도를 비교하는 보고서":::

## <a name="next-steps"></a>다음 단계

[T-SQL을 사용하여 Azure Synapse Link를 통해 Azure Cosmos DB 데이터 쿼리](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

서버리스 SQL 풀을 사용하여 [Azure Open Datasets를 분석하고 그 결과를 Azure Synapse Studio에서 시각화](../synapse-analytics/sql/tutorial-data-analyst.md)
