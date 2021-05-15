---
title: AKS Pod에 상태 프로브 추가
description: 이 문서에서는 Application Gateway를 사용하여 AKS Pod에 상태 프로브(준비 및/또는 활동성)를 추가하는 방법을 설명합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8c8b8b0090877db7abc8fae0e44f928e8b10dcf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84808001"
---
# <a name="add-health-probes-to-your-service"></a>서비스에 상태 프로브 추가
기본적으로 수신 컨트롤러는 노출된 Pod에 대한 HTTP GET 프로브를 프로비저닝합니다.
`deployment`/`pod` 사양에 [준비 또는 활동성 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)를 추가하여 프로브 속성을 사용자 지정할 수 있습니다.

## <a name="with-readinessprobe-or-livenessprobe"></a>`readinessProbe` 또는 `livenessProbe` 사용
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

Kubernetes API 참조:
* [컨테이너 프로브](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet 작업](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` 및 `livenessProbe`는 `httpGet`로 구성된 경우 지원됩니다.
> * 현재 Pod에 노출된 포트 이외의 포트에 대한 프로빙은 지원되지 않습니다.
> * `HttpHeaders`, `InitialDelaySeconds`, `SuccessThreshold`는 지원되지 않습니다.

##  <a name="without-readinessprobe-or-livenessprobe"></a>`readinessProbe` 또는 `livenessProbe` 없음
위의 프로브가 제공되지 않으면 수신 컨트롤러는 서비스가 `backend-path-prefix` 주석에 지정된 `Path` 또는 서비스에 대한 `ingress` 정의에 지정된 `path`에 대해 연결할 수 있다고 가정합니다.

## <a name="default-values-for-health-probe"></a>상태 프로브에 대한 기본값
준비/활동성 프로브에서 유추할 수 없는 모든 속성의 경우 기본값이 설정됩니다.

| Application Gateway 프로브 속성 | 기본값 |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |