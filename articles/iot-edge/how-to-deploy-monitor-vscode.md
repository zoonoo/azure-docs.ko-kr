---
title: 비주얼 스튜디오 코드 - Azure IoT Edge를 사용하여 대규모 모듈 배포
description: Visual Studio 코드의 IoT 확장을 사용하여 IoT Edge 장치 그룹에 대한 자동 배포를 만듭니다.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774134"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>비주얼 스튜디오 코드를 사용하여 대규모로 IoT 에지 모듈 배포

Visual Studio 코드를 사용하여 **IoT Edge 자동 배포를** 만들어 여러 장치의 지속적인 배포를 한 번에 관리할 수 있습니다. IoT Edge를 위한 자동 배포는 IoT Hub의 [자동 장치 관리](/azure/iot-hub/iot-hub-automatic-device-management) 기능의 일부입니다. 배포는 여러 장치에 여러 모듈을 배포할 수 있는 동적 프로세스입니다. 모듈의 상태와 상태를 추적하고 필요한 경우 변경할 수도 있습니다.

자세한 내용은 [단일 장치 또는 규모에 대한 IoT Edge 자동 배포 이해를](module-deployment-monitoring.md)참조하십시오.

이 문서에서는 Visual Studio 코드 및 IoT 확장을 설정합니다. 그런 다음 IoT Edge 장치 집합에 모듈을 배포하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독의 [IoT 허브입니다.](../iot-hub/iot-hub-create-through-portal.md)
* IoT Edge 런타임이 설치된 [IoT Edge 디바이스](how-to-register-device.md#register-with-visual-studio-code)
* [비주얼 스튜디오 코드](https://code.visualstudio.com/).
* 비주얼 스튜디오 코드에 대 한 [Azure IoT 도구입니다.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)

## <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

Visual Studio 코드에 Azure IoT 확장을 사용하여 허브에서 작업을 수행할 수 있습니다. 이러한 작업이 작동하려면 Azure 계정에 로그인하고 작업 중인 IoT 허브를 선택해야 합니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.

1. 탐색기 의 하단에서 **Azure IoT Hub** 섹션을 확장합니다.

1. **Azure IoT Hub** 섹션 헤더에서 **...를** 클릭합니다. 줄임표가 표시되지 않으면 헤더를 마우스로 가리킵니다.

1. **IoT Hub 선택**을 선택합니다.

1. Azure 계정에 로그인하지 않은 경우 프롬프트에 따라 로그인합니다.

1. Azure 구독을 선택합니다.

1. IoT Hub를 선택합니다.

## <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈을 설명하는 JSON 문서입니다. 또한 모듈 간에 데이터가 흐르는 방법과 모듈 쌍둥이의 원하는 속성에 대해서도 설명합니다. 자세한 내용은 [IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기를](module-composition.md)참조하십시오.

Visual Studio Code를 사용하여 모듈을 배포하려면 배포 매니페스트를 로컬에 .JSON 파일로 저장합니다. 장치에 구성을 적용하려면 명령을 실행할 때 해당 위치를 제공해야 합니다.

예를 들어 한 개의 모듈이 있는 기본 배포 매니페스트의 예제는 다음과 같습니다.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
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
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
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

현재 구성할 수 있는 IoT Edge 장치를 결정해야 하는 경우 **IoT Edge: 장치 정보 가져오기** 명령을 실행합니다.

## <a name="identify-devices-with-target-conditions"></a>대상 조건이 있는 장치 식별

배포를 수신할 IoT Edge 장치를 식별하려면 대상 조건을 지정해야 합니다. 지정된 조건이 deviceId, 태그 값 또는 보고된 속성 값과 일치하면 대상 조건이 충족됩니다.

장치 쌍에서 태그를 구성합니다. 다음은 태그가 있는 장치 쌍의 예입니다.

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

이 장치는 배포의 대상 조건에 와 같은 `tag.location.building = '20'`태그 값 중 하나와 일치하는 식이 포함된 경우 배포를 받게 됩니다.

태그 나 다른 값에 관계 없이 특정 장치를 대상으로 하려는 `deviceId` 경우 대상 조건에 대 한 지정 만.

다음은 몇 가지 예입니다.

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' OR deviceId = 'linuxprod2' OR deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' 및 태그.위치 = 'westus2'
* tags.environment = 'prod' OR 태그.위치 = 'westus2'
* tags.operator = '존' 및 tags.environment = 'prod' 및 not deviceId = 'linuxprod1'

자세한 내용은 [대상 조건을](module-deployment-monitoring.md#target-condition) 참조하십시오. 디바이스 쌍 및 태그에 대한 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.

### <a name="edit-the-device-twin"></a>장치 쌍 편집

Visual Studio 코드에서 장치 쌍으로 편집하여 태그를 구성할 수 있습니다. **보기** 메뉴에서 **명령 팔레트를** 선택하고 **IoT 가장자리: 장치 트윈 편집** 명령을 실행합니다. IoT Edge 장치를 선택하면 장치 쌍이 나타납니다.

이 예제에서는 정의된 태그가 없습니다. 현재 빈 섹션을 `"tags": {}` 사용자 고유의 태그 정의로 바꿉습니다.

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

로컬 파일을 저장한 후 **IoT Edge: 장치 트윈 업데이트** 명령을 실행합니다.

## <a name="create-deployment-at-scale"></a>대규모 배포 만들기

장치 쌍에서 배포 매니페스트 및 구성 태그를 구성하면 배포할 준비가 된 것입니다.

1. **보기** 메뉴에서 **명령 팔레트를** 선택하고 **Azure IoT 가장자리: 배율 조정 명령에서 배포 만들기를** 선택합니다.

1. 사용하려는 배포 매니페스트 JSON 파일로 이동하고 **에지 배포 매니페스트 선택**을 클릭합니다.

1. **배포 ID로**시작하여 프롬프트대로 값을 제공합니다.

   ![배포 ID 지정](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   다음 매개 변수에 대한 값 지정:

  | 매개 변수 | 설명 |
  | --- | --- |
  | 배포 ID | IoT 허브에서 생성될 배포의 이름입니다. 배포에 최대 128자의 소문자로 된 고유한 이름을 지정합니다. 공백과 잘못된 문자(`& ^ [ ] { } \ | " < > /`)는 사용하지 않도록 합니다. |
  | 대상 조건 | 대상 조건을 입력하여 이 배포를 통해 대상으로 지정할 장치를 결정합니다.조건은 디바이스 쌍 태그 또는 보고되는 디바이스 쌍 속성을 기반으로 하며, 표현 형식이 일치해야 합니다.예를 들어, `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | 우선 순위 |  양의 정수입니다. 둘 이상의 배포가 동일한 장치를 대상으로 하는 경우 Priority에 대한 수치가 가장 높은 배포가 적용됩니다. |

  우선 순위를 지정한 후 터미널은 다음 그림과 유사한 출력을 표시해야 합니다.

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>배포 모니터링 및 수정

Azure [CLI](how-to-deploy-monitor-cli.md#monitor-a-deployment) 또는 [Azure 포털을](how-to-deploy-monitor.md#monitor-a-deployment) 사용하여 배포를 모니터링, 수정 및 삭제합니다. 둘 다 배포에 대한 메트릭을 제공합니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 장치에 모듈 배포에](module-deployment-monitoring.md)대해 자세히 알아보십시오.
