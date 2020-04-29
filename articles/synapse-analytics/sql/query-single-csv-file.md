---
title: SQL 주문형 (미리 보기)을 사용 하 여 CSV 파일 쿼리
description: 이 문서에서는 SQL 주문형 (미리 보기)를 사용 하 여 다른 파일 형식으로 단일 CSV 파일을 쿼리 하는 방법에 대해 알아봅니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431593"
---
# <a name="query-csv-files"></a>CSV 파일 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형 (미리 보기)를 사용 하 여 단일 CSV 파일을 쿼리 하는 방법에 대해 알아봅니다. CSV 파일의 형식은 서로 다를 수 있습니다. 

- 머리글 행 유무에 관계 없이
- 쉼표 및 탭으로 구분 된 값
- Windows 및 Unix 스타일 줄 끝
- 따옴표 붙지 않은 값 및 따옴표 붙은 값과 이스케이프 문자

위의 모든 변형은 아래에서 설명 합니다.

## <a name="prerequisites"></a>전제 조건

이 문서의 나머지 부분을 읽기 전에 다음 문서를 검토 하세요.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Windows 스타일 새 줄

다음 쿼리는 헤더 행 없이 Windows 스타일의 줄 바꿈 및 쉼표로 구분 된 열을 사용 하 여 CSV 파일을 읽는 방법을 보여 줍니다.

파일 미리 보기:

![헤더가 없는 CSV 파일의 처음 10개 행, Windows 스타일 새 줄](./media/query-single-csv-file/population.png)

```sql
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
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Unix 스타일 새 줄

다음 쿼리에서는 헤더 행 없이 Unix 스타일의 줄 바꿈 및 쉼표로 구분 된 열을 사용 하 여 파일을 읽는 방법을 보여 줍니다. 다른 예제와 비교할 때 파일의 다른 위치를 확인 합니다.

파일 미리 보기:

![헤더 행이 없고 Unix 스타일 새 줄이 있는 CSV 파일의 처음 10 개 행입니다.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
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

## <a name="header-row"></a>머리글 행

다음 쿼리는 Unix 스타일의 줄 바꿈 및 쉼표로 구분 된 열이 있는 머리글 행이 있는 파일을 읽는 방법을 보여 줍니다. 다른 예제와 비교할 때 파일의 다른 위치를 확인 합니다.

파일 미리 보기:

![머리글 행이 있는 CSV 파일의 처음 10 개 행 및 Unix 스타일의 새 줄입니다.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
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

## <a name="custom-quote-character"></a>사용자 지정 따옴표 문자

다음 쿼리에서는 헤더 행이 있는 파일을 읽는 방법을 보여 줍니다. 여기에는 Unix 스타일의 줄 바꿈, 쉼표로 구분 된 열 및 따옴표 붙은 값이 있습니다. 다른 예제와 비교할 때 파일의 다른 위치를 확인 합니다.

파일 미리 보기:

![머리글 행이 있는 CSV 파일의 처음 10 개 행과 Unix 스타일의 줄 바꿈 및 따옴표 붙은 값](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
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
> Fieldquote의 기본값은 큰따옴표 이므로 FIELDQUOTE 매개 변수를 생략 하면이 쿼리는 동일한 결과를 반환 합니다.

## <a name="escaping-characters"></a>문자 이스케이프

다음 쿼리에서는 헤더 행이 있는 파일을 읽는 방법, Unix 스타일의 줄 바꿈 열, 쉼표로 구분 된 열 및 값 내의 필드 구분 기호 (쉼표)에 사용 되는 이스케이프 문자를 보여 줍니다. 다른 예제와 비교할 때 파일의 다른 위치를 확인 합니다.

파일 미리 보기:

![머리글 행이 있는 CSV 파일의 처음 10 개 행과, 필드 구분 기호에 사용 되는 Unix 스타일의 줄 바꿈 및 이스케이프 char.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
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
> "Slov, enia"의 쉼표가 country name의 일부가 아니라 필드 구분 기호로 처리 되기 때문에 ESCAPECHAR를 지정 하지 않으면이 쿼리는 실패 합니다. "Slov, enia"는 두 개의 열로 처리 됩니다. 따라서 특정 행에 다른 열이 하나 이상 있고 WITH 절에 정의한 열이 하나 이상 있습니다.

## <a name="tab-delimited-files"></a>탭으로 구분 된 파일

다음 쿼리는 Unix 스타일의 줄 바꿈 및 탭으로 구분 된 열을 사용 하 여 머리글 행이 있는 파일을 읽는 방법을 보여 줍니다. 다른 예제와 비교할 때 파일의 다른 위치를 확인 합니다.

파일 미리 보기:

![머리글 행이 있는 CSV 파일의 처음 10 개 행과 Unix 스타일의 줄 바꿈 및 탭 구분 기호](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
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

지금까지를 사용 하 여 CSV 파일 스키마를 지정 하 고 모든 열을 나열 했습니다. 필요한 각 열에 대해 서 수를 사용 하 여 쿼리에 실제로 필요한 열만 지정할 수 있습니다. 또한 관심 없는 열도 생략 합니다.

다음 쿼리는 파일의 고유한 국가 이름 수를 반환 하 고 필요한 열만 지정 합니다.

> [!NOTE]
> 아래의 쿼리에서 WITH 절을 확인 하 고 *[country_name]* 열을 정의 하는 행의 끝에 "2" (따옴표 제외)가 있는지 확인 합니다. *[Country_name]* 열이 파일의 두 번째 열 임을 의미 합니다. 쿼리는 파일에서 두 번째 열을 제외한 모든 열을 무시 합니다.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 다음을 수행 하는 방법을 보여 줍니다.

- [Parquet 파일 쿼리](query-parquet-files.md)
- [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)
