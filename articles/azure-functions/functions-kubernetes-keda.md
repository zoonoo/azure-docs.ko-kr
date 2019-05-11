---
title: KEDA 사용 하 여 Kubernetes에서 azure Functions
description: 클라우드에서 Kubernetes에서 Azure Functions를 실행 하는 방법을 이해 또는 온-프레미스 KEDA, Kubernetes 기반 이벤트 기반 자동 크기 조정을 사용 합니다.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure functions, 함수, 이벤트 처리, 동적 계산, 서버가 없는 아키텍처, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077622"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>KEDA 사용 하 여 Kubernetes에서 azure Functions

Azure Functions 런타임 호스팅에 유연성을 원하는 위치와 방법을 제공 합니다.  [KEDA](https://github.com/kedacore/kore) (Kubernetes 기반 이벤트 기반 자동 크기 조정)는 Azure Functions 런타임 및 Kubernetes에서 이벤트 기반 확장을 제공 하는 도구를 사용 하 여 원활 하 게 쌍입니다.

## <a name="how-kubernetes-based-functions-work"></a>Kubernetes 기반 함수 작업

Azure Functions 서비스는 두 가지 주요 구성 요소 이루어져: 런타임 및 크기 조정 컨트롤러입니다.  Functions 런타임은 실행 하 고 코드를 실행 합니다.  런타임에서 트리거, 로그 및 함수 실행을 관리 하는 방법에 대 한 논리를 포함 합니다.  다른 구성 요소는 크기 조정 컨트롤러입니다.  크기 조정 컨트롤러는 함수를 대상으로 하는 이벤트의 속도 모니터링 하 고 사전에 앱을 실행 하는 인스턴스 수를 확장 합니다.  자세한 내용은 참조 하세요 [Azure Functions 크기 조정 및 호스팅](functions-scale.md)합니다.

Kubernetes 기반 함수에서 함수 런타임은 제공을 [Docker 컨테이너](functions-create-function-linux-custom-image.md) KEDA 통해 크기 조정 이벤트를 기반으로 합니다.  KEDA 규모를 축소할 수 (이벤트가 발생 하는) 하는 경우 0 인스턴스를 최대 *n* 인스턴스. Kubernetes autoscaler (가로 Pod Autoscaler)에 대 한 사용자 지정 메트릭을 노출 하 여 수행 합니다.  함수 컨테이너를 사용 하 여 KEDA를 사용 하 여 Kubernetes 클러스터에서 서버 리스 함수 기능을 복제할 수 있습니다.  사용 하 여 이러한 함수 배포할 수도 있습니다 [Azure Kubernetes 서비스 (AKS) 가상 노드](../aks/virtual-nodes-cli.md) 서버 리스 인프라에 대 한 기능입니다.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Kubernetes에서 함수와 KEDA 관리

함수에서 Kubernetes 클러스터를 실행 하려면 KEDA 구성 요소를 설치 해야 합니다. 사용 하 여이 구성 요소를 설치할 수 있습니다 [Azure Functions 핵심 도구](functions-run-local.md)합니다.

### <a name="installing-with-the-azure-functions-core-tools"></a>Azure Functions를 사용 하 여 핵심 도구 설치

기본적으로 Core Tools 설치 문제를 이벤트 구동 지 KEDA 및 시체 구성 요소와 HTTP 크기 조정 하는 데 각각 합니다.  설치를 사용 하 여 `kubectl` 현재 컨텍스트에서 실행 됩니다.

설치 명령을 실행 하 여 클러스터에 KEDA를 설치 합니다.

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Kubernetes에 함수 앱 배포

KEDA를 실행 하는 Kubernetes 클러스터에 모든 함수 앱을 배포할 수 있습니다.  프로젝트에 필요한 함수를 Docker 컨테이너에서 실행 되므로 `Dockerfile`합니다.  아직 없는 경우 하나, Functions 프로젝트의 루트에서 다음 명령을 실행 하 여 Dockerfile을 추가할 수 있습니다.

```cli
func init --docker-only
```

이미지를 빌드하고 함수 Kubernetes에 배포 하려면 다음 명령을 실행 합니다.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> `<name-of-function-deployment>`은 함수 앱 이름으로 바꿉니다.

Kubernetes를 이렇게 `Deployment` 리소스를 `ScaledObject` 리소스 및 `Secrets`에서 가져온 환경 변수를 포함 하는 프로그램 `local.settings.json` 파일입니다.

## <a name="removing-a-function-app-from-kubernetes"></a>Kubernetes에서 함수 앱을 제거합니다.

연결을 제거 하 여 함수를 제거할 수 있습니다 배포 후 `Deployment`, `ScaledObject`, `Secrets` 생성 합니다.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Kubernetes에서 KEDA 제거

KEDA Kubernetes 클러스터를 제거 하려면 다음 핵심 도구 명령을 실행할 수 있습니다.

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>KEDA에서 지원 되는 트리거

KEDA는 현재 베타 버전 다음 Azure Function 트리거를 지원 합니다.

* [Azure Storage 큐](functions-bindings-storage-queue.md)
* [Azure Service Bus 큐](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [사용자 지정 이미지를 사용 하는 함수 만들기](functions-create-function-linux-custom-image.md)
* [Azure Functions를 로컬에서 코딩 및 테스트](functions-develop-local.md)
* [Azure Function 소비 계획의 작동 원리](functions-scale.md)