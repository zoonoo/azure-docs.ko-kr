---
title: 처음부터 Azure용 OPC 트윈 모듈을 배포하는 방법 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 포털의 IoT Edge 블레이드를 사용하고 AZ CLI를 사용하여 OPC Twin을 처음부터 배포하는 방법에 대해 설명합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241069"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>OPC 트윈 모듈 및 종속성을 처음부터 배포

OPC Twin 모듈은 IoT Edge에서 실행되며 OPC 장치 트윈 및 레지스트리 서비스에 여러 에지 서비스를 제공합니다. 

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 게이트웨이에 모듈을 배포하는 몇 가지 옵션이 있습니다.

- [Azure 포털의 IoT 에지 블레이드에서 배포](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [AZ CLI를 사용한 배포](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> 배포 세부 정보 및 지침에 대한 자세한 내용은 GitHub [리포지토리](https://github.com/Azure/azure-iiot-components)를 참조하십시오.

## <a name="deployment-manifest"></a>배포 매니페스트

모든 모듈은 배포 매니페스트를 사용하여 배포됩니다.  [OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher) 및 [OPC 트윈을](https://github.com/Azure/azure-iiot-opc-twin-module) 배포하는 예제 매니페스트는 다음과 같습니다.

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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Azure 포털에서 배포

Azure IoT Edge 게이트웨이 장치에 모듈을 배포하는 가장 쉬운 방법은 Azure 포털을 사용하는 것입니다.  

### <a name="prerequisites"></a>사전 요구 사항

1. OPC Twin [종속성을](howto-opc-twin-deploy-dependencies.md) 배포하고 결과 `.env` 파일을 얻었습니다. 결과 `.env` 파일에 `hub name` 변수가 `PCS_IOTHUBREACT_HUB_NAME` 배포된 것을 기록합니다.

2. 등록하고 [리눅스](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) 또는 [윈도우](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT `device id`에지 게이트웨이를 시작하고 의 기록 .

### <a name="deploy-to-an-edge-device"></a>에지 장치에 배포

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 IoT Hub로 이동합니다.

2. 왼쪽 메뉴에서 **IoT 가장자리를** 선택합니다.

3. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.

4. **모듈 설정**선택 .

5. 페이지의 배포 모듈 섹션에서 **추가** 및 **IoT 에지** **모듈을** 선택합니다.

6. **IoT Edge 사용자 지정** 모듈 `opctwin` 대화 상자에서 모듈의 이름으로 사용 하 여 컨테이너 *이미지 URI를*

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   *컨테이너 만들기 옵션으로*다음 JSON을 사용합니다.

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   필요한 경우 선택적 필드를 작성합니다. 컨테이너 생성 옵션, 다시 시작 정책 및 원하는 상태에 대한 자세한 내용은 [EdgeAgent desired 속성](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)을 참조하세요. 모듈 쌍에 대한 자세한 내용은 [desired 속성 정의 또는 업데이트](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)을 참조하세요.

7. **저장** 및 반복 단계를 **선택합니다.**  

8. IoT Edge 사용자 지정 모듈 `opcpublisher` 대화 상자에서 모듈 및 컨테이너 *이미지 URI의* 이름으로 사용합니다. 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   *컨테이너 만들기 옵션으로*다음 JSON을 사용합니다.

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. **저장을** 선택한 다음 **다음을** 클릭하여 경로 섹션을 계속합니다.

10. 경로 탭에서 다음을 붙여넣습니다. 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    다음을 **선택합니다.**

11. 배포 정보를 검토하고 매니페스트합니다.  위의 배포 매니페스트처럼 보일 것입니다.  **제출**을 선택합니다.

12. 디바이스에 모듈을 배포하면 포털의 **디바이스 세부 정보** 페이지에서 모두 볼 수 있습니다. 이 페이지에서는 배포 상태 및 종료 코드와 같은 유용한 정보뿐만 아니라 배포된 각 모듈의 이름을 표시합니다.

## <a name="deploying-using-azure-cli"></a>Azure CLI를 사용하여 배포

### <a name="prerequisites"></a>사전 요구 사항

1. 여기에서 [AZURE 명령줄 인터페이스(AZ)의](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 최신 버전을 [설치합니다.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

### <a name="quickstart"></a>빠른 시작

1. 위의 배포 매니페스트를 `deployment.json` 파일에 저장합니다.  

2. 다음 명령을 사용하여 IoT Edge 디바이스에 구성을 적용합니다.

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   매개 `device id` 변수는 대/소문자를 구분합니다. 콘텐츠 매개 변수는 저장한 배포 매니페스트 파일을 가리킵니다. 
    ![az IoT 에지 세트 모듈 출력](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. 디바이스에 모듈을 배포하면 다음 명령을 사용하여 모두 볼 수 있습니다.

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   장치 ID 매개 변수는 대/소문자를 구분합니다. ![az iot hub module-identity list output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>다음 단계

OPC Twin을 처음부터 배포하는 방법을 배웠으므로 다음 단계로 제안된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [기존 프로젝트에 OPC 트윈 배포](howto-opc-twin-deploy-existing.md)
