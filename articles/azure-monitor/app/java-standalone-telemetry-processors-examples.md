---
title: 원격 분석 프로세서 예-Java 용 Azure Monitor Application Insights
description: Java 용 Azure Monitor Application Insights의 원격 분석 프로세서를 보여 주는 예제
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: b9ad5347e146fc94b513180c591b00c4f449619f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146447"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>원격 분석 프로세서 예-Java 용 Azure Monitor Application Insights

## <a name="includeexclude-samples"></a>포함/제외 샘플

### <a name="1-include-spans"></a>1. 범위 포함

다음은이 특성 프로세서의 범위를 포함 하는 것을 보여 줍니다. 속성과 일치 하지 않는 다른 모든 범위는이 프로세서에서 처리 되지 않습니다.

일치 항목에 대해 충족 해야 하는 조건은 다음과 같습니다.
* 범위 이름은 "spanA" 또는 "spanB"와 같아야 합니다. 

다음은 포함 속성과 일치 하 고 프로세서 동작이 적용 되는 범위입니다.
* Span1 Name: ' spanA ' 특성: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: ' spanB ' 특성: {env: dev, test_request: false}
* Span3 Name: ' spanA ' 특성: {env: 1, test_request: dev, credit_card: 1234}

다음 범위는 include 속성과 일치 하지 않으며 프로세서 작업은 적용 되지 않습니다.
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

### <a name="2-exclude-spans"></a>2. 범위 제외

다음에서는이 특성 프로세서의 범위를 제외 하는 방법을 보여 줍니다. 속성과 일치 하는 모든 범위는이 프로세서에서 처리 되지 않습니다.

일치 항목에 대해 충족 해야 하는 조건은 다음과 같습니다.
* 범위 이름은 "spanA" 또는 "spanB"와 같아야 합니다. 

다음은 exclude 속성과 일치 하는 범위이 고 프로세서 동작은 적용 되지 않습니다.
* Span1 Name: ' spanA ' 특성: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: ' spanB ' 특성: {env: dev, test_request: false}
* Span3 Name: ' spanA ' 특성: {env: 1, test_request: dev, credit_card: 1234}

다음 범위는 제외 속성과 일치 하지 않으며 프로세서 동작이 적용 됩니다.
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

### <a name="3-excludemulti-spans"></a>3. ExcludeMulti 범위

다음에서는이 특성 프로세서의 범위를 제외 하는 방법을 보여 줍니다. 속성과 일치 하는 모든 범위는이 프로세서에서 처리 되지 않습니다.

일치 항목에 대해 충족 해야 하는 조건은 다음과 같습니다.
* 일치 항목에 대해 범위에 특성 (' env ', ' dev ')이 있어야 합니다.
* 범위에 ' test_request ' 키가 있는 특성이 있으면 일치 하는 항목이 있습니다.

다음은 exclude 속성과 일치 하는 범위이 고 프로세서 동작은 적용 되지 않습니다.
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

### <a name="4-selective-processing"></a>4. 선택적 처리

다음은이 프로세서가 적용 되는 범위를 나타내는 span 속성 집합을 지정 하는 방법을 보여 줍니다. 속성의는 포함 해야 하는 속성을 `include` 포함 하며 `exclude` 처리 되지 않아야 하는 범위를 추가로 필터링 합니다.

아래 구성을 사용 하는 경우 다음 범위가 속성과 일치 하 고 프로세서 작업이 적용 됩니다.

* Span1 Name: ' spanB ' 특성: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Span2 Name: ' spanA ' 특성: {env: staging, test_request: false, redact_trace: true}

다음 범위는 include 속성과 일치 하지 않으며 프로세서 작업이 적용 되지 않습니다.
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

다음은 "attribute1" 키가 존재 하지 않는 범위에 새 특성 {"attribute1": "attributeValue1"}를 삽입 합니다.

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

다음은 "anotherkey" 특성의 값을 사용 하 여 ' anotherkey '} 특성의 새 특성 {"newKey": "값을" newKey "키가 없는 범위에 삽입 합니다. ' Anotherkey ' 특성이 없으면 범위에 새 특성을 삽입 하지 않습니다.

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

다음은 특성을 {"db-library": "교정 된"}로 업데이트 하 고 ' foo ' 특성의 값을 사용 하 여 ' boo ' 특성을 업데이트 합니다. ' Boo ' 특성이 없는 범위는 변경 되지 않습니다.

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

### <a name="delete"></a>삭제

다음은 ' credit_card ' 키를 사용 하 여 특성을 삭제 하는 방법을 보여 줍니다.

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

### <a name="hash"></a>해시

다음은 기존 특성 값을 해시 하는 방법을 보여 줍니다.

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

다음 예에서는 regex를 사용 하 여 다른 특성의 값을 기반으로 새 특성을 만드는 방법을 보여 줍니다.
예를 들어 http. url = http://example.com/path?queryParam1=value1 , queryParam2 = value2를 지정 하면 다음 특성이 삽입 됩니다.
* httpProtocol: http
* httpDomain: example.com
* httpPath: 경로
* httpQueryParams: queryParam1 = value1, queryParam2 = value2
* http. url 값은 변경 되지 않습니다.

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


## <a name="span-processor-samples"></a>범위 프로세서 샘플

### <a name="name-a-span"></a>범위 이름

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
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Include 및 exclude를 사용 하 여 범위 이름에서 특성을 추출 합니다.

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
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```