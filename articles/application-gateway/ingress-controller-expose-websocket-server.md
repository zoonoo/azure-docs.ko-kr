---
title: 웹소켓 서버를 응용 프로그램 게이트웨이에 노출
description: 이 문서에서는 AKS 클러스터에 대한 인그컨트롤러를 사용하여 WebSocket 서버를 응용 프로그램 게이트웨이에 노출하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297835"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>웹소켓 서버를 응용 프로그램 게이트웨이에 노출

응용 프로그램 게이트웨이 v2 설명서에 설명 된 대로 - [WebSocket 및 HTTP/2 프로토콜에 대 한 기본 지원을 제공 합니다.](features.md#websocket-and-http2-traffic) 응용 프로그램 게이트웨이와 Kubernetes 인그레스 모두에 대해 WebSocket 지원을 선택적으로 활성화하거나 사용하지 않도록 설정하는 사용자 구성 설정이 없습니다.

아래 Kubernetes 배포 YAML에는 일반 웹 서버를 배포하는 것과 동일한 WebSocket 서버를 배포하는 데 사용되는 최소 구성이 표시됩니다.
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

모든 필수 구성 프로그램이 충족되고 AKS의 Kubernetes 침투에 의해 제어되는 응용 프로그램 게이트웨이가 있다는 점을 감안할 때 위의 배포는 응용 프로그램 게이트웨이의 공용 IP `ws.contoso.com` 및 도메인의 포트 80에 노출되는 WebSockets 서버가 발생합니다.

다음 cURL 명령은 WebSocket 서버 배포를 테스트합니다.
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>웹소켓 상태 프로브

배포에서 상태 프로브를 명시적으로 정의하지 않는 경우 응용 프로그램 게이트웨이는 WebSocket 서버 끝점에서 HTTP GET을 시도합니다.
서버 구현에 따라[(여기 우리가 좋아하는 것)](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)WebSocket 특정`Sec-Websocket-Version` 헤더가 필요할 수 있습니다 (예를 들어).
응용 프로그램 게이트웨이는 WebSocket 헤더를 추가하지 않으므로 WebSocket 서버의 응용 프로그램 `400 Bad Request`게이트웨이의 상태 프로브 응답이 가장 많을 것입니다.
결과적으로 응용 프로그램 게이트웨이는 포드를 비정상으로 표시하여 결국 `502 Bad Gateway` WebSocket 서버의 소비자를 위한 결과를 가져옵니다.
이를 방지하려면 서버(예:`/health` 반환되는 `200 OK`상태)에 상태 확인을 위해 HTTP GET 처리기를 추가해야 할 수 있습니다.
