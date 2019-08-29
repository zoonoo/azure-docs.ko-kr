---
title: KEDA를 사용 하 여 Kubernetes에서 Azure Functions
description: Kubernetes 기반 이벤트 기반 자동 크기 조정을 사용 하 여 클라우드 또는 온-프레미스에서 Kubernetes의 Azure Functions를 실행 하는 방법에 대해 알아봅니다.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure 함수, 함수, 이벤트 처리, 동적 계산, 서버를 사용 하지 않는 아키텍처, kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: b581d7c9b5876813e36ebbf41be713b44dd97735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096087"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>KEDA를 사용 하 여 Kubernetes에서 Azure Functions

Azure Functions 런타임은 원하는 위치와 방법을 호스팅할 수 있는 유연성을 제공 합니다.  [Keda](https://github.com/kedacore/kore) (Kubernetes 기반 이벤트 기반 자동 크기 조정)은 Azure Functions 런타임과 도구를 사용 하 여 Kubernetes에서 이벤트 중심 크기 조정을 제공 합니다.

## <a name="how-kubernetes-based-functions-work"></a>Kubernetes 기반 함수의 작동 방식

Azure Functions 서비스는 런타임 및 크기 조정 컨트롤러의 두 가지 주요 구성 요소로 구성 됩니다.  함수 런타임은 코드를 실행 하 고 실행 합니다.  런타임에는 함수 실행을 트리거, 로그 및 관리 하는 방법에 대 한 논리가 포함 되어 있습니다.  다른 구성 요소는 크기 조정 컨트롤러입니다.  크기 조정 컨트롤러는 함수를 대상으로 하는 이벤트의 비율을 모니터링 하 고 앱을 실행 하는 인스턴스 수를 사전에 확장 합니다.  자세한 내용은 [Azure Functions 크기 조정 및 호스팅](functions-scale.md)을 참조하세요.

Kubernetes 기반 함수는 KEDA를 통해 이벤트 기반 크기 조정을 사용 하 여 [Docker 컨테이너](functions-create-function-linux-custom-image.md) 에서 함수 런타임을 제공 합니다.  KEDA는 이벤트가 발생 하지 않는 경우 0 개 인스턴스 및 최대 *n* 개의 인스턴스로 확장할 수 있습니다. Kubernetes autoscaler (수평 Pod Autoscaler)에 대 한 사용자 지정 메트릭을 노출 하 여이를 수행 합니다.  KEDA에서 함수 컨테이너를 사용 하면 Kubernetes 클러스터에서 서버를 사용 하지 않는 함수 기능을 복제할 수 있습니다.  서버 리스 인프라의 [AKS (Azure Kubernetes Services) 가상 노드](../aks/virtual-nodes-cli.md) 기능을 사용 하 여 이러한 함수를 배포할 수도 있습니다.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Kubernetes에서 KEDA 및 함수 관리

Kubernetes 클러스터에서 함수를 실행 하려면 KEDA 구성 요소를 설치 해야 합니다. [Azure Functions Core Tools](functions-run-local.md)를 사용 하 여이 구성 요소를 설치할 수 있습니다.

### <a name="installing-with-the-azure-functions-core-tools"></a>Azure Functions Core Tools를 사용 하 여 설치

기본적으로 핵심 도구는 각각 이벤트 기반 및 HTTP 크기 조정을 지 원하는 KEDA 및 오시리스 구성 요소를 모두 설치 합니다.  설치는 현재 `kubectl` 컨텍스트에서를 실행 합니다.

다음 설치 명령을 실행 하 여 클러스터에 KEDA를 설치 합니다.

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Kubernetes에 함수 앱 배포

Kubernetes를 실행 하는 모든 함수 앱을 KEDA를 실행 하는 클러스터에 배포할 수 있습니다.  함수는 Docker 컨테이너에서 실행 되므로 프로젝트에는가 `Dockerfile`필요 합니다.  아직 없는 경우 함수 프로젝트의 루트에서 다음 명령을 실행 하 여 Dockerfile을 추가할 수 있습니다.

```cli
func init --docker-only
```

이미지를 빌드하고 함수를 Kubernetes에 배포 하려면 다음 명령을 실행 합니다.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> `<name-of-function-deployment>`은 함수 앱 이름으로 바꿉니다.

그러면 `Deployment` 파일에서`local.settings.json` 가져온 환경 변수를 `ScaledObject` 포함 하는 `Secrets`Kubernetes 리소스 (리소스)가 생성 됩니다.

## <a name="removing-a-function-app-from-kubernetes"></a>Kubernetes에서 함수 앱 제거

배포한 후에는 `Deployment` `Secrets` 만든 연결 `ScaledObject`된를 제거 하 여 함수를 제거할 수 있습니다.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Kubernetes에서 KEDA 제거

다음 핵심 도구 명령을 실행 하 여 Kubernetes 클러스터에서 KEDA를 제거할 수 있습니다.

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>KEDA에서 지원 되는 트리거

KEDA는 현재 베타 버전으로, 다음 Azure Function 트리거를 지원 합니다.

* [Azure Storage 큐](functions-bindings-storage-queue.md)
* [Azure Service Bus 큐](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하십시오.

* [사용자 지정 이미지를 사용 하 여 함수 만들기](functions-create-function-linux-custom-image.md)
* [Azure Functions를 로컬에서 코딩 및 테스트](functions-develop-local.md)
* [Azure 함수 소비 계획의 작동 원리](functions-scale.md)