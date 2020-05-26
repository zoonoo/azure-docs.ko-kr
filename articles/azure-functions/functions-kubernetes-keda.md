---
title: Kubernetes의 Azure Functions 및 KEDA
description: Kubernetes-based event-driven autoscaling(KEDA, Kubernetes 기반 이벤트 기반 자동 크기 조정)을 사용하여 클라우드 또는 온-프레미스의 Kubernetes에서 Azure Functions를 실행하는 방법에 대해 알아봅니다.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 5c050eb38e47ce2ab9d11e5c92eb7bdd3ac8e572
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121697"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Kubernetes의 Azure Functions 및 KEDA

Azure Functions 런타임을 사용하면 원하는 위치에 원하는 방식으로 유연하게 호스팅할 수 있습니다.  [KEDA](https://keda.sh)(Kubernetes-based event-driven autoscaling)은 Azure Functions 런타임, 도구와 결합하여 Kubernetes에서 이벤트 기반 스케일을 제공합니다.

## <a name="how-kubernetes-based-functions-work"></a>Kubernetes 기반 함수의 작동 방식

Azure Functions 서비스는 런타임과 크기 조정 컨트롤러라는 두 가지 주요 구성 요소로 구성됩니다.  Functions 런타임은 코드를 실행합니다.  런타임에는 함수 실행을 트리거, 로그 및 관리하는 방법에 대한 논리가 포함되어 있습니다.  Azure Functions 런타임은 *어디에서나* 사용할 수 있습니다.  다른 구성 요소는 크기 조정 컨트롤러입니다.  크기 조정 컨트롤러는 함수를 대상으로 하는 이벤트의 비율을 모니터링하고 앱을 실행하는 인스턴스 수를 사전에 조정합니다.  자세한 내용은 [Azure Functions 크기 조정 및 호스팅](functions-scale.md)을 참조하세요.

Kubernetes 기반 Functions는 KEDA를 통해 이벤트 기반 스케일링을 사용하여 [Docker 컨테이너](functions-create-function-linux-custom-image.md)에서 Functions 런타임을 제공합니다.  KEDA는 (이벤트가 발생하지 않을 때) 0개의 인스턴스로 스케일 인하고 *n*개의 인스턴스로 스케일 아웃할 수 있습니다. Kubernetes 자동 크기 조정기(Horizontal Pod Autoscaler)에 대한 사용자 지정 메트릭을 노출하여 이를 수행합니다.  KEDA에서 Functions 컨테이너를 사용하면 Kubernetes 클러스터 종류에 관계없이 서버리스 함수 기능을 복제할 수 있습니다.  이러한 함수는 서버리스 인프라 구현에 필요한 [AKS(Azure Kubernetes Services) 가상 노드](../aks/virtual-nodes-cli.md) 기능을 통해 배포할 수도 있습니다.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Kubernetes에서 KEDA 및 함수 관리

Kubernetes 클러스터에서 Functions를 실행하려면 KEDA 구성 요소를 설치해야 합니다. [Azure Functions Core Tools](functions-run-local.md)를 사용하여 이 구성 요소를 설치할 수 있습니다.

### <a name="installing-with-helm"></a>Helm을 사용하여 설치

Helm을 포함하여 Kubernetes 클러스터에 KEDA를 설치하는 다양한 방법이 있습니다.  배포 옵션은 [KEDA 사이트](https://keda.sh/docs/deploy/)에 설명되어 있습니다.

## <a name="deploying-a-function-app-to-kubernetes"></a>Kubernetes에 함수 앱 배포

모든 함수 앱을 KEDA를 실행하는 Kubernetes를 실행하는 클러스터에 배포할 수 있습니다.  함수는 Docker 컨테이너에서 실행되므로 프로젝트에 `Dockerfile`이(가) 필요합니다.  아직 없는 경우 Functions 프로젝트의 루트에서 다음 명령을 실행하여 Dockerfile을 추가할 수 있습니다.

```cli
func init --docker-only
```

이미지를 빌드하고 함수를 Kubernetes에 배포하려면 다음 명령을 실행합니다.

> [!NOTE]
> Core Tools는 Docker CLI를 활용하여 이미지를 빌드하고 게시합니다. Docker를 미리 설치하고 `docker login`을 사용하여 계정에 연결해야 합니다.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> `<name-of-function-deployment>`은 함수 앱 이름으로 바꿉니다.

그러면 `local.settings.json` 파일에서 가져온 환경 변수를 포함하는 Kubernetes `Deployment` 리소스, `ScaledObject` 리소스 및 `Secrets`이(가) 만들어집니다.

### <a name="deploying-a-function-app-from-a-private-registry"></a>프라이빗 레지스트리의 함수 앱 배포

위의 흐름은 프라이빗 레지스트리에서도 작동합니다.  프라이빗 레지스트리에서 컨테이너 이미지를 끌어오는 경우 `func kubernetes deploy`을(를) 실행할 때 프라이빗 레지스트리 자격 증명을 포함하는 Kubernetes 비밀을 참조하는 `--pull-secret` 플래그를 포함합니다.

## <a name="removing-a-function-app-from-kubernetes"></a>Kubernetes에서 함수 앱 제거

배포한 후에는 연결된 `Deployment`, `ScaledObject`, 생성된 `Secrets`을(를) 제거하여 함수를 제거할 수 있습니다.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Kubernetes에서 KEDA 제거

KEDA를 제거하는 단계는 [KEDA 사이트에](https://keda.sh/deploy/) 설명되어 있습니다.

## <a name="supported-triggers-in-keda"></a>KEDA에서 지원되는 트리거

KEDA는 다음 Azure 함수 트리거를 지원합니다.

* [Azure Storage 큐](functions-bindings-storage-queue.md)
* [Azure Service Bus 큐](functions-bindings-service-bus.md)
* [Azure Event/IoT Hub](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ 큐](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP 트리거 지원

HTTP 트리거를 노출하는 Azure Functions를 사용할 수 있지만 KEDA는 직접 관리하지 않습니다.  KEDA prometheus 트리거를 활용하여 [HTTP Azure Functions 1에서 *n* 인스턴스까지 확장할 수 있습니다](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [사용자 지정 이미지를 사용하여 함수 만들기](functions-create-function-linux-custom-image.md)
* [Azure Functions를 로컬에서 코딩 및 테스트](functions-develop-local.md)
* [Azure 함수 소비 플랜의 작동 방식](functions-scale.md)