---
title: Azure IoT Edge 워크로드 배포(미리 보기)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure IoT Edge 워크로드 배포
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너
ms.openlocfilehash: bfaa43a03ddd98616b22fc3fc7b4dccb4c38f44c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86103979"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Azure IoT Edge 워크로드 배포(미리 보기)

## <a name="overview"></a>개요

Azure Arc 및 Azure IoT Edge는 서로의 기능을 보완합니다. Azure Arc는 클러스터 운영자가 클러스터에 대한 기본 구성 요소를 구성하고 클러스터 정책을 적용하기 위한 메커니즘을 제공합니다. 또한 IoT Edge를 사용하면 애플리케이션 운영자는 편리한 클라우드 수집 및 양방향 통신 기본 형식을 사용하여 대규모로 워크로드를 원격으로 배포 및 관리할 수 있습니다. 아래 다이어그램에서는 이러한 과정을 보여 줍니다.

![IoT Arc 구성](./media/edge-arc.png)

## <a name="pre-requisites"></a>필수 구성 요소

* [IoT Edge 디바이스 등록](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) 및 [시뮬레이트된 온도 센서 모듈 배포](../../iot-edge/quickstart-linux.md#deploy-a-module) 디바이스의 연결 문자열을 확인해야 합니다.

* [Kubernetes에 대한 IoT Edge 지원](https://aka.ms/edgek8sdoc)을 사용하여 Azure Arc의 Flux 연산자를 통해 배포합니다.

* IoT Edge Helm 차트에 대한 [**values.yaml**](https://github.com/Azure/iotedge/blob/master/kubernetes/charts/edge-kubernetes/values.yaml) 파일을 다운로드하고, 파일 끝에 있는 **deviceConnectionString** 자리 표시자를 1단계에서 적어 둔 내용으로 바꿉니다. 필요에 따라 지원되는 다른 차트 설치 옵션을 설정할 수 있습니다. IoT Edge 작업에 대 한 네임 스페이스를 만들고 여기에 비밀을 만듭니다.

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    [클러스터 구성 예제](./use-gitops-connected-cluster.md)를 사용하여 원격으로 설정할 수도 있습니다.

## <a name="connect-a-cluster"></a>클러스터 연결

`az` CLI `connectedk8s` 확장을 사용하여 Kubernetes 클러스터를 Azure Arc에 연결합니다.

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>IoT Edge의 구성 만들기

예제 리포지토리: https://github.com/veyalla/edgearc

이 리포지토리는 IoT Edge Helm 차트를 가리키며 필수 조건 섹션에서 만든 비밀을 참조합니다.

1. `az` CLI `k8sconfiguration` 확장을 사용하여 연결된 클러스터를 git 리포지토리에 연결하는 구성을 만듭니다.

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    1~2분 내에 클러스터의 `iotedge` 네임스페이스에 배포된 IoT Edge 작업 모듈이 표시됩니다. 해당 네임스페이스에서 `SimulatedTemperatureSensor` pod의 로그를 확인하여 생성되는 샘플 값을 확인할 수 있습니다. [Visual Studio Code용 Azure IoT Hub Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 IoT Hub에 메시지가 들어오는 것을 확인할 수도 있습니다.

## <a name="cleanup"></a>정리

다음을 사용하여 구성을 제거할 수 있습니다.

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>다음 단계

[Azure Policy를 사용하여 클러스터 구성 관리](./use-azure-policy.md)
