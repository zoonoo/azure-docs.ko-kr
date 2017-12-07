---
title: "Azure IoT Edge를 사용하여 Azure Machine Learning 배포 | Microsoft Docs"
description: "Azure Machine Learning을 Edge 장치에 모듈로 배포"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e061e599f365bf3d343cb59b8dc6a61e06627517
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Azure Machine Learning을 IoT Edge 모듈로 배포 - 미리 보기

비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 [Windows][lnk-tutorial1-win] 또는 [Linux][lnk-tutorial1-lin]에서 시뮬레이트된 장치에 Azure IoT Edge 배포 자습서에서 만든 시뮬레이트된 IoT Edge 장치에서 센서 데이터를 기준으로 장치에서 오류가 발생할 때를 예측하는 Azure Machine Learning 모듈을 배포하는 과정을 안내합니다. 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Azure Machine Learning 모듈을 IoT Edge 장치에 배포
> * 생성된 데이터 보기

솔루션에서 자체 [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) 모델을 사용하려는 경우 IoT Edge용 [모델을 배포](https://aka.ms/aml-iot-edge-doc)하고 [Azure Container Registry](../container-registry/index.yml) 또는 Docker와 같은 컨테이너 레지스트리에 호스트합니다.

## <a name="prerequisites"></a>필수 조건

* 빠른 시작 또는 첫 번째 자습서에서 만든 Azure IoT Edge 장치
* IoT Edge 장치를 연결할 IoT Hub용 IoT Hub 연결 문자열
* Azure ML 컨테이너

## <a name="create-the-azure-ml-container"></a>Azure ML 컨테이너 만들기
Azure ML 컨테이너를 만들려면 [Azure IoT Edge용 AI 도구 키트](https://aka.ms/aml-iot-edge-anomaly-detection)의 지침을 따릅니다.

## <a name="run-the-solution"></a>솔루션 실행

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
1. **IoT Edge(미리 보기)**로 이동하여 IoT Edge 장치를 선택합니다.
1. **모듈 설정**을 선택합니다.
1. **IoT Edge 모듈 추가**를 선택합니다.
1. **이름** 필드에 `tempSensor`를 입력합니다.
1. **이미지 URI** 필드에 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`를 입력합니다.
1. 다른 설정은 변경하지 않고 **저장**을 선택합니다.
1. **모듈 추가** 단계에서 **Azure IoT Edge 모듈**을 다시 선택합니다.
1. **이름** 필드에 이전 섹션에서 만든 컨테이너의 이름을 입력합니다. 이름을 찾는 데 도움을 얻으려면 [Azure IoT Edge용 AI 도구 키트](https://aka.ms/aml-iot-edge-anomaly-detection)를 참조하세요.
1. **이미지** 필드에 이전 섹션에서 만든 컨테이너의 이미지 URI를 입력합니다. 이미지를 찾는 데 도움을 얻으려면 [Azure IoT Edge용 AI 도구 키트](https://aka.ms/aml-iot-edge-anomaly-detection)를 참조하세요.
1. **Save**를 클릭합니다.
1. **모듈 추가** 단계로 돌아가서 **다음**을 클릭합니다.
1. 모듈 경로를 업데이트합니다.
1. **경로 지정** 단계에서 다음 JSON을 텍스트 상자에 복사합니다. 모듈은 모든 메시지를 Edge 런타임에 게시합니다. 런타임의 선언적 규칙은 해당 메시지가 어디로 흐르는지를 정의합니다. 이 자습서에서는 두 개의 경로가 필요합니다. 첫 번째 경로는 모든 Azure Machine Learning 모듈이 사용하는 끝점인 "amlInput" 끝점을 통해 온도 센서에서 Machine Learning 모듈로 메시지를 전송합니다. 두 번째 경로는 Machine Learning 모듈에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 “amlOutput”은 모든 Azure Machine Learning 모듈이 데이터를 출력하는 데 사용하는끝점이고 '$upstream'은 IoT Hub로 메시지를 전송하도록 Edge Hub에 알려주는 특별한 대상입니다. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. **다음**을 누릅니다. 
1. “템플릿 검토” 단계에서 “제출”을 클릭합니다. 
1. 장치 세부 정보 페이지로 돌아가서 “새로 고침”을 클릭합니다.  “tempSensor 모듈” 및 “IoT Edge runtime”과 함께 실행되는 새로운 ''machinelearningmodule''이 표시됩니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

 VS Code에서 **보기 | 명령 팔레트... | IoT: D2C 메시지 모니터링 시작** 메뉴 명령을 사용하여 IoT Hub에 도착하는 데이터를 모니터링합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Machine Learning에서 제공하는 IoT Edge 모듈을 배포했습니다. Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다른 자습서 중 하나를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 함수를 모듈로 배포](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
