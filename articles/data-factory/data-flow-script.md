---
title: 데이터 흐름 스크립트 매핑
description: Data Factory의 데이터 흐름 스크립트 코드 숨겨진 언어 개요
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/10/2019
ms.openlocfilehash: d861a4355158dfe18ac3aa40a7f98dc11ebda90b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930251"
---
# <a name="data-flow-script-dfs"></a>데이터 흐름 스크립트 (DFS)

데이터 흐름 스크립트 (DFS)는 코딩 언어와 비슷하며 매핑 데이터 흐름에 포함 된 변환을 실행 하는 데 사용 되는 기본 메타 데이터입니다. 모든 변환은 작업을 제대로 실행 하는 데 필요한 정보를 제공 하는 일련의 속성으로 표시 됩니다. 브라우저 UI의 위쪽 리본에서 "스크립트" 단추를 클릭 하 여 ADF에서 스크립트를 표시 하 고 편집할 수 있습니다.

![스크립트 단추](media/data-flow/scriptbutton.png "스크립트 단추")

예를 들어 원본 변환에서 `allowSchemaDrift: true,`은 스키마 프로젝션에 포함 되지 않은 경우에도 데이터 흐름에 있는 원본 데이터 집합의 모든 열을 포함 하도록 서비스에 지시 합니다.

## <a name="use-cases"></a>사례 사용
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

파생 변환을 추가 하기로 결정 한 경우 먼저 `upperCaseTitle`이라는 새 대문자 열을 추가 하는 간단한 식이 있는 핵심 변환 텍스트를 만들어야 합니다.
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

이제는 새 변환에서 가져올 변환 (이 경우 `source1`)을 식별 하 고 스트림의 이름을 새 변환으로 복사 하 여 들어오는 스트림을 다시 라우팅합니다.
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

마지막으로이 새 변환 후에 제공 하려는 변환을 식별 하 고 입력 스트림 (이 경우 `sink1`)을 새 변환의 출력 스트림 이름으로 바꿉니다.
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

## <a name="next-steps"></a>다음 단계

[데이터 흐름 개요 문서](concepts-data-flow-overview.md) 를 시작 하 여 데이터 흐름 탐색
