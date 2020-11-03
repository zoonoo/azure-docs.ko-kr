---
title: Azure Data Factory의 랭 글 링 데이터 흐름 변환 함수
description: Azure Data Factory에서 사용 가능한 랭 글 링 데이터 흐름 함수 개요
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: c56c52193f433571f16e4acf7bd6e7b89641b26f
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233953"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>랭 글 링 데이터 흐름의 변환 함수

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory의 랭 글 링 데이터 흐름을 사용 하면 클라우드 규모에서 코드 없이 agile 데이터를 준비 하 고 랭 글 링 수 있습니다. 랭 글 링 데이터 흐름은 [온라인 파워 쿼리](/powerquery-m/power-query-m-reference) 와 통합 되며 spark 실행을 통해 데이터 랭 글 링에 파워 쿼리 M 함수를 사용할 수 있습니다. 

> [!NOTE]
> 랭 글 링 데이터 흐름은 현재 공개 미리 보기로 제공 되는 가능한

현재 모든 파워 쿼리 M 함수는 제작 중에도 사용할 수 있는 데이터 랭 글 링 지원 되지 않습니다. 랭 글 링 데이터 흐름을 작성 하는 동안 함수가 지원 되지 않으면 다음과 같은 오류 메시지가 표시 됩니다.

`The Wrangling Data Flow is invalid. Expression.Error: The transformation logic is not supported. Please try a simpler expression.`

다음은 지원 되는 파워 쿼리 M 함수 목록입니다.

## <a name="column-management"></a>열 관리

* 선택: [테이블. SelectColumns](/powerquery-m/table-selectcolumns)
* 제거: [table.removecolumns](/powerquery-m/table-removecolumns)
* 이름 바꾸기: [RenameColumns](/powerquery-m/table-renamecolumns), [PrefixColumns](/powerquery-m/table-prefixcolumns), [TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* 다시 정렬: [ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>행 필터링

M 함수 테이블을 사용 합니다. 다음 조건을 필터링 하려면 [행을 selectrows.](/powerquery-m/table-selectrows)

* 같음 및 같지 않음
* 숫자, 텍스트 및 날짜 비교 (DateTime은 제외)
* 숫자 정보 (예: [IsEven](/powerquery-m/number-iseven) / [홀수](/powerquery-m/number-iseven) )
* Text를 사용 하는 텍스트 포함 [. Contains](/powerquery-m/text-contains), [StartsWith](/powerquery-m/text-startswith)또는 [텍스트. EndsWith](/powerquery-m/text-endswith)
* 모든 ' IsIn' [date 함수](/powerquery-m/date-functions)를 포함 하는 날짜 범위 ' 
* And, or 또는 not 조건을 사용한 조합

## <a name="adding-and-transforming-columns"></a>열 추가 및 변환

다음 M 함수는 [열을 추가 하거나 변환 합니다.](/powerquery-m/table-addcolumn) [table.transformcolumns](/powerquery-m/table-transformcolumns), [ReplaceValue](/powerquery-m/table-replacevalue), [DuplicateColumn](/powerquery-m/table-duplicatecolumn). 다음은 지원 되는 변환 함수입니다.

* 숫자 산술 연산
* 텍스트 연결
* 날짜 및 시간 산술 연산 (산술 연산자, [AddDays](/powerquery-m/date-adddays), [Date. addmonths](/powerquery-m/date-addmonths), [date. Addmonths](/powerquery-m/date-addquarters), date. [addmonths](/powerquery-m/date-addweeks), [date. addmonths](/powerquery-m/date-addyears))
* 기간을 날짜 및 시간 산술 연산에 사용할 수 있지만 싱크에 기록 하기 전에 다른 형식으로 변환 [해야 합니다 (](/powerquery-m/duration-minutes)산술 연산자, [#duration](/powerquery-m/sharpduration), 기간. [일](/powerquery-m/duration-days), duration [, duration, duration](/powerquery-m/duration-hours), duration. [초](/powerquery-m/duration-seconds), [duration. Totaldays](/powerquery-m/duration-totaldays), [duration. totaldays](/powerquery-m/duration-totalhours), [duration. totaldays](/powerquery-m/duration-totalminutes), duration [. TotalSeconds](/powerquery-m/duration-totalseconds)).    
* 대부분의 standard, 과학적 및 삼각 숫자 함수 (숫자. 계승값, 숫자. 순열 및 숫자 조합을 *제외 하* 고 [작업](/powerquery-m/number-functions#operations), [반올림](/powerquery-m/number-functions#rounding)및 [삼각](/powerquery-m/number-functions#trigonometry) 의 모든 함수)
* [Replace](/powerquery-m/text-replace)([치환, ReplaceText](/powerquery-m/replacer-replacetext), [치환, ReplaceValue](/powerquery-m/replacer-replacevalue), [text. Remove](/powerquery-m/text-remove))
* 위치 텍스트 추출 ([텍스트](/powerquery-m/text-positionof). 위치, 텍스트. [길이](/powerquery-m/text-length), 텍스트 [. 시작](/powerquery-m/text-start), [텍스트. 끝](/powerquery-m/text-end), 텍스트. [중간](/powerquery-m/text-middle), 텍스트. [ReplaceRange](/powerquery-m/text-replacerange), [텍스트. RemoveRange](/powerquery-m/text-removerange))
* 기본 텍스트 서식 ([텍스트. 아래쪽](/powerquery-m/text-lower), [텍스트. 위쪽](/powerquery-m/text-upper), [텍스트. Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [end](/powerquery-m/text-trimend), [PadStart](/powerquery-m/text-padstart) / [end](/powerquery-m/text-padend), [text. Reverse](/powerquery-m/text-reverse))
* 날짜/시간 함수 ([date, Day](/powerquery-m/date-day), [date. Month](/powerquery-m/date-month), [Date. Year](/powerquery-m/date-year) [time. Hour](/powerquery-m/time-hour), [time. Minute](/powerquery-m/time-minute), [Second](/powerquery-m/time-second), [date. DayOfWeek](/powerquery-m/date-dayofweek), [DayOfYear](/powerquery-m/date-dayofyear), [date. DaysInMonth](/powerquery-m/date-daysinmonth))
* 식 (그러나 분기에 일치 하는 형식이 있어야 함)
* 논리적 열로 행 필터
* 숫자, 텍스트, 논리, 날짜 및 날짜/시간 상수

<a name="mergingjoining-tables"></a>테이블 병합/조인
----------------------
* 중첩 된 조인 (NestedJoin; 사용자는 수동으로 [table. AddJoinColumn](/powerquery-m/table-addjoincolumn))을 생성할 수도 있습니다. 파워 쿼리
    그러면 사용자가 중첩 된 조인 열을 중첩 되지 않은 조인 (Table.expandtablecolumn table.expandtablecolumn, 다른 컨텍스트에서 지원 되지 않음)으로 확장 해야 합니다.
* M 함수 테이블을 직접 작성 하 여 추가 확장 단계가 필요 하지 않도록 할 수 있지만, 사용자가 조인 된 테이블 간에 중복 된 열 이름이 없는지 확인 해야 합니다 [.](/powerquery-m/table-join)
* 지원 되는 조인 종류:   [Inner](/powerquery-m/joinkind-inner),   [LeftOuter](/powerquery-m/joinkind-leftouter),   [rightouter](/powerquery-m/joinkind-rightouter),   [fullouter](/powerquery-m/joinkind-fullouter)
* 두   [값 모두 Equals](/powerquery-m/value-equals) 및   [value. NullableEquals](/powerquery-m/value-nullableequals) 는 키 같음 비교자로 지원 됩니다.

## <a name="group-by"></a>Group By

[표. Group](/powerquery-m/table-group) 을 사용 하 여 값을 집계 합니다.
* 집계 함수와 함께 사용 해야 합니다.
* 지원 되는 집계 함수:   [list. Sum](/powerquery-m/list-sum),   [list. Count](/powerquery-m/list-count),   [list. Average](/powerquery-m/list-average),   [list. Min](/powerquery-m/list-min),   [list. Max](/powerquery-m/list-max),   [List. standarddeviation](/powerquery-m/list-standarddeviation),   [list. First](/powerquery-m/list-first),   [list. Last](/powerquery-m/list-last)

## <a name="sorting"></a>정렬

표를 사용 하 여 값을 정렬 합니다 [.](/powerquery-m/table-sort)

## <a name="reducing-rows"></a>행 줄이기

유지 및 제거 범위 (해당 M 함수는 조건을 제외 하 고, 테이블. [FirstN](/powerquery-m/table-firstn), [table. Skip](/powerquery-m/table-skip), [table. removefirstn](/powerquery-m/table-removefirstn), [Table. range](/powerquery-m/table-range), [table. Minn](/powerquery-m/table-minn), [table. maxn](/powerquery-m/table-maxn))를 유지 합니다.

## <a name="known-unsupported-functions"></a>지원 되지 않는 알려진 함수

| 함수 | 상태 |
| -- | -- |
| Table.PromoteHeaders | 지원되지 않습니다. 데이터 집합에서 "First row as header"를 설정 하 여 동일한 결과를 얻을 수 있습니다. |
| Table.CombineColumns | 이는 직접 지원 되지 않지만 지정 된 두 열을 연결 하는 새 열을 추가 하 여 달성할 수 있는 일반적인 시나리오입니다.  예: Table. AddColumn (RemoveEmailColumn, "Name", each [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | 이는 대부분의 경우 지원 됩니다. 다음 시나리오는 지원 되지 않습니다. 문자열을 통화 형식으로 변환, 문자열을 시간 형식으로 변환, 문자열을 백분율 형식으로 변환 |
| Table.NestedJoin | 조인을 수행 하면 유효성 검사 오류가 발생 합니다. 열이 제대로 작동 하려면 확장 해야 합니다. |
| Table.Distinct | 중복 행 제거는 지원 되지 않습니다. |
| Table.RemoveLastN | 하위 행 제거는 지원 되지 않습니다. |
| Table.RowCount | 지원 되지 않지만 값 1을 포함 하는 사용자 지정 열을 추가한 다음이 열을 Sum으로 집계 하 여 달성할 수 있습니다. 테이블. 그룹이 지원 됩니다. | 
| 행 수준 오류 처리 | 행 수준 오류 처리는 현재 지원 되지 않습니다. 예를 들어 열에서 숫자가 아닌 값을 필터링 하려면 텍스트 열을 숫자로 변환 하는 방법 중 하나를 사용 합니다. 변환에 실패 하는 모든 셀은 오류 상태가 되며 필터링 해야 합니다. 이 시나리오는 랭 글 링 데이터 흐름에서 사용할 수 없습니다. |
| Table.Transpose | 지원되지 않음 |
| Table.Pivot | 지원되지 않음 |

## <a name="next-steps"></a>다음 단계

[랭 글 링 데이터 흐름을 만드는](wrangling-data-flow-tutorial.md)방법에 대해 알아봅니다.
