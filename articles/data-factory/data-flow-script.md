---
title: 데이터 흐름 스크립트 매핑
description: Data Factory의 데이터 흐름 스크립트 코드 숨겨진 언어 개요
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 8310c34e06d52dc12af42f8bc33f4a4d7e99d68d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598091"
---
# <a name="data-flow-script-dfs"></a>데이터 흐름 스크립트 (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

데이터 흐름 스크립트 (DFS)는 코딩 언어와 비슷하며 매핑 데이터 흐름에 포함 된 변환을 실행 하는 데 사용 되는 기본 메타 데이터입니다. 모든 변환은 작업을 제대로 실행 하는 데 필요한 정보를 제공 하는 일련의 속성으로 표시 됩니다. 브라우저 UI의 위쪽 리본에서 "스크립트" 단추를 클릭 하 여 ADF에서 스크립트를 표시 하 고 편집할 수 있습니다.

![스크립트 단추](media/data-flow/scriptbutton.png "스크립트 단추")

예를 들어 `allowSchemaDrift: true,` 원본 변환에서 원본 데이터 집합의 모든 열이 스키마 프로젝션에 포함 되지 않은 경우에도 해당 데이터 흐름의 모든 열을 포함 하도록 서비스에 지시 합니다.

## <a name="use-cases"></a>사용 사례
DFS는 사용자 인터페이스에 의해 자동으로 생성 됩니다. 스크립트 단추를 클릭 하 여 스크립트를 확인 하 고 사용자 지정할 수 있습니다. ADF UI 외부에서 스크립트를 생성 한 다음 PowerShell cmdlet으로 전달할 수도 있습니다. 복잡 한 데이터 흐름을 디버깅할 때 흐름의 UI 그래프 표현을 검색 하는 대신 스크립트 코드를 검색 하는 것이 더 쉬울 수 있습니다.

몇 가지 예제 사용 사례는 다음과 같습니다.
- 매우 유사한 여러 데이터 흐름 (예: "스탬프 아웃" 데이터 흐름)을 프로그래밍 방식으로 생성 합니다.
- UI에서 관리 하기 어렵거나 유효성 검사 문제가 발생 하는 복잡 한 식입니다.
- 실행 중에 반환 되는 다양 한 오류를 디버깅 하 고 잘 이해 합니다.

PowerShell 또는 API와 함께 사용할 데이터 흐름 스크립트를 작성 하는 경우 서식이 지정 된 텍스트를 한 줄로 축소 해야 합니다. 탭과 줄바꿈을 이스케이프 문자로 유지할 수 있습니다. 그러나 JSON 속성 내에 맞게 텍스트의 서식을 지정 해야 합니다. 스크립트를 한 줄로 서식 지정 하는 스크립트 편집기 UI의 맨 아래에는 단추가 있습니다.

![복사 단추](media/data-flow/copybutton.png "복사 단추")

## <a name="how-to-add-transforms"></a>변환 추가 방법
변환 추가에는 세 가지 기본 단계 (핵심 변환 데이터 추가, 입력 스트림 다시 라우팅, 출력 스트림 다시 라우팅)가 필요 합니다. 이는 예제에서 가장 쉽게 볼 수 있습니다.
간단한 원본으로 시작 하 여 다음과 같은 데이터 흐름을 싱크 한다고 가정해 보겠습니다.

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

파생 변환을 추가 하기로 결정 한 경우 먼저 라는 새 대문자 열을 추가 하는 간단한 식이 있는 핵심 변환 텍스트를 만들어야 합니다 `upperCaseTitle` .
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

그런 다음 기존 DFS를 사용 하 여 변환을 추가 합니다.
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

이제 새 변환을 수행 하려는 변환 (이 경우)을 확인 하 `source1` 고 스트림의 이름을 새 변환으로 복사 하 여 들어오는 스트림을 다시 라우팅합니다.
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

마지막으로이 새 변환 후에 사용할 변환을 식별 하 고 입력 스트림 (이 경우 `sink1` )을 새 변환의 출력 스트림 이름으로 바꿉니다.
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS 기본 사항
DFS는 원본, 싱크 및 새 열을 추가 하 고, 데이터를 필터링 하 고, 데이터를 조인 하는 등의 다양 한 데이터를 포함 하 여 일련의 연결 된 변형으로 구성 됩니다. 일반적으로를 포함 하는 스크립트는 하나 이상의 소스와 많은 변환이 오고 하나 이상의 싱크로 끝납니다.

원본에는 모두 동일한 기본 구성이 있습니다.
```
source(
  source properties
) ~> source_name
```

예를 들어 세 개의 열 (movieId, title, 장르)이 있는 간단한 원본은 다음과 같습니다.
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

원본 이외의 모든 변환에는 동일한 기본 구성이 있습니다.
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

예를 들어 열 (제목)을 가져와서 대문자 버전으로 덮어쓰는 간단한 파생 변환은 다음과 같습니다.
```
source1 derive(
  title = upper(title)
) ~> derive1
```

스키마가 없는 싱크는 다음과 같이 간단 합니다.
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>스크립트 조각

스크립트 조각은 데이터 흐름 전체에서 공유 하는 데 사용할 수 있는 데이터 흐름 스크립트의 공유할 수 있는 코드입니다. 아래 비디오는 스크립트 조각을 사용 하 고 데이터 흐름 스크립트를 활용 하 여 데이터 흐름 그래프 뒤에 스크립트의 일부를 복사 하 여 붙여 넣는 방법을 설명 합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>집계 요약 통계
"요약 통계" 라는 데이터 흐름에 집계 변환을 추가한 다음 스크립트의 집계 함수에 대 한 아래 코드를 붙여넣어 기존 요약 통계를 바꿉니다. 이렇게 하면 데이터 프로필 요약 통계에 대 한 일반 패턴을 제공 합니다.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
아래 샘플을 사용 하 여 데이터의 고유 개수와 고유 행 수를 계산할 수도 있습니다. 다음 예에서는 ValueDistAgg 라는 집계 변환을 사용 하 여 데이터 흐름에 붙여 넣을 수 있습니다. 이 예에서는 "title" 이라는 열을 사용 합니다. 값 개수를 가져오는 데 사용 하려는 데이터의 문자열 열로 "title"을 바꾸어야 합니다.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>집계에 모든 열 포함
이 패턴은 집계를 빌드할 때 출력 메타 데이터에 나머지 열을 유지할 수 있는 방법을 보여 주는 일반 집계 패턴입니다. 이 경우 함수를 사용 하 여 ```first()``` 이름이 "movie"가 아닌 모든 열의 첫 번째 값을 선택 합니다. 이를 사용 하려면 DistinctRows 라는 집계 변환을 만든 다음이를 스크립트에서 기존 DistinctRows 집계 스크립트의 맨 위에 붙여넣습니다.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>행 해시 지문 만들기 
데이터 흐름 스크립트에서 다음 코드를 사용 하 여 ```DWhash``` 세 개의 열에 대 한 해시를 생성 하는 라는 새 파생 열을 만듭니다 ```sha1``` .

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

아래 스크립트를 사용 하 여 각 열의 이름을 지정할 필요 없이 스트림에 있는 모든 열을 사용 하 여 행 해시를 생성할 수도 있습니다.

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg 동일
이 코드는 T-sql 함수와 동일 하 게 작동 ```string_agg()``` 하며 문자열 값을 배열로 집계 합니다. 그런 다음 해당 배열을 SQL 대상과 함께 사용할 문자열로 캐스팅할 수 있습니다.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>업데이트 수, upsert, 삽입, 삭제 수
Alter Row 변환을 사용 하는 경우 Alter Row 정책에서 발생 하는 업데이트의 수, upsert, 삽입, 삭제 수를 계산 하는 것이 좋습니다. Alter row 뒤에 집계 변환을 추가 하 고이 데이터 흐름 스크립트를 해당 개수에 대 한 집계 정의에 붙여넣습니다.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>모든 열을 사용 하는 고유 행
이 코드 조각은 들어오는 모든 열을 사용 하 고, 그룹화에 사용 되는 해시를 생성 하 여 중복 항목을 제거 하 고, 각 중복 항목을 출력으로 제공 하는 새 집계 변환을 데이터 흐름에 추가 합니다. 열 이름을 명시적으로 지정할 필요는 없으며 들어오는 데이터 스트림에서 자동으로 생성 됩니다.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>모든 열에서 Null 확인
이 코드 조각은 일반적으로 모든 열에서 NULL 값을 확인 하기 위해 데이터 흐름에 붙여넣을 수 있는 코드 조각입니다. 이 기법은 스키마 드리프트를 활용 하 여 모든 행의 모든 열을 확인 하 고 조건부 분할을 사용 하 여 null이 없는 행의 행을 Null로 구분 합니다. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

## <a name="next-steps"></a>다음 단계

[데이터 흐름 개요 문서](concepts-data-flow-overview.md) 를 시작 하 여 데이터 흐름 탐색
