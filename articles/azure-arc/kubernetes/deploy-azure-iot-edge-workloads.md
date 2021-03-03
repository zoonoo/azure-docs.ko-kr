---
title: Azure IoT Edge 워크로드 배포(미리 보기)
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure IoT Edge 워크로드 배포
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너
ms.openlocfilehash: c352455b69360df0b26f5aac38fc40ccb30fb9de
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650416"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Azure IoT Edge 워크로드 배포(미리 보기)

## <a name="overview"></a>개요

Azure Arc와 Azure IoT Edge는 서로의 기능을 쉽게 보완할 있습니다. 

Azure Arc는 클러스터 운영자가 클러스터의 기본 구성 요소를 구성 하 고 클러스터 정책을 적용 하 고 적용 하는 메커니즘을 제공 합니다. 

Azure IoT Edge를 사용 하면 응용 프로그램 운영자가 편리한 클라우드 수집 및 양방향 통신 기본 형식을 사용 하 여 대규모로 워크 로드를 원격으로 배포 하 고 관리할 수 있습니다. 

아래 다이어그램은 Azure Arc 및 Azure IoT Edge의 관계를 보여 줍니다.

![IoT Arc 구성](./media/edge-arc.png)

## <a name="pre-requisites"></a>필수 구성 요소

* [IoT Edge 디바이스 등록](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) 및 [시뮬레이트된 온도 센서 모듈 배포](../../iot-edge/quickstart-linux.md#deploy-a-module) 아래에서 언급 한 값에 대 한 장치의 연결 문자열을 적어둡니다 *.*

* [Kubernetes에 대한 IoT Edge 지원](https://aka.ms/edgek8sdoc)을 사용하여 Azure Arc의 Flux 연산자를 통해 배포합니다.

* IoT Edge 투구 chart에 대 한 [*yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) 파일을 다운로드 하 고 `deviceConnectionString` 파일의 끝에 있는 자리 표시자를 앞에서 기록한 연결 문자열로 바꿉니다. 필요에 따라 지원 되는 기타 차트 설치 옵션을 설정 합니다. IoT Edge 작업에 대 한 네임 스페이스를 만들고 암호를 생성 합니다.

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  [클러스터 구성 예제](./tutorial-use-gitops-connected-cluster.md)를 사용 하 여 원격으로 설정할 수도 있습니다.

## <a name="connect-a-cluster"></a>클러스터 연결

`az`Azure CLI 확장을 사용 `connectedk8s` 하 여 Azure Arc에 Kubernetes 클러스터를 연결 합니다.

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>IoT Edge의 구성 만들기

[예제 Git 리포지토리](https://github.com/veyalla/edgearc) 는 IoT Edge 투구 차트를 가리키고 필수 조건 섹션에서 만든 비밀을 참조 합니다.

Azure CLI 확장을 사용 하 여 `az` `k8s-configuration` 연결 된 클러스터를 Git 리포지토리에 연결 하는 구성을 만듭니다.

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

몇 분 안에 클러스터의 네임 스페이스에 배포 된 IoT Edge 작업 모듈이 표시 됩니다 `iotedge` . 

`SimulatedTemperatureSensor`해당 네임 스페이스의 pod 로그를 확인 하 여 생성 되는 샘플 값을 확인 합니다. [Visual Studio Code용 Azure IoT Hub Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 IoT Hub에 메시지가 들어오는 것을 확인할 수도 있습니다.

## <a name="cleanup"></a>정리

다음을 사용 하 여 구성을 제거 합니다.

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>다음 단계

[Azure Policy를 사용하여 클러스터 구성 관리](./use-azure-policy.md)
