---
title: Azure API Management Eapr 통합 정책 | Microsoft Docs
description: Eapr 마이크로 서비스 확장과 상호 작용 하기 위한 Azure API Management 정책에 대해 알아봅니다.
author: vladvino
ms.author: vlvinogr
ms.date: 10/23/2020
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 2bf9c4d233cfad454d63da4dce30a38af80d24ab
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558400"
---
# <a name="api-management-dapr-integration-policies"></a>API Management Eapr 통합 정책

이 항목에서는 Eapr 통합 API Management 정책에 대 한 참조를 제공 합니다. 6apr는 모든 언어나 프레임 워크를 사용 하 여 상태 비저장 및 상태 저장 마이크로 서비스 기반 응용 프로그램을 빌드하기 위한 이식 가능한 런타임입니다. 이는 서비스 검색 및 빌드에 다시 시도 논리를 사용 하 여 호출 하는 경우, 최소 한 번 배달 의미 체계를 사용 하 여 게시 및 구독, 외부 서비스를 사용 하 여 쉽게 컴퍼지션을 위한 플러그형 바인딩 리소스와 같은 일반적인 마이크로 서비스 패턴을 체계화 합니다. [Dapr.io](https://dapr.io) 로 이동 하 여 자세한 정보를 확인 하 고 d 4를 시작 하는 방법에 대 한 지침을 제공 합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](api-management-howto-policies.md)을 참조하세요.

> [!CAUTION]
> 이 항목에서 참조 하는 정책은 공개 미리 보기 상태 이며 [Microsoft Azure 미리 보기에 대 한 추가 사용 약관이](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)적용 됩니다.

> [!IMPORTANT]
> 이 항목에서 참조 하는 정책은 지원 되는 [API Management 게이트웨이의 자체 호스팅 버전](self-hosted-gateway-overview.md) 에서만 사용할 수 있습니다.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>자체 호스팅 게이트웨이에서 6Apr 지원 사용

자체 호스팅 게이트웨이에서이 지원 기능을 설정 하려면 "Kubernetes"를 원하는 이름으로 대체 [하는 '](https://github.com/dapr/docs/blob/master/howto/configure-k8s/README.md) 앱 이름 '을 [배포 템플릿에](how-to-deploy-self-hosted-gateway-kubernetes.md) 추가 합니다. API Management를 설정 하 고 사용 하는 방법에 대 한 전체 연습은 [여기](https://aka.ms/apim/dapr/walkthru)에서 사용할 수 있습니다.
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Dapr(Distributed Application Runtime) 통합 정책

-  [서비스에 요청 보내기](api-management-dapr-policies.md#invoke): eapr 런타임을 사용 하 여 마이크로 서비스를 찾고 안정적으로 통신 합니다. D 4에서 서비스 호출에 대 한 자세한 내용은이 [추가 정보](https://github.com/dapr/docs/blob/master/concepts/service-invocation/README.md#service-invocation) 파일의 설명을 참조 하세요.
-  [Pub/Sub 토픽으로 메시지 보내기](api-management-dapr-policies.md#pubsub): aapr 런타임을 사용 하 여 게시/구독 항목에 메시지를 게시 합니다. 6Apr의 게시/구독 메시징에 대해 자세히 알아보려면이 [추가 정보](https://github.com/dapr/docs/blob/master/concepts/publish-subscribe-messaging/README.md) 파일의 설명을 참조 하세요.
-  [출력 바인딩 트리거](api-management-dapr-policies.md#bind): gapr 런타임을 사용 하 여 출력 바인딩을 통해 외부 시스템을 호출 합니다. Eapr의 바인딩에 대해 자세히 알아보려면이 [추가 정보](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md) 파일의 설명을 참조 하세요.

## <a name="send-request-to-a-service"></a><a name="invoke"></a> 서비스에 요청 보내기

이 정책은 `http://localhost:3500/v1.0/invoke/{app-id}/method/{method-name}` 템플릿 매개 변수를 policy 문에 지정 된 값으로 바꾸기 위해 현재 요청에 대 한 대상 URL을 설정 합니다.

이 정책에서는 사이드카가 게이트웨이와 동일한 pod의 컨테이너에서 실행 되는 것으로 가정 합니다. 요청을 받을 때, ccruntime은 HTTP 및 gRPC 간의 가능한 프로토콜 변환, 다시 시도, 분산 추적 및 오류 처리를 포함 하 여 서비스 검색 및 실제 호출을 수행 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" />
```

### <a name="examples"></a>예

#### <a name="example"></a>예제

다음 예제에서는 "echo" 라는 마이크로 서비스에서 "back" 이라는 메서드를 호출 하는 방법을 보여 줍니다. `set-backend-service`정책은 대상 URL을 설정 합니다. `forward-request`정책은 요청을 마이크로 서비스로 전달 하는 Aapr 런타임으로 디스패치합니다.

`forward-request`이 정책은 이해를 돕기 위해 여기에 표시 됩니다. 이 정책은 일반적으로 키워드를 통해 전역 범위에서 "상속" 됩니다 `base` .

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>요소

| 요소             | 설명  | 필수 |
|---------------------|--------------|----------|
| set-backend-service | 루트 요소 | 예      |

### <a name="attributes"></a>특성

| attribute        | 설명                     | 필수 | Default |
|------------------|---------------------------------|----------|---------|
| backend-id       | "Eapr"로 설정 해야 합니다.           | 예      | 해당 없음     |
| i 4-앱 id      | 대상 마이크로 서비스의 이름입니다. 는 Eapr의 [appId](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) 매개 변수에 매핑됩니다.| 예 | 해당 없음 |
| aapr-메서드      | 대상 마이크로 서비스 호출할 메서드 또는 URL의 이름입니다. D 4의 [메서드 이름](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) 매개 변수에 매핑됩니다.| 예 | 해당 없음 |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound
- **정책 범위:** 모든 범위

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Pub/Sub 토픽으로 메시지 보내기

이 정책은 API Management 게이트웨이에 게 메시지를 메시지를 만들도록 지시 합니다. 정책에서는 `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` 템플릿 매개 변수를 바꾸고 정책 문에 지정 된 콘텐츠를 추가 하는 HTTP POST 요청을 수행 하 여이 작업을 수행 합니다.

이 정책에서는 사이드카가 게이트웨이와 동일한 pod의 컨테이너에서 실행 되 고 있다고 가정 합니다. 6apr 런타임에서는 Pub/Sub 의미 체계를 구현 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic=”topic-name” ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid” content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>예

#### <a name="example"></a>예제

다음 예제에서는 현재 요청의 본문을 "orders" Pub/Sub [구성 요소의](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters)"new" [항목](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters) 으로 보내는 방법을 보여 줍니다. 6Apr 런타임에서 받은 응답은 [컨텍스트](api-management-policy-expressions.md#ContextVariables) 개체의 Variables 컬렉션의 "' d 4-응답 ' 항목에 저장 됩니다.

예를 들어, 및가 오류로 응답 하는 경우에는 ccruntime이 대상 항목을 찾을 수 없는 경우 "오류 발생" 섹션이 트리거됩니다. 6Apr 런타임에서 받은 응답은 호출자로 축 자로 반환 됩니다. 그렇지 않으면 기본 `200 OK` 응답이 반환 됩니다.

"백엔드" 섹션이 비어 있고 요청이 백 엔드로 전달 되지 않습니다.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>요소

| 요소             | 설명  | 필수 |
|---------------------|--------------|----------|
| 게시-d 4 월     | 루트 요소 | 예      |

### <a name="attributes"></a>특성

| attribute        | 설명                     | 필수 | Default |
|------------------|---------------------------------|----------|---------|
| pubsub 이름      | 대상 PubSub 구성 요소의 이름입니다. 은 (는) d 4에서 [pubceparameter](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md) 에 매핑됩니다. 표시 되지 않는 경우 __토픽__ 특성 값은 형식 이어야 합니다 `pubsub-name/topic-name` .    | 예       | None    |
| 토픽            | 항목의 이름입니다. 는 d 4의 [토픽](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md) 매개 변수에 매핑됩니다.               | 예      | 해당 없음     |
| ignore-error     | 로 설정 하 `true` 는 경우에는 Gapr 런타임에서 오류를 수신 하는 동안 ["오류 발생"](api-management-error-handling-policies.md) 섹션을 트리거하지 않도록 정책에 지시 합니다. | 아니요 | `false` |
| response-variable-name | 6Apr 런타임의 응답을 저장 하는 데 사용할 [Variables](api-management-policy-expressions.md#ContextVariables) 컬렉션 항목의 이름입니다. | 예 | None |
| 시간 제한 | 6Apr 런타임이 응답할 때까지 대기 하는 시간 (초)입니다. 범위는 1 ~ 240 초입니다. | 아니요 | 5 |
| template | 메시지 콘텐츠를 변환 하는 데 사용할 템플릿 엔진입니다. "액체"는 유일 하 게 지원 되는 값입니다. | 예 | None |
| content-type | 메시지 내용의 유형입니다. "application/json"은 유일 하 게 지원 되는 값입니다. | 예 | None |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, on-error
- **정책 범위:** 모든 범위

## <a name="trigger-output-binding"></a><a name="bind"></a> 트리거 출력 바인딩

이 정책은 API Management 게이트웨이에서 아웃 바운드 64 차 [바인딩을](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md)트리거하도록 지시 합니다. 정책에서는 `http://localhost:3500/v1.0/bindings/{{bind-name}}` 템플릿 매개 변수를 바꾸고 정책 문에 지정 된 콘텐츠를 추가 하는 HTTP POST 요청을 수행 하 여이 작업을 수행 합니다.

이 정책에서는 사이드카가 게이트웨이와 동일한 pod의 컨테이너에서 실행 되 고 있다고 가정 합니다. 6apr 런타임에서는 바인딩으로 표시 되는 외부 리소스를 호출 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<invoke-dapr-binding name=”bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid content-type="application/json">
    <metadata>
        <item key=”item-name”><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>예

#### <a name="example"></a>예제

다음 예제에서는 작업 이름이 "만들기" 인 "외부 시스템", "원본" 및 "클라이언트 ip" 라는 두 개의 키/값 항목으로 구성 된 메타 데이터, 원래 요청에서 오는 본문으로 명명 된 아웃 바운드 바인딩의 트리거를 보여 줍니다. Eapr 런타임에서 받은 응답은 [컨텍스트](api-management-policy-expressions.md#ContextVariables) 개체의 Variables 컬렉션의 "바인딩-응답" 항목에 캡처됩니다.

몇 가지 이유로 인해 Capr 런타임이 실패 하 고 오류로 응답 하는 경우에는 "오류 발생" 섹션이 트리거되고 cain 런타임에서 수신한 응답이 호출자에 게 반환 됩니다. 그렇지 않으면 기본 `200 OK` 응답이 반환 됩니다.

"백엔드" 섹션이 비어 있고 요청이 백 엔드로 전달 되지 않습니다.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>요소

| 요소             | 설명  | 필수 |
|---------------------|--------------|----------|
| 호출-차원 4-바인딩 | 루트 요소 | 예      |
| metadata            | 특정 메타 데이터를 키/값 쌍의 형식으로 바인딩합니다. 는 Eapr의 [metadata](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) 속성에 매핑됩니다. | 아니요 |
| 데이터            | 메시지 내용입니다. 는 d 4의 [데이터](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) 속성에 매핑됩니다. | 아니요 |


### <a name="attributes"></a>특성

| attribute        | 설명                     | 필수 | Default |
|------------------|---------------------------------|----------|---------|
| name            | 대상 바인딩 이름입니다. 은 (는) d 4에서 [정의](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#bindings-structure) 된 바인딩의 이름과 일치 해야 합니다.           | 예      | 해당 없음     |
| operation       | 대상 작업 이름 (바인딩 관련)입니다. 는 d 4의 [작업](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) 속성에 매핑됩니다. | 예 | None |
| ignore-error     | 로 설정 하 `true` 는 경우에는 Gapr 런타임에서 오류를 수신 하는 동안 ["오류 발생"](api-management-error-handling-policies.md) 섹션을 트리거하지 않도록 정책에 지시 합니다. | 아니요 | `false` |
| response-variable-name | 6Apr 런타임의 응답을 저장 하는 데 사용할 [Variables](api-management-policy-expressions.md#ContextVariables) 컬렉션 항목의 이름입니다. | 예 | None |
| 시간 제한 | 6Apr 런타임이 응답할 때까지 대기 하는 시간 (초)입니다. 범위는 1 ~ 240 초입니다. | 아니요 | 5 |
| template | 메시지 콘텐츠를 변환 하는 데 사용할 템플릿 엔진입니다. "액체"는 유일 하 게 지원 되는 값입니다. | 예 | None |
| content-type | 메시지 내용의 유형입니다. "application/json"은 유일 하 게 지원 되는 값입니다. | 예 | None |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, on-error
- **정책 범위:** 모든 범위
