---
title: SQL 주문형(미리 보기)에서 외부 테이블 만들기 및 사용
description: 이 섹션에서는 SQL 주문형(미리 보기)에서 외부 테이블을 만들고 사용하는 방법에 대해 알아봅니다. 외부 테이블은 SQL 주문형에서 외부 데이터에 대한 액세스를 제어하고 Power BI와 같은 도구를 주문형 SQL과 함께 사용하려는 경우에 유용합니다.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420797"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Azure Synapse Analytics를 사용하여 SQL 주문형(미리 보기)에서 외부 테이블 만들기 및 사용

이 섹션에서는 SQL 주문형(미리 보기)에서 외부 테이블을 만들고 사용하는 방법에 대해 알아봅니다. 외부 테이블은 SQL 주문형에서 외부 데이터에 대한 액세스를 제어하고 Power BI와 같은 도구를 주문형 SQL과 함께 사용하려는 경우에 유용합니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 아래의 문서를 검토하여 SQL 주문형 외부 테이블을 만들고 사용하기 위한 필수 구성 요소를 충족하는지 확인하는 것입니다.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>외부 테이블 만들기

일반 SQL Server 외부 테이블을 만드는 것과 같은 방법으로 외부 테이블을 만들 수 있습니다. 아래 쿼리는 *population.csv* 파일을 읽는 외부 테이블을 만듭니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다. 데이터베이스를 만들지 않은 경우 [처음 설정](query-data-storage.md#first-time-setup)을 참조하세요.

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>외부 테이블 사용

SQL Server 쿼리에서 사용하는 것과 동일한 방식으로 쿼리에서 외부 테이블을 사용할 수 있습니다.

다음 쿼리에서는 [외부 테이블 만들기](#create-an-external-table) 섹션에서 만든 *모집단* 외부 테이블을 사용하는 방법을 보여줍니다. 2019년의 인구를 기준으로 내림차순으로 국가 이름을 반환합니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다. 데이터베이스를 만들지 않은 경우 [처음 설정](query-data-storage.md#first-time-setup)을 참조하세요.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>다음 단계

스토리지에 쿼리 결과를 저장하는 방법에 대한 자세한 내용은 [쿼리 결과를 스토리지에 저장](../sql/create-external-table-as-select.md)을 참조하세요.
