---
title: Azure Data Factory의 데이터 흐름 매핑 기능에서 식 함수
description: 매핑 데이터 흐름에서 식 함수에 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 6b4df70114dd481566ae1a666c91c1c9b5bad86f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717006"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>매핑 데이터 흐름에서 데이터 변환 식 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>식 함수

Data Factory에서 데이터 변환을 구성 하려면 데이터 흐름 매핑 기능 식 언어를 사용 합니다.

*********************************
<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
이 식은 숫자의 쌍의 양의 모듈러스를 표시합니다.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 역 코사인을 계산 합니다.
* ``acos(1) -> 0.0``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
이 식은 문자열 또는 숫자의 쌍을 추가 합니다. 날짜 일 수를 추가합니다. 다른 유사한 형식의 배열을 하나 추가 합니다. 
* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``

합니다 **추가** 연산자와 동일 합니다 **+** 연산자입니다.
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
이 식은 날짜 또는 타임 스탬프 일을 추가합니다. 
* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``

합니다 **addDays** 연산자와 동일 합니다 **+** 날짜에 대 한 연산자입니다.
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
이 식은 날짜 또는 타임 스탬프 개월을 추가합니다.
* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
이 논리적 **고** 연산자입니다. 이 동일 합니다 **&&** 연산자입니다.
* ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 역 사인 값을 계산 합니다.
* ``asin(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 역 탄젠트 값을 계산 합니다.
* ``atan(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 평면 양의 x-축 좌표를 제공 하는 시점 사이의 라디안에서 각도 반환 합니다.
* ``atan2(0, 0) -> 0.0``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
이 식은 열 값의 평균을 가져옵니다.
* ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
이 식은 조건에 따라 열 값의 평균을 가져옵니다.
* ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>byName</code>
==============================
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
이 식은 스트림의 이름으로 열 값을 선택합니다. 여러 일치 항목이 있으면 첫 번째 일치 항목이 반환 됩니다. 일치 하는 경우 식에 NULL 값을 반환 합니다. 반환된 된 값에는 형식 변환 함수 (TO_DATE, TO_STRING...) 중 하나에서 형식 변환 해야 합니다. 디자인 타임에 알려진 된 열 이름은 이름별으로만 해결 해야 합니다. 계산 된 입력은 지원 되지 않습니다 하지만 매개 변수 대체를 사용할 수 있습니다.

* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
*********************************
<code>byPosition</code>
==============================
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
이 식은 스트림에 상대 위치 (1부터 시작)를 기준으로 열 값을 선택합니다. 위치 범위를 벗어난 경우 NULL 값을 반환 합니다. 반환된 된 값에는 형식 변환 함수 (TO_DATE, TO_STRING, 및 등) 중 하나에서 형식을 변환 해야 합니다. 계산 된 입력은 지원 되지 않습니다 하지만 매개 변수 대체를 사용할 수 있습니다.

* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
이 식은 교대로 반복 되는 조건에 따라 하나의 값 또는 다른 적용 됩니다. 
* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``

입력 수가 짝수 이면, 다른 값은 마지막으로 조건에 대해 NULL입니다.
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 숫자의 제곱근을 계산 합니다.
* ``cbrt(8) -> 2.0``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
이 식은 수보다 작은 없는 가장 작은 정수를 반환 합니다.
* ``ceil(-0.1) -> 0``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
이 식은 가변 개수의 문자열을 연결합니다. **concat** 연산자와 동일 합니다 **+** 문자열 연산자입니다.
* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
이 식에는 가변 개수의 문자열 구분 기호를 함께 연결합니다. 첫 번째 매개 변수는 구분 기호입니다.
* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식을 코사인 값을 계산합니다.
* ``cos(10) -> -0.83907152907``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 값의 쌍 곡 코사인을 계산 합니다.
* ``cosh(0) -> 1.0``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
이 식은 집계 값 개수를 가져옵니다. 선택적 열 또는 열은 NULL을 지정 하는 경우 개수에는 값은 무시 됩니다.
* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
이 식은 집계 열 집합의 고유 값 개수를 가져옵니다.
* ``countDistinct(custId, custName) -> 60``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
이 식은 조건에 따라 집계 값 개수를 가져옵니다. 선택적 열이 NULL을 지정 하는 경우 개수에는 값은 무시 됩니다.
* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 두 열 사이의 모집단 공변성 (covariance)을 가져옵니다.
* ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 두 열의 모집단 공변성 (covariance)을 가져옵니다.
* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 두 열의 샘플 공변성 (covariance)을 가져옵니다.
* ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 두 열의 샘플 공변성 (covariance)을 가져옵니다.
* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
이 식은 계산 열 값만 0(256) 수 비트 길이 지정 된 기본 데이터 형식에 다양 한 집합의 CRC32 해시 224, 256, 384 512입니다. 사용할 수는 **crc32** 행에 대 한 지문을 계산 하는 연산자입니다.
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
이 함수는 파티션에 모든 값을 기준으로 값의 위치를 계산합니다. 결과 이전 또는 창 파티션에서 행의 총 수로 나눈 파티션의 순서에서 현재 행에 동일한 행의 수입니다. 동률 값 순서에서 같은 위치를 반환합니다.
* ``cumeDist() -> 1``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
이 식은 작업 실행을 시작 하는 경우 현재 날짜를 가져옵니다. 
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``

선택적 표준 시간대 형식으로 전달할 수 있습니다 `'GMT'`, `'PST'`를 `'UTC'`, `'America/Cayman'`합니다. 현지 표준 시간대에는 기본값입니다.
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
이 식은 현지 표준 시간대를 사용 하 여 실행 하는 작업이 시작 되 면 현재 타임 스탬프를 가져옵니다.
* ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
이 식은 현재 타임 스탬프를 UTC로 가져옵니다. 
* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``

선택적 표준 시간대 형식으로 전달할 수 있습니다 `'GMT'`, `'PST'`를 `'UTC'`, `'America/Cayman'`합니다. 현지 표준 시간대에는 기본값입니다.
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
날짜를 지정이 식을 해당 월의 일을 가져옵니다.
* ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
날짜를 지정이 식을 주의 일을 가져옵니다. 
* ``dayOfWeek(toDate('2018-06-08')) -> 7``

일 값은 다음과 같습니다.
- 1-일요일
- 2-월요일 
- ...
- 7-토요일
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
이 식은 날짜 들어 연간 일자를 가져옵니다.
* ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식에 라디안을 각도로 변환합니다.
* ``degrees(3.141592653589793) -> 180``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
이 식은 값 그룹에 있는 값의 순위를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격이 생성 하지 않습니다. 
* ``denseRank(salesQtr, salesAmt) -> 1``

합니다 **denseRank** 연산자 데이터가 정렬 되지 경우에 작동 합니다. 값의 변경 내용을 찾습니다.
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
이 식은 숫자의 쌍을 나눕니다. 합니다 **나눌** 연산자와 동일 합니다 **/** 연산자.
* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
이 식은 문자열을 제공 된 문자열로 끝나는지 여부를 확인 합니다.
* ``endsWith('great', 'eat') -> true``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 equals 연산자입니다. 와 동일 합니다 **==** 연산자입니다.
* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
합니다 **equalsIgnoreCase** 연산자는 대/소문자를 구분 하지 않는 비교 equals 연산자입니다. 와 동일 합니다 **<=>** 연산자입니다.
* ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
이 식은 숫자의 계승값을 계산 합니다.
* ``factorial(5) -> 120``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
이 표현식은 항상 false 값을 반환 합니다. 
* ``isDiscounted == false()``
* ``isDiscounted() == false``

사용 된 `syntax(false())` 함수는 열 이름이 *false*.
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
이 식은 열 그룹의 첫 번째 값을 가져옵니다. 두 번째 매개 변수를 생략 하면 `ignoreNulls`를 false로 간주 됩니다.
* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
이 식은 수보다 크지 않은 가장 큰 정수를 반환 합니다.
* ``floor(-0.1) -> -1``
*********************************
<code>fromUTC</code>
==============================
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
이 식은 타임 스탬프 UTC에서 변환합니다. 형식의 표준 시간대를 선택적으로 전달할 수 있습니다 `'GMT'`, `'PST'`를 `'UTC'`, `'America/Cayman'`합니다. 기본값은 현재 표준 시간대입니다.

* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
이것은 비교 **큰** 연산자입니다. 와 동일 합니다 **>** 연산자입니다.
* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 또는 같음 연산자입니다. 이 연산자는 동일 합니다 **>=** 연산자입니다.
* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
이 식은 입력으로 값의 목록에서 가장 큰 값을 반환합니다. 모든 입력이 null 인 경우에 NULL을 반환 합니다.
* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
이 식에는 타임 스탬프의 시간 값을 가져옵니다. 
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``

선택적 표준 시간대 형식으로 전달할 수 있습니다 `'GMT'`, `'PST'`를 `'UTC'`, `'America/Cayman'`합니다. 현지 표준 시간대에는 기본값입니다.
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
이 식은 조건에 따라 하나의 값 또는 다른 적용 됩니다. 다른 값을 지정 하지 않으면 NULL을 간주 됩니다. 값이 모두 호환 되어야 합니다 (숫자 또는 예를 들어 문자열)입니다.
* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
이 식은 배열에 항목을 검사합니다.
* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
이 식에 모든 단어의 첫 글자를 대문자로 변환합니다. 단어 공백을 퇴사 하 여 식별 됩니다.
* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
이 식은 문자열 내에서 부분 문자열의 (1부터 시작) 위치를 찾습니다. 위치를 찾을 수 없으면 0이 반환 됩니다. 
* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
이 식은 삭제 표시 된 행이 있는지 확인 합니다. 
* ``isDelete() -> true``
* ``isDelete(1) -> false``

둘 이상의 입력된 스트림의 사용 하 여 변환 하는 경우 스트림의 인덱스 (1부터 시작)를 전달할 수 있습니다. 스트림 인덱스에 대 한 기본값은 1입니다.
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
이 식은 오류로 표시 하는 행이 있는지 확인 합니다.
* ``isError() -> true``
* ``isError(1) -> false``

둘 이상의 입력된 스트림의 사용 하 여 변환 하는 경우 스트림의 인덱스 (1부터 시작)를 전달할 수 있습니다. 스트림 인덱스에 대 한 기본값은 1입니다.
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
이 식은 무시 하도록 표시 하는 행이 있는지 확인 합니다.
* ``isIgnore() -> true``
* ``isIgnore(1) -> false``

둘 이상의 입력된 스트림의 사용 하 여 변환 하는 경우 스트림의 인덱스 (1부터 시작)를 전달할 수 있습니다. 스트림 인덱스에 대 한 기본값은 1입니다.
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
이 식은 행 삽입에 대 한 표시를 확인 합니다. 
* ``isInsert() -> true``
* ``isInsert(1) -> false``

둘 이상의 입력된 스트림의 사용 하 여 변환 하는 경우 스트림의 인덱스 (1부터 시작)를 전달할 수 있습니다. 스트림 인덱스에 대 한 기본값은 1입니다.
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
이 식 조회 시 일치 하는 행이 있는지 확인 합니다. 
* ``isMatch() -> true``
* ``isMatch(1) -> false``

둘 이상의 입력된 스트림의 사용 하 여 변환 하는 경우 스트림의 인덱스 (1부터 시작)를 전달할 수 있습니다. 스트림 인덱스에 대 한 기본값은 1입니다.
*********************************
<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
이 식은 NULL 값에 대 한 확인합니다.
* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
이 식은 업데이트에 대 한 표시 하는 행이 있는지 확인 합니다. 
* ``isUpdate() -> true``
* ``isUpdate(1) -> false``

둘 이상의 입력된 스트림의 사용 하 여 변환 하는 경우 스트림의 인덱스 (1부터 시작)를 전달할 수 있습니다. 스트림 인덱스에 대 한 기본값은 1입니다.
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 열 첨도를 가져옵니다.
* ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 열의 첨도를 가져옵니다.
* ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
이 식을 평가 하는 첫 번째 매개 변수의 값을 가져옵니다 *n* 행에서 현재 행 앞입니다. 두 번째 매개 변수는 거꾸로 확인할 행의 수입니다. 기본값은 1입니다. 많은 행이 없으면 기본값을 지정 하지 않으면 NULL 값이 반환 됩니다.
* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
이 식은 열 그룹의 마지막 값을 가져옵니다. 두 번째 매개 변수를 생략 `ignoreNulls`, 식이 반환 `false`합니다.
* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
이 식 날짜 지정 달의 마지막 날짜를 가져옵니다.
* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
이 식을 평가 하는 첫 번째 매개 변수의 값을 가져옵니다 *n* 현재 행 뒤 행입니다. 두 번째 매개 변수는 앞으로 확인할 행의 수입니다. 기본값은 1입니다. 많은 행이 없으면 기본값을 지정 하지 않으면 NULL 값이 반환 됩니다.
* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
비교 중 더 작은 값 보다-크거나 같음 연산자입니다. 와 동일 합니다 **<=** 연산자입니다.
* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>left</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
이 식은 인덱스 1에서 시작 하는 부분 문자열을 추출 하 고 문자 수를 사용 합니다. 합니다 **왼쪽** 연산자와 같습니다 **SUBSTRING (str, 1, n)** 합니다.
* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
이 식은 문자열의 길이 반환합니다.
* ``length('kiddo') -> 5``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
이것은 낮은 비교-than 연산자입니다. 와 동일 합니다 **<** 연산자입니다.
* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 중 더 작은 값 보다-크거나 같음 연산자입니다. 와 동일 합니다 **<=** 연산자입니다.
* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
이 식은 두 문자열 간의 Levenshtein 거리를 가져옵니다.
* ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
이 식에서 문자열을 패턴으로는 문자 그대로 일치 하는 합니다. 
* ``like('icecream', 'ice%') -> true``

예외는 다음 특수 기호:  
* `_` 이 입력에서 하나의 문자를 찾습니다. 비슷합니다 `.` POSIX 정규식의 합니다.
* `%` 이 입력에 0 개 이상의 문자와 대응합니다. 이 기호는 비슷합니다 `.*` POSIX 정규식의 합니다.
* `''` 이스케이프 문자입니다. 특수 기호 또는 다른 이스케이프 문자 앞에 이스케이프 문자가 오면 다음 문자는 글자 그대로 일치됩니다. 다른 모든 문자를 이스케이프 처리할 수 없습니다.
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
이 식은 특정 위치에서 시작 하는 문자열에 부분 문자열의 (1부터 시작) 위치를 찾습니다. 위치를 생략 하면 문자열의 시작 부분에서 평가 시작 합니다. 위치를 찾을 수 없으면 0이 반환 됩니다. 
* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
이 식은 로그 값을 계산합니다. 사용 하는 경우 선택적인 기본 또는 Euler 숫자로 제공할 수 있습니다.
* ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 기본 10를 사용 하 여 로그 값을 계산 합니다.
* ``log10(100) -> 2``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
이 식은 소문자에서 문자열을 설정합니다.
* ``lower('GunChus') -> 'gunchus'``
*********************************
<code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
제공 된 안쪽 여백이 식 왼쪽 패드를 사용 하 여 문자열까지 문자열 특정 길이 도달 합니다. 문자열 길이 보다 크거나 같은 경우 없는 작업 (아무) 간주 합니다.
* ``lpad('great', 10, '-') -> '-----great'``
* ``lpad('great', 4, '-') -> 'great'``
* ``lpad('great', 8, '<>') -> '<><great'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
이 식은 선행 문자 문자열에 왼쪽을 삭제 합니다. 두 번째 매개 변수를 지정 하지 않으면 경우 식 공백을 삭제 합니다. 그렇지 않으면 두 번째 매개 변수를 지정 하는 문자를 삭제 합니다.
* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
이 식에는 열의 최대 값을 가져옵니다.
* ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
이 식은 조건에 따라 열의 최대값을 가져옵니다.
* ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
이 식은 다양 한 기본 데이터 형식의 열 집합의 MD5 다이제스트를 계산 합니다. 32 자리 16 진수 문자열을 반환 합니다. 
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``

사용할 수는 **md5** 행에 대 한 지문을 계산 하는 연산자입니다.
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
이 식에는 열 값의 평균을 가져옵니다. 합니다 **의미** 연산자 평균 동일
* ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
이 식은 조건에 따라 열 값의 평균을 가져옵니다. 합니다 **meanIf** 연산자와 같습니다 **avgIf**합니다.
* ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
이 식은 열의 최소값을 가져옵니다.
* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
이 식은 조건에 따라 열의 최소값을 가져옵니다.
* ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
이 식은 숫자를 뺍니다. 예를 들어 날짜의 일 수를 뺄 수 것입니다. 합니다 **빼기** 연산자와 동일 합니다 **-** 연산자입니다.
* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
이 식에는 타임 스탬프의 분 값을 가져옵니다. 
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``

선택적 표준 시간대 형식으로 전달할 수 있습니다 `'GMT'`, `'PST'`를 `'UTC'`, `'America/Cayman'`합니다. 현지 표준 시간대에는 기본값입니다.
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
합니다 **mod** 연산자는 숫자의 모듈러스 쌍을 표시 합니다. 이 동일 합니다 **%** 연산자입니다.
* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
이 식은 date 또는 timestamp의 월 값을 가져옵니다.
* ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
이 식은 두 날짜 사이의 개월 수를 가져옵니다. 
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``

선택적 표준 시간대 형식으로 전달할 수 있습니다 `'GMT'`, `'PST'`를 `'UTC'`, `'America/Cayman'`합니다. 현지 표준 시간대에는 기본값입니다.
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
이 식은 숫자의 쌍을 곱합니다. 합니다 **곱하기** 연산자와 동일 합니다 * 연산자입니다.
* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
합니다 **nTile** 함수에 각 창 파티션에 대해 행을 나눕니다 *n* 1부터 최대 범위는 버킷 *n*합니다. 버킷 값 최대 1만 다릅니다. 파티션의 행 수가 버킷 수를 균등 하 게 나눌 하지 않습니다, 각 나머지 값 첫 번째 버킷에 버킷으로 배분 됩니다. 

* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``

합니다 **nTile** 함수 tertiles, 변 위치, 10 분 위 수, 및 기타 일반적인 요약 통계를 계산 해야 할 때 유용 합니다. 함수는 초기화 하는 동안 두 개의 변수를 계산합니다. 하나의 행이 일반 버킷에 추가 됩니다. 두 변수는 현재 파티션의 크기를 기반으로 합니다. 

을 계산 하는 동안 함수는 추적 현재 행 수, 현재 버킷 수를 및 행 번호는 버킷 (bucketThreshold) 변경 됩니다. 행 번호를 버킷 임계값에 도달 하면 버킷에 값을 1 씩 증가 합니다. 임계값을 늘립니다 버킷 크기를 더한 하나 추가 현재 버킷에 채워지는 경우.
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
이 식에 숫자를 부정합니다. 양수를 판명 음수로 또는 그 반대로 합니다.
* ``negate(13) -> -13``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
이 식은 다음 고유한 시퀀스를 반환합니다. 수는 파티션 내 에서만 연속 됩니다. 이 접두사로 `partitionId`합니다.
* ``nextSequence() -> 12313112``
*********************************
<code>normalize</code>
==============================
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
악센트 부호가 있는 유니코드 문자를 구분 하는 문자열 값을 정규화 합니다. * ``normalize('boys') -> 'boys'``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
합니다 **되지** 연산자는 논리 부정 연산자입니다.
* ``not(true) -> false``
* ``not(premium)``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
합니다 **notEquals** 연산자 비교 같지 않음 연산자입니다. 와 동일 합니다 **! =** 연산자입니다.
* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
이 식은 NULL 값을 반환합니다. 
* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``

함수를 사용 하 여 **syntax(null())** 열 이름이 *null*합니다. NULL 연산자를 사용 하는 모든 작업 결과가 NULL입니다.
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
합니다 **하거나** 연산자가 논리 **OR** 연산자. 와 동일 합니다 **||** 연산자입니다.
* ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
합니다 **pMod** 연산자는 숫자의 쌍의 양의 모듈러스를 표시 합니다.
* ``pmod(-20, 8) -> 4``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 다른 거듭제곱 한 숫자를 발생 시킵니다.
* ``power(10, 2) -> 100``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
이 식은 값 그룹에 있는 값의 순위를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격이 생성합니다. 
* ``rank(salesQtr, salesAmt) -> 1``

합니다 **순위** 연산자 데이터가 정렬 되지 경우에 작동 합니다. 값의 변경 내용을 찾습니다.
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
이 식은 지정 된 정규식 패턴 일치 하는 하위 문자열을 추출 합니다. 마지막 매개 변수 일치 그룹을 식별 합니다. 마지막 매개 변수를 생략 하면 기본값은 1입니다. 
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``

사용 하 여 `<regex>`(역따옴표)를 이스케이프 하지 않고 문자열을 일치 하도록 합니다.
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
이 식은 문자열에 지정 된 정규식 패턴 일치 하는지 여부를 확인 합니다. 
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``

사용 하 여 `<regex>`(역따옴표)를 이스케이프 하지 않고 문자열을 일치 하도록 합니다.
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
이 식은 지정된 된 문자열의 다른 부분 문자열을 사용 하 여 정규식 패턴의 모든 항목을 바꿉니다.
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``

사용 하 여 `<regex>`(역따옴표)를 이스케이프 하지 않고 문자열을 일치 하도록 합니다.
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
이 식은 regex에 따라 구분 기호를 기반으로 문자열을 분할 합니다. 문자열의 배열을 반환합니다.
* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
이 식은 지정된 된 문자열의 다른 부분 문자열을 사용 하 여 부분 문자열의 모든 항목을 바꿉니다.
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
이 식은 문자열을 반대로 바꿉니다.
* ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
이 식은 오른쪽의 문자 수 만큼의 부분 문자열을 추출합니다. 
* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``

합니다 **오른쪽** 함수는 부분 문자열 (str LENGTH(str)-n, n)와 동일 합니다.
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
이 식은 문자열에 지정 된 정규식 패턴 일치 하는지 여부를 확인 합니다.
* ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
이 식은 숫자를 반올림 선택적 확장을 및 선택적 반올림 모드를 지정 합니다. 소수 자릿수를 생략할 경우 기본값은 0입니다. 모드를 생략할 경우 기본값은 ROUND_HALF_UP(5) 합니다. 

* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``

다음은 반올림 하는 것에 대 한 값입니다.
* 1 - ROUND_UP
* 2 - ROUND_DOWN
* 3-ROUND_CEILING
* 4 - ROUND_FLOOR
* 5 - ROUND_HALF_UP
* 6 - ROUND_HALF_DOWN
* 7 - ROUND_HALF_EVEN
* 8 - ROUND_UNNECESSARY

*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
이 식은 1부터 시작 하는 창에서 행에 대 한 번호 매기기 순차 행을 할당 합니다.
* ``rowNumber() -> 1``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
이 식 오른쪽 채움 문자열까지 제공 된 안쪽 여백 문자열에 특정 길이 도달합니다. 문자열 길이 보다 크거나 같은 경우 없는 작업 (아무) 간주 합니다.
* ``rpad('great', 10, '-') -> 'great-----'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
*********************************
<code>rtrim</code>rtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
이 식은 선행 문자 문자열에 오른쪽을 삭제 합니다. 두 번째 매개 변수를 지정 하지 않으면 식 공백을 삭제 합니다. 그렇지 않으면 두 번째 매개 변수를 지정 하는 모든 문자를 삭제 합니다.
* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
이 식에는 날짜의 두 번째 값을 가져옵니다. 
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``

선택적 표준 시간대 형식으로 전달할 수 있습니다 `'GMT'`, `'PST'`를 `'UTC'`, `'America/Cayman'`합니다. 현지 표준 시간대에는 기본값입니다.
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
이 식은 다양 한 기본 데이터 형식의 열 집합의 sha-1 다이제스트를 계산 합니다. 40 자리 16 진수 문자열로 반환합니다. 
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``

사용할 수 있습니다 `sha1` 행에 대 한 지문을 계산 합니다.
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
이 식은 계산 열 값만 0(256) 수 비트 길이 지정 된 기본 데이터 형식에 다양 한 집합의 sha-2 다이제스트 224, 256, 384 512입니다. 
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``

사용할 수 있습니다 `sha2` 행에 대 한 지문을 계산 합니다.
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식을 사인 값을 계산합니다.
* ``sin(2) -> 0.90929742682``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식의 쌍 곡 사인 값을 계산합니다.
* ``sinh(0) -> 0.0``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 열 왜곡도를 가져옵니다.
* ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 열의 왜곡도를 가져옵니다.
* ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
이 식은 위치에서 배열의 하위 집합을 추출합니다. 위치는 1을 기반으로 합니다. 길이 생략할 경우 기본값은 문자열의 끝입니다.
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
이 식은 문자열의 soundex 코드를 가져옵니다.
* ``soundex('genius') -> 'G520'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
이 식은 구분 기호를 기반으로 문자열을 분할 합니다. 문자열의 배열을 반환합니다.
* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 숫자의 제곱근을 계산 합니다.
* ``sqrt(9) -> 3``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
이 식은 문자열을 제공 된 문자열로 시작 하는지 여부를 확인 합니다.
* ``startsWith('great', 'gr') -> true``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 열의 표준 편차를 가져옵니다.
* ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 열의 표준 편차를 가져옵니다.
* ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 열의 모집단 표준 편차를 가져옵니다.
* ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 열의 모집단 표준 편차를 가져옵니다.
* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 열의 샘플 표준 편차를 가져옵니다.
* ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 열의 샘플 표준 편차를 가져옵니다.
* ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
이 식은 날짜의 월을 뺍니다. 
* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
합니다 **subDays** 연산자와 동일 합니다 **-** 날짜에 대 한 연산자입니다.
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
이 식은 날짜 또는 타임 스탬프에서 월을 뺍니다.
* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
이 식은 위치에서 특정 길이의 부분 문자열을 추출합니다. 위치는 1을 기반으로 합니다. 길이 생략할 경우 기본값은 문자열의 끝입니다.
* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
이 식은 숫자 열의 집계 합계를 가져옵니다.
* ``sum(col) -> value``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
이 식은 숫자 열의 고유 값의 집계 합계를 가져옵니다.
* ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
이 식은 조건에 따라, 숫자 열의 집계 합계를 가져옵니다. 모든 열에 조건을 만들 수 있습니다.
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
이 식은 조건에 따라 숫자 열의 집계 합계를 가져옵니다. 모든 열에 조건을 만들 수 있습니다.
* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식을 탄젠트 값을 계산합니다.
* ``tan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 쌍 곡 탄젠트 값을 계산 합니다.
* ``tanh(0) -> 0.0``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
이 식은 값으로 변환 `('t', 'true', 'y', 'yes', '1')` true로 합니다. 변환 `('f', 'false', 'n', 'no', '0')` 를 false로 합니다. 다른 값이 NULL을 반환합니다.
* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
선택적 날짜 형식 지정,이 식은 문자열을 날짜입니다. 모든 날짜 형식에 대 한 Java SimpleDateFormat를 가리킵니다. 
* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``

날짜 형식은 생략 하면 다음의 조합을 허용 됩니다. [yyyy, yyyy-[M] M, yyyy [d]-[M] M-d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *].
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
이 식은 10 진수 값으로 모든 숫자 또는 문자열을 변환합니다. 
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``

전체 자릿수 및 소수를 지정 하지 않으면, 기본값은 (10,2). 선택적 Java 10 진수 형식 변환 하는 데 사용할 수 있습니다.

*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
이 식은 double 값으로 모든 숫자 또는 문자열을 변환합니다. 선택적 Java 10 진수 형식 변환 하는 데 사용할 수 있습니다.
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
이 식은 부동 소수점 값으로 모든 숫자 또는 문자열을 변환합니다. 선택적 Java 10 진수 형식 변환 하는 데 사용할 수 있습니다. 
* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``

합니다 **toFloat** 함수 모든 double을 자릅니다.
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
이 식은 정수 값으로 모든 숫자 또는 문자열을 변환합니다. 선택적 Java 10 진수 형식 변환 하는 데 사용할 수 있습니다. 
* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``

합니다 **toInteger** 함수 자릅니다 모든 long, float, 또는 두 번입니다.
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
이 식은 모든 숫자 또는 문자열 long 값을 변환합니다. 선택적 Java 10 진수 형식 변환 하는 데 사용할 수 있습니다. 
* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``

합니다 **toLong** 함수 모든 float 또는 double을 자릅니다.
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
이 식은 짧은 값으로 모든 숫자 또는 문자열을 변환합니다. 선택적 Java 10 진수 형식 변환 하는 데 사용할 수 있습니다. 
* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``

합니다 **toShort** 함수 자릅니다 정수 long, float, 또는 두 번입니다.
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
이 식은 기본 데이터 형식을 문자열로 변환합니다. 
* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``

숫자 및 날짜 형식을 지정할 수 있습니다. 형식으로 지정 하지 않으면, 시스템 기본값으로 사용 됩니다. 기본 형식은 `yyyy-MM-dd`합니다. 숫자는 Java 10 진수 형식을 따릅니다. 모든 날짜 형식에 대 한 Java SimpleDateFormat를 가리킵니다. 
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
선택적 타임 스탬프 형식이 지정 되 면이 식은 문자열을 날짜입니다. 
* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``

가능한 모든 형식에 대 한 Java SimpleDateFormat를 가리킵니다. 타임 스탬프를 기본 패턴을 생략 하면 `yyyy-[M]M-[d]d hh:mm:ss[.f...]` 사용 됩니다.
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
이 식은 UTC 타임 스탬프를 변환합니다. 
* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``

선택적 표준 시간대 형식으로 전달할 수 있습니다 `'GMT'`, `'PST'`를 `'UTC'`, `'America/Cayman'`합니다. 기본값은 현재 표준 시간대입니다.
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
이 식은 문자 집합을 다른 문자열의 문자 집합을 바꿉니다. 문자는 한 일을 대체 합니다.
* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
이 식은 선행 및 후행 문자의 문자열을 삭제합니다. 
* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``

두 번째 매개 변수를 지정 하지 않으면 함수는 공백 문자를 삭제 합니다. 그렇지 않으면 두 번째 매개 변수를 지정 하는 모든 문자를 삭제 합니다.

*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
이 표현식은 항상 반환을 `true` 값입니다.  
* ``isDiscounted == true()``
* ``isDiscounted() == true``

열 이름이 *true*, 함수를 사용 하 여 **syntax(true())** 합니다.
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
이 식은 열 유형과 일치 합니다. 
* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``

이 함수를 사용 하 여 패턴 식에만: 수와 일치 short, integer, long, double, float 또는 10 진수 정수 계열 일치 즉, 정수, 긴, 소수 자릿수 일치 double, float, 10 진수, 및 datetime 일치 date 또는 timestamp 형식입니다.
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
이 식은 대문자 문자열을 설정합니다.
* ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 열의 분산을 가져옵니다.
* ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 열의 분산을 가져옵니다.
* ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 열의 모집단 분산을 가져옵니다.
* ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 열의 모집단 분산을 가져옵니다.
* ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
이 식은 열의 비편향된 분산을 가져옵니다.
* ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
이 식은 조건에 따라 열의 비편향된 분산을 가져옵니다.
* ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
날짜를 지정이 식을 해당 연도의 주를 가져옵니다.
* ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
이 식에서는 논리적 **xor** 연산자입니다. 이 연산자는 동일 합니다 **^** 연산자입니다.
* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
날짜를 지정이 식을 연도 값을 가져옵니다.
* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>다음 단계

[식 작성기를 사용 하는 방법을 알아봅니다](concepts-data-flow-expression-builder.md)합니다.
