---
title: 원격 분석 프로세서 예제-Java 용 Azure Monitor Application Insights
description: Java 용 Azure Monitor Application Insights에서 원격 분석 프로세서를 표시 하는 예제를 살펴봅니다.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734590"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>원격 분석 프로세서 예제-Java 용 Azure Monitor Application Insights

이 문서에서는 Java 용 Application Insights의 원격 분석 프로세서 예제를 제공 합니다. 구성 포함 및 제외에 대 한 샘플을 찾을 수 있습니다. 또한 특성 프로세서 및 범위 프로세서에 대 한 샘플을 찾을 수 있습니다.
## <a name="include-and-exclude-samples"></a>Include 및 exclude 샘플

이 섹션에서는 범위를 포함 하 고 제외 하는 방법에 대해 알아봅니다. 또한 여러 범위를 제외 하 고 선택적 처리를 적용 하는 방법도 확인할 수 있습니다.
### <a name="include-spans"></a>범위 포함

이 섹션에서는 특성 프로세서에 대 한 범위를 포함 하는 방법을 보여 줍니다. 속성과 일치 하지 않는 범위는 프로세서에서 처리 되지 않습니다.

일치 항목을 찾으려면 범위 이름이 또는와 동일 해야 `spanA` 합니다 `spanB` . 

이러한 범위는 include 속성과 일치 하 고 프로세서 동작이 적용 됩니다.
* Span1 Name: ' spanA ' 특성: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: ' spanB ' 특성: {env: dev, test_request: false}
* Span3 Name: ' spanA ' 특성: {env: 1, test_request: dev, credit_card: 1234}

이 범위는 include 속성과 일치 하지 않으며 프로세서 작업이 적용 되지 않습니다.
* Span4 Name: ' spanC ' 특성: {env: dev, test_request: false}

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
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans"></a>범위 제외

이 섹션에서는 특성 프로세서에 대 한 범위를 제외 하는 방법을 보여 줍니다. 속성과 일치 하는 범위는이 프로세서에서 처리 되지 않습니다.

일치 항목을 찾으려면 범위 이름이 또는와 동일 해야 `spanA` 합니다 `spanB` .

다음 범위는 제외 속성과 일치 하며, 프로세서 작업이 적용 되지 않습니다.
* Span1 Name: ' spanA ' 특성: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: ' spanB ' 특성: {env: dev, test_request: false}
* Span3 Name: ' spanA ' 특성: {env: 1, test_request: dev, credit_card: 1234}

이 범위는 제외 속성과 일치 하지 않으며 프로세서 동작이 적용 됩니다.
* Span4 Name: ' spanC ' 특성: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans-by-using-multiple-criteria"></a>여러 조건을 사용 하 여 범위를 제외 합니다.

이 섹션에서는 특성 프로세서에 대 한 범위를 제외 하는 방법을 보여 줍니다. 속성과 일치 하는 범위는이 프로세서에서 처리 되지 않습니다.

일치 항목을 찾으려면 다음 조건을 충족 해야 합니다.
* 범위에 특성 (예: `env` 또는 `dev` )이 있어야 합니다.
* 범위에는 키가 있는 특성이 있어야 합니다 `test_request` .

다음 범위는 제외 속성과 일치 하며, 프로세서 작업은 적용 되지 않습니다.
* Span1 Name: ' spanB ' 특성: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: ' spanA ' 특성: {env: dev, test_request: false}

다음 범위는 제외 속성과 일치 하지 않으며 프로세서 동작이 적용 됩니다.
* Span3 Name: ' spanB ' 특성: {env: 1, test_request: dev, credit_card: 1234}
* Span4 Name: ' spanC ' 특성: {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="selective-processing"></a>선택적 처리

이 섹션에서는이 프로세서가 적용 되는 범위를 나타내는 범위 속성 집합을 지정 하는 방법을 보여 줍니다. Include 속성은 처리 해야 하는 범위를 표시 합니다. 제외 속성은 처리 되지 않아야 하는 범위를 필터링 합니다.

다음 구성에서 이러한 범위는 속성과 일치 하 고 프로세서 작업이 적용 됩니다.

* Span1 Name: ' spanB ' 특성: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Span2 Name: ' spanA ' 특성: {env: staging, test_request: false, redact_trace: true}

이러한 범위는 include 속성과 일치 하지 않으며 프로세서 작업이 적용 되지 않습니다.
* Span3 Name: ' spanB ' 특성: {env: production, test_request: true, credit_card: 1234, redact_trace: false}
* Span4 Name: ' spanC ' 특성: {env: dev, test_request: false}

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
  }
}
```
## <a name="attribute-processor-samples"></a>특성 프로세서 샘플

### <a name="insert"></a>삽입

다음 샘플에서는 `{"attribute1": "attributeValue1"}` 키 `attribute1` 가 존재 하지 않는 범위에 새 특성을 삽입 합니다.

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
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>다른 키에서 삽입

다음 샘플에서는 특성의 값을 사용 `anotherkey` 하 여 `{"newKey": "<value from attribute anotherkey>"}` 키 `newKey` 가 존재 하지 않는 범위에 새 특성을 삽입 합니다. 특성이 없는 경우 `anotherkey` 새 특성이 범위에 삽입 되지 않습니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>업데이트

다음 샘플에서는 특성을로 업데이트 합니다 `{"db.secret": "redacted"}` . 특성의 값을 `boo` 사용 하 여 특성을 업데이트 `foo` 합니다. 특성이 없는 범위는 `boo` 변경 되지 않습니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>DELETE

다음 샘플에서는 키가 있는 특성을 삭제 하는 방법을 보여 줍니다 `credit_card` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Hash

다음 샘플에서는 기존 특성 값을 해시 하는 방법을 보여 줍니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
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

### <a name="extract"></a>Extract

다음 샘플에서는 정규식 (regex)을 사용 하 여 다른 특성의 값을 기반으로 새 특성을 만드는 방법을 보여 줍니다.
예를 들어, 지정 된 경우 `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` 다음 특성이 삽입 됩니다.
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http. url: 변경 내용 *없음*

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

다음 샘플에서는 regex 패턴과 일치 하는 범위 이름을 가진 범위를 처리 하는 방법을 보여 줍니다.
이 프로세서는 특성을 제거 합니다 `token` . `password`범위 이름이와 일치 `auth.*` 하 고 범위 이름이 일치 하지 않는 범위 내에서 특성을 난독 처리 `login.*` 합니다.

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


## <a name="span-processor-samples"></a>범위 프로세서 샘플

### <a name="name-a-span"></a>범위 이름

다음 샘플에서는, 및 특성의 값을 지정 합니다 `db.svc` `operation` `id` . 이러한 특성을 값으로 구분 하 여 해당 순서 대로 사용 하 여 범위의 새 이름을 형성 합니다 `::` .
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

### <a name="extract-attributes-from-a-span-name"></a>범위 이름에서 특성을 추출 합니다.

입력 범위 이름이 인 것으로 가정 하겠습니다 `/api/v1/document/12345678/update` . 다음 샘플에서는 출력 범위 이름을 반환 합니다 `/api/v1/document/{documentId}/update` . 범위에 새 특성을 추가 합니다 `documentId=12345678` .
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
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Include 및 exclude를 사용 하 여 범위 이름에서 특성을 추출 합니다.

다음 샘플에서는 범위 이름을로 변경 하는 방법을 보여 줍니다 `{operation_website}` . `operation_website`범위에 다음과 같은 속성이 있는 경우 키 및 값을 사용 하 여 특성을 추가 합니다 `{oldSpanName}` .
- 범위 이름에는 `/` 문자열의 임의의 위치가 포함 됩니다.
- 범위 이름은이 아닙니다 `donot/change` .
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
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```
