---
title: Azure Data Factory의 랭 글 링 데이터 흐름 변환 함수 | Microsoft Docs
description: Azure Data Factory에서 사용 가능한 랭 글 링 데이터 흐름 함수 개요
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 73ad0b39e119a9ef33fa1fa595ddcc47f61ae163
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518555"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>랭 글 링 데이터 흐름의 변환 함수

Azure Data Factory의 랭 글 링 데이터 흐름을 사용 하면 클라우드 규모에서 코드 없이 agile 데이터를 준비 하 고 랭 글 링 수 있습니다. 랭 글 링 데이터 흐름은 [온라인 파워 쿼리](https://docs.microsoft.com/powerquery-m/power-query-m-reference) 와 통합 되며 spark 실행을 통해 데이터 랭 글 링에 파워 쿼리 M 함수를 사용할 수 있습니다. 

현재 모든 파워 쿼리 M 함수는 제작 중에도 사용할 수 있는 데이터 랭 글 링 지원 되지 않습니다. 랭 글 링 데이터 흐름을 작성 하는 동안 함수가 지원 되지 않으면 다음과 같은 오류 메시지가 표시 됩니다.

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

다음은 지원 되는 파워 쿼리 M 함수 목록입니다.

## <a name="column-management"></a>열 관리

* 선택: [테이블. SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* 제거: [table.removecolumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* 이름 바꾸기: [RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* 다시 정렬: [ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>행 필터링

M 함수 테이블을 사용 합니다. 다음 조건을 필터링 하려면 [행을 selectrows.](https://docs.microsoft.com/powerquery-m/table-selectrows)

* 같음 및 같지 않음
* 숫자, 텍스트 및 날짜 비교 (DateTime은 제외)
* 숫자 정보 (예: [IsEven](https://docs.microsoft.com/powerquery-m/number-iseven) [)/](https://docs.microsoft.com/powerquery-m/number-iseven)
* Text를 사용 하는 텍스트 포함 [. Contains](https://docs.microsoft.com/powerquery-m/text-contains), [StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)또는 [텍스트. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* 모든 ' IsIn' [date 함수](https://docs.microsoft.com/powerquery-m/date-functions)를 포함 하는 날짜 범위 ' 
* And, or 또는 not 조건을 사용한 조합

## <a name="adding-and-transforming-columns"></a>열 추가 및 변환

다음 M 함수는 [열을 추가 하거나 변환 합니다.](https://docs.microsoft.com/powerquery-m/table-addcolumn) [table.transformcolumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). 다음은 지원 되는 변환 함수입니다.

* 숫자 산술 연산
* 텍스트 연결
* 날짜 및 시간 산술 연산 (산술 연산자, [AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date. addmonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [date. Addmonths](https://docs.microsoft.com/powerquery-m/date-addquarters), date. [addmonths](https://docs.microsoft.com/powerquery-m/date-addweeks), [date. addmonths](https://docs.microsoft.com/powerquery-m/date-addyears))
* 기간은 날짜 및 시간 산술 연산에 사용할 수 있지만, 싱크에 기록 하기 전에 다른 형식으로 변환 해야 합니다 (산술 연산자, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [기간. 일](https://docs.microsoft.com/powerquery-m/duration-days), [duration](https://docs.microsoft.com/powerquery-m/duration-hours), [duration. 분](https://docs.microsoft.com/powerquery-m/duration-minutes), [ Duration. 초](https://docs.microsoft.com/powerquery-m/duration-seconds), [Duration. totaldays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [duration. Totaldays](https://docs.microsoft.com/powerquery-m/duration-totalhours), [duration. totaldays](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [duration. TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* 대부분의 standard, 과학적 및 삼각 숫자 함수 (숫자. 계승값, 숫자. 순열 및 숫자 조합을 *제외 하* 고 [작업](https://docs.microsoft.com/powerquery-m/number-functions#operations), [반올림](https://docs.microsoft.com/powerquery-m/number-functions#rounding)및 [삼각](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) 의 모든 함수)
* [Replace](https://docs.microsoft.com/powerquery-m/text-replace)([치환, ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [치환, ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [text. Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* 위치 텍스트 추출 ([텍스트](https://docs.microsoft.com/powerquery-m/text-positionof). 위치, 텍스트. [길이](https://docs.microsoft.com/powerquery-m/text-length), 텍스트 [. 시작](https://docs.microsoft.com/powerquery-m/text-start), [텍스트. 끝](https://docs.microsoft.com/powerquery-m/text-end), 텍스트. [중간](https://docs.microsoft.com/powerquery-m/text-middle), 텍스트. [ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [텍스트. RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* 기본 텍스트 서식 지정 ([text. Lower](https://docs.microsoft.com/powerquery-m/text-lower), [text. Upper](https://docs.microsoft.com/powerquery-m/text-upper), [text. Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[end](https://docs.microsoft.com/powerquery-m/text-trimend), [PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[end](https://docs.microsoft.com/powerquery-m/text-padend), [text. Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* 날짜/시간 함수 ([date, Day](https://docs.microsoft.com/powerquery-m/date-day), [date. Month](https://docs.microsoft.com/powerquery-m/date-month), [Date. Year](https://docs.microsoft.com/powerquery-m/date-year) [time. Hour](https://docs.microsoft.com/powerquery-m/time-hour), [time. Minute](https://docs.microsoft.com/powerquery-m/time-minute), [Second](https://docs.microsoft.com/powerquery-m/time-second), [date. DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [date. DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* 식 (그러나 분기에 일치 하는 형식이 있어야 함)
* 논리적 열로 행 필터
* 숫자, 텍스트, 논리, 날짜 및 날짜/시간 상수

<a name="mergingjoining-tables"></a>테이블 병합/조인
----------------------
* 중첩 된 조인 (NestedJoin; 사용자는 수동으로 [table. AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn))을 생성할 수도 있습니다. 파워 쿼리
    그러면 사용자가 중첩 된 조인 열을 중첩 되지 않은 조인 (Table.expandtablecolumn table.expandtablecolumn, 다른 컨텍스트에서 지원 되지 않음)으로 확장 해야 합니다.
* M 함수 테이블을 직접 작성 하 여 추가 확장 단계가 필요 하지 않도록 할 수 있지만, 사용자가 조인 된 테이블 간에 중복 된 열 이름이 없는지 확인 해야 합니다 [.](https://docs.microsoft.com/powerquery-m/table-join)
* 지원 되는 조인 종류: [Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [rightouter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [fullouter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* 두 [값 모두 Equals](https://docs.microsoft.com/powerquery-m/value-equals) 및 [value. NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) 는 키 같음 비교자로 지원 됩니다.

## <a name="group-by"></a>그룹화 방법

[표. Group](https://docs.microsoft.com/powerquery-m/table-group) 을 사용 하 여 값을 집계 합니다.
* 집계 함수와 함께 사용 해야 합니다.
* 지원 되는 집계 함수: [테이블. RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [list. Sum](https://docs.microsoft.com/powerquery-m/list-sum), [list. Count](https://docs.microsoft.com/powerquery-m/list-count), [list. Average](https://docs.microsoft.com/powerquery-m/list-average), [list. Min](https://docs.microsoft.com/powerquery-m/list-min), [List. Max](https://docs.microsoft.com/powerquery-m/list-max), [list. standarddeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), [list. First](https://docs.microsoft.com/powerquery-m/list-first), [list. Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>기능

표를 사용 하 여 값을 정렬 합니다 [.](https://docs.microsoft.com/powerquery-m/table-sort)

## <a name="reducing-rows"></a>행 줄이기

유지 및 제거 범위 (해당 M 함수는 조건을 제외 하 고, 테이블. [FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [table. Skip](https://docs.microsoft.com/powerquery-m/table-skip), [table. removefirstn](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table. range](https://docs.microsoft.com/powerquery-m/table-range), [table. Minn](https://docs.microsoft.com/powerquery-m/table-minn), [table. maxn](https://docs.microsoft.com/powerquery-m/table-maxn))를 유지 합니다.

## <a name="known-unsupported-functionality"></a>알려진 지원 되지 않는 기능

다음은 지원 되지 않는 함수입니다. 이 목록은 완전 하지 않으며 변경 될 수 있습니다.
* 열 병합 (AddColumn을 사용 하 여 달성할 수 있음)
* 열 분할
* 쿼리 추가
* ' 첫 번째 행을 헤더로 사용 ' 및 ' 첫 번째 행으로 헤더 사용 '

## <a name="next-steps"></a>다음 단계
