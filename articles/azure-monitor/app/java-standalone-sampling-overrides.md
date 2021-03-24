---
title: 샘플링 재정의 (미리 보기)-Java 용 Azure Monitor Application Insights
description: Java에 대 한 Azure Monitor Application Insights에서 샘플링 재정의를 구성 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 03d3093f14d97b2cc64d91e0d1b7adf34204a021
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962603"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>샘플링 재정의 (미리 보기)-Java 용 Azure Monitor Application Insights

> [!NOTE]
> 샘플링 재정의 기능은 미리 보기 상태입니다.

샘플링 재정의에 대 한 몇 가지 사용 사례는 다음과 같습니다.
 * 상태 검사에 대 한 원격 분석 수집을 억제 합니다.
 * 잡음이 있는 종속성 호출에 대 한 원격 분석 수집을 억제 합니다.
 * 상태 검사 또는 잡음이 있는 종속성 호출을 완전히 억제 하지 않고 노이즈를 줄입니다.
 * `/login`기본 샘플링이 더 낮은 것으로 구성 된 경우에도 중요 한 요청 유형 (예:)에 대해 100%의 원격 분석을 수집 합니다.

## <a name="terminology"></a>용어

샘플링 재정의에 대해 알아보기 전에 용어 *범위* 를 이해 해야 합니다. 범위는 다음에 대 한 일반적인 용어입니다.

* 들어오는 요청입니다.
* 나가는 종속성 (예: 다른 서비스에 대 한 원격 호출)입니다.
* In-process 종속성 (예: 서비스의 하위 구성 요소에 의해 수행 되는 작업)

샘플링 재정의의 경우 이러한 범위 구성 요소가 중요 합니다.

* 특성

Span 특성은 지정 된 요청 또는 종속성의 표준 및 사용자 지정 속성을 모두 나타냅니다.

## <a name="getting-started"></a>시작

시작 하려면 *에applicationinsights.js* 이라는 구성 파일을 만듭니다. *Applicationinsights-에이전트- \* jar* 와 동일한 디렉터리에 저장 합니다. 다음 템플릿을 사용합니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>작동 방식

범위가 시작 되 면 해당 시간에 범위에 있는 특성을 사용 하 여 샘플링 재정의가 일치 하는지 확인 합니다.

샘플링 재정의 중 하나가 일치 하면 해당 샘플링 비율을 사용 하 여 범위를 샘플링할 것인지 여부를 결정 합니다.

일치 하는 첫 번째 샘플링 재정의만 사용 됩니다.

일치 하는 샘플링 재정의가 없으면 다음을 수행 합니다.

* 추적의 첫 번째 범위 이면 [일반 샘플링 백분율이](./java-standalone-config.md#sampling) 사용 됩니다.
* 추적의 첫 번째 범위가 아닌 경우 부모 샘플링 결정이 사용 됩니다.

> [!IMPORTANT]
> 범위를 수집 하지 않기로 결정 한 경우 다운스트림 범위와 일치 하는 샘플링 재정의가 있더라도 모든 다운스트림 범위가 수집 되지 않습니다.
> 이 동작은 다운스트림 범위가 수집 되었지만 수집 되지 않은 범위에 대 한 부모로 되어 있기 때문에 중단 된 추적 때문에 필요 합니다.

> [!NOTE]
> 샘플링 결정은 traceId (operationId 라고도 함)를 0에서 100 사이의 숫자로 해시 한 다음 해당 해시를 샘플링 비율과 비교 하는 것을 기반으로 합니다.
> 지정 된 추적의 모든 범위는 동일한 traceId를 가지 므로 동일한 해시를 갖게 되므로 전체 추적에서 샘플링 결정이 일치 하 게 됩니다.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>예: 상태 검사에 대 한 원격 분석 수집 억제

이렇게 하면에 대 한 모든 요청에 대 한 원격 분석 수집이 생략 됩니다 `/health-checks` .

이 경우에서 일반적으로 수집 되는 다운스트림 범위 (종속성)를 수집 하지 않습니다 `/health-checks` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>예: 잡음이 있는 종속성 호출에 대 한 원격 분석 수집 억제

이렇게 하면 모든 redis 호출에 대 한 원격 분석 수집이 생략 됩니다 `GET my-noisy-key` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>예: 중요 한 요청 유형에 대 한 원격 분석의 100% 수집

그러면에 대 한 원격 분석의 100%가 수집 됩니다 `/login` .

다운스트림 범위 (종속성)는 부모의 샘플링 결정 (다운스트림 범위에 대 한 샘플링 재정의 제외)을 고려 하므로 모든 '/로그인 ' 요청에 대해서도 수집 됩니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>공통 범위 특성

이 섹션에서는 샘플링 재정의에서 사용할 수 있는 몇 가지 공통 범위 특성을 보여 줍니다.

### <a name="http-spans"></a>HTTP 범위

| attribute  | Type | Description | 
|---|---|---|
| `http.method` | 문자열 | HTTP 요청 메서드입니다.|
| `http.url` | string | 형식의 전체 HTTP 요청 URL `scheme://host[:port]/path?query[#fragment]` 입니다. 조각은 일반적으로 HTTP를 통해 전송 되지 않습니다. 하지만 조각이 알려져 있는 경우이를 포함 해야 합니다.|
| `http.status_code` | number | [HTTP 응답 상태 코드](https://tools.ietf.org/html/rfc7231#section-6)입니다.|
| `http.flavor` | string | HTTP 프로토콜의 유형입니다. |
| `http.user_agent` | string | 클라이언트에서 보낸 [HTTP 사용자 에이전트](https://tools.ietf.org/html/rfc7231#section-5.5.3) 헤더의 값입니다. |

### <a name="jdbc-spans"></a>JDBC 범위

| attribute  | Type | Description  |
|---|---|---|
| `db.system` | 문자열 | 사용 중인 DBMS (데이터베이스 관리 시스템) 제품의 식별자입니다. |
| `db.connection_string` | string | 데이터베이스에 연결하는 데 사용되는 연결 문자열입니다. 포함 된 자격 증명을 제거 하는 것이 좋습니다.|
| `db.user` | string | 데이터베이스에 액세스 하기 위한 사용자 이름입니다. |
| `db.name` | string | 액세스 중인 데이터베이스의 이름을 보고 하는 데 사용 되는 문자열입니다. 데이터베이스를 전환 하는 명령의 경우 명령이 실패 하더라도이 문자열을 대상 데이터베이스로 설정 해야 합니다.|
| `db.statement` | string | 실행 중인 데이터베이스 문입니다.|
