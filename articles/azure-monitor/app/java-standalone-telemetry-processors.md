---
title: 원격 분석 프로세서 (미리 보기)-Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java 용 원격 분석 프로세서
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 1908e36dcf5b24301a08e543c5cf7cc106af6bff
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380329"
---
# <a name="telemetry-processors-preview-for-azure-monitor-application-insights-java"></a>Azure Monitor Application Insights Java 용 원격 분석 프로세서 (미리 보기)

> [!NOTE]
> 이 기능은 아직 미리 보기 상태입니다.

Application Insights 용 Java 3.0 에이전트는 데이터를 내보내기 전에 원격 분석 데이터를 처리 하는 기능을 제공 합니다.

### <a name="some-use-cases"></a>일부 사용 사례:
 * 중요 한 데이터 마스킹
 * 조건부로 사용자 지정 차원 추가
 * 집계 및 표시에 사용 되는 원격 분석 이름을 업데이트 합니다.

### <a name="supported-processors"></a>지원 되는 프로세서:
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

특성 프로세서와 범위 프로세서는 일치 시킬 범위의 속성 집합을 제공 하는 옵션을 노출 하 여 범위를 프로세서에서 포함할지 또는 제외할지를 결정 합니다. 이 옵션을 구성 하려면 `include` 및/또는 `exclude` 하나 이상의 `matchType` 및 또는 중 하나가 `spanNames` `attributes` 필요 합니다. 포함/제외 구성은 지정 된 조건을 두 개 이상 포함할 수 있습니다. 일치가 발생 하려면 지정 된 모든 조건이 true로 평가 되어야 합니다. 

**필수 필드** : 
* `matchType` 및 배열의 항목을 해석 하는 방법을 제어 `spanNames` `attributes` 합니다. 가능한 값은 `regexp` 또는 `strict`입니다. 

**선택적 필드** : 
* `spanNames` 항목 중 하나 이상과 일치 해야 합니다. 
* `attributes` 일치 시킬 특성 목록을 지정 합니다. 일치 항목 일치가 발생 하려면 이러한 특성이 모두 정확히 일치 해야 합니다.

> [!NOTE]
> 및를 모두 지정 하는 경우 속성 `include` `exclude` `include` 전에 속성이 확인 됩니다 `exclude` .

#### <a name="sample-usage"></a>샘플 사용

다음은이 프로세서가 적용 되는 범위를 나타내는 span 속성 집합을 지정 하는 방법을 보여 줍니다. 속성의는 포함 해야 하는 속성을 `include` 포함 하며 `exclude` 처리 되지 않아야 하는 범위를 추가로 필터링 합니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
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
  }
}
```

위의 구성을 사용 하는 경우 다음과 같은 범위가 속성 및 프로세서 동작이 적용 됩니다.

* Span1 Name: ' svcB ' 특성: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}

* Span2 이름: ' svcA ' 특성: {env: staging, test_request: false, redact_trace: true}

다음 범위는 include 속성과 일치 하지 않으며 프로세서 작업이 적용 되지 않습니다.

* Span3 Name: ' svcB ' 특성: {env: production, test_request: true, credit_card: 1234, redact_trace: false}

* Span4 이름: ' svcC ' 특성: {env: dev, test_request: false}

## <a name="attribute-processor"></a>특성 프로세서 

특성 프로세서는 범위의 특성을 수정 합니다. 필요에 따라 범위를 포함/제외 하는 기능을 지원 합니다.
구성 파일에 지정 된 순서 대로 수행 되는 작업 목록을 사용 합니다. 지원 되는 작업은 다음과 같습니다.

* `insert` : 키가 아직 없는 범위에 새 특성을 삽입 합니다.
* `update` : 키가 있는 범위에서 특성을 업데이트 합니다.
* `delete` : 범위에서 특성을 삭제 합니다.
* `hash`   : 기존 특성 값을 해시 (SHA1) 합니다.

작업 `insert` 및 `update`
* `key` 필수
* 또는 중 `value` 하나가 `fromAttribute` 필요 합니다.
* `action`은 필수입니다.

작업의 경우 `delete`
* `key` 필수
* `action`: `delete` 가 필요 합니다.

작업의 경우 `hash`
* `key` 필수
* `action` : `hash` 가 필요 합니다.

작업 목록을 작성 하 여 특성 다시 채우기, 새 키로 값 복사, 중요 한 정보 수정 등 다양 한 시나리오를 만들 수 있습니다.

#### <a name="sample-usage"></a>샘플 사용

다음 예에서는 키/값을 범위에 삽입 하는 방법을 보여 줍니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

다음 예제에서는 특성의 기존 키만 업데이트 하도록 프로세서를 구성 하는 방법을 보여 줍니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

다음 예제에서는 regexp 패턴과 일치 하는 범위 이름을 가진 범위를 처리 하는 방법을 보여 줍니다.
이 프로세서는 "token" 특성을 제거 하 고 범위 이름이 "auth"와 일치 하는 범위에서 "password" 특성을 난독 처리 \* 합니다. 범위 이름이 "login"과 일치 하지 않는 경우 \*

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>프로세서 범위

범위 프로세서는 범위 이름 또는 범위 이름을 기반으로 범위의 특성을 수정 합니다. 필요에 따라 범위를 포함/제외 하는 기능을 지원 합니다.

### <a name="name-a-span"></a>범위 이름

이름 섹션의 일부로 다음 설정이 필요 합니다.

* `fromAttributes`: 키에 대 한 특성 값은 구성에 지정 된 순서에 따라 새 이름을 만드는 데 사용 됩니다. 프로세서의 이름을 바꾸려면 범위에서 모든 특성 키를 지정 해야 합니다.

선택적으로 구성할 수 있는 설정은 다음과 같습니다.

* `separator`: 지정 된 문자열은 값을 분할 하는 데 사용 됩니다.
> [!NOTE]
> 이름 바꾸기가 특성 프로세서에 의해 수정 되는 특성에 종속 되는 경우 파이프라인 사양의 특성 프로세서 뒤에 범위 프로세서가 지정 되어 있는지 확인 합니다.

#### <a name="sample-usage"></a>샘플 사용

다음 예에서는 "db-library", "operation" 및 "id" 특성의 값을 "::" 값으로 구분 하 여 해당 순서로 범위의 새 이름을 구성 합니다.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>범위 이름에서 특성 추출

식 목록을 사용 하 여 범위 이름을 일치 시키고 하위 식을 기반으로 특성을 추출 합니다. 섹션 아래에를 지정 해야 합니다 `toAttributes` .

다음 설정이 필요 합니다.

`rules` : 범위 이름에서 특성 값을 추출 하는 규칙의 목록입니다. 범위 이름의 값은 추출 된 특성 이름으로 바뀝니다. 목록의 각 규칙은 regex 패턴 문자열입니다. 범위 이름은 regex에 대해 확인 됩니다. Regex가 일치 하면 regex의 모든 명명 된 하위 식이 특성으로 추출 되어 범위에 추가 됩니다. 각 하위 식 이름은 특성 이름이 되 고 하위 식은 일치 하는 부분으로 특성 값이 됩니다. 범위 이름에서 일치 하는 부분은 추출 된 특성 이름으로 바뀝니다. 범위가 범위에 이미 있는 경우이 특성을 덮어씁니다. 프로세스는 지정 된 순서 대로 모든 규칙에 대해 반복 됩니다. 각 후속 규칙은 이전 규칙을 처리 한 후 출력 되는 범위 이름에 대해 작동 합니다.

#### <a name="sample-usage"></a>샘플 사용

입력 범위 이름이/api/v1/document/12345678/update. 라고 가정 하겠습니다. 출력 범위 이름에 다음 결과를 적용 하면/api/v1/document/{documentId}/update는 새 특성 "documentId" = "12345678"를 범위에 추가 합니다.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?P<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

다음은 범위에 다음 속성이 있는 경우 범위 이름을 "{operation_website}"로 바꾸고 특성 {Key: operation_website, Value: oldSpanName}를 추가 하는 방법을 보여 줍니다.
- 범위 이름에는 문자열의 아무 곳에 나 '/'가 포함 됩니다.
- 범위 이름이 ' 펜스 안/change '가 아닙니다.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?P<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```