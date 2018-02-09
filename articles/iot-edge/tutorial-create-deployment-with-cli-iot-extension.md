---
title: "Azure CLI 2.0용 IoT 확장을 사용하여 IoT Edge 장치 배포 | Microsoft Docs"
description: "Azure CLI 2.0용 IoT 확장을 사용하여 IoT Edge 장치 배포"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Azure CLI 2.0용 IoT 확장을 사용하여 IoT Edge 장치 배포

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest)은 IoT Edge 같은 Azure 리소스를 관리하기 위한 오픈 소스 교차 플랫폼 명령줄 도구입니다. Azure CLI 2.0은 Windows, Linux 및 MacOS에서 사용할 수 있습니다.

Azure CLI 2.0을 사용하면 Azure IoT Hub 리소스, 장치 프로비전 서비스 인스턴스 및 연결된 허브를 즉시 관리할 수 있습니다. 새로운 IoT 확장은 장치 관리 및 전체 IoT Edge 같은 기능으로 Azure CLI 2.0을 강화합니다.

이 자습서에서는 먼저 Azure CLI 2.0 및 IoT 확장을 설치하는 단계를 완료합니다. 그런 후 사용 가능한 CLI 명령을 사용하여 IoT Edge 장치에 모듈을 배포하는 방법을 배웁니다.

## <a name="installation"></a>설치 

### <a name="step-1---install-python"></a>1단계 - Python 설치

[Python 2.7x 또는 Python 3.x](https://www.python.org/downloads/)가 필요합니다.

### <a name="step-2---install-azure-cli-20"></a>2단계 - Azure CLI 2.0 설치

[설치 지침](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)에 따라 환경에 Azure CLI 2.0을 설치합니다. Azure CLI 2.0 버전이 2.0.24 이상이어야 합니다. `az –version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다. Windows에 설치하는 간단한 방법 중 하나는 [MSI](https://aka.ms/InstallAzureCliWindows)를 다운로드하여 설치하는 것입니다.

### <a name="step-3---install-iot-extension"></a>3단계 - IoT 확장 설치

[IoT 확장 추가 정보](https://github.com/Azure/azure-iot-cli-extension)에는 확장을 설치하는 여러 가지 방법이 설명되어 있습니다. 가장 간단한 방법은 `az extension add --name azure-cli-iot-ext` 명령을 사용하는 것입니다. 설치 후 `az extension list` 명령을 사용하여 현재 설치된 확장의 유효성을 검사하거나 `az extension show --name azure-cli-iot-ext` 명령을 사용하여 IoT 확장에 대한 세부 정보를 볼 수 있습니다. 확장을 제거하려면 `az extension remove --name azure-cli-iot-ext` 명령을 사용합니다.


## <a name="deploy-modules-to-an-iot-edge-device"></a>IoT Edge 장치에 모듈 배포
이 자습서에서는 IoT Edge 배포를 만드는 방법을 배웁니다. 이 예제에서는 Azure 계정에 로그인하고, Azure 리소스 그룹(Azure 솔루션에 대한 관련 리소스를 보관하는 컨테이너)을 만들고, IoT Hub를 만들고, 세 개의 IoT Edge 장치 ID를 만들고, 태그를 설정하고, 이러한 장치를 대상으로 하는 IoT Edge 배포를 만드는 방법을 보여줍니다. 시작하기 전에 앞에서 설명한 설치 단계를 완료하세요. Azure 계정이 없으면 지금 [무료 계정](https://azure.microsoft.com/free/?v=17.39a)을 만듭니다. 


### <a name="1-login-to-the-azure-account"></a>1. Azure 계정에 로그인
  
    az login

![로그인][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. eastus에 IoTHubBlogDemo라는 리소스 그룹 만들기

    az group create -l eastus -n IoTHubBlogDemo

![리소스 그룹 만들기][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3. 새로 만든 리소스 그룹 아래에 blogDemoHub라는 IoT Hub 만들기

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub 만들기][3]


### <a name="4-create-an-iot-edge-device"></a>4. IoT Edge 장치 만들기

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![IoT Edge 장치 만들기][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5. IoT Edge 장치에 구성 적용

배포 JSON 템플릿을 로컬에 txt 파일로 저장합니다. apply-configuration 명령을 실행할 때 파일 경로가 필요합니다.

아래는 tempSensor 모듈 하나를 포함하고 있는 샘플 배포 JSON 템플릿입니다.

```json
{
  "moduleContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": ""
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "tempSensor": {
      "properties.desired": {}
    }
  }
}
```

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![구성 적용][5]

### <a name="6-list-modules"></a>6. 모듈 목록 표시
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![모듈 목록 표시][6]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치에서 생성된 원시 데이터를 필터링하는 코드가 포함된 Azure 함수를 만들었습니다. Azure IoT Edge을 계속 탐색하려면 IoT Edge 장치를 게이트웨이로 사용하는 방법을 알아보세요. 

> [!div class="nextstepaction"]
> [IoT Edge 게이트웨이 장치 만들기](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

