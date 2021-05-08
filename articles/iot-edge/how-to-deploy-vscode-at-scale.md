---
title: Visual Studio Code를 사용하여 대규모로 모듈 배포 - Azure IoT Edge
description: Visual Studio Code용 IoT 확장을 사용하여 IoT Edge 디바이스 그룹에 대한 자동 배포를 만듭니다.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a20992ef26b74bcc37a7403e4ee77cacc0f8f66e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200287"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Visual Studio Code를 사용하여 대규모로 IoT Edge 모듈 배포

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Visual Studio Code를 사용하여 한 번에 여러 디바이스에 대한 지속적인 배포를 관리하는 **IoT Edge 자동 배포** 를 만들 수 있습니다. IoT Edge의 자동 배포는 IoT Hub의 [자동 디바이스 관리](../iot-hub/iot-hub-automatic-device-management.md) 기능의 일부입니다. 배포는 다수의 모듈을 다수의 디바이스에 배포할 수 있도록 하는 동적인 프로세스입니다. 모듈의 상태를 추적하고 필요한 경우 변경할 수도 있습니다.

자세한 내용은 [단일 디바이스 또는 대규모 IoT Edge 자동 배포에 대한 이해](module-deployment-monitoring.md)를 참조하세요.

이 문서에서는 Visual Studio Code 및 IoT 확장을 설정합니다. 그런 다음 IoT Edge 디바이스 집합에 모듈을 배포하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)
* 하나 이상의 IoT Edge 디바이스.

  IoT Edge 디바이스를 설정하지 않은 경우 Azure 가상 머신에서 만들 수 있습니다. 빠른 시작 문서 중 하나에 있는 단계에 따라 [가상 Linux 디바이스를 만들거나](quickstart-linux.md) [가상 Windows 디바이스를 만듭니다](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)

## <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

Visual Studio Code용 Azure IoT 확장을 사용하여 Hub에 대한 작업을 수행할 수 있습니다. 해당 작업이 수행되려면 Azure 계정에 로그인하고 작업 중인 IoT Hub를 선택해야 합니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.

1. 탐색기의 맨 아래에서 **Azure IoT Hub** 섹션을 확장합니다.

1. **Azure IoT Hub** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 마우스로 가리킵니다.

1. **IoT Hub 선택** 을 선택합니다.

1. Azure 계정에 로그인하지 않은 경우, 프롬프트를 따라 로그인합니다.

1. Azure 구독을 선택합니다.

1. IoT Hub를 선택합니다.

## <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 어떤 모듈을 배포할지 설명하는 JSON 문서입니다. 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성 또한 설명합니다. 자세한 내용은 [IoT Edge에서 모듈 배포 및 경로를 설정하는 방법 알아보기](module-composition.md)을 참조하세요.

Visual Studio Code를 사용하여 모듈을 배포하려면 배포 매니페스트를 로컬에 .JSON 파일로 저장합니다. 디바이스에 구성을 적용하도록 하는 명령을 실행할 때는 해당 위치를 제공해야 합니다.

예를 들어 한 개의 모듈이 있는 기본 배포 매니페스트의 예제는 다음과 같습니다.

>[!NOTE]
>이 샘플 배포 매니페스트는 IoT Edge 에이전트 및 허브용 스키마 버전 1.1을 사용합니다. 스키마 버전 1.1은 IoT Edge 버전 1.0.10과 함께 출시되었으며 모듈 시작 순서 및 경로 우선 순위와 같은 기능을 사용 설정합니다.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

현재 구성할 수 있는 IoT Edge 디바이스를 확인해야 하는 경우 **IoT Edge: 디바이스 정보 가져오기** 명령을 실행합니다.

## <a name="identify-devices-with-target-conditions"></a>대상 조건으로 디바이스 식별

배포를 수신할 IoT Edge 디바이스를 식별하려면 대상 조건을 지정해야 합니다. 지정된 조건이 deviceId, tag 값 또는 reported 속성 값과 일치하는 경우 대상 조건이 충족됩니다.

디바이스 쌍에서 태그를 구성합니다. 다음은 태그를 포함하는 디바이스 쌍의 예입니다.

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

배포를 위한 대상 조건에 `tag.location.building = '20'`과 같은 태그 값 중 하나와 일치하는 식이 포함된 경우 해당 디바이스는 배포를 수신하게 됩니다.

태그 또는 다른 값과 관계없이 특정 디바이스를 대상으로 지정하려면 대상 조건으로 `deviceId`를 지정하면 됩니다.

다음은 추가적인 예입니다.

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' OR deviceId = 'linuxprod2' OR deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' AND tags.environment = 'prod' AND NOT deviceId = 'linuxprod1'

자세한 내용은 [대상 조건](module-deployment-monitoring.md#target-condition)을 참조하세요. 디바이스 쌍 및 태그에 대한 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.

### <a name="edit-the-device-twin"></a>디바이스 쌍 편집

Visual Studio Code에서 디바이스 쌍을 편집하여 태그를 구성할 수 있습니다. **보기** 메뉴에서 **명령 팔레트** 를 선택하고 **IoT Edge: 디바이스 쌍 편집** 명령을 실행합니다. IoT Edge 디바이스를 선택하면 디바이스 쌍이 표시됩니다.

이 예제에서는 태그가 정의되지 않았습니다. 현재 비어 있는 섹션 `"tags": {}`을 원하는 태그 정의로 바꿉니다.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

로컬 파일을 저장한 후 **IoT Edge: 디바이스 쌍 업데이트** 명령을 실행합니다.

## <a name="create-deployment-at-scale"></a>대규모로 배포 만들기

디바이스 쌍에서 배포 매니페스트 및 구성된 태그를 구성하고 나면 배포할 준비가 완료된 것입니다.

1. **보기** 메뉴에서 **명령 팔레트** 를 선택하고 **Azure IoT Edge: 대규모로 배포 만들기** 명령을 선택합니다.

1. 사용하려는 배포 매니페스트 JSON 파일로 이동하고 **에지 배포 매니페스트 선택** 을 클릭합니다.

1. **배포 ID** 부터 시작하여 프롬프트에 따라 값을 입력합니다.

   ![배포 ID 지정](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   다음 매개 변수에 대한 값을 지정합니다.

  | 매개 변수 | Description |
  | --- | --- |
  | 배포 ID | IoT Hub에 만들 배포 이름입니다. 배포에 최대 128자의 소문자로 된 고유한 이름을 지정합니다. 공백과 잘못된 문자(`& ^ [ ] { } \ | " < > /`)는 사용하지 않도록 합니다. |
  | 대상 조건 | 대상 조건을 입력하여 이 배포의 대상으로 지정할 디바이스를 결정합니다.  조건은 디바이스 쌍 태그 또는 보고되는 디바이스 쌍 속성을 기반으로 하며, 표현 형식이 일치해야 합니다.  `tags.environment='test' and properties.reported.devicemodel='4000x'`)을 입력합니다. |
  | 우선 순위 |  양의 정수 둘 이상의 배포가 동일한 디바이스를 대상으로 하는 경우, Priority의 숫자 값이 가장 큰 배포가 적용됩니다. |

  우선 순위를 지정하면 터미널은 다음 표현과 유사한 출력을 표시합니다.

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>배포 모니터링 및 수정

[Azure Portal](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) 또는 [Azure CLI](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)를 사용하여 배포를 모니터링, 수정 및 삭제할 수 있습니다. 두 방법 모두 배포에 대한 메트릭을 제공합니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 디바이스에 모듈 배포](module-deployment-monitoring.md)에 대해 자세히 알아봅니다.