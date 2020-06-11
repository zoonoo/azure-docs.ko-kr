---
title: SQL 주문형(미리 보기)을 사용하여 CSV 파일 쿼리
description: 이 문서에서는 SQL 주문형(미리 보기)으로 다른 파일 형식의 단일 CSV 파일을 쿼리하는 방법에 대해 알아봅니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 41c4a8940cc49a3859a2511f0de65d0019817078
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836552"
---
# <a name="query-csv-files"></a>CSV 파일 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 단일 CSV 파일을 쿼리하는 방법에 대해 알아봅니다. CSV 파일의 형식은 서로 다를 수 있습니다. 

- 헤더 행 포함 또는 제외
- 쉼표 및 탭으로 구분된 값
- Windows 및 Unix 스타일 줄 끝
- 따옴표가 붙지 않은 값 및 따옴표가 붙은 값과 문자 이스케이프

위의 모든 변형은 아래에서 다룹니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 테이블을 만들 **데이터베이스를 만드는 것**입니다. 그런 다음 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 예에서 사용되는 데이터 원본, 데이터베이스 범위의 자격 증명 및 외부 파일 형식을 만듭니다.

## <a name="windows-style-new-line"></a>Windows 스타일 줄 바꿈

다음 쿼리는 Windows 스타일 새 행과 쉼표로 구분된 열을 사용하여 헤더 행 없는 CSV 파일을 읽는 방법을 보여 줍니다.

파일 미리 보기:

![헤더가 없는 CSV 파일의 처음 10개 행, Windows 스타일 새 줄](./media/query-single-csv-file/population.png)

```sql
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
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Unix 스타일 줄 바꿈

다음 쿼리에서는 헤더 행이 없이 Unix 스타일 줄 바꿈 및 쉼표로 구분된 열로 구성된 파일을 읽는 방법을 보여 줍니다. 다른 예와 비교하면 파일의 위치가 다르다는 것을 알 수 있습니다.

파일 미리 보기:

![헤더 행이 없이 Unix 스타일 줄 바꿈이 있는 CSV 파일의 처음 10개 행입니다.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>헤더 행

다음 쿼리는 헤더 행이 있고 Unix 스타일 줄 바꿈 및 쉼표로 구분된 열로 구성된 파일을 읽는 방법을 보여 줍니다. 다른 예와 비교하면 파일의 위치가 다르다는 것을 알 수 있습니다.

파일 미리 보기:

![헤더 행과 Unix 스타일 줄 바꿈이 있는 CSV 파일의 처음 10개 행입니다.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>사용자 지정 따옴표

다음 쿼리에서는 헤더 행이 있고 Unix 스타일 줄 바꿈, 쉼표로 구분된 열 및 따옴표가 붙은 값으로 구성된 파일을 읽는 방법을 보여 줍니다. 다른 예와 비교하면 파일의 위치가 다르다는 것을 알 수 있습니다.

파일 미리 보기:

![헤더 행이 있고 Unix 스타일 줄 바꿈 및 따옴표가 붙은 값이 있는 CSV 파일의 처음 10개 행입니다.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> FIELDQUOTE의 기본값은 큰따옴표이므로 FIELDQUOTE 매개 변수를 생략하면 이 쿼리는 동일한 결과를 반환합니다.

## <a name="escaping-characters"></a>문자 이스케이프

다음 쿼리에서는 헤더 행이 있고 Unix 스타일 줄 바꿈, 쉼표로 구분된 열 및 값 내의 필드 구분 기호(쉼표)에 사용되는 이스케이프 문자로 구성된 파일을 읽는 방법을 보여 줍니다. 다른 예와 비교하면 파일의 위치가 다르다는 것을 알 수 있습니다.

파일 미리 보기:

![헤더 행이 있고 Unix 스타일 줄 바꿈 및 필드 구분 기호에 사용되는 이스케이프 문자가 있는 CSV 파일의 처음 10개 행입니다.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slov,enia';
```

> [!NOTE]
> "Slov,enia"의 쉼표가 국가/지역 이름의 일부가 아니라 필드 구분 기호로 처리되기 때문에 ESCAPECHAR를 지정하지 않으면 이 쿼리는 실패합니다. "Slov,enia"는 두 개의 열로 처리됩니다. 따라서 특정 행에는 다른 행보다 하나의 열이 더 있고, WITH 절에서 정의한 것보다 하나의 열이 더 있습니다.

## <a name="tab-delimited-files"></a>탭으로 구분된 파일

다음 쿼리는 헤더 행이 있고 Unix 스타일 줄 바꿈 및 탭으로 구분된 열로 구성된 파일을 읽는 방법을 보여 줍니다. 다른 예와 비교하면 파일의 위치가 다르다는 것을 알 수 있습니다.

파일 미리 보기:

![헤더 행이 있고 Unix 스타일 줄 바꿈 및 탭 구분 기호가 있는 CSV 파일의 처음 10개 행입니다.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>열의 하위 집합 반환

지금까지 WITH를 사용하고 모든 열을 나열하는 CSV 파일 스키마를 지정했습니다. 필요한 각 열에 서수를 사용하여 쿼리에 실제로 필요한 열만 지정할 수 있습니다. 또한 관심이 없는 열은 생략합니다.

다음 쿼리는 필요한 열만 지정하여 파일의 고유한 국가/지역 이름 수를 반환합니다.

> [!NOTE]
> 아래 쿼리의 WITH 절을 보면 *[country_name]* 열을 정의하는 행의 끝에 "2"(따옴표 제외)가 있습니다. 이는 *[country_name]* 열이 파일의 두 번째 열임을 의미합니다. 이 쿼리는 파일에서 두 번째 열을 제외한 모든 열을 무시합니다.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 다음을 수행하는 방법을 보여 줍니다.

- [Parquet 파일 쿼리](query-parquet-files.md)
- [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)
