---
title: Azure IoT Edge를 사용하여 Azure Machine Learning 배포 | Microsoft Docs
description: 이 자습서에서는 Azure Machine Learning을 Edge 장치에 모듈로 배포합니다.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 59ce51ba4b015c7236df9e20416832e877dd26c6
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748377"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>자습서: Azure Machine Learning을 IoT Edge 모듈로 배포(미리 보기)

비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 시뮬레이션된 컴퓨터 온도 데이터에 따라 장치가 실패하는 경우를 예측하는 Azure Machine Learning 모듈을 배포하는 과정을 안내합니다. IoT Edge의 Azure ML에 대한 자세한 내용은 [Azure Machine Learning 설명서](../machine-learning/desktop-workbench/use-azure-iot-edge-ai-toolkit.md)를 참조하세요.

이 자습서에서 만드는 Azure Machine Learning 모듈은 장치에서 생성된 환경 데이터를 읽고, 메시지에 비정상 레이블을 지정하기도 합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning 모듈 만들기
> * Azure 컨테이너 레지스트리에 모듈 컨테이너 푸시
> * Azure Machine Learning 모듈을 IoT Edge 장치에 배포
> * 생성된 데이터 보기

>[!NOTE]
>Azure IoT Edge의 Azure Machine Learning 모듈은 공개 미리 보기로 있습니다. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 장치:

* [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)의 빠른 시작에 설명된 단계에 따라 개발 머신 또는 가상 머신을 Edge 장치로 사용할 수 있습니다.
* Azure Machine Learning 모듈은 ARM 프로세서를 지원하지 않습니다.

클라우드 리소스:

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md). 
* Azure Machine Learning 작업 영역 [IoT Edge에서 모델 배포 준비](../machine-learning/service/how-to-deploy-to-iot.md)의 지침에 따라 만들 수 있습니다.


### <a name="disable-process-identification"></a>프로세스 식별 사용 안 함

>[!NOTE]
>
> 미리 보기로 있는 동안 Azure Machine Learning은 IoT Edge에서 기본적으로 사용하도록 설정된 프로세스 식별 보안 기능을 지원하지 않습니다. 
> 이를 사용하지 않도록 설정하는 단계는 다음과 같습니다. 그러나 프로덕션 환경에서 사용하는 경우 이러한 단계는 적합하지 않습니다. Windows Edge 런타임 설치 시 이 단계를 이미 완료했으므로 Linux에서만 이 단계를 수행하면 됩니다.

IoT Edge 장치에서 프로세스 식별을 사용하지 않도록 설정하려면 IoT Edge 디먼 구성의 **연결** 섹션에서 **workload_uri** 및 **management_uri**의 IP 주소와 포트를 입력해야 합니다.

먼저 IP 주소를 가져옵니다. 명령줄에 `ipconfig`를 입력하고 **docker0** 인터페이스의 IP 주소를 복사합니다.

IoT Edge 디먼 구성 파일을 편집합니다.

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

구성의 **connect** 섹션을 해당 IP 주소로 업데이트합니다. 예: 
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

구성의 **listen** 섹션에 동일한 주소를 입력합니다. 예: 

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

management_uri 주소를 사용하여 IOTEDGE_HOST 환경 변수를 만듭니다(영구적으로 설정하려면 `/etc/environment`에 추가). 예를 들어 다음과 같습니다.

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-ml-container"></a>Azure ML 컨테이너 만들기
이 섹션에서는 학습된 모델 파일을 다운로드하고 이를 Azure ML 컨테이너로 변환합니다.

[IoT Edge에서 모델 배포 준비](../machine-learning/service/how-to-deploy-to-iot.md) 설명서의 지침에 따라 기계 학습 모델을 사용하여 Docker 컨테이너를 만들 수 있습니다.  Docker 이미지에 필요한 모든 구성 요소는 [Azure IoT Edge용 AI 도구 키트](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)에 있습니다.

### <a name="view-the-container-repository"></a>컨테이너 리포지토리 보기

컨테이너 이미지가 성공적으로 만들어지고 기계 학습 환경과 연결된 Azure Container Registry에 저장되었는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**로 이동하여 **컨테이너 레지스트리**를 선택합니다.
2. 레지스트리를 선택합니다. 이름은 **mlcr**로 시작되어야 하며 모듈 관리를 설정하는 데 사용한 리소스 그룹, 위치 및 구독에 속합니다.
3. **액세스 키**를 선택합니다.
4. **로그인 서버**, **사용자 이름** 및 **암호**를 복사합니다.  Edge 장치에서 레지스트리에 액세스하려면 이러한 항목이 필요합니다.
5. **리포지토리** 선택
6. **machinelearningmodule** 선택
7. 이제 컨테이너의 전체 이미지 경로가 있습니다. 다음 섹션에서 사용하기 위해 이 이미지 경로를 적어둡니다. **<registry_name>.azurecr.io/machinelearningmodule:1**처럼 표시됩니다.

## <a name="deploy-to-your-device"></a>새 장치에 배포

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.

1. **IoT Edge**로 이동하여 IoT Edge 장치를 선택합니다.

1. **모듈 설정**을 선택합니다.

1. **레지스트리 설정** 섹션에서 Azure 컨테이너 레지스트리에서 복사한 자격 증명을 추가합니다. 

   ![레지스트리 자격 증명 추가](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. 이전에 IoT Edge 장치에 tempSensor 모듈을 배포한 경우 자동으로 입력될 수 있습니다. 모듈 목록에 없는 경우 모듈을 추가합니다.

    1. **추가**를 클릭하고 **IoT Edge 모듈**을 선택합니다.
    2. **이름** 필드에 `tempSensor`을 입력합니다.
    3. **이미지 URI** 필드에 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`를 입력합니다.
    4. **저장**을 선택합니다.

1. 만든 기계 학습 모듈을 추가합니다.

    1. **추가**를 클릭하고 **IoT Edge 모듈**을 선택합니다.
    1. **이름** 필드에 `machinelearningmodule`을 입력합니다.
    1. **이미지** 필드에 이미지 주소(예: `<registry_name>.azurecr.io/machinelearningmodule:1`)를 입력합니다.
    1. **저장**을 선택합니다.

1. **모듈 추가** 단계로 돌아가서 **다음**을 선택합니다.

1. **경로 지정** 단계에서 다음 JSON을 텍스트 상자에 복사합니다. 첫 번째 경로는 모든 Azure Machine Learning 모듈이 사용하는 엔드포인트인 "amlInput" 엔드포인트를 통해 온도 센서에서 Machine Learning 모듈로 메시지를 전송합니다. 두 번째 경로는 Machine Learning 모듈에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 “amlOutput”은 모든 Azure Machine Learning 모듈이 데이터를 출력하는 데 사용하는 엔드포인트이고 ‘$upstream’은 IoT Hub를 나타냅니다.

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ```

1. **다음**을 선택합니다.

1. **배포 검토** 단계에서 **제출**을 선택합니다.

1. 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다.  **tempSensor** 모듈 및 IoT Edge 런타임 모듈과 함께 실행되는 새로운 **machinelearningmodule**이 표시됩니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

각 IoT Edge 모듈에서 생성된 메시지를 볼 수 있으며 IoT 허브에 전달된 메시지도 볼 수 있습니다.

### <a name="view-data-on-your-iot-edge-device"></a>IoT Edge 장치에서 데이터 보기

IoT Edge 장치에서 모든 개별 모듈에서 보낸 메시지를 볼 수 있습니다. 

Linux 장치에서 이러한 명령을 수행하는 경우 승격된 권한에 대해 `sudo`를 사용해야 할 수 있습니다.

1. IoT Edge 장치에서 모든 모듈을 봅니다.

   ```cmd/sh
   iotedge list
   ```

2. 특정 장치에서 보낸 메시지를 봅니다. 이전 명령의 출력에서 나온 모듈 이름을 사용합니다.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>IoT 허브에 도착한 데이터 보기

[Visual Studio Code용 Azure IoT Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)을 사용하여 IoT Hub가 받는 장치-클라우드 메시지를 볼 수 있습니다.

다음 단계에서는 IoT 허브에 도착한 장치-클라우드 메시지를 모니터링하도록 Visual Studio Code를 설정하는 방법을 보여 줍니다. 

1. Visual Studio Code에서 **IoT Hub 장치**를 선택합니다.

2. **...** 를 선택하고 나서 메뉴에서 **IoT Hub 연결 문자열 설정**을 선택합니다.

   ![IoT Hub 장치 추가 메뉴](./media/tutorial-deploy-machine-learning/set-connection.png)

3. 페이지의 맨 위에 열리는 텍스트 상자에 IoT Hub의 iothubowner 연결 문자열을 입력합니다. IoT Edge 장치가 IoT Hub 장치 목록에 표시됩니다.

4. **...** 를 다시 선택한 후 **D2C 메시지 모니터링 시작**을 선택합니다.

5. 5초마다 tempSensor에서 보낸 메시지를 관찰합니다. 메시지 본문에 machinelearningmodule이 true 또는 false 값을 제공하는 **anomaly**라는 속성이 포함되어 있습니다. **AzureMLResponse** 속성에는 모델이 성공적으로 실행된 경우 값 "OK"가 포함됩니다.

   ![메시지 본문의 Azure ML 응답](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>리소스 정리 

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 장치와 동일한 IoT Edge 장치를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Machine Learning에서 제공하는 IoT Edge 모듈을 배포했습니다. Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다른 자습서 중 하나를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Custom Vision 서비스를 사용하여 이미지 분류](tutorial-deploy-custom-vision.md)

