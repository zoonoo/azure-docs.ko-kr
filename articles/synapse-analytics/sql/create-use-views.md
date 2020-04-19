---
title: SQL 주문형(미리 보기)에서 뷰 만들기 및 사용
description: 이 섹션에서는 뷰를 만들고 사용하여 SQL 주문형(미리 보기) 쿼리를 래핑하는 방법에 대해 알아봅니다. 뷰를 사용하면 해당 쿼리를 다시 사용할 수 있습니다. Power BI와 같은 도구를 SQL 주문형과 함께 사용하려는 경우에도 뷰가 필요합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420777"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Azure Synapse Analytics를 사용하여 SQL 주문형(미리 보기)에서 뷰 만들기 및 사용

이 섹션에서는 뷰를 만들고 사용하여 SQL 주문형(미리 보기) 쿼리를 래핑하는 방법에 대해 알아봅니다. 뷰를 사용하면 해당 쿼리를 다시 사용할 수 있습니다. Power BI와 같은 도구를 SQL 주문형과 함께 사용하려는 경우에도 뷰가 필요합니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 아래의 문서를 검토하여 SQL 주문형 뷰를 만들고 사용하기 위한 필수 구성 요소를 충족하는지 확인하는 것입니다.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>보기 만들기

일반 SQL Server 뷰를 만드는 것과 동일한 방법으로 뷰를 만들 수 있습니다. 아래 쿼리는 *population.csv* 파일을 읽는 뷰를 만듭니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다. 데이터베이스를 만들지 않은 경우 [처음 설정](query-data-storage.md#first-time-setup)을 참조하세요.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
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

## <a name="use-a-view"></a>뷰 사용

SQL Server 쿼리에서 뷰를 사용하는 것과 동일한 방식으로 쿼리에서 뷰를 사용할 수 있습니다.

다음 쿼리에서는 [뷰 만들기](#create-a-view)에서 만든 *population_csv* 뷰를 사용하는 방법을 보여줍니다. 2019년의 인구를 기준으로 내림차순으로 국가 이름을 반환합니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다. 데이터베이스를 만들지 않은 경우 [처음 설정](query-data-storage.md#first-time-setup)을 참조하세요.

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
