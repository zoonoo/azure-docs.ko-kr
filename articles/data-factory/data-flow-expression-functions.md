---
title: Azure Data Factory의 데이터 흐름 매핑 기능에서 식 함수
description: 데이터 흐름 매핑의 식 함수에 대해 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: c062a75516a1b865c1ff6c35f00d4fbf7c4881c6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029381"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>데이터 흐름 매핑의 데이터 변환 식 



## <a name="expression-functions"></a>식 함수

Data Factory에서 데이터 흐름 매핑 기능의 식 언어를 사용 하 여 데이터 변환을 구성 합니다.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
숫자의 절대값입니다.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
-0 @ no__t-1 @ no__t-2 @no__t 코사인 역 값을 계산 합니다.<br/><br/>
문자열 또는 숫자의 쌍을 추가합니다. 날짜에 일 수를 추가합니다. 타임 스탬프에 기간을 추가 합니다. 유사한 형식의 한 배열을 다른 배열에 추가합니다. \+ 연산자 * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
날짜 또는 타임스탬프에 일을 추가합니다. 날짜 @no__t + 연산자와 동일 합니다.-0 @ no__t-1 @ no__t-2<br/><br/>
날짜 또는 타임 스탬프에 월을 추가 합니다. 필요에 따라 표준 시간대 * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t-3을 전달할 수 있습니다.<br/><br/>
논리 AND 연산자입니다. & & @No__t-0 @ no__t-1 @ no__t @-3과 같습니다.<br/><br/>
-0 @ no__t-1 @ no__t-2 @no__t 역 사인 값을 계산 합니다.<br/><br/>
-0 @ no__t-1 @ no__t-2 @no__t 역 탄젠트 값을 계산 합니다.<br/><br/>
평면의 양의 x 축과 좌표에 지정 된 점의 각도 (라디안)를 반환 합니다 * ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
열 값의 평균을 가져옵니다 * ``avg(sales)`` @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 열 값의 평균을 가져옵니다 * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
스트림에서 이름을 기준으로 열 값을 선택 합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 일치 하는 항목이 여러 개 있는 경우 첫 번째 일치 항목이 반환 됩니다. 일치 하는 항목이 없으면 NULL 값을 반환 합니다. 반환 된 값은 형식 변환 함수 (TO_DATE, TO_STRING) 중 하나로 변환 되어야 합니다.  디자인 타임에 알려진 열 이름은 해당 이름 으로만 처리 되어야 합니다. 계산 된 입력은 지원 되지 않지만 매개 변수 대체 @no__t 사용할 수 있습니다.-0 @ no__t-1 @ no__t @ no__t @ @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
스트림에서 상대 위치 (1부터) 만큼 열 값을 선택 합니다. 위치가 범위를 벗어나면 NULL 값을 반환 합니다. 반환 된 값은 형식 변환 함수 (TO_DATE, TO_STRING) 중 하나로 변환 되어야 합니다. 계산 된 입력은 지원 되지 않지만 매개 변수 대체 @no__t 사용할 수 있습니다.-0 @ no__t-1 @ no__t @ no__t @ @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
교대로 나오는 조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 입력 수가 짝수인 경우에는 마지막 조건 * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t @ no__t @ no__t @ @ no__t-4 @ no__t-5에 대 한 기본값이 NULL로 설정 됩니다.<br/><br/>
숫자의 큐브 루트를 계산 * ``cbrt(8) -> 2.0`` @ no__t-1 @ no__t-2<br/><br/>
@No__t 값 보다 작은 가장 작은 정수를 반환 합니다.-0 @ no__t-1 @ no__t-2<br/><br/>
입력 집합에서 null이 아닌 첫 번째 값을 반환 합니다. 모든 입력은 같은 유형 이어야 합니다 * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
동일한 형식의 두 값을 비교 합니다. Value1 < value2 인 경우 음의 정수를 반환 하 고 value1 = = value2 인 경우 0을 반환 합니다. value1 > value2 @no__t 값이 0 이면 양수 값이 고, 그렇지 않으면-0 @ no__t @ no__t-2 @ no__t<br/><br/>
가변 개수의 문자열을 함께 연결합니다. 문자열이 있는 + 연산자와 같습니다. * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
가변 개수의 문자열을 구분 기호와 함께 연결합니다. 첫 번째 매개 변수는 구분 기호 * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4입니다.<br/><br/>
제공 된 배열의 요소가 제공 된 조건자에서 true로 평가 되 면 true를 반환 합니다. Contains에는 #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t-1 @ no__t-2 @ no__t-3과 같은 조건자 함수의 한 요소에 대 한 참조가 필요 합니다.<br/><br/>
-0 @ no__t-1 @ no__t-2 @no__t 코사인 값을 계산 합니다.<br/><br/>
값의 하이퍼볼릭 코사인을 계산 * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2<br/><br/>
값의 집계 개수를 가져옵니다. 선택적 열을 지정한 경우에는 count * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5의 NULL 값을 무시 합니다.<br/><br/>
열 집합에 대 한 고유 값의 집계 수를 가져옵니다 * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 값의 집계 개수를 가져옵니다. 선택적 열을 지정 하면 count @no__t에서 NULL 값을 무시 합니다.-0 @ no__t-1 @ no__t-2<br/><br/>
두 열 사이의 모집단 공 분산을 가져옵니다 * ``covariancePopulation(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 두 열의 모집단 공 분산 * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2를 가져옵니다.<br/><br/>
두 열의 표본 공 분산을 가져옵니다 * ``covarianceSample(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 @no__t 두 열의 표본 공 분산을 가져옵니다.-0 @ no__t-1 @ no__t-2<br/><br/>
0(256), 224, 256, 384, 512 값의 지정된 비트 길이를 갖는 다양한 기본 데이터 형식의 열 세트에서 CRC32 해시를 계산합니다. @No__t-0 @ no__t-1 @ no__t-2의 행에 대 한 지문을 계산 하는 데 사용할 수 있습니다.<br/><br/>
CumeDist 함수는 파티션의 모든 값을 기준으로 값의 위치를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행 수를 창 파티션의 총 행 수로 나눈 값입니다. 정렬의 모든 동률 값은 동일한 위치로 평가 됩니다.
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
이 작업 실행이 시작되는 현재 날짜를 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대 한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false``
* ``currentDate('PST')  == toDate('2250-12-31') -> false``
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
작업이 로컬 표준 시간대를 사용 하 여 실행 되기 시작할 때의 현재 타임 스탬프를 가져옵니다 * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2<br/><br/>
현재 타임 스탬프를 UTC로 가져옵니다. 현재 시간을 클러스터 표준 시간대와 다른 표준 시간대로 해석 하려면 선택적인 표준 시간대를 ' GMT ', ' PST ', ' UTC ', ' 아메리카/케이맨 ' 형식으로 전달할 수 있습니다. 기본적으로 현재 표준 시간대로 설정 됩니다. 사용 가능한 형식에 대 한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. @no__t UTC 시간을 다른 표준 시간대로 변환 하는 fromUTC () * ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t @ no__t-4 @ no__t-5<br/><br/>
날짜가 지정 된 날짜 * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2에 해당 하는 날짜를 가져옵니다.<br/><br/>
지정된 날짜의 요일을 가져옵니다. 1-일요일, 2-월요일 ..., 7-토요일 * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
날짜가 지정 된 날짜 * ``dayOfYear(toDate('2016-04-09')) -> 100`` @ no__t-1 @ no__t-2에 해당 하는 날짜를 가져옵니다.<br/><br/>
일 수에 대 한 시간 (밀리초) * ``days(2) -> 172800000L`` @ no__t-1 @ no__t-2<br/><br/>
라디안을도로 변환 * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2<br/><br/>
값 그룹에 있는 값의 순위를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성하지 않습니다. 조밀한 Rank는 데이터가 정렬 되지 않은 경우에도 작동 하며 값 * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2를 찾습니다.<br/><br/>
숫자 쌍을 나눕니다. /연산자 * ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t-3과 같습니다.<br/><br/>
문자열이 제공 된 문자열로 끝나는지 여부를 확인 합니다. * ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2<br/><br/>
비교 같음 연산자. = = 연산자 * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
비교는 대/소문자를 무시하는 연산자와 같습니다. < = > Operator * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t @-3과 같습니다.<br/><br/>
숫자의 계승값을 계산 * ``factorial(5) -> 120`` @ no__t-1 @ no__t-2<br/><br/>
항상 false 값을 반환합니다. ' F a l s e @no__t 인 열이 있는 경우 함수 구문 (false ())을 사용 합니다.-0 @ no__t @ no__t @ no__t @<br/><br/>
제공 된 조건자를 충족 하지 않는 배열에서 요소를 필터링 합니다. 필터에는 #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t-3과 같이 조건자 함수의 한 요소에 대 한 참조가 필요 합니다.<br/><br/>
열 그룹의 첫 번째 값을 가져옵니다. 두 번째 매개 변수 ignoreNulls을 생략 하면 false * ``first(sales)`` @ no__t-1 @ no__t @ no__t-3으로 간주 됩니다.<br/><br/>
@No__t 값 보다 크지 않은 가장 큰 정수를 반환 합니다.-0 @ no__t-1 @ no__t-2<br/><br/>
UTC의 타임 스탬프로 변환 합니다. 필요에 따라 표준 시간대를 ' GMT ', ' PST ', ' UTC ', ' 아메리카/케이맨 ' 형식으로 전달할 수 있습니다. 사용 가능한 형식에 대 한 현재 timezoneRefer Java의 SimpleDateFormat로 기본값을 사용 합니다. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 큼 연산자. > Operator * ``greater(12, 24) -> false`` @ no__t @ no__t @ no__t @ no__t-4와 동일 합니다.<br/><br/>
비교 보다 크거나 같음 연산자. > = 연산자 * ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3과 같습니다.<br/><br/>
값 목록 중에서 null 값을 건너뛴 입력으로 가장 큰 값을 반환 합니다. 모든 입력이 null @no__t 경우 null을 반환 합니다.-0 @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5<br/><br/>
스트림에서 이름으로 열 값을 확인 합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다.  디자인 타임에 알려진 열 이름은 해당 이름 으로만 처리 되어야 합니다. 계산 된 입력은 지원 되지 않지만 매개 변수 대체 @no__t 사용할 수 있습니다.-0 @ no__t-1 @ no__t-2<br/><br/>
타임스탬프의 시간 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대 한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
시간 @no__t 시간 (밀리초)의 기간 (밀리초)-0 @ no__t-1 @ no__t-2<br/><br/>
제공 된 식을 사용 하 여 배열의 각 요소를 새 요소에 매핑합니다. Map에는 #item 식 함수에서 하나의 요소에 대 한 참조와 #index @no__t 요소 인덱스에 대 한 참조가 필요 합니다.-0 @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 다른가 지정 되지 않은 경우 NULL로 간주 됩니다. 두 값이 모두 호환 되어야 합니다 (numeric, string ...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
값이 NULL이 아닌지 여부를 확인 하 고 다른 값을 반환 합니다. Null이 아닌 첫 번째 값을 찾을 때까지 모든 입력을 테스트 합니다 * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
항목이 배열에 있는지 확인 * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
모든 단어의 첫 글자를 대문자로 변환합니다. 단어는 공백으로 구분 되는 것으로 식별 됩니다 * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2<br/><br/>
문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. @no__t를 찾을 수 없으면 0이 반환 됩니다.-0 @ no__t-1 @ no__t @ no__t @ no__t-4<br/><br/>
행이 삭제용으로 표시되어 있는지 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스의 기본값은 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t-3입니다.<br/><br/>
행이 오류로 표시되는지 여부를 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스의 기본값은 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t-3입니다.<br/><br/>
행이 무시되도록 표시되는지 여부를 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스의 기본값은 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t-3입니다.<br/><br/>
행이 삽입용으로 표시되어 있는지 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스의 기본값은 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t-3입니다.<br/><br/>
조회 시 행이 일치하는지 여부를 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스의 기본값은 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t-3입니다.<br/><br/>
값이 NULL @no__t 인지 확인 합니다.-0 @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
행이 업데이트용으로 표시되어 있는지 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스의 기본값은 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t-3입니다.<br/><br/>
행이 삽입용으로 표시되어 있는지 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스의 기본값은 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t-3입니다.<br/><br/>
열의 첨도를 가져옵니다 * ``kurtosis(sales)`` @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 열의 첨도 * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2를 가져옵니다.<br/><br/>
현재 행보다 n개 행 전에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 두 번째 매개 변수는 다시 검색할 행 수 이며 기본값은 1입니다. 기본값이 없는 경우에는 기본값을 지정 하지 않는 한 null 값이 반환 됩니다 * ``lag(amount, 2)`` @ no__t-1 @ no__t @ no__t @-3<br/><br/>
열 그룹의 마지막 값을 가져옵니다. 두 번째 매개 변수 ignoreNulls을 생략 하면 false * ``last(sales)`` @ no__t-1 @ no__t @ no__t-3으로 간주 됩니다.<br/><br/>
날짜가 지정 된 달의 마지막 날짜를 가져옵니다 * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2<br/><br/>
현재 행보다 n개 행 뒤에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 두 번째 매개 변수는 앞으로 조회할 행의 수이 고 기본값은 1입니다. 기본값이 없는 경우에는 기본값을 지정 하지 않는 한 null 값이 반환 됩니다 * ``lead(amount, 2)`` @ no__t-1 @ no__t @ no__t @-3<br/><br/>
비교 보다 작거나 같음 연산자. < = 연산자 * ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3과 같습니다.<br/><br/>
인덱스 1에서 시작하여 지정된 문자 수를 갖는 부분 문자열을 추출합니다. SUBSTRING (str, 1, n) * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3과 같습니다.<br/><br/>
@No__t-0 @ no__t-1 @ no__t-2의 문자열 길이를 반환 합니다.<br/><br/>
비교 보다 작음 연산자. < Operator * ``lesser(12, 24) -> true`` @ no__t @ no__t @ no__t @ no__t-4와 동일 합니다.<br/><br/>
비교 보다 작거나 같음 연산자. < = 연산자 * ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3과 같습니다.<br/><br/>
두 문자열 사이의 levenshtein 거리를 가져옵니다 * ``levenshtein('boys', 'girls') -> 4`` @ no__t-1 @ no__t-2<br/><br/>
패턴은 문자 그대로 일치 하는 문자열입니다. 예외는 다음과 같은 특수 한 기호입니다. _은 입력에서 한 문자 (와 유사)를 찾습니다. .과 유사). %는 입력의 0개 이상의 문자와 일치합니다(posix 정규식의 .*와 유사).
이스케이프 문자는 ''입니다. 특수 기호 또는 다른 이스케이프 문자 앞에 이스케이프 문자가 오면 다음 문자는 글자 그대로 일치됩니다. 다른 모든 문자를 이스케이프하는 것은 유효하지 않습니다.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
특정 위치에서 시작하는 문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. 이 위치를 생략하면 문자열의 시작 부분에서 찾습니다. @no__t를 찾을 수 없으면 0이 반환 됩니다.-0 @ no__t-1 @ no__t @ no__t @ no__t-4<br/><br/>
로그 값을 계산합니다. 선택적 base는 사용 하는 경우 오일러 number를 @no__t 사용 하는 경우 number를 제공할 수 있습니다.-0 @ no__t-1 @ no__t-2<br/><br/>
10 기본 @no__t를 기준으로 로그 값을 계산 합니다. 0 @ no__t-1 @ no__t-2<br/><br/>
Lowercases string * ``lower('GunChus') -> 'gunchus'`` @ no__t-1 @ no__t-2<br/><br/>
특정 길이가 될 때까지 제공된 패딩으로 문자열의 왼쪽 여백을 채웁니다. 문자열이 길이와 같거나 큰 경우 길이 * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2 ' ' lpad (' dumbo ', 8, ' < > ')-> ' < > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5로 잘립니다.<br/><br/>
왼쪽의 선행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않으면 두 번째 매개 변수에 지정 된 모든 문자를 트리밍하는 * ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
제공 된 식을 사용 하 여 배열의 각 요소를 새 요소에 매핑합니다. Map에는 #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t-3과 같이 식 함수에서 하나의 요소에 대 한 참조가 필요 합니다.<br/><br/>
열의 최대값을 가져옵니다 * ``max(sales)`` @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 열 @no__t 최대값을 가져옵니다.-0 @ no__t-1 @ no__t-2<br/><br/>
다양한 기본 데이터 형식의 열 세트에서 MD5 다이제스트를 계산하고 32자의 16진수 문자열을 반환합니다. @No__t-0 @ no__t-1 @ no__t-2의 행에 대 한 지문을 계산 하는 데 사용할 수 있습니다.<br/><br/>
열 값의 평균값을 가져옵니다. AVG * ``mean(sales)`` @ no__t-1 @ no__t-2와 동일 합니다.<br/><br/>
조건에 따라 열 값의 평균값을 가져옵니다. AvgIf @no__t와 동일-0 @ no__t-1 @ no__t-2<br/><br/>
날짜의 밀리초 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대 한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
시간 (밀리초)의 시간 (밀리초) * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
열의 최소값을 가져옵니다. * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
조건에 따라 열 값 * ``minIf(region == 'West', sales)`` @ no__t-1 @ no__t-2를 가져옵니다.<br/><br/>
숫자를 뺍니다. 날짜에서 일 수를 뺍니다. 타임 스탬프의 substract 기간 두 타임 스탬프는 차이를 얻기 위해 두 타임 스탬프를 작동 합니다 (밀리초 단위). -Operator * ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
타임스탬프의 분 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대 한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
기간 (분)의 기간 (밀리초) * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
숫자 쌍의 모듈러스입니다. % Operator * ``mod(20, 8) -> 4`` @ no__t @ no__t-2 @ no__t-3과 같습니다.<br/><br/>
날짜 또는 타임 스탬프의 월 값을 가져옵니다 * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
두 날짜 사이의 개월 수를 가져옵니다. 계산을 반올림할 수 있습니다. 선택적인 표준 시간대를 ' GMT ', ' PST ', ' UTC ', ' 아메리카/케이맨 ' 형식으로 전달할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대 한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍을 곱합니다. \* 연산자 * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t-3과 같습니다.<br/><br/>
NTile 함수는 각 창 파티션의 행을 1부터 `n`개 이내의 `n` 버킷 수로 나눕니다. 버킷 값 차이는 최대 1 이내입니다. 파티션의 행 수가 버킷 수로 균일하게 나누어 떨어지지 않으면 나머지 값은 첫 번째 버킷부터 시작해서 하나씩 분산됩니다. NTile 함수는 tertiles, 변 위치, deciles 및 기타 일반적인 요약 통계의 계산에 유용 합니다. 이 함수는 초기화하는 동안 두 개의 변수를 계산합니다. 일반 버킷의 크기에는 하나의 추가 행이 추가 됩니다. 두 변수는 현재 파티션의 크기를 기준으로 합니다. 계산 프로세스 동안 이 함수는 현재 행 번호, 현재 버킷 수 및 버킷이 변경되는 행 번호(bucketThreshold)를 추적합니다. 현재 행 번호가 버킷 임계값에 도달하면 버킷 값이 1씩 커지고, 임계값은 버킷 크기만큼 커집니다(현재 버킷이 채워질 경우 하나씩 추가).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
숫자를 부정합니다. 양수를 음수 값으로, 또는 그 반대로 * ``negate(13) -> -13`` @ no__t-1 @ no__t-2를 바꿉니다.<br/><br/>
고유한 다음 시퀀스를 반환합니다. 이 숫자는 파티션 내 에서만 연속 되며 partitionId * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2가 접두사로 붙습니다.<br/><br/>
문자열 값을 정규화 된 유니코드 문자를 구분 하는 * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2<br/><br/>
논리 부정 연산자 * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
비교 같지 않음 연산자. ! = Operator * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t-3과 같습니다.<br/><br/>
NULL 값을 반환합니다. 'Null'라는 열이 있는 경우 함수 syntax(null())를 사용합니다. 에서 사용 하는 모든 작업은 NULL * ``isNull('dumbo' + null) -> true`` @ no__t @ no__t @ no__t @ @ no__t-4 @ no__t-5 @ no__t-6을 반환 합니다.<br/><br/>
논리 OR 연산자입니다. | |와 동일 * ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
숫자 쌍의 양의 모듈러스입니다.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
입력 행이 @no__t 현재 파티션 id를 반환 합니다.-0 @ no__t-1 @ no__t-2<br/><br/>
한 숫자를 다른 @no__t의 거듭제곱으로 계산 합니다.-0 @ no__t-1 @ no__t-2<br/><br/>
값 그룹에 있는 값의 순위를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성합니다. Rank는 데이터가 정렬 되지 않은 경우에도 작동 하며 값 * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2를 찾습니다.<br/><br/>
배열의 요소를 누적 합니다. 줄이기는 첫 번째 식 함수에서 누적기 및 one 요소에 대 한 참조를 #acc 및 #item으로 예상 하며, 결과 값을 두 번째 식 함수 * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4에 사용할 #result 것으로 예상 합니다.<br/><br/>
지정된 정규식 패턴에 대해 일치하는 부분 문자열을 추출합니다. 마지막 매개 변수는 일치 그룹을 식별하고 생략하면 기본적으로 1이 지정됩니다. ' @No__t-0 ' (큰따옴표)를 사용 하 여 문자열을 이스케이프 * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t-4 없이 찾습니다.<br/><br/>
문자열이 지정된 정규식 패턴과 일치하는지 확인합니다. ' @No__t-0 ' (큰따옴표)를 사용 하 여 문자열을 이스케이프 * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t-4 없이 찾습니다.<br/><br/>
Regex 패턴의 모든 항목을 지정 된 문자열의 다른 부분 문자열로 바꿉니다. * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t @ no__t @ no__t-4 없이 문자열을 일치 시키려면 ' <regex> ' (back 따옴표)를 사용 합니다.<br/><br/>
Regex를 기반으로 하는 구분 기호를 기반으로 문자열을 분할 하 고 문자열의 배열을 반환 * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t @ no__t @ no__t @ @ no__t-4 @ no__t-5<br/><br/>
모든 부분 문자열을 지정 된 문자열의 다른 부분 문자열로 바꿉니다. 마지막 매개 변수를 생략 하는 경우 기본값은 빈 문자열 * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4입니다.<br/><br/>
@No__t-0 @ no__t-1 @ no__t-2의 문자열을 반대로 바꿉니다.<br/><br/>
오른쪽부터 지정된 문자 수를 갖는 부분 문자열을 추출합니다. SUBSTRING (str, LENGTH (str)-n, n) * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3과 같습니다.<br/><br/>
문자열이 지정 된 regex 패턴과 일치 하는지 확인 합니다 * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
선택적인 배율 및 선택적 반올림 모드를 지정 하 여 숫자를 반올림 합니다. 소수 자릿수가 생략 된 경우 기본값은 0입니다.  이 모드를 생략 하면 기본적으로 ROUND_HALF_UP (5)로 설정 됩니다. 반올림 값에는 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4가 포함 됩니다.<br/><br/>
@No__t 1부터 시작 하 여 행에 대 한 순차 행 번호 매기기를 할당 합니다.-0 @ no__t-1 @ no__t-2<br/><br/>
특정 길이가 될 때까지 제공된 패딩으로 문자열의 오른쪽 여백을 채웁니다. 문자열이 길이 보다 크거나 같은 경우 길이 * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5로 잘립니다.<br/><br/>
오른쪽의 선행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않으면 두 번째 매개 변수에 지정 된 모든 문자를 트리밍하는 * ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
날짜의 초 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대 한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
@No__t 시간 (초)의 시간 (밀리초)-0 @ no__t-1 @ no__t-2<br/><br/>
다양한 기본 데이터 형식의 열 세트에서 SHA-1 다이제스트를 계산하고 40자의 16진수 문자열을 반환합니다. @No__t-0 @ no__t-1 @ no__t-2의 행에 대 한 지문을 계산 하는 데 사용할 수 있습니다.<br/><br/>
0 (256), 224, 256, 384, 512 값만 사용할 수 있는 비트 길이를 지정 하 여 다양 한 기본 데이터 형식의 열 집합에 대 한 SHA-2 다이제스트를 계산 합니다. @No__t-0 @ no__t-1 @ no__t-2의 행에 대 한 지문을 계산 하는 데 사용할 수 있습니다.<br/><br/>
-0 @ no__t-1 @ no__t-2 @no__t 사인 값을 계산 합니다.<br/><br/>
-0 @ no__t-1 @ no__t-2 @no__t 하이퍼볼릭 사인 값을 계산 합니다.<br/><br/>
@No__t 열에 대 한 왜곡도를 가져옵니다.-0 @ no__t-1 @ no__t-2<br/><br/>
조건에 따라-0 @ no__t-1 @ no__t-2 @no__t 열에 대 한 왜곡도를 가져옵니다.<br/><br/>
특정 위치에서 배열의 하위 세트를 추출합니다. 위치는 1부터 시작합니다. 길이를 생략 하면 기본적으로 @no__t는 문자열의 끝이 됩니다.-0 @ no__t-1 @ no__t @ no__t @ @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
제공 된 조건자 함수를 사용 하 여 배열을 정렬 합니다. Sort는 #item1 식 함수에서 두 개의 연속 된 요소에 대 한 참조를 필요로 하 고 #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t @-3입니다.<br/><br/>
문자열 * ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2에 대 한 soundex 코드를 가져옵니다.<br/><br/>
구분 기호를 기반으로 문자열을 분할 하 고 문자열의 배열을 반환 * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t @ no__t @ no__t @ @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
숫자의 제곱근을 계산 * ``sqrt(9) -> 3`` @ no__t-1 @ no__t-2<br/><br/>
문자열이 제공 된 문자열로 시작 하는지 확인 합니다. * ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2<br/><br/>
@No__t 열에 대 한 표준 편차를 가져옵니다.-0 @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 열에 대 한 표준 편차 * ``stddevIf(region == 'West', sales)`` @ no__t-1 @ no__t-2를 가져옵니다.<br/><br/>
열의 모집단 표준 편차를 가져옵니다 * ``stddevPopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 열에 대 한 모집단 표준 편차 * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2를 가져옵니다.<br/><br/>
@No__t 열에 대 한 샘플 표준 편차를 가져옵니다.-0 @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 열에 대 한 샘플 표준 편차 * ``stddevSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2를 가져옵니다.<br/><br/>
날짜 또는 타임 스탬프에서 월을 뺍니다. Date * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t @ no__t-2에 대 한-연산자와 동일 합니다.<br/><br/>
날짜 또는 타임 스탬프에서 개월 빼기 * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
특정 위치에서 특정 길이의 부분 문자열을 추출합니다. 위치는 1부터 시작합니다. 길이를 생략 하면 기본적으로 @no__t는 문자열의 끝이 됩니다.-0 @ no__t-1 @ no__t @ no__t @ @ no__t-4 @ no__t-5<br/><br/>
숫자 열의 집계 합계를 가져옵니다 * ``sum(col)`` @ no__t-1 @ no__t-2<br/><br/>
숫자 열 @no__t 고유 값의 집계 합계를 가져옵니다.-0 @ no__t-1 @ no__t-2<br/><br/>
기준에 따라 숫자 열의 집계 합계를 가져옵니다. 이 조건은 * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3 열을 기반으로 할 수 있습니다.<br/><br/>
기준에 따라 숫자 열의 집계 합계를 가져옵니다. 이 조건은 * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3 열을 기반으로 할 수 있습니다.<br/><br/>
-0 @ no__t-1 @ no__t-2 @no__t 탄젠트 값을 계산 합니다.<br/><br/>
-0 @ no__t-1 @ no__t-2 @no__t 하이퍼볼릭 탄젠트 값을 계산 합니다.<br/><br/>
모든 숫자/날짜/타임 스탬프/문자열을 이진 표현으로 변환 * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2<br/><br/>
값 (' t ', ' true ', ' y ', ' yes ', ' 1 ')을 true와 (' f ', ' false ', ' n ', ' no ', ' 0 ')로 변환 합니다. 다른 값 * ``toBoolean('true') -> true`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4<br/><br/>
선택적인 입력 날짜 형식을 사용 하 여 입력 날짜 문자열을 날짜로 변환 합니다. 사용 가능한 형식에 대 한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. 입력 날짜 형식이 생략 된 경우 기본 형식은 yyyy-[M] M-[d] d입니다. 허용 되는 형식은 다음과 같습니다. [yyyy, yyyy-[M] M, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t @ no__t @ no__t @-3<br/><br/>
숫자 또는 문자열을 10진수 값으로 변환합니다. 자릿수와 소수 자릿수를 지정하지 않으면 기본값인 (10,2)가 사용됩니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. En-us, de, zh-cn * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5와 같은 BCP47 언어 형식의 선택적 로캘 형식입니다.<br/><br/>
숫자 또는 문자열을 배정도 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. En-us, de, zh-cn * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5와 같은 BCP47 언어 형식의 선택적 로캘 형식입니다.<br/><br/>
숫자 또는 문자열을 부동 소수점 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. Double * ``toFloat(123.45) -> 123.45f`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4를 자릅니다.<br/><br/>
숫자 또는 문자열을 정수 값으로 변환 합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. Long, float, double * ``toInteger(123) -> 123`` @ no__t-1 @ no__t @ no__t @ no__t-4를 자릅니다.<br/><br/>
숫자 또는 문자열을 긴 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. Float, double * ``toLong(123) -> 123`` @ no__t @ no__t @ no__t @ no__t-4를 자릅니다.<br/><br/>
숫자 또는 문자열을 짧은 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. Integer, long, float, double * ``toShort(123) -> 123`` @ no__t-1 @ no__t @ no__t @ no__t-4를 자릅니다.<br/><br/>
기본 데이터 형식을 문자열로 변환합니다. 숫자 및 날짜의 경우 형식을 지정할 수 있습니다. 지정하지 않으면 시스템 기본값은 picked.Java입니다. 숫자에는 10진수 형식이 사용됩니다. 가능한 모든 날짜 형식에 대해서는 Java SimpleDateFormat를 참조 하세요. 기본 형식은 yyyy-MM-dd * ``toString(10) -> '10'`` @ no__t @ no__t @ no__t @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9입니다.<br/><br/>
선택적 타임 스탬프 형식을 지정 하 여 문자열을 타임 스탬프로 변환 합니다. 가능한 모든 형식에 대해서는 Java SimpleDateFormat를 참조 하세요. 타임 스탬프를 생략 하면 기본 패턴이 사용 됩니다. yyyy-[M] M-[d] d hh: mm: ss [.f ...]가 사용 됩니다. 선택적인 표준 시간대를 ' GMT ', ' PST ', ' UTC ', ' 아메리카/케이맨 ' 형식으로 전달할 수 있습니다. Timestamp는 사용 가능한 형식에 대 한 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t @ no__t @ @ no__t @ no__t-5 @ no__t-6<br/><br/>
타임스탬프를 UTC로 변환합니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 사용 가능한 형식에 대 한 현재 timezoneRefer Java의 SimpleDateFormat로 기본값을 사용 합니다. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
하나의 문자 세트를 문자열의 다른 문자 세트로 바꿉니다. 문자에는 1-1 개의 대체가 * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3이 있습니다.<br/><br/>
선행 및 후행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않으면 두 번째 매개 변수에 지정 된 모든 문자를 트리밍하는 * ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
항상 true 값을 반환합니다. ' T r u e @no__t ' 라는 열이 있는 경우 함수 구문 (true ())을 사용 합니다.<br/><br/>
열의 형식과 일치합니다. 패턴 식 에서만 사용할 수 있습니다. number는 short, integer, long, double, float 또는 decimal과 일치 합니다. 정수, 정수, long, 소수는 double, float, decimal 및 datetime과 일치 하는 날짜 또는 타임 스탬프 형식 * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__ t-3<br/><br/>
Uppercases string * ``upper('bojjus') -> 'BOJJUS'`` @ no__t-1 @ no__t-2<br/><br/>
생성 된 UUID * ``uuid()`` @ no__t-1 @ no__t-2를 반환 합니다.<br/><br/>
@No__t-0 @ no__t-1 @ no__t-2 열의 분산을 가져옵니다.<br/><br/>
조건에 따라 * ``varianceIf(region == 'West', sales)`` @ no__t-1 @ no__t-2 열의 분산을 가져옵니다.<br/><br/>
@No__t 열에 대 한 모집단 분산을 가져옵니다.-0 @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2 열의 모집단 분산을 가져옵니다.<br/><br/>
@No__t 열에 대 한 비편향 분산을 가져옵니다.-0 @ no__t-1 @ no__t-2<br/><br/>
조건에 따라 열에 대 한 비편향 분산 * ``varianceSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2를 가져옵니다.<br/><br/>
날짜 * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2를 지정 하 여 해당 연도의 주를 가져옵니다.<br/><br/>
주 수의 기간 (밀리초) * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
논리 XOR 연산자입니다. ^ Operator * ``xor(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t @ no__t-4와 같습니다.<br/><br/>
날짜 @no__t의 연도 값을 가져옵니다.

## <a name="next-steps"></a>다음 단계

[식 작성기를 사용 하는 방법을 알아봅니다](concepts-data-flow-expression-builder.md).
