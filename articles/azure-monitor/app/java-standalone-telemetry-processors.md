---
title: 원격 분석 프로세서(미리 보기) - Java용 Azure Monitor Application Insights
description: Java용 Azure Monitor Application Insights에 원격 분석 프로세서 구성 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 79d46d7f967ec5d6a223b5ec8690536b3a84162a
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061545"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>원격 분석 프로세서(미리 보기) - Java용 Azure Monitor Application Insights

> [!NOTE]
> 원격 분석 프로세서 기능은 미리 보기 상태입니다.

Application Insights Java 3.x는 데이터를 내보내기 전에 원격 분석 데이터를 처리할 수 있습니다.

다음은 원격 분석 프로세서 사용 사례입니다.
 * 중요한 데이터를 마스크합니다.
 * 조건부로 사용자 지정 차원을 추가합니다.
 * Azure Portal에서 유사한 원격 분석을 집계하는 데 사용되는 범위 이름을 업데이트합니다.
 * 수집 비용을 제어하기 위해 특정 범위 특성을 삭제합니다.
 * 일부 메트릭을 필터링하여 수집 비용을 제어합니다.

> [!NOTE]
> 수집 비용을 제어하기 위해 특정(전체) 범위를 삭제하려는 경우 [샘플링 재정의](./java-standalone-sampling-overrides.md)를 참조하세요.

## <a name="terminology"></a>용어

원격 분석 프로세서에 대해 알아보기 전에 ‘범위’ 및 ‘로그’라는 용어를 이해해야 합니다.

범위는 다음 중 하나를 나타내는 원격 분석 유형입니다.

* 들어오는 요청.
* 나가는 종속성(예: 다른 서비스에 대한 원격 호출).
* In-process 종속성(예: 서비스의 하위 구성 요소에 의해 수행되는 작업).

로그는 다음을 나타내는 원격 분석 유형입니다.

* log4j, logback 및 java.util.logging에서 캡처한 로그 데이터 

원격 분석 프로세서의 경우 다음 범위/로그 구성 요소가 중요합니다.

* 속성
* 본문
* 특성

범위 이름은 Azure Portal의 요청 및 종속성에 대한 기본 디스플레이입니다. 범위 특성은 지정된 요청 또는 종속성의 표준 및 사용자 지정 속성을 모두 나타냅니다.

추적 메시지 또는 본문은 Azure Portal에서 로그에 대한 기본 표시입니다. 로그 특성은 지정된 로그의 표준 및 사용자 지정 속성을 모두 나타냅니다.

## <a name="telemetry-processor-types"></a>원격 분석 프로세서 유형

현재, 4가지 유형의 원격 분석 프로세서는 특성 프로세서, 범위 프로세서, 로그 프로세서 및 메트릭 필터입니다.

특성 프로세서는 원격 분석 항목(`span` 또는 `log`)의 특성을 삽입, 업데이트, 삭제 또는 해시할 수 있습니다.
정규식을 사용하여 기존 특성에서 하나 이상의 새 특성을 추출할 수도 있습니다.

범위 프로세서는 요청 및 의존성의 원격 분석 이름을 업데이트할 수 있습니다.
정규식을 사용하여 범위 이름에서 하나 이상의 새 특성을 추출할 수도 있습니다.

로그 프로세서는 로그의 원격 분석 이름을 업데이트할 수 있습니다.
정규식을 사용하여 로그 이름에서 하나 이상의 새 특성을 추출할 수도 있습니다.

메트릭 필터는 메트릭을 필터링하여 수집 비용을 제어할 수 있습니다.

> [!NOTE]
> 현재 원격 분석 프로세서는 문자열 형식의 특성만 처리합니다. 부울 또는 숫자 형식의 특성은 처리하지 않습니다.

## <a name="getting-started"></a>시작

시작하려면 *applicationinsights.json* 이라는 구성 파일을 만듭니다. 이를 동일한 디렉터리에 *applicationinsights-agent-\*.jar* 로 저장합니다. 다음 템플릿을 사용합니다.

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
      },
      {
        "type": "log",
        ...
      },
      {
        "type": "metric-filter",
        ...
      }
    ]
  }
}
```

## <a name="attribute-processor"></a>특성 프로세서

특성 프로세서는 `span` 또는 `log`의 특성을 수정합니다. `span` 또는 `log`를 포함하거나 제외하는 기능을 지원할 수 있습니다. 구성 파일에서 지정하는 순서대로 수행되는 작업 목록을 사용합니다. 프로세서는 다음 작업을 지원합니다.

- `insert`
- `update`
- `delete`
- `hash`
- `extract`

### `insert`

`insert` 작업은 `key`가 아직 없는 원격 분석 항목에 새 특성을 삽입합니다.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
`insert` 작업은 다음 설정이 필요합니다.
* `key`
* `value` 또는 `fromAttribute`
* `action`: `insert`

### `update`

`update` 작업은 `key`가 이미 있는 원격 분석 항목의 특성을 업데이트합니다.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
`update` 작업은 다음 설정이 필요합니다.
* `key`
* `value` 또는 `fromAttribute`
* `action`: `update`


### `delete` 

`delete` 작업은 원격 분석 항목에서 특성을 삭제합니다.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
`delete` 작업은 다음 설정이 필요합니다.
* `key`
* `action`: `delete`

### `hash`

`hash` 작업은 기존 특성 값을 해시(SHA1)합니다.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
`hash` 작업은 다음 설정이 필요합니다.
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> `extract` 기능은 버전 3.0.2 이상에서만 사용할 수 있습니다.

`extract` 작업은 입력 키에서 규칙을 지정하는 대상 키로 정규식 규칙을 사용하여 값을 추출합니다. 대상 키가 이미 있으면 재정의됩니다. 이 동작은 기존 특성이 원본인 [범위 프로세서](#extract-attributes-from-the-span-name) `toAttributes` 설정처럼 동작합니다.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
`extract` 작업은 다음 설정이 필요합니다.
* `key`
* `pattern`
* `action`: `extract`

### <a name="include-criteria-and-exclude-criteria"></a>include 조건 및 exclude 조건

특성 프로세서는 선택적 `include` 및 `exclude` 조건을 지원합니다.
특성 프로세서는 해당 `include` 조건(제공되는 경우)과 일치 _하면서_ `exclude` 조건(제공되는 경우)과 일치하지 않는 원격 분석에만 적용됩니다.

이 옵션을 구성하려면 `include` 또는 `exclude`(또는 둘 다)에서 `matchType` 및 `spanNames`이나 `attributes` 중 하나 이상을 지정합니다.
include-exclude(포함-제외) 구성에서는 둘 이상의 조건을 지정할 수 있습니다.
일치하는 결과를 얻으려면 지정된 모든 조건이 true로 평가되어야 합니다. 

* **필수 필드**: `matchType`은 `spanNames` 배열과 `attributes` 배열의 항목을 해석하는 방법을 제어합니다. 가능한 값은 `regexp` 및 `strict`입니다. 

* **선택적 필드**: 
    * `spanNames`는 하나 이상의 항목과 일치해야 합니다. 
    * `attributes`는 일치시킬 특성 목록을 지정합니다. 일치하는 결과를 얻으려면 이러한 특성은 모두 정확히 일치해야 합니다.
    
> [!NOTE]
> `include` 및 `exclude` 모두 지정하는 경우 `exclude` 속성이 확인되기 전에 `include` 속성이 확인됩니다.

> [!NOTE]
> `include` 또는 `exclude` 구성에 `spanNames`가 지정되지 않은 경우 일치 조건이 `spans` 및 `logs` 둘 다에 적용됩니다.

### <a name="sample-usage"></a>샘플 사용

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
자세한 내용은 [원격 분석 프로세서 예시](./java-standalone-telemetry-processors-examples.md)를 참조하세요.

## <a name="span-processor"></a>범위 프로세서

범위 프로세서는 범위 이름 또는 범위 이름을 기반으로 범위의 특성을 수정합니다. 이는 범위를 포함하거나 제외하는 기능을 지원할 수 있습니다.

### <a name="name-a-span"></a>범위 이름 지정

`name` 섹션은 `fromAttributes` 설정이 필요합니다. 이러한 특성 값은 구성에서 지정하는 순서로 연결된 새 이름을 만드는 데 사용됩니다. 프로세서는 범위에 이러한 모든 특성이 있는 경우에만 범위 이름을 변경합니다.

`separator` 설정은 선택 사항입니다. 이 설정은 문자열입니다. 값을 분할하기 위해 지정됩니다.
> [!NOTE]
> 이름 바꾸기가 특성 프로세서를 사용하여 특성을 수정하는 경우 파이프라인 사양의 특성 프로세서가 지정된 후에 범위 프로세서가 지정되었는지 확인합니다.

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

### <a name="extract-attributes-from-the-span-name"></a>범위 이름에서 특성 추출

`toAttributes` 섹션에서는 범위 이름과 일치하는 정규식을 나열합니다. 하위 식을 기반으로 특성을 추출합니다.

`rules` 설정이 필요합니다. 이 설정은 범위 이름에서 특성 값을 추출하는 데 사용되는 규칙을 나열합니다. 

범위 이름의 값은 추출된 특성 이름으로 바뀝니다. 목록의 각 규칙은 정규식(regex) 패턴 문자열입니다. 

추출된 특성 이름으로 값을 대체하는 방법은 다음과 같습니다.

1. 범위 이름이 정규식에 대해 확인됩니다. 
2. 정규식이 일치하면 정규식의 모든 명명된 하위 식이 특성으로 추출됩니다. 
3. 추출된 특성이 범위에 추가됩니다. 
4. 각 하위 식 이름은 특성 이름이 됩니다. 
5. 하위 식과 일치하는 부분은 특성 값이 됩니다. 
6. 범위 이름에서 일치하는 부분이 추출된 특성 이름으로 바뀝니다. 특성이 범위에 이미 있는 경우 해당 특성을 덮어씁니다. 
 
이 프로세스는 지정된 순서대로 모든 규칙에 대해 반복됩니다. 각 후속 규칙은 이전 규칙의 출력인 범위 이름에 대해 작동합니다.

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

## <a name="common-span-attributes"></a>공통 범위 특성

이 섹션에서는 원격 분석 프로세서에서 사용할 수 있는 몇 가지 공통 범위 특성이 나열됩니다.

### <a name="http-spans"></a>HTTP 범위

| attribute  | Type | Description | 
|---|---|---|
| `http.method` | 문자열 | HTTP 요청 메서드입니다.|
| `http.url` | 문자열 | `scheme://host[:port]/path?query[#fragment]` 형식의 전체 HTTP 요청 URL. 조각은 일반적으로 HTTP를 통해 전송되지 않습니다. 하지만 조각이 알려져 있는 경우 이를 포함해야 합니다.|
| `http.status_code` | number | [HTTP 응답 상태 코드](https://tools.ietf.org/html/rfc7231#section-6)입니다.|
| `http.flavor` | 문자열 | HTTP 프로토콜의 유형입니다. |
| `http.user_agent` | 문자열 | 클라이언트에서 보낸 [HTTP 사용자 에이전트](https://tools.ietf.org/html/rfc7231#section-5.5.3) 헤더의 값입니다. |

### <a name="jdbc-spans"></a>JDBC 범위

| attribute  | Type | Description  |
|---|---|---|
| `db.system` | 문자열 | 사용 중인 DBMS(데이터베이스 관리 시스템) 제품의 식별자입니다. |
| `db.connection_string` | 문자열 | 데이터베이스에 연결하는 데 사용되는 연결 문자열입니다. 포함된 자격 증명을 제거하는 것이 좋습니다.|
| `db.user` | 문자열 | 데이터베이스에 액세스하기 위한 사용자 이름입니다. |
| `db.name` | 문자열 | 액세스 중인 데이터베이스의 이름을 보고하는 데 사용되는 문자열입니다. 데이터베이스를 전환하는 명령의 경우 명령이 실패하더라도 이 문자열을 대상 데이터베이스로 설정해야 합니다.|
| `db.statement` | 문자열 | 실행 중인 데이터베이스 문입니다.|

### <a name="include-criteria-and-exclude-criteria"></a>include 조건 및 exclude 조건

범위 프로세서는 선택적 `include` 및 `exclude` 조건을 지원합니다.
범위 프로세서는 해당 `include` 조건(제공되는 경우)과 일치 _하면서_ `exclude` 조건(제공되는 경우)과 일치하지 않는 원격 분석에만 적용됩니다.

이 옵션을 구성하려면 `include` 또는 `exclude`(또는 둘 다)에서 하나 이상의 `matchType`과 `spanNames` 또는 범위 `attributes`를 지정합니다.
include-exclude(포함-제외) 구성에서는 둘 이상의 조건을 지정할 수 있습니다.
일치하는 결과를 얻으려면 지정된 모든 조건이 true로 평가되어야 합니다. 

* **필수 필드**: `matchType`은 `spanNames` 배열과 `attributes` 배열의 항목을 해석하는 방법을 제어합니다. 가능한 값은 `regexp` 및 `strict`입니다. 

* **선택적 필드**: 
    * `spanNames`는 하나 이상의 항목과 일치해야 합니다. 
    * `attributes`는 일치시킬 특성 목록을 지정합니다. 일치하는 결과를 얻으려면 이러한 특성은 모두 정확히 일치해야 합니다.
    
> [!NOTE]
> `include` 및 `exclude` 모두 지정하는 경우 `exclude` 속성이 확인되기 전에 `include` 속성이 확인됩니다.

### <a name="sample-usage"></a>샘플 사용

```json
"processors": [
  {
    "type": "span",
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
          "key": "attribute1",
          "value": "attributeValue1"
        }
      ]
    },
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
자세한 내용은 [원격 분석 프로세서 예시](./java-standalone-telemetry-processors-examples.md)를 참조하세요.

## <a name="log-processor"></a>로그 프로세서

> [!NOTE]
> 로그 프로세서는 버전 3.1.1부터 사용할 수 있습니다.

로그 프로세서는 로그 메시지 본문 또는 로그 메시지 본문을 기준으로 하는 로그의 특성을 수정합니다. 로그를 포함하거나 제외하는 기능을 지원할 수 있습니다.

### <a name="update-log-message-body"></a>로그 메시지 본문 업데이트

`body` 섹션은 `fromAttributes` 설정이 필요합니다. 이러한 특성 값은 구성에서 지정하는 순서로 연결된 새 본문을 만드는 데 사용됩니다. 프로세서는 로그에 이러한 모든 특성이 있는 경우에만 로그 본문을 변경합니다.

`separator` 설정은 선택 사항입니다. 이 설정은 문자열입니다. 값을 분할하기 위해 지정됩니다.
> [!NOTE]
> 이름 바꾸기가 특성 프로세서를 사용하여 특성을 수정하는 경우 파이프라인 사양의 특성 프로세서가 지정된 후에 로그 프로세서가 지정되었는지 확인합니다.

```json
"processors": [
  {
    "type": "log",
    "body": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-the-log-message-body"></a>로그 메시지 본문에서 특성 추출

`toAttributes` 섹션에서는 로그 메시지 본문과 일치하는 정규식을 나열합니다. 하위 식을 기반으로 특성을 추출합니다.

`rules` 설정이 필요합니다. 이 설정은 본문에서 특성 값을 추출하는 데 사용되는 규칙을 나열합니다. 

로그 메시지 본문의 값은 추출된 특성 이름으로 바뀝니다. 목록의 각 규칙은 정규식(regex) 패턴 문자열입니다. 

추출된 특성 이름으로 값을 대체하는 방법은 다음과 같습니다.

1. 로그 메시지 본문이 정규식에 대해 검사됩니다. 
2. 정규식이 일치하면 정규식의 모든 명명된 하위 식이 특성으로 추출됩니다. 
3. 추출된 특성이 로그에 추가됩니다. 
4. 각 하위 식 이름은 특성 이름이 됩니다. 
5. 하위 식과 일치하는 부분은 특성 값이 됩니다. 
6. 로그 이름에서 일치하는 부분이 추출된 특성 이름으로 바뀝니다. 특성이 로그에 이미 있는 경우 해당 특성을 덮어씁니다. 
 
이 프로세스는 지정된 순서대로 모든 규칙에 대해 반복됩니다. 각 후속 규칙은 이전 규칙의 출력인 로그 이름에 대해 작동합니다.

```json
"processors": [
  {
    "type": "log",
    "body": {
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

### <a name="include-criteria-and-exclude-criteria"></a>include 조건 및 exclude 조건

로그 프로세서는 선택적 `include` 및 `exclude` 조건을 지원합니다.
로그 프로세서는 해당 `include` 조건(제공되는 경우)과 일치 _하면서_ `exclude` 조건(제공되는 경우)과 일치하지 않는 원격 분석에만 적용됩니다.

이 옵션을 구성하려면 `include` 또는 `exclude`(또는 둘 다)에서 `matchType` 및 `attributes`를 지정합니다.
include-exclude(포함-제외) 구성에서는 둘 이상의 조건을 지정할 수 있습니다.
일치하는 결과를 얻으려면 지정된 모든 조건이 true로 평가되어야 합니다. 

* **필수 필드**: 
  * `matchType`은 `attributes` 배열의 항목을 해석하는 방법을 제어합니다. 가능한 값은 `regexp` 및 `strict`입니다. 
  * `attributes`는 일치시킬 특성 목록을 지정합니다. 일치하는 결과를 얻으려면 이러한 특성은 모두 정확히 일치해야 합니다.
    
> [!NOTE]
> `include` 및 `exclude` 모두 지정하는 경우 `exclude` 속성이 확인되기 전에 `include` 속성이 확인됩니다.

> [!NOTE]
> 로그 프로세서는 `spanNames`를 지원하지 않습니다.

### <a name="sample-usage"></a>샘플 사용

```json
"processors": [
  {
    "type": "log",
    "include": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "attribute1",
          "value": "value1"
        }
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "attribute2",
          "value": "value2"
        }
      ]
    },
    "body": {
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
자세한 내용은 [원격 분석 프로세서 예시](./java-standalone-telemetry-processors-examples.md)를 참조하세요.

## <a name="metric-filter"></a>메트릭 필터

> [!NOTE]
> 메트릭 필터는 버전 3.1.1부터 사용할 수 있습니다.

메트릭 필터는 수집 비용을 제어하기 위해 일부 메트릭을 제외하는 데 사용됩니다.

메트릭 필터는 `exclude` 조건만 지원합니다. 해당 `exclude` 조건과 일치하는 메트릭은 내보내지 않습니다.

이 옵션을 구성하려면 `exclude` 아래에서 하나 이상의 `matchType` `metricNames`를 지정합니다.

* **필수 필드**:
  * `matchType`은 `metricNames`의 항목이 일치하는 방식을 제어합니다. 가능한 값은 `regexp` 및 `strict`입니다.
  * `metricNames`는 하나 이상의 항목과 일치해야 합니다.

### <a name="sample-usage"></a>샘플 사용

```json
"processors": [
  {
    "type": "metric-filter",
    "exclude": {
      "matchType": "strict",
      "metricNames": [
        "metricA",
        "metricB"
      ]
    }
  }
]
```
