---
title: 스토리지에 쿼리 결과 저장
description: 이 문서에서는 SQL 주문형(미리 보기)을 사용하여 쿼리 결과를 스토리지에 저장하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: dd7666bb9f22214fb4701e6be9edc171912d9bf9
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691856"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Azure Synapse Analytics를 통해 SQL 주문형(미리 보기)를 사용하여 스토리지에 쿼리 결과 저장

이 문서에서는 SQL 주문형(미리 보기)을 사용하여 쿼리 결과를 스토리지에 저장하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 아래 문서를 검토하여 필수 구성 요소를 충족하는지 확인하는 것입니다.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>선택 항목으로 외부 테이블 만들기

CREATE EXTERNAL TABLE AS SELECT(CETAS) 문을 사용하여 스토리지에 쿼리 결과를 저장할 수 있습니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다. 데이터베이스를 만들지 않은 경우 [처음 설정](query-data-storage.md#first-time-setup)을 참조하세요. 쓰기 권한이 있는 위치를 가리키도록 MyDataSource 외부 데이터 원본에 대한 위치를 변경해야 합니다. 

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>생성된 외부 테이블 사용

CETAS를 통해 생성된 외부 테이블을 일반 외부 테이블처럼 사용할 수 있습니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다. 데이터베이스를 만들지 않은 경우 [처음 설정](query-data-storage.md#first-time-setup)을 참조하세요.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>다음 단계

다른 파일 형식을 쿼리하는 방법에 대한 자세한 내용은 [단일 CSV 파일 쿼리](query-single-csv-file.md), [Parquet 파일 쿼리](query-parquet-files.md) 및 [JSON 파일 쿼리](query-json-files.md) 문서를 참조하세요.
