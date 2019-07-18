---
title: Kubernetes에서 IoT Edge를 설치 하는 방법 | Microsoft Docs
description: 로컬 개발 클러스터 환경을 사용 하 여 Kubernetes에서 IoT Edge를 설치 하는 방법에 알아봅니다.
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160697"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Kubernetes (미리 보기)에서 IoT Edge를 설치 하는 방법

IoT Edge는 Kubernetes를 사용 하 여 복원 력 있는 고가용성 인프라 계층으로 통합할 수 있습니다. 등록 하는 IoT Edge *사용자 지정 리소스 정의* (CRD) Kubernetes API 서버를 사용 하 여 합니다. 제공 합니다는 *연산자* (IoT Edge 에이전트)는 로컬 클러스터 상태를 사용 하 여 클라우드 관리 원하는 상태를 조정 합니다. 

모듈 수명 모듈 가용성을 유지 관리 하 고 배치를 선택 하는 Kubernetes 스케줄러에 의해 관리 됩니다. IoT Edge는 edge 클러스터의 상태를 사용 하 여 IoT Hub에 지정 된 원하는 상태를 지속적으로 조정 위쪽에서 실행 중인 edge 응용 프로그램 플랫폼을 관리 합니다. Edge 응용 프로그램 모델은 여전히 친숙 한 IoT Edge 모듈 및 경로 기반으로 모델. IoT Edge 에이전트 운영자 수행 *자동* pod, 배포, 서비스 등 Kubernetes 원주민 변환 생성 합니다.

다음은 상위 수준 아키텍처 다이어그램:

![kubernetes 아키텍처](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Edge 배포의 모든 구성 요소가 여러 edge 장치 및 해당 배포 간에 동일한 클러스터 리소스를 공유할 수 있도록 장치에 특정 Kubernetes 네임 스페이스에 범위가 지정 됩니다.

>[!NOTE]
>에 Kubernetes에 IoT Edge [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)합니다.

## <a name="install-locally-for-a-quick-test-environment"></a>빠른 테스트 환경에 로컬로 설치

### <a name="prerequisites"></a>필수 조건

* Kubernetes 1.10 이상입니다. 기존 클러스터 설치 프로그램에 없는 경우 사용할 수 있습니다 [Minikube](https://kubernetes.io/docs/setup/minikube/) 로컬 클러스터 환경에 대 한 합니다. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), Kubernetes 패키지 관리자입니다.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 보기 및 클러스터와 상호 작용 합니다.

### <a name="setup-steps"></a>설정 단계

1. 시작 **Minikube**

    ``` shell
    minikube start
    ```

1. 초기화 된 **Helm** 서버 구성 요소 (*tiller*) 클러스터에서

    ``` shell
    helm init
    ```

1. IoT Edge 리포지토리를 추가 하 고 업데이트 된 helm 설치

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [IoT Hub 만들기](../iot-hub/iot-hub-create-through-portal.md), [IoT Edge 장치를 등록](how-to-register-device-portal.md), 연결 문자열을 기록해 둡니다.

1. 클러스터에 iotedged 및 IoT Edge 에이전트 설치

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. 브라우저에서 Kubernetes 대시보드를 열려면

    ```shell
    minikube dashboard
    ```

    클러스터 네임 스페이스는 표시 규칙에 따라 IoT Edge 장치에 대해 *msiot-\<iothub-이름 >-\<edgedevice-이름 >* 합니다. IoT Edge 에이전트 및 iotedged pod이 네임이 스페이스의를 실행 해야 합니다.

1. 단계를 사용 하 여 시뮬레이션 된 온도 센서 모듈을 추가 합니다 [모듈을 배포](quickstart-linux.md#deploy-a-module) 빠른 시작의 섹션입니다. IoT Edge 모듈 관리는 다른 IoT Edge 장치에서와 마찬가지로 IoT Hub 포털에서 수행 됩니다. Kubernetes 도구를 통해 모듈 구성에 대 한 로컬 변경 덮어쓰여질 수 있습니다.으로 권장 되지 않습니다.

1. 몇 초 후 새로 고치는 중에 **Pod** IoT Edge 허브를 사용 하 여 실행으로 시뮬레이션 된 센서 pod 단계인 데이터 수집을 IoT Hub에 pod 및 대시보드에서 edge 장치 네임 스페이스에 있는 페이지에는 IoT Edge 허브 나열 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Edge 배포에서 만든 모든 리소스를 제거 하려면 이전 섹션의 5 단계에서 사용 된 이름을 사용 하 여 다음 명령을 사용 합니다.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>다음 단계

### <a name="deploy-as-a-highly-available-edge-gateway"></a>항상 사용 가능한에 지 게이트웨이 배포 

다운스트림 장치에 대 한 Kubernetes 클러스터에서 edge 장치를 IoT 게이트웨이로 사용할 수 있습니다. 따라서에 지 배포에 고가용성을 제공 하는 노드 오류에도 잘 복원 되도록 구성할 수 있습니다. 이 참조 하세요 [연습은](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) 이 시나리오에서 IoT Edge를 사용 하 합니다.