---
title: 쿼리 가속 SQL 언어 참조(미리 보기)
titleSuffix: Azure Storage
description: 쿼리 가속 sql 구문을 사용하는 방법에 대해 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772120"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>쿼리 가속 SQL 언어 참조(미리 보기)

쿼리 가속은 Blob 내용에 대한 쿼리를 표현하기 위한 ANSI SQL과 같은 언어를 지원합니다.  쿼리 가속 SQL 방언은 지원되는 데이터 형식, 연산자 등의 집합이 제한된 ANSI SQL의 하위 집합이지만 ANSI SQL에서 확장되어 JSON과 같은 계층적 반구조화 데이터 형식에 대한 쿼리를 지원합니다. 

> [!NOTE]
> 쿼리 가속 기능은 공개 미리 보기에 있으며 캐나다 중부 및 프랑스 중부 지역에서 사용할 수 있습니다. 제한 을 검토하려면 [알려진 문제](data-lake-storage-known-issues.md) 문서를 참조하세요. 미리 보기에 등록하려면 [이 양식을](https://aka.ms/adls/qa-preview-signup)참조하십시오. 

## <a name="select-syntax"></a>구문 선택

쿼리 가속에서 지원하는 유일한 SQL 문은 SELECT 문입니다. 이 예제는 식이 true를 반환하는 모든 행을 반환합니다.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

CSV 형식의 데이터의 경우 *테이블이* 여야 합니다. `BlobStorage`  즉, REST 호출에 지정된 Blob에 대해 쿼리가 실행됩니다.
JSON 형식의 데이터의 경우 *테이블은* "테이블 설명자"입니다.   이 문서의 [표 설명자](#table-descriptors) 섹션을 참조하십시오.

다음 예제에서 WHERE *식이* true를 반환하는 각 행에 대해 이 문은 각 투영 식을 평가하여 만들어진 새 행을 반환합니다.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

다음 예제에서는 *식이* true를 반환하는 각 행에 대해 집계 계산(예: 특정 열의 평균 값)을 반환합니다. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

다음 예제는 CSV 형식의 Blob분할에 적합한 오프셋을 반환합니다.  이 문서의 [Sys.Split](#sys-split) 섹션을 참조하십시오.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>데이터 형식

|데이터 형식|설명|
|---------|-------------------------------------------|
|INT      |부호 있는 64비트 정수.                     |
|FLOAT    |64비트("이중 정밀도") 부동 점.|
|STRING   |가변 길이 유니코드 문자열입니다.            |
|timestamp|시간의 한 지점입니다.                           |
|BOOLEAN  |True 또는 False입니다.                             |

CSV 형식의 데이터에서 값을 읽을 때 모든 값은 문자열로 읽습니다.  문자열 값은 CAST 식을 사용하여 다른 유형으로 변환될 수 있습니다.  값은 컨텍스트에 따라 다른 형식에 암시적으로 캐스팅될 수 있습니다. 자세한 내용은 [데이터 형식 우선 순위(거래-SQL)를](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)참조하십시오.

## <a name="expressions"></a>식

### <a name="referencing-fields"></a>참조 필드

JSON 형식의 데이터 또는 헤더 행이 있는 CSV 형식의 데이터의 경우 필드를 이름으로 참조할 수 있습니다.  필드 이름은 인용또는 인용되지 않을 수 있습니다. 따옴표로 묶인 필드 이름은 큰따옴표 문자(")로 동봉되고 공백을 포함할 수 있으며 대/소문자를 구분할 수 있습니다.  인용되지 않은 필드 이름은 대/소문자를 구분하지 않으며 특수 문자를 포함하지 않을 수 있습니다.

CSV 형식의 데이터에서 필드는 밑줄(_) 문자로 접두어 서수로 참조할 수도 있습니다.  예를 들어, 제1 필드는 _1로 참조되거나 11번째 필드는 _11로 참조될 수 있다.  서수별로 필드를 참조하는 것은 헤더 행을 포함하지 않는 CSV 형식의 데이터에 유용하며, 이 경우 특정 필드를 참조하는 유일한 방법은 서수입니다.

### <a name="operators"></a>연산자

다음과 같은 표준 SQL 연산자가 지원됩니다.

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

연산자의 왼쪽과 오른쪽에 있는 데이터 형식이 다른 경우 여기에 지정된 규칙인 [데이터 형식 우선 순위(Transact-SQL)에](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)따라 자동 변환이 수행됩니다.

쿼리 가속 SQL 언어는 해당 문서에서 설명하는 매우 작은 데이터 형식 하위 집합만 지원합니다.  이 문서의 [데이터 유형](#data-types) 섹션을 참조하십시오.

### <a name="casts"></a>캐스팅

쿼리 가속 SQL 언어는 여기에 있는 규칙에 따라 CAST 연산자(데이터베이스 [엔진)를](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017)지원합니다.  

쿼리 가속 SQL 언어는 해당 문서에서 설명하는 데이터 형식의 작은 하위 집합만 지원합니다.  이 문서의 [데이터 유형](#data-types) 섹션을 참조하십시오.

### <a name="string-functions"></a>문자열 함수

쿼리 가속 SQL 언어는 다음과 같은 표준 SQL 문자열 함수를 지원합니다.

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

다음은 몇 가지 예입니다.

|기능|예제|결과|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

[LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) 함수는 패턴을 검색하는 데 도움이 됩니다. 다음은 [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) 함수를 사용하여 데이터 문자열을 ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``검색하는 몇 가지 예입니다.

|쿼리|예제|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>날짜 함수

다음과 같은 표준 SQL 날짜 함수가 지원됩니다.

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

현재 표준 [IS08601의 모든 날짜 형식을 변환합니다.](https://www.w3.org/TR/NOTE-datetime) 

#### <a name="date_add-function"></a>DATE_ADD 기능

쿼리 가속 SQL 언어는 함수에 대해 연도, 월, ``DATE_ADD`` 일, 시간, 분, 초를 지원합니다.

예제:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF 기능

쿼리 가속 SQL 언어는 함수에 대해 연도, 월, ``DATE_DIFF`` 일, 시간, 분, 초를 지원합니다.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>추출 기능

``DATE_ADD`` 함수에 지원되는 날짜 부분 이외의 EXTRACT의 경우 쿼리 가속 SQL 언어는 timezone_hour 지원하며 날짜 부분으로 timezone_minute.

예제:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING 기능

예제:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

이 표에서는 함수의 출력 형식을 지정하는 데 ``TO_STRING`` 사용할 수 있는 문자열에 대해 설명합니다.

|형식 문자열    |출력                               |
|-----------------|-------------------------------------|
|yy               |연도 2자리 형식 - 1999년 '99'로|
|y                |연도 4자리 형식               |
|yyyy             |연도 4자리 형식               |
|M                |월 - 1                    |
|MM               |제로 패딩 월 – 01               |
|MMM              |Abbr. 연도의 월 -JAN            |
|MMMM             |전체 월 - 5 월                      |
|d                |월(1-31)                  |
|dd               |매월 제로 패딩 일 (01-31)     |
|a                |오전 또는 오후                             |
|h                |하루 중 시간 (1-12)                   |
|hh               |제로 패딩 시간 od 일 (01-12)     |
|H                |시간대 (0-23)                   |
|HH               |제로 패딩 시간 (00-23)      |
|m                |시간의 분 (0-59)                |
|MM               |제로 패딩 분 (00-59)           |
|s                |초분 (0-59)             |
|ss               |제로 패딩 초 (00-59)          |
|S                |초분수(0.1-0.9)        |
|SS               |초분(0.01-0.99)      |
|Sss              |초분(0.001-0.999)    |
|X                |시간 오프셋                      |
|트리플 엑스 또는 트리플 엑스       |시간 및 분 간격 띄우기(+0430)  |
|트리플 엑스 또는 트리플 엑스     |시간 및 분 간격 띄우기(-07:00) |
|x                |시간 오프셋 (7)                  |
|트리플 엑스 또는 트리플 엑스       |시간 및 분 간격 띄우기(+0530)    |
|트리플 엑스 또는 트리플 엑스     |시간 및 분 간격 띄우기(+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP 기능

IS08601 형식만 지원됩니다.

예제:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> 이 함수를 ``UTCNOW`` 사용하여 시스템 시간을 얻을 수도 있습니다.


## <a name="aggregate-expressions"></a>집계 식

SELECT 문에는 하나 이상의 프로젝션 식 또는 단일 집계 식이 포함될 수 있습니다.  다음 집계 식은 지원됩니다.

|식|설명|
|--|--|
|[개수()\*](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |조건자 식과 일치하는 레코드 수를 반환합니다.|
|[COUNT(식)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |null이 아닌 레코드 수를 반환합니다.|
|[평균(표현식)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |null이 아닌 식 값의 평균을 반환합니다.|
|[MIN(표현식)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |식의 최소 비null 값을 반환합니다.|
|[MAX(표현식)](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)    |식의 최대 비null 값을 반환합니다.|
|[SUM(표현식)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |null이 아닌 모든 값의 합계를 반환합니다.|

### <a name="missing"></a>누락 된

연산자는 ``IS MISSING`` 쿼리 가속 SQL 언어가 지원하는 유일한 비표준입니다.  JSON 데이터의 경우 특정 입력 레코드에서 필드가 누락된 ``IS MISSING`` 경우 표현식 필드는 부울 값 true로 평가됩니다.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>테이블 설명자

CSV 데이터의 경우 테이블 이름은 `BlobStorage`항상 입니다.  예를 들어:

```sql
SELECT * FROM BlobStorage
```

JSON 데이터의 경우 추가 옵션을 사용할 수 있습니다.

```sql
SELECT * FROM BlobStorage[*].path
```

이렇게 하면 JSON 데이터의 하위 집합에 대한 쿼리가 가능합니다.

JSON 쿼리의 경우 FROM 절의 일부에서 경로를 언급할 수 있습니다. 이러한 경로는 JSON 데이터의 하위 집합을 구문 분석하는 데 도움이 됩니다. 이러한 경로는 JSON 배열 및 개체 값을 참조할 수 있습니다.

이것을 더 자세히 이해하기 위해 예를 들어 봅시다.

샘플 데이터는 다음과 같은 데이터입니다.

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

위의 데이터에서 `warehouses` JSON 개체에만 관심이 있을 수 있습니다. 개체는 `warehouses` JSON 배열 형식이므로 FROM 절에서 이 것을 언급할 수 있습니다. 샘플 쿼리는 다음과 같이 보일 수 있습니다.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

쿼리는 모든 필드를 가져옵니다하지만 위도만 선택합니다.

`dimensions` JSON 개체 값에만 액세스하려는 경우 쿼리에서 해당 개체참조를 사용할 수 있습니다. 예를 들어:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

또한 개체의 멤버에 대한 `dimensions` 액세스도 제한됩니다. JSON 필드의 다른 멤버와 JSON 개체의 내부 값에 액세스하려면 다음 예제와 같은 쿼리를 사용할 수 있습니다.

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage 및 BlobStorage []\*모두 전체 개체를 참조합니다. 그러나 FROM 절에 경로가 있는 경우 BlobStorage[].path를\*사용해야 합니다.

<a id="sys-split" />

## <a name="syssplit"></a>Sys.Split

이것은 CSV 형식의 데이터에대해서만 사용할 수 있는 SELECT 문의 특별한 형태입니다.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

CSV 데이터 레코드를 일괄 처리하려는 경우 이 문을 사용합니다. 이렇게 하면 모든 레코드를 한 번에 다운로드할 필요 없이 레코드를 병렬로 처리할 수 있습니다. 이 문은 CSV 파일에서 레코드를 반환 하지 않습니다. 대신 일괄 처리 크기의 컬렉션을 반환합니다. 그런 다음 각 일괄 처리 크기를 사용하여 데이터 레코드 일괄 처리를 검색할 수 있습니다. 

*split_size* 매개 변수를 사용하여 각 일괄 처리에 포함할 바이트 수를 지정합니다. 예를 들어 한 번에 10MB의 데이터만 처리하려는 경우 10MB가 10,485,760바이트와 같기 `SELECT sys.split(10485760)FROM BlobStorage` 때문에 다음과 같이 설명합니다. 각 일괄 처리에는 해당 10MB에 들어갈 수 있는 만큼의 레코드가 포함됩니다. 

대부분의 경우 각 일괄 처리의 크기는 지정한 수보다 약간 높습니다. 일괄 처리에 부분 레코드를 포함할 수 없기 때문입니다. 일괄 처리의 마지막 레코드가 임계값이 끝나기 전에 시작되면 전체 레코드를 포함할 수 있도록 일괄 처리가 더 커집니다. 마지막 일괄 처리의 크기는 지정한 크기보다 작을 수 있습니다.

>[!NOTE]
> split_size 10MB(10485760)이상이어야 합니다.

## <a name="see-also"></a>참조

- [Azure 데이터 레이크 저장소 쿼리 가속(미리 보기)](data-lake-storage-query-acceleration.md)
- [Azure Data Lake Storage 쿼리 가속(미리 보기)을 사용하여 데이터 필터링](data-lake-storage-query-acceleration-how-to.md)

