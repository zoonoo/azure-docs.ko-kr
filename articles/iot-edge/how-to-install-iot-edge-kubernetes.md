---
title: Kubernetes에 IoT Edge를 설치 하는 방법 | Microsoft Docs
description: 로컬 개발 클러스터 환경을 사용 하 여 Kubernetes에 IoT Edge를 설치 하는 방법에 대해 알아봅니다.
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d11d23cf7d96482028a9d3738196fc5a787fec91
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510212"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Kubernetes에 IoT Edge를 설치 하는 방법 (미리 보기)

IoT Edge를 사용 하 여 복원 력이 높은 고가용성 인프라 계층으로 사용 하는 Kubernetes와 통합할 수 있습니다. Kubernetes API 서버를 사용 하 여 IoT Edge *사용자 지정 리소스 정의* (CRD)를 등록 합니다. 또한 로컬 클러스터 상태를 사용 하 여 클라우드 관리 desired 상태를 조정 하는 *운영자* (IoT Edge 에이전트)를 제공 합니다.

모듈 수명은 Kubernetes scheduler에서 관리 하며, 모듈 가용성을 유지 하 고 배치를 선택 합니다. IoT Edge는 위쪽에서 실행 되는 edge 응용 프로그램 플랫폼을 관리 하 고, IoT Hub에 지정 된 원하는 상태를에 지 클러스터의 상태로 지속적으로 조정 합니다. Edge 응용 프로그램 모델은 IoT Edge 모듈과 경로를 기반으로 하는 친숙 한 모델입니다. IoT Edge agent 연산자는 pod, 배포, 서비스 등과 같은 Kubernetes natives 구문으로 *자동* 번역을 수행 합니다.

개략적인 아키텍처 다이어그램은 다음과 같습니다.

![kubernetes 아치](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Edge 배포의 모든 구성 요소는 장치와 관련 된 Kubernetes 네임 스페이스로 범위가 지정 되므로 동일한 클러스터 리소스를 여러 edge 장치 및 해당 배포에서 공유할 수 있습니다.

>[!NOTE]
>Kubernetes의 IoT Edge는 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)상태입니다.

## <a name="install-locally-for-a-quick-test-environment"></a>빠른 테스트 환경에 대 한 로컬 설치

### <a name="prerequisites"></a>필수 조건

* Kubernetes 1.10 이상. 기존 클러스터 설정이 없는 경우 로컬 클러스터 환경에 [Minikube](https://kubernetes.io/docs/setup/minikube/) 를 사용할 수 있습니다.

* Kubernetes 패키지 관리자 인 [투구](https://helm.sh/docs/using_helm/#quickstart-guide).

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 를 사용 하 여 클러스터를 보고 상호 작용 합니다.

### <a name="setup-steps"></a>설정 단계

1. **Minikube** 시작

    ``` shell
    minikube start
    ```

1. 클러스터에서 **투구** 서버 구성 요소 (*tiller*)를 초기화 합니다.

    ``` shell
    helm init
    ```

1. IoT Edge 리포지토리 추가 및 투구 설치 업데이트

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [IoT Hub 만들고](../iot-hub/iot-hub-create-through-portal.md), [IoT Edge 장치를 등록](how-to-register-device.md)하 고, 연결 문자열을 기록해 둡니다.

1. 클러스터에 iotedged 나 IoT Edge 에이전트를 설치 합니다.

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```

1. 브라우저에서 Kubernetes 대시보드를 엽니다.

    ```shell
    minikube dashboard
    ```

    클러스터 네임 스페이스 아래에 IoT Edge 장치에 대 한 *\<iothub >\<-name >* -name 규칙 다음에 표시 됩니다. 이 네임 스페이스에는 IoT Edge 에이전트 및 ipod 가장자리가 실행 되어야 합니다.

1. 빠른 시작의 [모듈 배포](quickstart-linux.md#deploy-a-module) 섹션에 있는 단계를 사용 하 여 시뮬레이션 된 온도 센서 모듈을 추가 합니다. IoT Edge 모듈 관리는 다른 IoT Edge 장치와 마찬가지로 IoT Hub 포털에서 수행 됩니다. Kubernetes 도구를 통해 모듈 구성의 로컬 변경을 수행 하는 것은 덮어쓸 수 있으므로 권장 되지 않습니다.

1. 몇 초 후에는 대시보드의 edge 장치 네임 스페이스 아래에 있는 **pod** 페이지를 새로 고치면 IoT Edge hub pod 수집 IoT Hub 데이터를 사용 하 여 실행 되는 것으로 IoT Edge 허브 및 시뮬레이션 된 센서 pod 나열 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Edge 배포에 의해 생성 된 모든 리소스를 제거 하려면 이전 섹션의 5 단계에서 사용한 이름과 함께 다음 명령을 사용 합니다.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>다음 단계

### <a name="deploy-as-a-highly-available-edge-gateway"></a>항상 사용 가능한 edge 게이트웨이로 배포

Kubernetes 클러스터의에 지 장치를 다운스트림 장치에 대 한 IoT 게이트웨이로 사용할 수 있습니다. 노드 오류에 대 한 복원 력을 제공 하 여에 지 배포에 고가용성을 제공 하도록 구성할 수 있습니다. 이 시나리오에서 IoT Edge를 사용 하려면이 [자세한 연습](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) 을 참조 하세요.
