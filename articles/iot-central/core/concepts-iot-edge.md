---
title: Azure IoT Edge 및 Azure IoT Central | Microsoft Docs
description: IoT Central 응용 프로그램과 함께 Azure IoT Edge를 사용 하는 방법을 이해 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 1a464b9e039f256fae52c32d828b1ec39a20a228
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123278"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 연결

*이 문서는 솔루션 빌더 및 디바이스 개발자에게 적용됩니다.*

Azure IoT Edge 클라우드 분석과 사용자 지정 비즈니스 논리를 장치로 이동 하 여 조직에서 데이터 관리 대신 비즈니스 통찰력에 집중할 수 있도록 합니다. 비즈니스 논리를 표준 컨테이너로 패키징 하 고 해당 컨테이너를 장치에 배포 하 고 클라우드에서 모니터링 하 여 IoT 솔루션을 확장할 수 있습니다.

이 문서에서는 다음을 설명합니다.

* IoT Edge 장치가 IoT Central 응용 프로그램에 연결 하는 방법입니다.
* IoT Central를 사용 하 여 IoT Edge 장치를 관리 하는 방법

IoT Edge에 대 한 자세한 내용은 [Azure IoT Edge 항목](../../iot-edge/about-iot-edge.md) 을 참조 하세요.

## <a name="iot-edge"></a>IoT Edge

IoT Edge는 다음 세 가지 구성 요소로 구성됩니다.

* *IoT Edge 모듈* 은 Azure 서비스, 파트너 서비스 또는 사용자 고유의 코드를 실행하는 컨테이너입니다. 모듈은 IoT Edge 디바이스에 배포되며, 해당 디바이스에서 로컬로 실행됩니다. 자세히 알아보려면 [Azure IoT Edge 모듈 이해](../../iot-edge/iot-edge-modules.md)를 참조 하세요.
* *IoT Edge 런타임은* 각 IoT Edge 장치에서 실행 되며 각 장치에 배포 된 모듈을 관리 합니다. 런타임은 *IoT Edge 에이전트* 와 *IoT Edge 허브* 라는 두 개의 IoT Edge 모듈로 구성 됩니다. 자세히 알아보려면 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](../../iot-edge/iot-edge-runtime.md)를 참조 하세요.
* *클라우드 기반 인터페이스* 를 사용하여 IoT Edge 디바이스를 원격으로 모니터링 및 관리할 수 있습니다. IoT Central은 클라우드 인터페이스의 예입니다.

IoT Edge 장치는 다음과 같을 수 있습니다.

* 모듈로 구성 된 독립 실행형 장치입니다.
* 다운스트림 장치를 연결 하는 *게이트웨이 장치*

## <a name="iot-edge-as-a-gateway"></a>게이트웨이로 IoT Edge

IoT Edge 장치는 네트워크의 다른 다운스트림 장치와 IoT Central 응용 프로그램 간의 연결을 제공 하는 게이트웨이로 작동할 수 있습니다.

다음과 같은 두 가지 게이트웨이 패턴이 있습니다.

* *투명 게이트웨이* 패턴에서 IoT Edge 허브 모듈은 IoT Central와 같이 작동 하며 IoT Central에 등록 된 장치에서의 연결을 처리 합니다. 메시지는 다운스트림 장치 간에 게이트웨이가 없는 것 처럼 IoT Central으로 전달 됩니다.

* *변환 게이트웨이* 패턴에서 IoT Central에 연결할 수 없는 장치는 대신 사용자 지정 IoT Edge 모듈에 연결 합니다. IoT Edge 장치의 모듈은 들어오는 다운스트림 장치 메시지를 처리 한 다음 IoT Central으로 전달 합니다.

투명 및 변환 게이트웨이 패턴은 함께 사용할 수 없습니다. 단일 IoT Edge 장치는 투명 게이트웨이 및 번역 게이트웨이로 작동할 수 있습니다.

IoT Edge 게이트웨이 패턴에 대해 자세히 알아보려면 [IoT Edge 장치를 게이트웨이로 사용할 수 있는 방법](../../iot-edge/iot-edge-as-gateway.md)을 참조 하세요.

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>게이트웨이 및 모듈과의 다운스트림 디바이스 관계

다운스트림 장치는 *IoT Edge 허브*  모듈을 통해 IoT Edge 게이트웨이 장치에 연결할 수 있습니다. 이 시나리오에서 IoT Edge 장치는 투명 게이트웨이입니다.

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="투명한 게이트웨이의 다이어그램" border="false":::

다운스트림 디바이스는 사용자 지정 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수도 있습니다. 다음 시나리오에서 다운스트림 장치는 *Modbus* 사용자 지정 모듈을 통해 연결 됩니다. 이 시나리오에서 IoT Edge 장치는 번역 게이트웨이입니다.

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="사용자 지정 모듈 연결의 다이어그램" border="false":::

다음 다이어그램에서는 두 가지 유형의 모듈을 통해 IoT Edge 게이트웨이 장치에 대 한 연결을 보여 줍니다. 이 시나리오에서 IoT Edge 장치는 모두 투명 하 고 번역 게이트웨이입니다.

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="두 연결 모듈을 사용 하 여 연결 하는 다이어그램" border="false":::

다운스트림 장치는 여러 사용자 지정 모듈을 통해 IoT Edge 게이트웨이 장치에 연결할 수 있습니다. 다음 다이어그램에서는 Modbus 사용자 지정 모듈, 사용자 지정 모듈 및 *IoT Edge 허브*  모듈을 통해 연결 하는 다운스트림 장치를 보여 줍니다.

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="여러 사용자 지정 모듈을 사용 하 여 연결 다이어그램" border="false":::

<!-- To do: add link to how to configure gateway article? -->

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge 장치 및 IoT Central

IoT Edge 장치는 *공유 액세스 서명* 토큰 또는 x.509 인증서를 사용 하 여 IoT Central 인증할 수 있습니다. IoT Edge 장치를 처음으로 연결 하기 전에 IoT Central에 수동으로 등록 하거나 장치 프로 비전 서비스를 사용 하 여 등록을 처리할 수 있습니다. 자세한 내용은 [Azure IoT Central에 연결](concepts-get-connected.md)을 참조하세요.

IoT Central [장치 템플릿을](concepts-device-templates.md) 사용 하 여 IoT Central 장치와 상호 작용 하는 방법을 정의 합니다. 예를 들어 장치 템플릿은 다음을 지정 합니다.

* 장치에서 전송 하 고 시각화를 만들 수 IoT Central 있도록 장치에서 전송 하는 원격 분석 및 속성의 형식입니다.
* IoT Central에서 명령을 호출 하는 데 사용 하는 연산자에 대 한 UI를 표시할 수 있도록 장치가 응답 하는 명령입니다.

IoT Edge 장치는 원격 분석을 전송 하 고, 속성 값을 동기화 하 고, 표준 장치와 같은 방식으로 명령에 응답할 수 있습니다. 따라서 IoT Edge 장치에는 IoT Central 장치 템플릿이 필요 합니다.

### <a name="iot-edge-device-templates"></a>장치 템플릿 IoT Edge

장치 템플릿 IoT Central 모델을 사용 하 여 장치의 기능을 설명 합니다. 다음 다이어그램은 IoT Edge 장치의 모델 구조를 보여 줍니다.

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="IoT Central에 연결 된 IoT Edge 장치의 모델 구조" border="false":::

IoT Central은 다음과 같이 IoT Edge 장치를 모델링 합니다.

* 모든 IoT Edge 장치 템플릿에는 기능 모델이 있습니다.
* 배포 매니페스트에 나열 된 모든 사용자 지정 모듈에 대해 모듈 기능 모델이 생성 됩니다.
* 각 모듈 기능 모델과 장치 모델 간에 관계가 설정 됩니다.
* 모듈 기능 모델은 하나 이상의 모듈 인터페이스를 구현 합니다.
* 각 모듈 인터페이스에는 원격 분석, 속성 및 명령이 포함됩니다.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge 배포 매니페스트 및 IoT Central 장치 템플릿

IoT Edge에서는 모듈 형태로 비즈니스 논리를 배포 하 고 관리 합니다. IoT Edge 모듈은 IoT Edge에서 관리 하는 계산의 가장 작은 단위 이며, Azure Stream Analytics, 사용자 고유의 솔루션 관련 코드 등의 Azure 서비스를 포함할 수 있습니다.

IoT Edge *배포 매니페스트* 는 장치에 배포할 IoT Edge 모듈과 해당 모듈을 구성 하는 방법을 나열 합니다. 자세한 내용은 [IoT Edge에서 모듈을 배포 하 고 경로를 설정 하는 방법 알아보기](../../iot-edge/module-composition.md)를 참조 하세요.

Azure IoT Central에서 IoT Edge 장치에 대 한 장치 템플릿을 만들기 위해 배포 매니페스트를 가져옵니다.

다음 코드 조각에서는 배포 매니페스트 IoT Edge 예제를 보여 줍니다.

```json
{
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

위의 코드 조각에서 다음을 확인할 수 있습니다.

* 세 개의 모듈이 있습니다. 모든 배포 매니페스트에 있는 *IoT Edge 에이전트* 및 *IoT Edge 허브* 시스템 모듈 사용자 지정 **SimulatedTemperatureSensor** 모듈입니다.
* 연결 하는 데 자격 증명이 필요 하지 않은 Azure Container Registry 리포지토리에서 공용 모듈 이미지를 가져옵니다. Private module 이미지의 경우 `registryCredentials` *IoT Edge agent* 모듈의 설정에서 사용할 컨테이너 레지스트리 자격 증명을 설정 합니다.
* 사용자 지정 **SimulatedTemperatureSensor** 모듈에는 및 라는 두 개의 속성이 있습니다 `"SendData": true` `"SendInterval": 10` .

이 배포 매니페스트를 IoT Central 응용 프로그램으로 가져오면 다음 장치 템플릿이 생성 됩니다.

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="배포 매니페스트에서 만든 장치 템플릿을 보여 주는 스크린샷":::

이전 스크린샷은 다음을 확인할 수 있습니다.

* **SimulatedTemperatureSensor** 라는 모듈 *IoT Edge 에이전트* 및 *IoT Edge 허브* 시스템 모듈은 템플릿에 표시 되지 않습니다.
* **Senddata** 및 **senddata** 이라는 두 개의 쓰기 가능한 속성이 포함 된 **management** 라는 인터페이스입니다.

배포 매니페스트에는 **SimulatedTemperatureSensor** 모듈에서 전송 하는 원격 분석 또는 응답 하는 명령에 대 한 정보가 포함 되지 않습니다. 이러한 정의는 게시 하기 전에 장치 템플릿에 수동으로 추가 합니다.

자세히 알아보려면 [자습서: Azure IoT Central 응용 프로그램에 Azure IoT Edge 장치 추가](tutorial-add-edge-as-leaf-device.md)를 참조 하세요.

### <a name="update-a-deployment-manifest"></a>배포 매니페스트 업데이트

새 [버전](howto-version-device-template.md) 의 장치 템플릿을 만드는 경우 배포 매니페스트를 새 버전으로 바꿀 수 있습니다.

배포 매니페스트를 교체할 때 연결 된 모든 IoT Edge 장치는 새 매니페스트를 다운로드 하 고 해당 모듈을 업데이트 합니다. 그러나 IoT Central는 모듈 구성을 변경 하 여 장치 템플릿의 인터페이스를 업데이트 하지 않습니다. 예를 들어, 이전 코드 조각에 표시 된 매니페스트를 다음 매니페스트로 바꾸면 장치 템플릿의 **관리** 인터페이스에 **sendunits** 속성이 자동으로 표시 되지 않습니다. IoT Central를 인식할 수 있도록 **관리** 인터페이스에 새 속성을 수동으로 추가 합니다.

```json
{
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>IoT Edge 런타임 배포

IoT Edge 런타임을 실행할 수 있는 위치에 대 한 자세한 내용은 [지원 되는 시스템 Azure IoT Edge](../../iot-edge/support.md)을 참조 하세요.

다음 환경에서 IoT Edge 런타임을 설치할 수도 있습니다.

* [Linux 용 Azure IoT Edge 설치 또는 제거](../../iot-edge/how-to-install-iot-edge.md)
* [Windows 디바이스에서 Linux용 Azure IoT Edge 설치 및 프로비저닝(미리 보기)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Azure의 Ubuntu Virtual Machines에서 Azure IoT Edge 실행](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>IoT Edge 게이트웨이 장치

게이트웨이 장치로 IoT Edge 장치를 선택한 경우 게이트웨이 장치에 연결할 장치에 대 한 장치 모델에 다운스트림 관계를 추가할 수 있습니다.

<!-- TODO - add link to Edge Gateway how-to -->

## <a name="next-steps"></a>다음 단계

장치 개발자 인 경우 제안 되는 다음 단계는 사용자 [고유의 IoT Edge 모듈을 개발](../../iot-edge/module-development.md)하는 방법을 배우는 것입니다.
