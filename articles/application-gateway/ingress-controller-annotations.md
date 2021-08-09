---
title: Application Gateway 수신 컨트롤러 주석
description: 이 문서에서는 Application Gateway 수신 컨트롤러와 관련된 주석에 대한 설명서를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 4f570f9f18f9c9d484a9bc9c1a5c64d42dbdc714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397453"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러에 대한 주석 

## <a name="introductions"></a>소개

Kubernetes 수신 리소스는 임의의 키/값 쌍으로 주석을 달 수 있습니다. AGIC는 수신 YAML을 통해 구성할 수 없는 Application Gateway 기능을 프로그래밍하기 위해 주석에 의존합니다. 수신 주석은 수신 리소스에서 파생된 모든 HTTP 설정, 백 엔드 풀 및 수신기에 적용됩니다.

## <a name="list-of-supported-annotations"></a>지원되는 주석 목록

AGIC에서 수신 리소스를 관찰하려면 `kubernetes.io/ingress.class: azure/application-gateway`로 **주석을 추가** 해야 합니다. 그래야만 AGIC가 해당 수신 리소스와 함께 작동합니다.

| 주석 키 | 값 형식 | 기본값 | 허용되는 값
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(초) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(초) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>백 엔드 경로 접두사

이 주석을 사용하면 수신 리소스에 지정된 백 엔드 경로를 이 주석에 지정된 접두사로 다시 작성할 수 있습니다. 이를 통해 사용자는 수신 리소스에서 서비스를 노출하는 데 사용되는 엔드포인트 이름과 엔드포인트가 다른 서비스를 노출할 수 있습니다.

### <a name="usage"></a>사용법

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>예

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
위의 예에서는 `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` 주석이 있고 이름이 `go-server-ingress-bkprefix`인 수신 리소스를 정의했습니다. 주석은 `/hello`에서 `/test/`까지의 경로에 대해 경로 접두사를 재정의하는 HTTP 설정을 만들도록 애플리케이션 게이트웨이에 지시합니다.

> [!NOTE] 
> 위의 예에서는 하나의 규칙만 정의했습니다. 그러나 주석은 전체 수신 리소스에 적용할 수 있으므로 사용자가 여러 규칙을 정의한 경우 지정된 각 경로에 대해 백 엔드 경로 접두사가 설정됩니다. 따라서 사용자가 경로 접두사가 다른 규칙을 원하는 경우(동일한 서비스에 대해서도) 다른 수신 리소스를 정의해야 합니다.

## <a name="tls-redirect"></a>TLS 리디렉션

HTTP URL을 HTTPS URL로 자동 리디렉션하도록 Application Gateway를 [구성할 수 있습니다](./redirect-overview.md). 이 주석이 있고 TLS가 올바르게 구성된 경우 Kubernetes 수신 컨트롤러는 [리디렉션 구성이 있는 라우팅 규칙](./redirect-http-to-https-portal.md#add-a-routing-rule-with-a-redirection-configuration)을 만들고 변경 사항을 Application Gateway에 적용합니다. 만들어진 리디렉션은 HTTP `301 Moved Permanently`입니다.

### <a name="usage"></a>사용법

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>예

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>연결 드레이닝

`connection-draining`: 이 주석을 사용하면 연결 드레이닝을 사용하도록 설정할지 여부를 지정할 수 있습니다.
`connection-draining-timeout`: 이 주석을 사용하면 Application Gateway가 드레이닝 백 엔드 엔드포인트에 대한 요청을 종료할 시간 제한을 지정할 수 있습니다.

### <a name="usage"></a>사용법

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>예

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>쿠키 기반 선호도

이 주석을 사용하면 쿠키 기반 선호도를 사용하도록 설정할지 여부를 지정할 수 있습니다.

### <a name="usage"></a>사용법

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>예

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>요청 시간 초과

이 주석을 사용하면 응답이 수신되지 않을 경우 Application Gateway가 요청을 실패하게 될 요청 제한 시간(초)을 지정할 수 있습니다.

### <a name="usage"></a>사용법

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>예

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>개인 IP 사용

이 주석을 사용하면 이 엔드포인트를 Application Gateway의 개인 IP에 노출할지 여부를 지정할 수 있습니다.

> [!NOTE]
> * Application Gateway는 동일한 포트에서 여러 IP를 지원하지 않습니다(예: 80/443). `appgw.ingress.kubernetes.io/use-private-ip: "false"` 주석이 있는 수신과 `HTTP`에 `appgw.ingress.kubernetes.io/use-private-ip: "true"`가 있는 수신으로 인해 AGIC가 Application Gateway 업데이트에 실패합니다.
> * 개인 IP가 없는 Application Gateway의 경우 `appgw.ingress.kubernetes.io/use-private-ip: "true"`가 포함된 수신은 무시됩니다. 이는 `NoPrivateIP` 경고가 있는 수신에 대한 컨트롤러 로그 및 수신 이벤트에 반영됩니다.


### <a name="usage"></a>사용법
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>예
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>백 엔드 프로토콜

이 주석을 사용하면 Pod와 통신하는 동안 Application Gateway가 사용해야 하는 프로토콜을 지정할 수 있습니다. 지원되는 프로토콜: `http`, `https`

> [!NOTE]
> * 자체 서명된 인증서는 Application Gateway에서 지원되지만 현재 AGIC는 Pod가 잘 알려진 CA에서 서명한 인증서를 사용하는 경우에만 `https`를 지원합니다.
> * Pod에서 HTTPS에는 포트 80을 사용하면 안 되며 HTTP에는 포트 443을 사용하면 안 됩니다.

### <a name="usage"></a>사용법
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>예제
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```