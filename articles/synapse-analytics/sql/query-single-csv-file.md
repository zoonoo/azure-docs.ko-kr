---
title: 주문형 SQL을 사용하여 CSV 파일 쿼리(미리 보기)
description: 이 문서에서는 SQL 온디맨드(미리 보기)를 사용하여 다양한 파일 형식을 사용하여 단일 CSV 파일을 쿼리하는 방법을 배웁니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431593"
---
# <a name="query-csv-files"></a>CSV 파일 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 단일 CSV 파일을 쿼리하는 방법을 알아봅니다. CSV 파일에는 다음과 같은 형식이 다를 수 있습니다. 

- 헤더 행의 유무
- 쉼표 및 탭 구분 값
- 창 및 유닉스 스타일 선 끝
- 인용되지 않은 값과 인용된 값, 이스케이프 문자

위의 모든 변형은 아래에서 다룹니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 나머지 부분을 읽기 전에 다음 문서를 검토하십시오.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>윈도우 스타일 새 줄

다음 쿼리에서는 Windows 스타일의 새 줄과 쉼표 구분된 열을 사용하여 헤더 행없이 CSV 파일을 읽는 방법을 보여 주며,

파일 미리 보기:

![헤더없이 CSV 파일의 처음 10 행, 윈도우 스타일 새 줄.](./media/query-single-csv-file/population.png)

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

## <a name="unix-style-new-line"></a>유닉스 스타일의 새로운 라인

다음 쿼리에서는 유닉스 스타일의 새 줄과 쉼표 구분된 열을 사용하여 헤더 행없이 파일을 읽는 방법을 보여 주며 쉼표 구분된 열을 보여 주어 있습니다. 다른 예제와 비교하여 파일의 다른 위치를 기록합니다.

파일 미리 보기:

![헤더 행이없고 유닉스 스타일 새 줄이없는 CSV 파일의 처음 10 행.](./media/query-single-csv-file/population-unix.png)

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

## <a name="header-row"></a>헤더 행

다음 쿼리에서는 유닉스 스타일의 새 줄과 쉼표 구분된 열을 사용하여 헤더 행을 사용하여 읽기 파일을 만드는 방법을 보여 주습니다. 다른 예제와 비교하여 파일의 다른 위치를 기록합니다.

파일 미리 보기:

![헤더 행과 유닉스 스타일 새 줄이있는 CSV 파일의 처음 10 행.](./media/query-single-csv-file/population-unix-hdr.png)

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

## <a name="custom-quote-character"></a>사용자 지정 견적 문자

다음 쿼리에서는 유닉스 스타일의 새 줄, 쉼표 구분된 열 및 따옴표로 된 값으로 헤더 행이 있는 파일을 읽는 방법을 보여 주며, 다른 예제와 비교하여 파일의 다른 위치를 기록합니다.

파일 미리 보기:

![헤더 행과 유닉스 스타일 새 줄 및 인용 값을 가진 CSV 파일의 처음 10 행.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

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
> FIELDQUOTE의 기본값이 double quote이므로 FIELDQUOTE 매개 변수를 생략한 경우 이 쿼리는 동일한 결과를 반환합니다.

## <a name="escaping-characters"></a>문자 이스케이프

다음 쿼리에서는 유닉스 스타일의 새 줄, 쉼표 구분된 열 및 값 내의 필드 구분 기호(쉼표)에 사용되는 이스케이프 문자가 있는 헤더 행이 있는 파일을 읽는 방법을 보여 주며, 이스케이프 문자는 다음과 같은 내용을 보여 주며, 이스케이프 문자는 다른 예제와 비교하여 파일의 다른 위치를 기록합니다.

파일 미리 보기:

![헤더 행과 유닉스 스타일 새 줄및 이스케이프 문자가 있는 CSV 파일의 처음 10행은 필드 구분기호에 사용됩니다.](./media/query-single-csv-file/population-unix-hdr-escape.png)

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
> "Slov,enia"의 쉼표가 국가 이름의 일부가 아닌 필드 구분기호로 처리되므로 ESCAPECHAR가 지정되지 않으면 이 쿼리가 실패합니다. "슬로바키아, 에니아"는 두 개의 열로 취급됩니다. 따라서 특정 행에는 다른 행보다 한 개의 열이 있고 한 열이 WITH 절에 정의된 것보다 더 많은 열을 갖습니다.

## <a name="tab-delimited-files"></a>탭 구분 된 파일

다음 쿼리에서는 유닉스 스타일의 새 줄과 탭 구분된 열을 사용하여 헤더 행을 사용하여 파일을 읽는 방법을 보여 주며, 다른 예제와 비교하여 파일의 다른 위치를 기록합니다.

파일 미리 보기:

![헤더 행과 유닉스 스타일 새 줄 및 탭 구분 기호와 CSV 파일의 처음 10 행.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

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

## <a name="returning-subset-of-columns"></a>열하위 집합 반환

지금까지 WITH를 사용하고 모든 열을 나열하는 CSV 파일 스키마를 지정했습니다. 필요한 각 열에 대한 서수 번호를 사용하여 쿼리에 실제로 필요한 열만 지정할 수 있습니다. 또한 관심 없는 열을 생략합니다.

다음 쿼리는 필요한 열만 지정하여 파일의 고유한 국가 이름 수를 반환합니다.

> [!NOTE]
> 아래 쿼리에서 WITH 절을 살펴보고 *[country_name]* 열을 정의하는 행 끝에 "2"(따옴표 제외)가 있습니다. *이는 [country_name]* 열이 파일의 두 번째 열임을 의미합니다. 쿼리는 두 번째 열을 제외한 파일의 모든 열을 무시합니다.

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

다음 문서에서는 다음 방법을 보여 줄 것입니다.

- [마루 파일 쿼리](query-parquet-files.md)
- [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)
