---
title: 서버리스 SQL 풀(미리 보기)에서 뷰 만들기 및 사용
description: 이 섹션에서는 뷰를 만들고 사용하여 서버리스 SQL 풀(미리 보기) 쿼리를 래핑하는 방법을 알아봅니다. 뷰를 사용하면 해당 쿼리를 다시 사용할 수 있습니다. Power BI와 같은 도구를 서버리스 SQL 풀과 함께 사용하려는 경우에도 뷰가 필요합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: d578529839afb5393dd013cb10f48c755f08addd
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316235"
---
# <a name="create-and-use-views-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버리스 SQL 풀(미리 보기)을 사용하여 뷰 만들기 및 사용

이 섹션에서는 뷰를 만들고 사용하여 서버리스 SQL 풀(미리 보기) 쿼리를 래핑하는 방법을 알아봅니다. 뷰를 사용하면 해당 쿼리를 다시 사용할 수 있습니다. Power BI와 같은 도구를 서버리스 SQL 풀과 함께 사용하려는 경우에도 뷰가 필요합니다.

## <a name="prerequisites"></a>필수 구성 요소

첫 번째 단계는 보기가 생성되는 데이터베이스를 만들고 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 Azure 스토리지에 인증하는 데 필요한 개체를 초기화하는 것입니다. 이 문서의 모든 쿼리는 샘플 데이터베이스에서 실행됩니다.

## <a name="create-a-view"></a>보기 만들기

일반 SQL Server 뷰를 만드는 것과 동일한 방법으로 뷰를 만들 수 있습니다. 다음 쿼리는 *population.csv* 파일을 읽는 뷰를 만듭니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

이 예제의 보기에서는 기본 파일에 대한 절대 경로를 사용하는 `OPENROWSET` 함수를 사용합니다. 스토리지의 루트 URL을 사용하는 `EXTERNAL DATA SOURCE`가 있는 경우 `DATA_SOURCE` 및 상대 파일 경로와 함께 `OPENROWSET`를 사용할 수 있습니다.

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>뷰 사용

SQL Server 쿼리에서 뷰를 사용하는 것과 동일한 방식으로 쿼리에서 뷰를 사용할 수 있습니다.

다음 쿼리에서는 [뷰 만들기](#create-a-view)에서 만든 *population_csv* 뷰를 사용하는 방법을 보여줍니다. 2019년의 인구를 기준으로 내림차순으로 국가/지역 이름을 반환합니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>다음 단계

다른 파일 형식을 쿼리하는 방법에 대한 자세한 내용은 [단일 CSV 파일 쿼리](query-single-csv-file.md), [Parquet 파일 쿼리](query-parquet-files.md) 및 [JSON 파일 쿼리](query-json-files.md) 문서를 참조하세요.
