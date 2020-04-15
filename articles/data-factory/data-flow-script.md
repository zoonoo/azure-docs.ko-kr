---
title: 데이터 흐름 스크립트 매핑
description: 데이터 팩터리의 데이터 흐름 스크립트 코드 뒤에 있는 언어 개요
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.openlocfilehash: 251507c5740af69bd0818391dd2e8f857338b6cf
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313907"
---
# <a name="data-flow-script-dfs"></a>데이터 흐름 스크립트(DFS)

DFS(데이터 흐름 스크립트)는 매핑 데이터 흐름에 포함된 변환을 실행하는 데 사용되는 코딩 언어와 유사한 기본 메타데이터입니다. 모든 변환은 작업을 제대로 실행하는 데 필요한 정보를 제공하는 일련의 속성으로 표시됩니다. 브라우저 UI의 상단 리본에 있는 "스크립트" 단추를 클릭하여 ADF에서 스크립트를 표시하고 편집할 수 있습니다.

![스크립트 버튼](media/data-flow/scriptbutton.png "스크립트 버튼")

예를 들어 `allowSchemaDrift: true,` 원본 변환에서는 스키마 프로젝션에 포함되지 않은 경우에도 원본 데이터 집합의 모든 열을 데이터 흐름에 포함하도록 서비스에 지시합니다.

## <a name="use-cases"></a>사용 사례
DFS는 사용자 인터페이스에 의해 자동으로 생성됩니다. 스크립트 단추를 클릭하여 스크립트를 보고 사용자 지정할 수 있습니다. ADF UI 외부에서 스크립트를 생성한 다음 PowerShell cmdlet에 전달할 수도 있습니다. 복잡한 데이터 흐름을 디버깅할 때 흐름의 UI 그래프 표현을 검색하는 대신 스크립트 코드 숨결을 더 쉽게 검색할 수 있습니다.

다음은 몇 가지 예제 사용 사례입니다.
- "스탬핑 아웃" 데이터 흐름과 같이 상당히 유사한 많은 데이터 흐름을 프로그래밍식으로 생성합니다.
- UI에서 관리하기 어렵거나 유효성 검사 문제가 발생하는 복잡한 식입니다.
- 실행 중에 반환된 다양한 오류를 디버깅하고 더 잘 이해합니다.

PowerShell 또는 API와 함께 사용할 데이터 흐름 스크립트를 빌드할 때 서식이 지정된 텍스트를 한 줄로 축소해야 합니다. 탭과 줄 바선을 이스케이프 캐릭터로 유지할 수 있습니다. 그러나 텍스트는 JSON 속성 내에 맞게 서식을 지정해야 합니다. 스크립트 편집기 UI 하단에는 스크립트를 한 줄로 서식을 지정하는 단추가 있습니다.

![복사 단추](media/data-flow/copybutton.png "복사 단추")

## <a name="how-to-add-transforms"></a>변환을 추가하는 방법
변환을 추가하려면 코어 변환 데이터를 추가하고 입력 스트림을 다시 라우팅한 다음 출력 스트림을 다시 라우팅하는 세 가지 기본 단계가 필요합니다. 이것은 예에서 가장 쉬운 것을 볼 수 있습니다.
다음과 같이 데이터 흐름을 싱크하는 간단한 소스로 시작한다고 가정 해 봅시다.

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

파생 변환을 추가하려는 경우 먼저 다음과 같은 `upperCaseTitle`새 대문자 열을 추가하는 간단한 식이 있는 핵심 변환 텍스트를 만들어야 합니다.
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

그런 다음 기존 DFS를 도입하고 변환을 추가합니다.
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

이제 들어오는 스트림의 경로를 변경하여 새 변환이 수행될 변환을 식별하고(이 `source1`경우) 스트림 이름을 새 변환에 복사합니다.
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

마지막으로 이 새로운 변환 이후에 수행하려는 변환을 식별하고 입력 스트림(이 `sink1`경우)을 새 변환의 출력 스트림 이름으로 바꿉니다.
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
DFS는 소스, 싱크 및 새 열을 추가하고 데이터를 필터링하고 데이터를 조인하는 등 다양한 변환을 포함하는 일련의 연결된 변환으로 구성됩니다. 일반적으로 스크립트는 하나 이상의 소스로 시작하고 많은 변환이 뒤따르고 하나 이상의 싱크로 끝납니다.

소스는 모두 동일한 기본 구성을 가지고 있습니다.
```
source(
  source properties
) ~> source_name
```

예를 들어 세 개의 열(movieId, 제목, 장르)이 있는 간단한 소스는 다음과 같은 것입니다.
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

소스 이외의 모든 변환에는 동일한 기본 구성이 있습니다.
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

예를 들어 열(title)을 가져와 대문자 버전으로 덮어쓰는 간단한 파생 변환은 다음과 같습니다.
```
source1 derive(
  title = upper(title)
) ~> derive1
```

스키마가 없는 싱크대는 다음과 같은 것입니다.
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>스크립트 조각

스크립트 스니펫은 데이터 흐름 간에 공유하는 데 사용할 수 있는 데이터 흐름 스크립트의 공유 가능한 코드입니다. 아래 비디오에서는 스크립트 스니펫을 사용하고 데이터 흐름 스크립트를 사용하여 데이터 흐름 그래프 뒤에 스크립트 부분을 복사하고 붙여 넣는 방법에 대해 설명합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>집계된 요약 통계
"SummaryStats"라는 데이터 흐름에 집계 변환을 추가 한 다음 스크립트의 집계 함수에 대해 아래 코드에 붙여 넣어 기존 SummaryStats를 대체합니다. 이렇게 하면 데이터 프로필 요약 통계에 대한 일반 패턴이 제공됩니다.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
아래 샘플을 사용하여 데이터의 고유 행 수와 고유 행 수를 계산할 수도 있습니다. 아래 예제는 ValueDistAgg라는 집계 변환을 사용하여 데이터 흐름에 붙여넣을 수 있습니다. 이 예제에서는 "title"이라는 열을 사용합니다. "title"을 값 수를 얻기 위해 사용할 데이터의 문자열 열로 바꿔야 합니다.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>집계에 모든 열 포함
집계를 빌드할 때 출력 메타데이터에 나머지 열을 유지하는 방법을 보여 주는 일반 집계 패턴입니다. 이 경우 이 함수를 ```first()``` 사용하여 이름이 "movie"가 아닌 모든 열에서 첫 번째 값을 선택합니다. 이를 사용하려면 DistinctRows라는 집계 변환을 만든 다음 기존 DistinctRows 집계 스크립트 위에 스크립트에 붙여넣습니다.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>행 해시 지문 만들기 
데이터 흐름 스크립트에서 이 코드를 사용하여 세 개의 ```DWhash``` 열로 ```sha1``` 구성된 해시를 생성하는 새 파생 열을 만듭니다.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>다음 단계

데이터 흐름 [개요 문서로](concepts-data-flow-overview.md) 시작하여 데이터 흐름 탐색
