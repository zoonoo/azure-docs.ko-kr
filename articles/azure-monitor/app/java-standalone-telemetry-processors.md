---
title: 원격 분석 프로세서 (미리 보기)-Java 용 Azure Monitor Application Insights
description: Java 용 Azure Monitor Application Insights에서 원격 분석 프로세서를 구성 하는 방법
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133177"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>원격 분석 프로세서 (미리 보기)-Java 용 Azure Monitor Application Insights

> [!NOTE]
> 이 기능은 아직 미리 보기 상태입니다.

Application Insights 용 Java 3.0 에이전트는 데이터를 내보내기 전에 원격 분석 데이터를 처리 하는 기능을 제공 합니다.

다음은 원격 분석 프로세서의 몇 가지 사용 사례입니다.
 * 중요 한 데이터 마스킹
 * 조건부로 사용자 지정 차원 추가
 * 집계 및 표시에 사용 되는 원격 분석 이름을 업데이트 합니다.
 * 수집 비용을 제어 하는 범위 특성 삭제 또는 필터링

## <a name="terminology"></a>용어

원격 분석 프로세서로 이동 하기 전에 추적 및 범위 정의를 이해 하는 것이 중요 합니다.

### <a name="traces"></a>추적

추적은 `trace` 응용 프로그램을 구성 하는 서비스에서 처리 하는 것 처럼 단일 요청의 진행 상태를 추적 합니다. 사용자 또는 응용 프로그램에서 요청을 시작할 수 있습니다. 의 각 작업 단위를 `trace` 라고 하며 `span` ,는 `trace` 범위의 트리입니다. 는 `trace` 단일 루트 범위와 임의 개수의 자식 범위로 구성 됩니다.

### <a name="span"></a>거칠

범위는 시스템을 통해 흐를 때 요청과 관련 된 개별 서비스 또는 구성 요소에 의해 수행 되는 작업을 나타내는 개체입니다. 에는 `span` `span context` 각 범위가 포함 되는 고유한 요청을 나타내는 guid (globally unique identifier) 집합인이 포함 되어 있습니다. 

확장 캡슐화:

* 범위 이름
* `SpanContext`범위를 고유 하 게 식별 하는 변경할 수 없는입니다.
* `Span`, 또는 null 형식의 부모 범위 `SpanContext`
* `SpanKind`
* 시작 타임 스탬프
* 종료 타임 스탬프
* [`Attributes`](#attributes)
* 타임 스탬프 이벤트 목록
* `Status`

일반적으로 범위의 수명 주기는 다음과 유사 합니다.

* 서비스에서 요청을 수신 합니다. 범위 컨텍스트는 요청 헤더 (있는 경우)에서 추출 됩니다.
* 새 범위는 추출 된 span 컨텍스트의 자식으로 생성 됩니다. 이 항목이 없으면 새 루트 범위가 만들어집니다.
* 서비스에서 요청을 처리 합니다. 요청을 처리 하는 컴퓨터의 호스트 이름 또는 고객 식별자와 같은 요청 컨텍스트를 이해 하는 데 유용한 추가 특성 및 이벤트가 범위에 추가 됩니다.
* 서비스의 하위 구성 요소에 의해 수행 되는 작업을 나타내는 새로운 범위를 만들 수 있습니다.
* 서비스가 다른 서비스에 대 한 원격 호출을 수행 하면 현재 범위 컨텍스트가 직렬화 되 고 헤더 또는 메시지 봉투에 범위 컨텍스트를 삽입 하 여 다음 서비스에 전달 됩니다.
* 서비스에서 수행 하는 작업이 성공적으로 완료 되었습니다. 범위 상태가 적절 하 게 설정 되 고 범위는 완료로 표시 됩니다.

### <a name="attributes"></a>특성

`Attributes` 는에 캡슐화 된 0 개 이상의 키-값 쌍 목록입니다 `span` . 특성에는 다음과 같은 속성이 있어야 합니다.

Null이 아니고 비어 있지 않은 문자열 이어야 하는 특성 키입니다.
특성 값으로, 다음 중 하나입니다.
* 기본 형식은 문자열, 부울, 배정밀도 부동 소수점 (IEEE 754-1985) 또는 부호 있는 64 비트 정수입니다.
* 기본 형식 값의 배열입니다. 배열에는 유형이 같아야 합니다. 즉, 다른 유형의 값을 포함 하면 안 됩니다. 기본적으로 배열 값을 지원 하지 않는 프로토콜의 경우 이러한 값은 JSON 문자열로 표시 되어야 합니다.

## <a name="supported-processors"></a>지원 되는 프로세서:
 * 특성 프로세서
 * 범위 프로세서

## <a name="to-get-started"></a>시작하려면

이라는 구성 파일을 만들고와 `applicationinsights.json` 동일한 디렉터리에 `applicationinsights-agent-***.jar` 다음 템플릿을 사용 하 여 저장 합니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>포함/제외 범위

특성 프로세서와 범위 프로세서는 일치 시킬 범위의 속성 집합을 제공 하는 옵션을 노출 하 여, 원격 분석 프로세서에서 범위를 포함할지 또는 제외할지를 결정 합니다. 이 옵션을 구성 하려면 `include` 및/또는 `exclude` 하나 이상의 `matchType` 및 또는 중 하나가 `spanNames` `attributes` 필요 합니다. 포함/제외 구성은 지정 된 조건을 두 개 이상 포함할 수 있습니다. 일치가 발생 하려면 지정 된 모든 조건이 true로 평가 되어야 합니다. 

**필수 필드**: 
* `matchType` 및 배열의 항목을 해석 하는 방법을 제어 `spanNames` `attributes` 합니다. 가능한 값은 `regexp` 또는 `strict`입니다. 

**선택적 필드**: 
* `spanNames` 항목 중 하나 이상과 일치 해야 합니다. 
* `attributes` 일치 시킬 특성 목록을 지정 합니다. 일치 항목 일치가 발생 하려면 이러한 특성이 모두 정확히 일치 해야 합니다.

> [!NOTE]
> 및를 모두 지정 하는 경우 속성 `include` `exclude` `include` 전에 속성이 확인 됩니다 `exclude` .

#### <a name="sample-usage"></a>샘플 사용

```json

"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
더 이해 하려면 [원격 분석 프로세서 예제](./java-standalone-telemetry-processors-examples.md) 설명서를 확인 하세요.

## <a name="attribute-processor"></a>특성 프로세서 

특성 프로세서는 범위의 특성을 수정 합니다. 필요에 따라 범위를 포함/제외 하는 기능을 지원 합니다. 구성 파일에 지정 된 순서 대로 수행 되는 작업 목록을 사용 합니다. 지원 되는 작업은 다음과 같습니다.

### `insert`

키가 아직 없는 범위에 새 특성을 삽입 합니다.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      },
    ]
  }
]
```
작업의 경우 `insert` 다음이 필요 합니다.
  * `key`
  * 또는 중 하나 `value``fromAttribute`
  * `action`:`insert`

### `update`

키가 있는 범위에서 특성을 업데이트 합니다.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      },
    ]
  }
]
```
작업의 경우 `update` 다음이 필요 합니다.
  * `key`
  * 또는 중 하나 `value``fromAttribute`
  * `action`:`update`


### `delete` 

범위에서 특성을 삭제 합니다.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      },
    ]
  }
]
```
작업의 경우 `delete` 다음이 필요 합니다.
  * `key`
  * `action`: `delete`

### `hash`

해시 (SHA1) 기존 특성 값

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      },
    ]
  }
]
```
작업의 경우 `hash` 다음이 필요 합니다.
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> 이 기능은 3.0.1 이상에만 해당 됩니다.

입력 키에서 규칙에 지정 된 대상 키에 대 한 정규식 규칙을 사용 하 여 값을 추출 합니다. 대상 키가 이미 있으면 재정의 됩니다. 기존 특성을 소스로 사용 하는 [범위 프로세서](#extract-attributes-from-span-name) 설정과 유사 하 게 작동 합니다 `toAttributes` .

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      },
    ]
  }
]
```
작업의 경우 `extract` 다음이 필요 합니다.
* `key`
* `pattern`
* `action` : `extract`

더 이해 하려면 [원격 분석 프로세서 예제](./java-standalone-telemetry-processors-examples.md) 설명서를 확인 하세요.

## <a name="span-processors"></a>프로세서 범위

범위 프로세서는 범위 이름 또는 범위 이름을 기반으로 범위의 특성을 수정 합니다. 필요에 따라 범위를 포함/제외 하는 기능을 지원 합니다.

### <a name="name-a-span"></a>범위 이름

이름 섹션의 일부로 다음 설정이 필요 합니다.

* `fromAttributes`: 키에 대 한 특성 값은 구성에 지정 된 순서에 따라 새 이름을 만드는 데 사용 됩니다. 프로세서의 이름을 바꾸려면 범위에서 모든 특성 키를 지정 해야 합니다.

선택적으로 구성할 수 있는 설정은 다음과 같습니다.

* `separator`: 지정 된 문자열은 값을 분할 하는 데 사용 됩니다.
> [!NOTE]
> 이름 바꾸기가 특성 프로세서에 의해 수정 되는 특성에 종속 되는 경우 파이프라인 사양의 특성 프로세서 뒤에 범위 프로세서가 지정 되어 있는지 확인 합니다.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>범위 이름에서 특성 추출

식 목록을 사용 하 여 범위 이름을 일치 시키고 하위 식을 기반으로 특성을 추출 합니다. 섹션 아래에를 지정 해야 합니다 `toAttributes` .

다음 설정이 필요 합니다.

`rules` : 범위 이름에서 특성 값을 추출 하는 규칙의 목록입니다. 범위 이름의 값은 추출 된 특성 이름으로 바뀝니다. 목록의 각 규칙은 regex 패턴 문자열입니다. 범위 이름은 regex에 대해 확인 됩니다. Regex가 일치 하면 regex의 모든 명명 된 하위 식이 특성으로 추출 되어 범위에 추가 됩니다. 각 하위 식 이름은 특성 이름이 되 고 하위 식은 일치 하는 부분으로 특성 값이 됩니다. 범위 이름에서 일치 하는 부분은 추출 된 특성 이름으로 바뀝니다. 범위가 범위에 이미 있는 경우이 특성을 덮어씁니다. 프로세스는 지정 된 순서 대로 모든 규칙에 대해 반복 됩니다. 각 후속 규칙은 이전 규칙을 처리 한 후 출력 되는 범위 이름에 대해 작동 합니다.

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>특성 목록

다음은 원격 분석 프로세서에서 사용할 수 있는 몇 가지 공통 범위 특성의 목록입니다.

### <a name="http-spans"></a>HTTP 범위

| attribute  | Type | Description | 
|---|---|---|
| `http.method` | 문자열 | HTTP 요청 메서드입니다.|
| `http.url` | 문자열 | 형식의 전체 HTTP 요청 URL `scheme://host[:port]/path?query[#fragment]` 입니다. 일반적으로 조각은 HTTP를 통해 전송 되지 않지만, 알려진 경우에는 포함 해야 합니다.|
| `http.status_code` | number | [HTTP 응답 상태 코드](https://tools.ietf.org/html/rfc7231#section-6)입니다.|
| `http.flavor` | 문자열 | 사용 되는 HTTP 프로토콜 종류 |
| `http.user_agent` | 문자열 | 클라이언트에서 보낸 [HTTP 사용자 에이전트](https://tools.ietf.org/html/rfc7231#section-5.5.3) 헤더의 값입니다. |

### <a name="jdbc-spans"></a>JDBC 범위

| attribute  | Type | Description  |
|---|---|---|
| `db.system` | 문자열 | 사용 중인 DBMS (데이터베이스 관리 시스템) 제품의 식별자입니다. |
| `db.connection_string` | 문자열 | 데이터베이스에 연결 하는 데 사용 되는 연결 문자열입니다. 포함 된 자격 증명을 제거 하는 것이 좋습니다.|
| `db.user` | 문자열 | 데이터베이스에 액세스 하기 위한 사용자 이름입니다. |
| `db.name` | 문자열 | 이 특성은 액세스 하는 데이터베이스의 이름을 보고 하는 데 사용 됩니다. 데이터베이스를 전환 하는 명령의 경우 명령이 실패 하는 경우에도 대상 데이터베이스로 설정 되어야 합니다.|
| `db.statement` | 문자열 | 실행 중인 데이터베이스 문입니다.|