---
title: 응용 프로그램 게이트웨이 인그리스 컨트롤러 주석
description: 이 문서에서는 응용 프로그램 게이트웨이 받는 컨트롤러와 관련된 주석에 대한 설명서를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335818"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>응용 프로그램 게이트웨이 인그레스 컨트롤러에 대한 주석 

## <a name="introductions"></a>소개

Kubernetes 침투 리소스는 임의의 키/값 쌍으로 추가될 수 있습니다. AGIC는 Ingress YAML을 통해 구성할 수 없는 응용 프로그램 게이트웨이 기능을 프로그래밍하기 위한 주석에 의존합니다. 받는 주석은 모든 HTTP 설정, 백 엔드 풀 및 받는 리소스에서 파생된 수신기에 적용됩니다.

## <a name="list-of-supported-annotations"></a>지원되는 주석 목록

AGIC에서 관찰하는 받는 리소스의 경우 `kubernetes.io/ingress.class: azure/application-gateway`에 **추가되어야 합니다.** 그런 다음에야 AGIC는 문제의 Inress 리소스와 함께 작동합니다.

| 추가 키 | 값 형식 | 기본값 | 허용되는 값
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(초) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(초) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>백엔드 경로 접두사

이 어칭을 사용하면 inress 리소스에 지정된 백 엔드 경로를 이 어구에 지정된 접두사로 다시 작성할 수 있습니다. 이를 통해 사용자는 받는 리소스에 서비스를 노출하는 데 사용되는 끝점 이름과 끝점이 다른 서비스를 노출할 수 있습니다.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>예제

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
위의 예제에서는 추가로 `go-server-ingress-bkprefix` `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` 명명된 inress 리소스를 정의했습니다. 이 별추가 응용 프로그램 게이트웨이에 HTTP 설정을 만들도록 지시하며, 이 `/hello` 설정에는 `/test/`경로 에 대한 경로 접두사 재정의가 있습니다.

> [!NOTE] 
> 위의 예에서는 하나의 규칙만 정의되어 있습니다. 그러나 주석은 전체 받는 리소스에 적용할 수 있으므로 사용자가 여러 규칙을 정의한 경우 지정된 각 경로에 대해 백 엔드 경로 접두사가 설정됩니다. 따라서 사용자가 다른 경로 접두사를 가진 다른 규칙을 원하는 경우(동일한 서비스에 대해서도) 다른 받는 사람 리소스를 정의해야 합니다.

## <a name="tls-redirect"></a>TLS 리디렉션

응용 프로그램 게이트웨이는 HTTP URL을 HTTPS URL로 자동으로 리디렉션하도록 [구성할 수](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) 있습니다. 이 주석이 있고 TLS가 제대로 구성되면 Kubernetes 인그레스 컨트롤러는 [리디렉션 구성을 사용하여 라우팅 규칙을](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) 만들고 변경 내용을 응용 프로그램 게이트웨이에 적용합니다. 생성된 리디렉션은 HTTP `301 Moved Permanently`.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>예제

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

## <a name="connection-draining"></a>연결 드레인

`connection-draining`: 이 어칭을 통해 사용자는 연결 드레인을 활성화할지 여부를 지정할 수 있습니다.
`connection-draining-timeout`: 이 어칭을 사용하면 응용 프로그램 게이트웨이가 드레인 백 엔드 엔드에 대한 요청을 종료한 후 시간 시간을 지정할 수 있습니다.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>예제

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

이 추가를 사용하면 쿠키 기반 선호도를 활성화할지 여부를 지정할 수 있습니다.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>예제

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

이 기능을 사용하면 응답을 받지 못한 경우 응용 프로그램 게이트웨이가 요청에 실패한 후 몇 초 후에 요청 시간 시간을 지정할 수 있습니다.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
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

## <a name="use-private-ip"></a>프라이빗 IP 사용

이 기능을 사용하면 응용 프로그램 게이트웨이의 개인 IP에 이 끝점을 노출할지 여부를 지정할 수 있습니다.

> [!NOTE]
> * 응용 프로그램 게이트웨이는 동일한 포트에서 여러 IP를 지원하지 않습니다(예: 80/443). 추가 가 `appgw.ingress.kubernetes.io/use-private-ip: "false"` 있는 다른 ininss `appgw.ingress.kubernetes.io/use-private-ip: "true"` 로 `HTTP` 인해 AGIC 응용 프로그램 게이트웨이 업데이트에 실패 합니다.
> * 개인 IP가 없는 응용 프로그램 게이트웨이의 경우 사용 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 이 사용되지 않습니다. 이렇게 하면 컨트롤러 로그 및 경고가 있는 `NoPrivateIP` 해당 인크레세의 이벤트에 반영됩니다.


### <a name="usage"></a>사용
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
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

이 기능을 사용하면 Pod와 대화하는 동안 응용 프로그램 게이트웨이에서 사용해야 하는 프로토콜을 지정할 수 있습니다. 지원되는 프로토콜: `http``https`

> [!NOTE]
> * 자체 서명된 인증서는 현재 응용 프로그램 게이트웨이에서 지원되지만, 현재 AGIC는 Pod가 잘 알려진 CA에서 서명한 인증서를 사용하는 경우에만 지원합니다. `https`
> * 포트 80을 HTTPS 및 포트 443에서 POD에 HTTP로 사용하지 않도록 하십시오.

### <a name="usage"></a>사용
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