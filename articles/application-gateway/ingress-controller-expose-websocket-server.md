---
title: Application Gateway에 WebSocket 서버 노출
description: 이 문서에서는 AKS 클러스터에 대한 수신 컨트롤러와 Application Gateway에 WebSocket 서버를 노출하는 방법을 설명합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 68d4ff7e4617136e4c58ce672f34de56e46f0229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85207790"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Application Gateway에 WebSocket 서버 노출

Application Gateway v2 설명서에 설명된 대로 [WebSocket 및 HTTP/2 프로토콜에 대한 기본 지원을 제공](features.md#websocket-and-http2-traffic)합니다. Application Gateway 및 Kubernetes 수신의 경우 WebSocket 지원을 선택적으로 사용하거나 사용하지 않도록 설정할 수 있는 사용자 구성 설정이 없다는 점을 주의하세요.

아래 Kubernetes 배포 YAML은 WebSocket 서버를 배포하는 데 사용되는 최소 구성을 보여 줍니다. 이는 일반 웹 서버를 배포하는 것과 동일합니다.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

모든 필수 구성 요소가 충족되고 AKS에 Kubernetes 수신에 의해 제어되는 Application Gateway가 있는 경우 위의 배포는 Application Gateway의 공용 IP 및 `ws.contoso.com` 도메인의 포트 80에 WebSocket 서버가 노출됩니다.

다음 cURL 명령은 WebSocket 서버 배포를 테스트합니다.
```shell
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket 상태 프로브

배포에서 상태 프로브를 명시적으로 정의하지 않으면 Application Gateway에서 WebSocket 서버 엔드포인트에 HTTP GET을 시도합니다.
서버 구현([즐겨 사용하는 예](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go))에 따라 WebSocket 특정 헤더가 필요할 수 있습니다(예: `Sec-Websocket-Version`).
Application Gateway는 WebSocket 헤더를 추가하지 않으므로 WebSocket 서버 Application Gateway 상태 프로브 응답은 `400 Bad Request`가 될 가능성이 큽니다.
결과적으로 Application Gateway는 Pod를 비정상으로 표시하며 이로 인해 결국 WebSocket 서버 소비자에 대한 `502 Bad Gateway`가 발생합니다.
해당 문제를 방지하려면 서버에 상태 검사에 대한 HTTP GET 처리기를 추가해야 할 수 있습니다(예: `200 OK`를 반환하는 `/health`).
