---
title: KEDA를 가진 Kubernetes에 Azure 함수
description: Kubernetes 기반 이벤트 기반 자동 크기 조정KEDA를 사용하여 클라우드 또는 온-프레미스에서 Kubernetes에서 Azure 함수를 실행하는 방법을 이해합니다.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301678"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>KEDA를 가진 Kubernetes에 Azure 함수

Azure Functions 런타임은 원하는 위치와 방법을 호스팅할 수 있는 유연성을 제공합니다.  [KEDA(Kubernetes](https://keda.sh) 기반 이벤트 기반 자동 크기 조정)는 Azure 함수 런타임 및 툴링과 원활하게 쌍을 이루어 Kubernetes에서 이벤트 기반 스케일을 제공합니다.

## <a name="how-kubernetes-based-functions-work"></a>Kubernetes 기반 함수의 작동 방식

Azure Functions 서비스는 런타임과 스케일 컨트롤러의 두 가지 주요 구성 요소로 구성됩니다.  함수 런타임은 코드를 실행하고 실행합니다.  런타임에는 함수 실행을 트리거, 로그 및 관리하는 방법에 대한 논리가 포함됩니다.  Azure 함수 런타임은 *어디서나*실행할 수 있습니다.  다른 구성 요소는 축척 컨트롤러입니다.  확장 컨트롤러는 함수를 대상으로 하는 이벤트의 속도를 모니터링하고 앱을 실행하는 인스턴스 수를 사전에 확장합니다.  자세한 내용은 [Azure Functions 크기 조정 및 호스팅](functions-scale.md)을 참조하세요.

Kubernetes 기반 함수는 KEDA를 통해 이벤트 기반 크기 조정을 통해 [Docker 컨테이너에서](functions-create-function-linux-custom-image.md) 함수 런타임을 제공합니다.  KEDA는 이벤트가 발생하지 않는 경우 0개의 인스턴스로 확장하고 *n* 인스턴스로 확장할 수 있습니다. Kubernetes 자동 크기 조정기(수평 포드 자동 크기 조정기)에 대한 사용자 지정 메트릭을 노출하여 이 작업을 수행합니다.  KEDA와 함수 컨테이너를 사용하면 모든 Kubernetes 클러스터에서 서버리스 기능 기능을 복제할 수 있습니다.  이러한 기능은 서버리스 인프라에 대한 [AZURE Kubernetes 서비스(AKS) 가상 노드](../aks/virtual-nodes-cli.md) 기능을 사용하여 배포할 수도 있습니다.

## <a name="managing-keda-and-functions-in-kubernetes"></a>KEDA 및 쿠베네테스의 기능 관리

Kubernetes 클러스터에서 함수를 실행하려면 KEDA 구성 요소를 설치해야 합니다. [Azure Functions 핵심 도구를](functions-run-local.md)사용하여 이 구성 요소를 설치할 수 있습니다.

### <a name="installing-with-helm"></a>투구로 설치

헬름을 포함한 모든 Kubernetes 클러스터에 KEDA를 설치하는 방법에는 여러 가지가 있습니다.  배포 옵션은 [KEDA 사이트에](https://keda.sh/deploy/)설명되어 있습니다.

## <a name="deploying-a-function-app-to-kubernetes"></a>Kubernetes에 함수 앱 배포

KEDA를 실행하는 Kubernetes 클러스터에 모든 함수 앱을 배포할 수 있습니다.  함수는 Docker 컨테이너에서 실행되므로 프로젝트에 는 `Dockerfile`.가 필요합니다.  아직 없는 경우 Functions 프로젝트의 루트에서 다음 명령을 실행 하여 Dockerfile을 추가할 수 있습니다.

```cli
func init --docker-only
```

이미지를 빌드하고 Kubernetes에 함수를 배포하려면 다음 명령을 실행합니다.

> [!NOTE]
> 핵심 도구는 도커 CLI를 활용하여 이미지를 빌드하고 게시합니다. 도커를 이미 설치하고 `docker login`을 사용하여 계정에 연결되어 있어야 합니다.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> `<name-of-function-deployment>`은 함수 앱 이름으로 바꿉니다.

이렇게 하면 파일에서 `Deployment` 가져온 환경 `ScaledObject` 변수가 `Secrets`포함된 Kubernetes 리소스, `local.settings.json` 리소스 및 및 의 환경 변수가 생성됩니다.

### <a name="deploying-a-function-app-from-a-private-registry"></a>개인 레지스트리에서 함수 앱 배포

위의 흐름은 개인 레지스트리에도 작동합니다.  개인 레지스트리에서 컨테이너 이미지를 가져오는 경우 실행 `--pull-secret` `func kubernetes deploy`시 개인 레지스트리 자격 증명을 보유하는 Kubernetes 비밀을 참조하는 플래그를 포함합니다.

## <a name="removing-a-function-app-from-kubernetes"></a>Kubernetes에서 함수 앱 제거

배포 한 후 연결 `Deployment`된 을 제거 `ScaledObject`하 `Secrets` 여 함수를 제거할 수 있습니다 .

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Keda에서 KEDA 제거

KEDA 를 제거하는 단계는 [KEDA 사이트에](https://keda.sh/deploy/)문서화되어 있습니다.

## <a name="supported-triggers-in-keda"></a>KEDA에서 지원되는 트리거

KEDA는 다음과 같은 Azure Function 트리거를 지원합니다.

* [Azure Storage 큐](functions-bindings-storage-queue.md)
* [Azure 서비스 버스 큐](functions-bindings-service-bus.md)
* [Azure 이벤트 / IoT 허브](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [토끼MQ 대기열](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP 트리거 지원

HTTP 트리거를 노출하는 Azure 함수를 사용할 수 있지만 KEDA는 직접 관리하지 않습니다.  KEDA 프로메테우스 트리거를 활용하여 [HTTP Azure 함수를 1개에서 *n* 인스턴스로 확장할](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)수 있습니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 자료를 참조하세요.

* [사용자 지정 이미지를 사용하여 함수 만들기](functions-create-function-linux-custom-image.md)
* [Azure Functions를 로컬에서 코딩 및 테스트](functions-develop-local.md)
* [Azure 함수 소비 계획의 작동 방식](functions-scale.md)