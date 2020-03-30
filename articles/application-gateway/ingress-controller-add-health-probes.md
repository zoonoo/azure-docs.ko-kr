---
title: AKS 포드에 상태 프로브 추가
description: 이 문서에서는 응용 프로그램 게이트웨이를 사용하여 AKS 포드에 상태 프로브(준비 상태 및/또는 생동감)를 추가하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795587"
---
# <a name="add-health-probes-to-your-service"></a>서비스에 상태 프로브 추가
기본적으로 Ingress 컨트롤러는 노출된 포드에 대한 HTTP GET 프로브를 프로비전합니다.
프로브 속성은 사양에 [준비 또는 생생도 프로브를](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) 추가하여 사용자 정의할 수 있습니다. `deployment` / `pod`

## <a name="with-readinessprobe-or-livenessprobe"></a>또는 `readinessProbe``livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

쿠베르네테스 API 참조:
* [컨테이너 프로브](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet 작업](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`로 `livenessProbe` 구성할 때 `httpGet`지원됩니다.
> * 포드에 노출된 포트 이외의 포트에 대한 검색은 현재 지원되지 않습니다.
> * `HttpHeaders`을 `InitialDelaySeconds` `SuccessThreshold` 사용하지 않습니다.

##  <a name="without-readinessprobe-or-livenessprobe"></a>또는 `readinessProbe``livenessProbe`
위의 프로브가 제공되지 않으면 Ingress Controller는 지정된 지정 된 지정 `Path` 된 `backend-path-prefix` 경우 서비스에 `path` 대 한 `ingress` 지정 된 서비스 또는 서비스에 대 한 정의에 지정 된 서비스에 연결할 수 있습니다 가정 합니다.

## <a name="default-values-for-health-probe"></a>상태 프로브의 기본값
준비/누수 프로브에서 유추할 수 없는 속성의 경우 기본값이 설정됩니다.

| 응용 프로그램 게이트웨이 프로브 속성 | 기본값 |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |