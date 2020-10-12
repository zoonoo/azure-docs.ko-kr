---
title: 쿼리 가속 SQL 언어 참조
titleSuffix: Azure Storage
description: 쿼리 가속 sql 구문을 사용 하는 방법에 대해 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: affddf7367f58107106ae07a07b8baedac73e251
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659566"
---
# <a name="query-acceleration-sql-language-reference"></a>쿼리 가속 SQL 언어 참조

쿼리 가속은 blob 내용에 대해 쿼리를 표현 하기 위해 ANSI SQL과 같은 언어를 지원 합니다.  쿼리 가속 SQL 언어는 ANSI SQL의 하위 집합이 며 지원 되는 데이터 형식, 연산자 등의 제한 된 집합을 포함 하지만, ANSI SQL에서 확장 되어 JSON과 같은 계층적 반구조적 데이터 형식에 대 한 쿼리를 지원 합니다. 

## <a name="select-syntax"></a>SELECT 구문

쿼리 가속에서 지원 되는 유일한 SQL 문은 SELECT 문입니다. 이 예에서는 식이 true를 반환 하는 모든 행을 반환 합니다.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

CSV 형식 데이터의 경우 *테이블* 은 이어야 합니다 `BlobStorage` .  즉, REST 호출에 지정 된 blob에 대해 쿼리가 실행 됩니다.
JSON 형식 데이터의 경우 *테이블* 은 "테이블 설명자"입니다.   이 문서의 [테이블 설명자](#table-descriptors) 섹션을 참조 하세요.

다음 예에서는 WHERE *식이* true를 반환 하는 각 행에 대해이 문은 각 프로젝션 식의 평가에서 만들어진 새 행을 반환 합니다.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

다음 예에서는 *식* 에서 true를 반환 하는 각 행에 대 한 집계 계산 (예: 특정 열의 평균 값)을 반환 합니다. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

다음 예에서는 CSV 형식 blob을 분할 하기 위한 적절 한 오프셋을 반환 합니다.  이 문서의 [Sys. 분할](#sys-split) 섹션을 참조 하세요.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>데이터 형식

|데이터 형식|Description|
|---------|-------------------------------------------|
|INT      |부호 있는 64비트 정수.                     |
|FLOAT    |64 비트 ("배정밀도") 부동 소수점입니다.|
|STRING   |가변 길이 유니코드 문자열입니다.            |
|timestamp|지정 시간입니다.                           |
|BOOLEAN  |True 또는 False입니다.                             |

CSV 형식 데이터에서 값을 읽는 경우 모든 값을 문자열로 읽습니다.  캐스트 식을 사용 하 여 문자열 값을 다른 형식으로 변환할 수 있습니다.  컨텍스트에 따라 값이 암시적으로 다른 형식으로 캐스팅 될 수 있습니다. 자세한 내용은 [데이터 형식 우선 순위 (transact-sql)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql)를 참조 하세요.

## <a name="expressions"></a>식

### <a name="referencing-fields"></a>참조 필드

JSON 형식 데이터의 경우 또는 머리글 행이 있는 CSV 형식 데이터의 경우 필드를 이름으로 참조할 수 있습니다.  필드 이름은 따옴표 또는 따옴표 붙지 않을 수 있습니다. 따옴표로 묶은 필드 이름은 큰따옴표 문자 (")로 묶여 있으며 공백을 포함할 수 있으며 대/소문자를 구분 합니다.  따옴표 붙지 않은 필드 이름은 대/소문자를 구분 하지 않으며 특수 문자를 포함할 수 없습니다.

CSV 형식 데이터의 경우 필드를 서 수로 참조 하 고, 앞에 밑줄 (_) 문자를 붙일 수도 있습니다.  예를 들어 첫 번째 필드는 _1으로 참조 되거나 11 번째 필드는 _11로 참조 될 수 있습니다.  서 수로 필드를 참조 하는 것은 머리글 행을 포함 하지 않는 CSV 형식의 데이터에 유용 합니다 .이 경우 특정 필드를 참조 하는 유일한 방법은 서 수입니다.

### <a name="operators"></a>연산자

지원 되는 표준 SQL 연산자는 다음과 같습니다.

|연산자|Description|
|--|--|
|[=](https://docs.microsoft.com/sql/t-sql/language-elements/equals-transact-sql)    |두 식이 같은지 비교합니다(비교 연산자).|
|[!=](https://docs.microsoft.com/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |식이 다른 식과 같지 않은지 테스트합니다(비교 연산자).|
|[<>](https://docs.microsoft.com/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |같지 않음의 두 식 (비교 연산자)을 비교 합니다.|
|[<](https://docs.microsoft.com/sql/t-sql/language-elements/less-than-transact-sql)    |두 식이 보다 작은 지 비교 합니다 (비교 연산자).|
|[<=](https://docs.microsoft.com/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |두 식이 보다 작거나 같은지 비교 합니다 (비교 연산자).|
|[>](https://docs.microsoft.com/sql/t-sql/language-elements/greater-than-transact-sql)    |두 식이 보다 큰지 비교 합니다 (비교 연산자). |
|[>=](https://docs.microsoft.com/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |한 식이 다른 한 식보다 크거나 같은지 비교합니다(비교 연산자).|
|[+](https://docs.microsoft.com/sql/t-sql/language-elements/add-transact-sql)    |두 숫자를 더합니다. 이 더하기 산술 연산자를 사용하여 날짜에 일 수를 더할 수도 있습니다.|
|[-](https://docs.microsoft.com/sql/t-sql/language-elements/subtract-transact-sql)    |두 숫자를 빼는 빼기 산술 연산자입니다. |
|[/](https://docs.microsoft.com/sql/t-sql/language-elements/divide-transact-sql)    |숫자를 다른 숫자로 나눕니다(산술 나누기 연산자).|
|[*](https://docs.microsoft.com/sql/t-sql/language-elements/multiply-transact-sql)    |두 식을 곱합니다(산술 곱하기 연산자).|
|[%](https://docs.microsoft.com/sql/t-sql/language-elements/modulo-transact-sql)    |한 숫자를 다른 숫자로 나눈 나머지를 반환합니다.|
|[및](https://docs.microsoft.com/sql/t-sql/language-elements/bitwise-and-transact-sql)    |두 정수 값 간에 비트 논리 AND 연산을 수행합니다.|
|[OR](https://docs.microsoft.com/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Transact-sql 문 내의 이진 식으로 변환 되는 두 개의 지정 된 정수 값 간에 비트 논리적 OR 연산을 수행 합니다.|
|[다음이 아님](https://docs.microsoft.com/sql/t-sql/language-elements/not-transact-sql)    |부울 입력을 부정합니다.|
|[CAST](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)    |한 데이터 형식의 식을 다른 데이터 형식으로 변환합니다.|
|[BETWEEN](https://docs.microsoft.com/sql/t-sql/language-elements/between-transact-sql)    |테스트할 범위를 지정합니다.|
|[IN](https://docs.microsoft.com/sql/t-sql/language-elements/in-transact-sql)    |지정된 값과 일치하는 값이 하위 쿼리 또는 목록 내에 있는지 확인합니다.|
|[NULLIF](https://docs.microsoft.com/sql/t-sql/language-elements/nullif-transact-sql)    |지정된 두 식이 같으면 Null 값을 반환합니다.|
|[COALESCE](https://docs.microsoft.com/sql/t-sql/language-elements/coalesce-transact-sql)    |인수를 순서 대로 평가 하 고 처음에 NULL로 계산 되지 않는 첫 번째 식의 현재 값을 반환 합니다.|

연산자의 왼쪽과 오른쪽에 있는 데이터 형식이 다른 경우 자동 변환은 [데이터 형식 우선 순위 (transact-sql)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql)에 지정 된 규칙에 따라 수행 됩니다.

쿼리 가속 SQL 언어는 해당 문서에서 설명 하는 데이터 형식의 매우 작은 하위 집합만 지원 합니다.  이 문서의 [데이터 형식](#data-types) 섹션을 참조 하세요.

### <a name="casts"></a>캐스팅

쿼리 가속 SQL 언어는 [데이터 형식 변환 (데이터베이스 엔진)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine)의 규칙에 따라 CAST 연산자를 지원 합니다.  

쿼리 가속 SQL 언어는 해당 문서에서 설명 하는 데이터 형식의 작은 하위 집합만 지원 합니다.  이 문서의 [데이터 형식](#data-types) 섹션을 참조 하세요.

### <a name="string-functions"></a>문자열 함수

쿼리 가속 SQL 언어는 다음과 같은 표준 SQL 문자열 함수를 지원 합니다.

|함수|Description|
|--|--|
|CHAR_LENGTH    | 문자열 식이 문자 데이터 형식이 면 문자열 식의 문자 길이를 반환 합니다. 그렇지 않으면 문자열 식의 길이 (바이트)를 반환 합니다 (비트 수를 8로 나눈 값 보다 작은 정수). 이 함수는 CHARACTER_LENGTH 함수와 동일 합니다.|
|CHARACTER_LENGTH    |문자열 식이 문자 데이터 형식이 면 문자열 식의 문자 길이를 반환 합니다. 그렇지 않으면 문자열 식의 길이 (바이트)를 반환 합니다 (비트 수를 8로 나눈 값 보다 작은 정수). 이 함수는 CHAR_LENGTH 함수와 동일 합니다.|
|[LOWER](https://docs.microsoft.com/sql/t-sql/functions/lower-transact-sql)    |대문자 데이터를 소문자 데이터로 변환한 후에 문자 식을 반환합니다.|
|[UPPER](https://docs.microsoft.com/sql/t-sql/functions/upper-transact-sql)    |소문자 데이터를 대문자로 변환한 문자 식을 반환합니다.|
|[SUBSTRING](https://docs.microsoft.com/sql/t-sql/functions/substring-transact-sql)    |SQL Server에서 문자, 이진, 텍스트 또는 이미지 식의 일부를 반환 합니다.|
|[TRIM](https://docs.microsoft.com/sql/t-sql/functions/trim-transact-sql)    |문자열의 시작과 끝에서 공백 문자 char (32) 또는 기타 지정 된 문자를 제거 합니다.|
|LEADING    |Description|
|TRAILING    |Description|

다음은 몇 가지 예입니다.

|기능|예제|결과|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>날짜 함수

지원 되는 표준 SQL 날짜 함수는 다음과 같습니다.

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

현재 [표준 IS08601의 모든 날짜 형식을](https://www.w3.org/TR/NOTE-datetime)변환 합니다. 

#### <a name="date_add-function"></a>DATE_ADD 함수

쿼리 가속 SQL 언어는 함수에 대해 연도, 월, 일, 시, 분, 초를 지원 ``DATE_ADD`` 합니다.

예제:

' ' sql DATE_ADD (datepart, quantity, timestamp) DATE_ADD (' minute ', 1, CAST (' 2017-01-02T03:04:05.006 Z ' 타임 스탬프)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRACT 함수

함수에 대해 지원 되는 날짜 부분이 아닌 추출의 경우 ``DATE_ADD`` 쿼리 가속 SQL 언어는 날짜 부분으로 timezone_hour 및 timezone_minute를 지원 합니다.

예제:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING 함수

예제:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

다음 표에서는 함수의 출력 형식을 지정 하는 데 사용할 수 있는 문자열에 대해 설명 합니다 ``TO_STRING`` .

|형식 문자열    |출력                               |
|-----------------|-------------------------------------|
|yy               |2 자리 형식의 연도-1999을 ' 99 '로|
|y                |4 자리 형식의 연도               |
|yyyy             |4 자리 형식의 연도               |
|M                |연간 월 – 1                    |
|MM               |0으로 채워진 월 0 – 01               |
|MMM              |약어. 연간 월-1 월            |
|MMMM             |전체 월 – 월                      |
|일                |월간 일자 (1-31)                  |
|dd               |0으로 채워진 월 0 일 (01-31)     |
|a                |AM 또는 PM                             |
|h                |하루 중 시간 (1-12)                   |
|hh               |0으로 채워진 시간 od 일 (01-12)     |
|H                |하루 중 시간 (0-23)                   |
|HH               |0으로 채워진 시간 (00-23)      |
|분                |시간의 분 (0-59)                |
|MM               |0으로 채워진 분 (00-59)           |
|초                |분의 초 (0-59)             |
|ss               |0으로 채워진 초 (00-59)          |
|S                |초의 비율 (0.1-0.9)        |
|SS               |초의 소수 부분 (0.01-0.99)      |
|SSS              |초의 소수 부분 (0.999)    |
|X                |오프셋 (시간)                      |
|XX 또는 XXXX       |시간 및 분 단위로 오프셋 (+ 0430)  |
|XXX 또는 XXXXX     |시간 및 분 단위로 오프셋 (-07:00) |
|x                |오프셋 (시간) (7)                  |
|xx 또는 xxxx       |오프셋 (시간 및 분) (+ 0530)    |
|Xxx 또는 xxxxx     |오프셋 (시간 및 분) (+ 05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP 함수

IS08601 형식만 지원 됩니다.

예제:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> 함수를 사용 하 여 ``UTCNOW`` 시스템 시간을 가져올 수도 있습니다.


## <a name="aggregate-expressions"></a>집계 식

SELECT 문에는 하나 이상의 프로젝션 식 또는 단일 집계 식이 포함 될 수 있습니다.  다음 집계 식이 지원 됩니다.

|식|Description|
|--|--|
|[COUNT ( \* )](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |조건자 식과 일치 하는 레코드 수를 반환 합니다.|
|[COUNT (식)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |식이 null이 아닌 레코드 수를 반환 합니다.|
|[AVERAGE (expression)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |식의 null이 아닌 값에 대 한 평균을 반환 합니다.|
|[MIN (expression)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |식의 null이 아닌 최소 값을 반환 합니다.|
|[MAX (식](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |Expression의 null이 아닌 최대 값을 반환 합니다.|
|[합계 (식)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |식에서 null이 아닌 모든 값의 합을 반환 합니다.|

### <a name="missing"></a>누락

``IS MISSING``연산자는 쿼리 가속 SQL 언어가 지 원하는 유일한 비표준입니다.  JSON 데이터의 경우 특정 입력 레코드에서 필드가 누락 되 면 식 필드가 ``IS MISSING`` 부울 값 true로 평가 됩니다.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>테이블 설명자

CSV 데이터의 경우 테이블 이름은 항상 `BlobStorage` 입니다.  예를 들면 다음과 같습니다.

```sql
SELECT * FROM BlobStorage
```

JSON 데이터의 경우 추가 옵션을 사용할 수 있습니다.

```sql
SELECT * FROM BlobStorage[*].path
```

이를 통해 JSON 데이터의 하위 집합에 대 한 쿼리를 수행할 수 있습니다.

JSON 쿼리의 경우 FROM 절의 일부에서 경로를 언급할 수 있습니다. 이러한 경로는 JSON 데이터의 하위 집합을 구문 분석 하는 데 도움이 됩니다. 이러한 경로는 JSON 배열 및 개체 값을 참조할 수 있습니다.

이를 더 자세히 이해 하는 예를 살펴보겠습니다.

이 샘플 데이터는 다음과 같습니다.

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

위의 데이터에서 JSON 개체에만 관심이 있을 수 있습니다 `warehouses` . `warehouses`개체는 JSON 배열 형식 이므로 from 절에서이를 언급할 수 있습니다. 예제 쿼리는 다음과 같을 수 있습니다.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

쿼리가 모든 필드를 가져오지만 위도만 선택 합니다.

JSON 개체 값에만 액세스 하려는 경우 `dimensions` 쿼리에서 해당 개체에 대 한 참조를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

```sql
SELECT length FROM BlobStorage[*].dimensions
```

또한 개체의 멤버에 대 한 액세스를 제한 `dimensions` 합니다. Json 필드의 다른 멤버 및 JSON 개체의 내부 값에 액세스 하려는 경우 다음 예제와 같이 쿼리를 사용할 수 있습니다.

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage 및 BlobStorage [ \* ] 모두는 전체 개체를 참조 합니다. 그러나 FROM 절에 경로가 있는 경우 BlobStorage [ \* ]. 경로를 사용 해야 합니다.

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys. 분할

이는 CSV 형식의 데이터에만 사용할 수 있는 특수 한 형태의 SELECT 문입니다.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

CSV 데이터 레코드를 다운로드 한 다음 일괄 처리로 처리 하려는 경우에이 문을 사용 합니다. 이렇게 하면 한 번에 모든 레코드를 다운로드 하는 대신 레코드를 병렬로 처리할 수 있습니다. 이 문은 CSV 파일에서 레코드를 반환 하지 않습니다. 대신 일괄 처리 크기의 컬렉션을 반환 합니다. 그런 다음 각 일괄 처리 크기를 사용 하 여 데이터 레코드의 일괄 처리를 검색할 수 있습니다. 

*Split_size* 매개 변수를 사용 하 여 각 일괄 처리에 포함 시킬 바이트 수를 지정 합니다. 예를 들어 한 번에 10mb 데이터만 처리 하려는 경우는 다음과 같습니다. `SELECT sys.split(10485760)FROM BlobStorage` 10mb는 10485760 바이트와 동일 하기 때문입니다. 각 일괄 처리에는 해당 10mb에 들어갈 수 있는 레코드 수가 포함 됩니다. 

대부분의 경우 각 일괄 처리의 크기는 지정 된 숫자 보다 약간 더 높습니다. 일괄 처리에 부분 레코드를 포함할 수 없기 때문입니다. 일괄 처리의 마지막 레코드가 임계값의 끝 이전에 시작 하는 경우 일괄 처리는 전체 레코드를 포함할 수 있도록 커집니다. 마지막 일괄 처리의 크기는 지정한 크기 보다 작을 수 있습니다.

>[!NOTE]
> Split_size는 10mb (10485760) 이상 이어야 합니다.

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage 쿼리 가속](data-lake-storage-query-acceleration.md)
- [Azure Data Lake Storage 쿼리 가속을 사용 하 여 데이터 필터링](data-lake-storage-query-acceleration-how-to.md)

