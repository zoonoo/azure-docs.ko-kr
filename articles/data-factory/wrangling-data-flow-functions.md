---
title: Azure 데이터 팩터리에서 데이터 흐름 변환 함수 랭글링
description: Azure 데이터 팩터리에서 사용 가능한 랭글링 데이터 흐름 함수개요
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 7235e95e5b33fb931411a51796a8dbec96c46355
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417660"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>랭글링 데이터 흐름의 변환 함수

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory에서 데이터 흐름을 랭글링하면 클라우드 규모에서 코드 없는 민첩한 데이터 준비 및 랭글링 작업을 수행할 수 있습니다. 랭글링 데이터 흐름은 [Power Query Online과](https://docs.microsoft.com/powerquery-m/power-query-m-reference) 통합되며 스파크 실행을 통해 데이터 랭글링에 Power Query M 기능을 사용할 수 있습니다. 

현재 모든 Power Query M 함수가 작성 중에 사용 가능에도 불구하고 데이터 랭글링에 대해 지원되는 것은 아닙니다. 랭글링 데이터 흐름을 작성하는 동안 함수가 지원되지 않는 경우 다음과 같은 오류 메시지가 표시됩니다.

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

다음은 지원되는 Power Query M 함수 목록입니다.

## <a name="column-management"></a>열 관리

* 선택: [표.선택열](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* 제거: [표.제거열](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* 이름 바꾸기: [table.renameColumns,](https://docs.microsoft.com/powerquery-m/table-renamecolumns) [테이블.접두사열,](https://docs.microsoft.com/powerquery-m/table-prefixcolumns) [표.transformColumn이름](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* 재정렬: [테이블.재정렬열](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>행 필터링

M 함수 [Table.SelectRows를](https://docs.microsoft.com/powerquery-m/table-selectrows) 사용하여 다음 조건을 필터링합니다.

* 평등과 불평등
* 숫자, 텍스트 및 날짜 비교(DateTime 제외)
* 숫자와 같은 숫자 [정보.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[홀수](https://docs.microsoft.com/powerquery-m/number-iseven)
* [텍스트 포함,](https://docs.microsoft.com/powerquery-m/text-contains) [텍스트.시작,](https://docs.microsoft.com/powerquery-m/text-startswith)또는 [Text.EndsWith를](https://docs.microsoft.com/powerquery-m/text-endswith) 사용하는 텍스트 포함
* 모든 'IsIn' [날짜 함수를](https://docs.microsoft.com/powerquery-m/date-functions)포함한 날짜 범위) 
* 이들 조건의 사용 및 또는 아님의 조합

## <a name="adding-and-transforming-columns"></a>열 추가 및 변형

다음 M 함수는 열을 추가하거나 변환합니다: [Table.AddColumn,](https://docs.microsoft.com/powerquery-m/table-addcolumn) [표.TransformColumns,](https://docs.microsoft.com/powerquery-m/table-transformcolumns) [표.ReplaceValue,](https://docs.microsoft.com/powerquery-m/table-replacevalue) [표.중복열](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). 다음은 지원되는 변환 기능입니다.

* 숫자 산술
* 텍스트 연결
* 날짜 및 시간 산술 (산술 연산, [Date.AddDays,](https://docs.microsoft.com/powerquery-m/date-adddays) [날짜.Addmonths,](https://docs.microsoft.com/powerquery-m/date-addmonths) [날짜.AddQuarters,](https://docs.microsoft.com/powerquery-m/date-addquarters) [날짜.Addweeks,](https://docs.microsoft.com/powerquery-m/date-addweeks) [날짜.AddYears.](https://docs.microsoft.com/powerquery-m/date-addyears)
* 기간은 날짜 및 시간 산술 연산에 사용할 수 있지만 싱크에 기록하기 전에 다른 유형으로 변환해야합니다 (산술 연산자, [Duration.TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes) [#duration,](https://docs.microsoft.com/powerquery-m/sharpduration) [Duration.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds) [Duration.Days.Days.Days.시간.시간,](https://docs.microsoft.com/powerquery-m/duration-days) [시간. 시간. 시간. 시간.](https://docs.microsoft.com/powerquery-m/duration-minutes) [시간.](https://docs.microsoft.com/powerquery-m/duration-seconds) [Duration.Hours](https://docs.microsoft.com/powerquery-m/duration-hours) [Duration.TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays) [Duration.TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours)    
* 대부분의 표준, 과학적 및 삼각함수 함수(숫자.요인, 번호.순열 및 번호.조합제외 [연산,](https://docs.microsoft.com/powerquery-m/number-functions#operations) [반올림](https://docs.microsoft.com/powerquery-m/number-functions#rounding)및 [삼각법](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *except* 아래의 모든 함수)
* 대체[(대체.대체 텍스트,](https://docs.microsoft.com/powerquery-m/replacer-replacetext) [대체값,](https://docs.microsoft.com/powerquery-m/replacer-replacevalue) [텍스트.바꾸기,](https://docs.microsoft.com/powerquery-m/text-replace) [텍스트 제거)](https://docs.microsoft.com/powerquery-m/text-remove)
* 위치 텍스트 추출 ([텍스트.위치의](https://docs.microsoft.com/powerquery-m/text-positionof), [텍스트.길이,](https://docs.microsoft.com/powerquery-m/text-length) [텍스트.시작,](https://docs.microsoft.com/powerquery-m/text-start) [텍스트.끝,](https://docs.microsoft.com/powerquery-m/text-end) [텍스트.중간,](https://docs.microsoft.com/powerquery-m/text-middle) [텍스트.대체 범위,](https://docs.microsoft.com/powerquery-m/text-replacerange) [텍스트.제거 범위)](https://docs.microsoft.com/powerquery-m/text-removerange)
* 기본 텍스트 서식[(텍스트.아래쪽,](https://docs.microsoft.com/powerquery-m/text-lower) [텍스트.위,](https://docs.microsoft.com/powerquery-m/text-upper) [텍스트.트림](https://docs.microsoft.com/powerquery-m/text-trim)/[시작](https://docs.microsoft.com/powerquery-m/text-trimstart)/[끝,](https://docs.microsoft.com/powerquery-m/text-trimend) [텍스트.패드 스타트](https://docs.microsoft.com/powerquery-m/text-padstart)/[끝,](https://docs.microsoft.com/powerquery-m/text-padend) [텍스트.역)](https://docs.microsoft.com/powerquery-m/text-reverse)
* 날짜/시간 기능[(날짜.날짜,](https://docs.microsoft.com/powerquery-m/date-day) [날짜.월,](https://docs.microsoft.com/powerquery-m/date-month) [날짜.년](https://docs.microsoft.com/powerquery-m/date-year) [시간.시간,](https://docs.microsoft.com/powerquery-m/time-hour) [시간.분](https://docs.microsoft.com/powerquery-m/time-minute), [시간.2,](https://docs.microsoft.com/powerquery-m/time-second) [날짜.Dayofweek,](https://docs.microsoft.com/powerquery-m/date-dayofweek) [날짜.DayofYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [날짜.DayInMonth)](https://docs.microsoft.com/powerquery-m/date-daysinmonth)
* 식(그러나 분기에 일치하는 형식이 있어야 하는 경우)
* 행필터를 논리 열로 필터링합니다.
* 번호, 텍스트, 논리, 날짜 및 날짜 시간 상수

<a name="mergingjoining-tables"></a>테이블 병합/조인
----------------------
* 전원 쿼리는 중첩 된 조인을 생성합니다 (Table.NestedJoin; 사용자는 [Table.AddJoinColumn을](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)수동으로 작성할 수도 있습니다).
    그런 다음 사용자는 중첩된 조인 열을 중첩되지 않은 조인(Table.ExpandTableColumn, 다른 컨텍스트에서 지원되지 않음)으로 확장해야 합니다.
* M 함수 [Table.Join은](https://docs.microsoft.com/powerquery-m/table-join) 추가 확장 단계가 필요하지 않도록 직접 작성할 수 있지만 사용자는 조인된 테이블 사이에 중복 된 열 이름이 없는지 확인해야 합니다.
* 지원되는 조인 종류: [내부,](https://docs.microsoft.com/powerquery-m/joinkind-inner) [왼쪽 외부,](https://docs.microsoft.com/powerquery-m/joinkind-leftouter) [오른쪽 외부,](https://docs.microsoft.com/powerquery-m/joinkind-rightouter) [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* [값.같음](https://docs.microsoft.com/powerquery-m/value-equals) 및 [값.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) 키 같음 비교로 지원 됩니다.

## <a name="group-by"></a>Group By

[Table.Group을](https://docs.microsoft.com/powerquery-m/table-group) 사용하여 값을 집계합니다.
* 집계 함수와 함께 사용해야 합니다.
* 지원되는 집계 함수: [table.RowCount,](https://docs.microsoft.com/powerquery-m/table-rowcount) [list.Sum, list.Count,](https://docs.microsoft.com/powerquery-m/list-sum) [list.Average](https://docs.microsoft.com/powerquery-m/list-count), [list.Min](https://docs.microsoft.com/powerquery-m/list-min), [list.Max](https://docs.microsoft.com/powerquery-m/list-max), [list.StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [list.First](https://docs.microsoft.com/powerquery-m/list-first), [list.Last](https://docs.microsoft.com/powerquery-m/list-last) [List.Average](https://docs.microsoft.com/powerquery-m/list-average)

## <a name="sorting"></a>정렬

[Table.Sort를](https://docs.microsoft.com/powerquery-m/table-sort) 사용하여 값을 정렬합니다.

## <a name="reducing-rows"></a>행 감소

상단 을 유지하고 제거하고 범위를 유지하십시오 (해당 M 함수, 조건이 아닌 카운트만 지원: [table.FirstN,](https://docs.microsoft.com/powerquery-m/table-firstn) [Table.Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range,](https://docs.microsoft.com/powerquery-m/table-range) [Table.MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table.MaxN)](https://docs.microsoft.com/powerquery-m/table-maxn)

## <a name="known-unsupported-functions"></a>지원되지 않는 알려진 함수

| 함수 | 상태 |
| -- | -- |
| Table.PromoteHeaders | 지원되지 않습니다. 데이터 집합에서 "첫 번째 행을 헤더로" 설정하면 동일한 결과를 얻을 수 있습니다. |
| Table.CombineColumns | 이 시나리오는 직접 지원되지는 않지만 지정된 두 열을 통합하는 새 열을 추가하여 수행할 수 있는 일반적인 시나리오입니다.  예를 들어 table.Addcolumn(EmailColumn 제거, "이름", 각 [이름] & " "& [성]) |
| Table.TransformColumnTypes | 대부분의 경우 지원됩니다. 다음 시나리오는 지원되지 않습니다: 문자열을 통화 유형으로 변환하고, 문자열을 시간 유형으로 변환하고, 문자열을 백분율 유형으로 변환합니다. |
| Table.NestedJoin | 조인을 수행하면 유효성 검사 오류가 발생합니다. 열이 작동하려면 열을 확장해야 합니다. |
| Table.Distinct | 중복 행 제거는 지원되지 않습니다. |
| Table.RemoveLastN | 아래쪽 행을 제거하는 것은 지원되지 않습니다. |
| Table.RowCount | 지원되지는 않지만 모든 셀이 비어 있는 추가 열을 사용하여 (조건 열을 사용할 수 있음) 해당 열에서 그룹을 사용하여 수행할 수 있습니다. Table.Group이 지원됩니다. | 
| 행 수준 오류 처리 | 행 수준 오류 처리는 현재 지원되지 않습니다. 예를 들어 열에서 비숫자 값을 필터링하려면 텍스트 열을 숫자로 변환하는 것이 한 가지 방법입니다. 변환에 실패한 모든 셀은 오류 상태가 되어 필터링해야 합니다. 이 시나리오는 데이터 흐름을 랭글링할 때불가능합니다. |
| Table.Transpose | 지원 안 함 |
| Table.Pivot | 지원 안 함 |

## <a name="next-steps"></a>다음 단계

[랭글링 데이터 흐름을 만드는](wrangling-data-flow-tutorial.md)방법에 대해 알아봅니다.