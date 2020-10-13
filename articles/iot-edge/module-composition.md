---
title: 배포 매니페스트를 사용하여 모듈 및 경로 배포 - Azure IoT Edge
description: 배포 매니페스트에서 배포할 모듈을 선언하는 방법, 배포하는 방법 및 이들 간에 메시지 경로를 만드는 방법을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3f6c12b892e01aafd5beecdff14751481cf7fc96
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963400"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기

각 IoT Edge 디바이스는 적어도 $edgeAgent 및 $edgeHub라는 두 개의 모듈을 실행합니다. 두 모듈은 IoT Edge 런타임의 일부입니다. IoT Edge 디바이스는 개수에 관계 없는 프로세스에 대해 여러 추가 모듈을 실행할 수 있습니다. 배포 매니페스트를 사용하여 설치할 모듈 및 함께 작동하도록 구성하는 방법을 디바이스에 알립니다.

*배포 매니페스트*는 다음 항목을 설명하는 JSON 문서입니다.

* 다음 세 가지 구성 요소를 포함하는 **IoT Edge 에이전트** 모듈 쌍
  * 디바이스에서 실행되는 각 모듈의 컨테이너 이미지
  * 모듈 이미지를 포함하는 프라이빗 컨테이너 레지스트리에 액세스하기 위한 자격 증명
  * 각 모듈을 만들고 관리하는 방법에 대한 지침
* 모듈 간 및 궁극적으로 IoT Hub에 대한 메시지 흐름 방법을 포함하는 **IoT Edge 허브** 모듈 쌍입니다.
* 추가 모듈 쌍의 원하는 속성(선택 사항)

모든 IoT Edge 디바이스는 배포 매니페스트로 구성해야 합니다. 새로 설치된 IoT Edge 런타임은 유효한 매니페스트로 구성될 때까지 오류 코드를 보고합니다.

Azure IoT Edge 자습서에서는 Azure IoT Edge 포털의 마법사를 통해 배포 매니페스트를 빌드합니다. REST 또는 IoT Hub 서비스 SDK를 사용하여 프로그래밍 방식으로 배포 매니페스트를 적용할 수도 있습니다. 자세한 내용은 [IoT Edge 배포 이해](module-deployment-monitoring.md)를 참조하세요.

## <a name="create-a-deployment-manifest"></a>배포 매니페스트 만들기

상위 수준에서 배포 매니페스트는 원하는 속성으로 구성된 모듈 쌍의 목록입니다. 배포 매니페스트는 설치할 모듈과 구성 방법을 IoT Edge 디바이스(또는 디바이스 그룹)에 알려 줍니다. 배포 매니페스트에는 각 모듈 쌍의 ‘원하는 속성’이 포함되어 있습니다. IoT Edge 디바이스는 각 모듈에 대해 ‘보고된 속성’을 다시 보고합니다.

모든 배포 매니페스트에는 `$edgeAgent` 및 `$edgeHub`라는 두 개의 모듈이 필요합니다. 두 모듈은 IoT Edge 디바이스와 이 디바이스에서 실행되는 모듈을 관리하는 IoT Edge 런타임의 일부입니다. 이러한 모듈에 대한 자세한 내용은 [IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

두 개의 런타임 모듈 외에도 IoT Edge 장치에서 실행할 수 있는 최대 50 모듈을 추가할 수 있습니다.

IoT Edge 런타임(edgeAgent 및 edgeHub)만 포함하는 배포 매니페스트가 유효합니다.

배포 매니페스트는 다음 구조를 따릅니다.

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
        // includes the routing information between modules, and to IoT Hub
      }
    },
    "module1": {  // optional
      "properties.desired": {
        // desired properties of module1
      }
    },
    "module2": {  // optional
      "properties.desired": {
        // desired properties of module2
      }
    }
  }
}
```

## <a name="configure-modules"></a>모듈 구성

IoT Edge 런타임에서 사용자 배포에 모듈을 설치하는 방법을 정의합니다. IoT Edge 에이전트는 IoT Edge 디바이스에 대한 설치, 업데이트 및 상태 보고를 관리하는 런타임 구성 요소입니다. 따라서 $edgeAgent 모듈 쌍에는 모든 모듈에 대한 구성 및 관리 정보가 포함되어 있습니다. 이 정보에는 IoT Edge 에이전트 자체에 대한 구성 매개 변수가 포함됩니다.

$edgeAgent 속성은 다음과 같은 구조를 따릅니다.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            // configuration and management details
          },
          "edgeHub": {
            // configuration and management details
          }
        },
        "modules": {
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge 에이전트 스키마 버전 1.1은 IoT Edge 버전 1.0.10 함께 출시 되었으며 모듈 시작 순서를 사용 하도록 설정 합니다. 1.0.10 이상 버전을 실행 하는 IoT Edge 배포의 경우 스키마 버전 1.1이 권장 됩니다.

### <a name="module-configuration-and-management"></a>모듈 구성 및 관리

IoT Edge 에이전트 desired 속성 목록에서는 IoT Edge 장치에 배포 되는 모듈 및 구성 및 관리 하는 방법을 정의 합니다.

포함 하거나 포함할 수 있는 desired 속성의 전체 목록은 [IoT Edge 에이전트 및 IoT Edge 허브의 속성](module-edgeagent-edgehub.md)을 참조 하세요.

예를 들면 다음과 같습니다.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

모든 모듈에는 모듈 **이미지**, 컨테이너 레지스트리의 컨테이너 이미지에 대 한 주소 및 시작 시 이미지를 구성 하는 **createoptions** 를 포함 하는 **settings** 속성이 있습니다. 자세한 내용은 [IoT Edge 모듈의 컨테이너 만들기 옵션을 구성 하는 방법](how-to-use-create-options.md)을 참조 하세요.

EdgeHub 모듈 및 사용자 지정 모듈에는 IoT Edge 에이전트를 관리 하는 방법을 지시 하는 세 가지 속성도 있습니다.

* **상태**: 모듈을 처음 배포할 때 실행 해야 하는지 아니면 중지 해야 하는지를 나타냅니다. 필수 요소.
* **RestartPolicy**: IoT Edge 에이전트가 중지 된 경우 모듈을 다시 시작 해야 하는지 여부를 지정 합니다. 필수 요소.
* **Starver1.0.10 der**: *버전 IoT Edge에서 도입 되었습니다.* IoT Edge 에이전트가 처음 배포할 때 모듈을 시작 해야 하는 순서입니다. 순서는 정수를 사용 하 여 선언 됩니다. 여기서 시작 값이 0 인 모듈이 먼저 시작 된 다음 더 큰 숫자가 사용 됩니다. EdgeAgent 모듈은 항상 먼저 시작 되기 때문에 시작 값을 갖지 않습니다. 선택 사항입니다.

  IoT Edge 에이전트는 시작 값을 기준으로 모듈을 시작 하지만, 각 모듈이 완료 될 때까지 기다리지 않고 다음 항목으로 이동 합니다.

  시작 순서는 일부 모듈이 다른 모듈에 의존 하는 경우에 유용 합니다. 예를 들어 다른 모듈이 시작 될 때 메시지를 라우트할 준비가 되도록 edgeHub 모듈을 먼저 시작 하는 것이 좋습니다. 또는 데이터를 전송 하는 모듈 보다 먼저 저장소 모듈을 시작할 수도 있습니다. 그러나 항상 다른 모듈의 오류를 처리 하도록 모듈을 디자인 해야 합니다. 언제 든 지 언제 든 지 중지 및 다시 시작할 수 있는 컨테이너의 특성입니다.

## <a name="declare-routes"></a>경로 선언

IoT Edge 허브는 모듈, IoT Hub 및 리프 디바이스 간의 통신을 관리합니다. 따라서 $edgeHub 모듈 쌍에는 메시지가 배포 내에서 전달되는 방식을 선언하는 ‘경로’라는 원하는 속성이 포함되어 있습니다. 동일한 배포 내에 여러 경로가 있을 수 있습니다.

경로는 다음과 같은 구문을 사용하여 **$edgeHub** 원하는 속성에 선언됩니다.

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge 허브 스키마 버전 1.1은 IoT Edge 버전 1.0.10 함께 출시 되었으며 경로 우선 순위 지정 및 ttl (time to live)을 사용 하도록 설정 합니다. 1.0.10 이상 버전을 실행 하는 IoT Edge 배포의 경우 스키마 버전 1.1이 권장 됩니다.

모든 경로에는 메시지가 도착 하는 *원본* 및 메시지가 전달 되는 *싱크가* 필요 합니다. *조건은* 메시지를 필터링 하는 데 사용할 수 있는 선택적 부분입니다.

먼저 메시지를 처리 하도록 할 경로에 *우선 순위* 를 할당할 수 있습니다. 이 기능은 업스트림 연결이 취약 하거나 제한적 이며 표준 원격 분석 메시지에 우선 순위를 지정 해야 하는 중요 한 데이터가 있는 시나리오에서 유용 합니다.

### <a name="source"></a>원본

원본은 메시지가 발생한 위치를 지정합니다. IoT Edge는 모듈 또는 리프 디바이스의 메시지를 라우팅할 수 있습니다.

IoT SDK를 사용하여 모듈은 ModuleClient 클래스를 사용하여 메시지에 대한 특정 출력 큐를 선언할 수 있습니다. 출력 큐는 필요하지 않지만 여러 경로를 관리하는 데 유용합니다. 리프 디바이스는 IoT Hub에 메시지를 전송하는 것과 동일한 방식으로 IoT SDK의 DeviceClient 클래스를 사용하여 IoT Edge 게이트웨이 디바이스에 메시지를 보낼 수 있습니다. 자세한 내용은 [Azure IoT Hub SDK 이해 및 사용](../iot-hub/iot-hub-devguide-sdks.md)을 참조하세요.

원본 속성은 다음 값 중 하나일 수 있습니다.

| 원본 | Description |
| ------ | ----------- |
| `/*` | 모든 모듈 또는 리프 디바이스의 모든 디바이스-클라우드 메시지 또는 쌍 변경 알림 |
| `/twinChangeNotifications` | 모든 모듈 또는 리프 디바이스에서 발생하는 모든 쌍 변경(보고된 속성) |
| `/messages/*` | 일부 출력을 통하거나 어떠한 출력도 없이 모듈 또는 리프 디바이스에서 보낸 모든 디바이스-클라우드 메시지 |
| `/messages/modules/*` | 일부 출력을 통하거나 어떠한 출력도 없이 모듈에서 보낸 모든 디바이스-클라우드 메시지 |
| `/messages/modules/<moduleId>/*` | 일부 출력을 통하거나 어떠한 출력도 없이 특정 모듈에서 보낸 모든 디바이스-클라우드 메시지 |
| `/messages/modules/<moduleId>/outputs/*` | 일부 출력을 통해 특정 모듈에서 보낸 모든 디바이스-클라우드 메시지 |
| `/messages/modules/<moduleId>/outputs/<output>` | 특정 출력을 통해 특정 모듈에서 보낸 모든 디바이스-클라우드 메시지 |

### <a name="condition"></a>조건

조건은 경로 선언의 선택 사항입니다. 원본으로 싱크로 모든 메시지를 전달하려는 경우 전체 **WHERE** 절을 그대로 둡니다. 또는 [IoT Hub 쿼리 언어](../iot-hub/iot-hub-devguide-routing-query-syntax.md)를 사용하여 조건을 만족하는 특정 메시지 또는 메시지 유형에 대해 필터링할 수 있습니다. IoT Edge 경로는 쌍 태그 또는 속성을 기반으로 하는 메시지 필터링을 지원하지 않습니다.

IoT Edge의 모듈 간에 전달되는 메시지는 디바이스와 Azure IoT Hub 간에 전달되는 메시지와 동일한 서식이 지정됩니다. 모든 메시지는 JSON으로 서식이 지정되고 **systemProperties**, **appProperties** 및 **body** 매개 변수를 포함합니다.

다음 구문을 사용하여 3개 매개 변수 중 하나를 중심으로 쿼리를 작성할 수 있습니다.

* 시스템 속성: `$<propertyName>` 또는 `{$<propertyName>}`
* 애플리케이션 속성: `<propertyName>`
* 본문 속성: `$body.<propertyName>`

메시지 속성에 대한 쿼리를 만드는 방법에 대한 예제는 [디바이스-클라우드 메시지 경로에 대한 쿼리 식](../iot-hub/iot-hub-devguide-routing-query-syntax.md)을 참조하세요.

IoT Edge에 특정되는 예제는 리프 디바이스에서 게이트웨이 디바이스에 도착하는 메시지를 필터링하려는 경우입니다. 모듈에서 발생한 메시지에는 **connectionModuleId**라는 시스템 속성이 포함되어 있습니다. 따라서 리프 디바이스에서 IoT Hub로 직접 메시지를 라우팅하려는 경우 다음 경로를 사용하여 모듈 메시지를 제외합니다.

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>sink

싱크는 메시지를 보낼 위치를 정의합니다. 모듈 및 IoT Hub만 메시지를 받을 수 있습니다. 메시지를 다른 디바이스로 라우팅할 수 없습니다. 싱크 속성에는 와일드카드 옵션이 없습니다.

싱크 속성은 다음 값 중 하나일 수 있습니다.

| sink | Description |
| ---- | ----------- |
| `$upstream` | 메시지를 IoT Hub로 보냅니다. |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | 특정 모듈의 특정 입력으로 메시지 보내기 |

IoT Edge는 최소 한 번의 보장을 제공합니다. IoT Edge 허브는 경로에서 해당 싱크로 메시지를 전달할 수 없는 경우 로컬에 메시지를 저장합니다. 예를 들어 IoT Edge 허브에서 IoT Hub에 연결할 수 없거나 대상 모듈이 연결되지 않은 경우입니다.

IoT Edge 허브는 [IoT Edge 허브 원하는 속성](module-edgeagent-edgehub.md)의 `storeAndForwardConfiguration.timeToLiveSecs` 속성에서 지정된 시간까지 메시지를 저장합니다.

### <a name="priority-and-time-to-live"></a>우선 순위 및 ttl (time-to-live)

경로는 경로를 정의 하는 문자열 또는 경로 문자열, 우선 순위 정수 및 ttl (time to live) 정수를 사용 하 여 선언할 수 있습니다.

옵션 1:

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

옵션 2: IoT Edge 허브 스키마 버전 1.1을 사용 하 IoT Edge 버전 1.0.10에서 도입 되었습니다.

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

**우선 순위** 값은 0-9 (포함)이 될 수 있습니다. 여기서 0은 가장 높은 우선 순위입니다. 메시지는 해당 끝점에 따라 큐에 대기 됩니다. 특정 끝점을 대상으로 하는 모든 우선 순위 0 메시지는 동일한 끝점을 대상으로 하는 우선 순위 1 메시지를 처리 하 고 줄을 아래로 처리 합니다. 동일한 끝점에 대 한 여러 경로에 동일한 우선 순위가 있는 경우 해당 메시지는 첫 번째로 제공 되는 방식으로 처리 됩니다. 우선 순위를 지정 하지 않으면 경로가 가장 낮은 우선 순위로 할당 됩니다.

**TimeToLiveSecs** 속성은 명시적으로 **설정 하지 않는** 한 IoT Edge 허브의 기능을 통해 해당 값을 상속 합니다. 값은 임의의 양의 정수일 수 있습니다.

우선 순위 큐를 관리 하는 방법에 대 한 자세한 내용은 [경로 우선 순위 및 ttl (time-to-live](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md))에 대 한 참조 페이지를 참조 하세요.

## <a name="define-or-update-desired-properties"></a>원하는 속성 정의 또는 업데이트

배포 매니페스트는 IoT Edge 디바이스에 배포된 각 모듈의 원하는 속성을 지정합니다. 배포 매니페스트의 원하는 속성은 현재 모듈 쌍에 있는 원하는 속성을 모두 덮어씁니다.

배포 매니페스트에서 모듈 쌍의 원하는 속성을 지정하지 않으면, IoT Hub는 어떤 방식으로든 모듈 쌍을 수정하지 않습니다. 대신, 원하는 속성을 프로그래밍 방식으로 설정할 수 있습니다.

디바이스 쌍을 수정할 수 있게 해주는 동일한 메커니즘이 모듈 쌍을 수정하는 데 사용됩니다. 자세한 내용은 [모듈 쌍 개발자 가이드](../iot-hub/iot-hub-devguide-module-twins.md)를 참조하세요.

## <a name="deployment-manifest-example"></a>배포 매니페스트 예제

다음 예제에서는 유효한 배포 매니페스트 문서의 모양을 보여 줍니다.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

* $edgeAgent 및 $edgeHub에 포함될 수 있거나 포함되어야 하는 속성의 전체 목록은 [IoT Edge 에이전트 및 IoT Edge 허브의 속성](module-edgeagent-edgehub.md)을 참조하세요.

* 이제 IoT Edge 모듈을 사용하는 방법을 알았으므로 [IoT Edge 모듈 개발을 위한 요구 사항 및 도구에 대해 알아봅니다](module-development.md).
