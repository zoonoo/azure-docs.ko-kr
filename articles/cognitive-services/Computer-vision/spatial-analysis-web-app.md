---
title: 공간 분석 웹 앱 배포
titleSuffix: Azure Cognitive Services
description: 웹 응용 프로그램에서 공간 분석을 사용 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: aahi
ms.openlocfilehash: 3bc03cf03f8a8e0f2a222ca1089618eaade9485d
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92496074"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>방법: 웹 응용 프로그램을 계산 하는 사용자 배포

이 문서를 사용 하 여 공간 분석을 사람들의 움직임을 이해 하는 웹 앱에 통합 하 고 실제 공간을 차지 하는 사용자 수를 모니터링 하는 방법을 알아봅니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

* 공간 분석 컨테이너 배포
* 작업 및 카메라 구성
* 웹 응용 프로그램에서 IoT Hub 연결 구성
* 웹 응용 프로그램 배포 및 테스트

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure IoT Edge 배포 구성 및 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) 에 대 한 기본적인 이해
* 구성 된 [호스트 컴퓨터](spatial-analysis-container.md)입니다.

## <a name="deploy-the-spatial-analysis-container"></a>공간 분석 컨테이너 배포

컨테이너를 실행 하기 위한 액세스 권한을 얻기 위해 [요청 응용 프로그램](https://aka.ms/csgate) 을 작성 합니다. 

[호스트 컴퓨터 설정](./spatial-analysis-container.md) 에 따라 호스트 컴퓨터를 구성 하 고 IoT Edge 장치를 Azure IoT Hub에 연결 합니다. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>구독에 Azure IoT Hub 서비스 배포

먼저 표준 가격 책정 계층 (S1) 또는 무료 계층 (S0)을 사용 하 여 Azure IoT Hub 서비스의 인스턴스를 만듭니다. 다음 지침에 따라 Azure CLI를 사용 하 여이 인스턴스를 만듭니다.

필수 매개 변수를 입력 합니다.
* 구독: Azure 구독의 이름 또는 ID
* 리소스 그룹: 리소스 그룹의 이름을 만듭니다.
* Iot Hub 이름: IoT Hub의 이름을 만듭니다.
* IoTHub Name: 만든 IoT Hub의 이름입니다. 
* Edge 장치 이름:에 지 장치에 대 한 이름 만들기

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>호스트 컴퓨터의 Azure IoT Edge에 컨테이너 배포

Azure CLI를 사용 하 여 공간 분석 컨테이너를 호스트 컴퓨터에 IoT 모듈로 배포 합니다. 배포 프로세스에는 배포에 필요한 컨테이너, 변수 및 구성을 설명 하는 배포 매니페스트 파일이 필요 합니다. *공간 분석* 컨테이너에 대 한 기본 배포 구성을 포함 하는 GitHub에서 [Azure Stack](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) 에 지 특정 배포 매니페스트 뿐만 아니라 [edge 별 배포 매니페스트 Azure Stack](https://github.com/Azure-Samples/cognitive-services-rest-api-samples/) 샘플을 찾을 수 있습니다. 

> [!NOTE] 
> *Telegraf* 및 *공간 분석-진단* 컨테이너는 선택 사항입니다. 파일 * 의DeploymentManifest.js* 에서 제거 하도록 결정할 수 있습니다. 자세한 내용은 [원격 분석 및 문제 해결](./spatial-analysis-logging.md) 문서를 참조 하세요. Github의 파일에서 [Azure Stack Edge 장치](https://go.microsoft.com/fwlink/?linkid=2142179) 또는 다른 [데스크톱 컴퓨터](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) 에 대 한 두 개의 샘플 *DeploymentManifest.js* 를 찾을 수 있습니다.

### <a name="set-environment-variables"></a>환경 변수 설정

IoT Edge 모듈에 대 한 대부분의 **환경 변수** 는 위에 연결 된 파일 * 의 샘플DeploymentManifest.js* 에 이미 설정 되어 있습니다. 파일에서 `BILLING_ENDPOINT` 아래와 같이 및 환경 변수를 검색 합니다 `API_KEY` . 값을 이전에 만든 끝점 URI 및 API 키로 바꿉니다. EULA 값이 "수락"으로 설정 되어 있는지 확인 합니다. 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>작업 매개 변수 구성

이제 *공간 분석* 컨테이너의 초기 구성이 완료 되었으므로 다음 단계는 작업 매개 변수를 구성 하 고 배포에 추가 하는 것입니다. 

첫 번째 단계는 위에 연결 된 샘플 배포 매니페스트를 업데이트 하 고 아래와 같이에 대해 operationId를 구성 하는 것입니다 `cognitiveservices.vision.spatialanalysis-personcount` .


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

배포 매니페스트가 업데이트 된 후 카메라 제조업체의 지침에 따라 카메라를 설치 하 고, 카메라 url을 구성 하 고, 사용자 이름 및 암호를 구성 합니다. 

그런 다음 `VIDEO_URL` 카메라의 RTSP url로 설정 하 고, 카메라에 연결 하기 위한 자격 증명을 설정 합니다.

Edge 장치에 GPU가 둘 이상 있는 경우이 작업을 실행할 GPU를 선택 합니다. 단일 GPU에서 실행 되는 작업이 8 개를 초과 하지 않는 작업은 한 번에 부하를 분산 해야 합니다.  

다음으로, 사용자를 계산 하려는 영역을 구성 합니다. 영역 다각형을 구성 하려면 먼저 제조업체의 지침에 따라 카메라에서 프레임을 검색 합니다. 다각형의 각 꼭 짓 점을 확인 하려면 프레임에서 점을 선택 하 고, 프레임의 왼쪽, 위쪽 모퉁이를 기준으로 하는 점의 x, y 픽셀 좌표를 사용 하 고, 해당 프레임 치수로 나눕니다. 결과를 꼭 짓 점의 x, y 좌표로 설정 합니다. 필드에서 영역 다각형 구성을 설정할 수 있습니다 `SPACEANALYTICS_CONFIG` .

이 샘플 비디오 프레임은 크기 1920/1080 프레임에 대 한 꼭 짓 점 좌표가 계산 되는 방법을 보여 줍니다.
![샘플 비디오 프레임](./media/spatial-analysis/sample-video-frame.jpg)

검색 된 사용자가 계산 되 고 이벤트가 생성 되는 경우에 대 한 신뢰도 임계값을 선택할 수도 있습니다. 모든 이벤트를 출력 하려면 임계값을 0으로 설정 합니다.

### <a name="execute-the-deployment"></a>배포 실행

이제 배포 매니페스트가 완료 되었으므로 Azure CLI에서이 명령을 사용 하 여 호스트 컴퓨터에 IoT Edge 모듈로 컨테이너를 배포 합니다.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

필수 매개 변수를 입력 합니다.

* IoT Hub 이름: Azure IoT Hub 이름입니다.
* DeploymentManifest.js: 배포 파일의 이름
* IoT Edge 장치 이름: 호스트 컴퓨터의 IoT Edge 장치 이름입니다.
* 구독: 구독 ID 또는 이름입니다.

이 명령은 배포를 시작 하 고 Azure Portal Azure IoT Hub 인스턴스에서 배포 상태를 볼 수 있습니다. 상태는 417로 표시 될 수 있습니다. 장치 배포 구성은 장치가 컨테이너 이미지 다운로드를 완료 하 고 실행을 시작할 때까지 *설정 되지 않습니다* .

### <a name="validate-that-the-deployment-was-successful"></a>배포가 성공 했는지 확인

Azure Portal의 IoT Hub 인스턴스에서 공간 분석 모듈의 IoT Edge 모듈 설정에서 *런타임 상태* 를 찾습니다. *런타임 상태* 에 대해 **원하는 값** 및 **보고 된 값** 이 표시 됩니다 `Running` . Azure Portal에 대 한 자세한 내용은 아래를 참조 하세요.

![배포 확인 예](./media/spatial-analysis/deployment-verification.png)

이 시점에서 공간 분석 컨테이너는 작업을 실행 하 고 있습니다. 작업에 대 한 AI 정보를 내보내고 `cognitiveservices.vision.spatialanalysis-personcount` 이러한 정보를 Azure IoT Hub 인스턴스에 원격 분석으로 라우팅합니다. 추가 카메라를 구성 하기 위해 배포 매니페스트 파일을 업데이트 하 고 배포를 다시 실행할 수 있습니다.

## <a name="person-counting-web-application"></a>웹 응용 프로그램을 계산 하는 사람

이 사용자를 계산 하는 웹 응용 프로그램을 통해 샘플 웹 앱을 신속 하 게 구성 하 고 Azure 환경에서 호스트할 수 있습니다.

### <a name="get-the-person-counting-app-container"></a>앱 컨테이너를 계산 하는 사용자 가져오기

이 앱의 컨테이너 형식은 Azure Container Registry에서 사용할 수 있습니다. 다음 docker pull 명령을 사용 하 여 다운로드 합니다. 액세스 토큰은 Microsoft에 문의 하세요 projectarchon@microsoft.com .

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

컨테이너를 Azure Container Registry (ACR)에 푸시합니다.

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

컨테이너를 설치 하려면 새 Azure Web App for Containers 만들고 필요한 매개 변수를 입력 합니다. 그런 다음 **Docker** 탭으로 이동 하 여 **단일 컨테이너**를 선택 하 고 **Azure Container Registry**합니다. 위의 이미지를 푸시한 Azure Container Registry의 인스턴스를 사용 합니다.

![이미지 세부 정보 입력](./media/spatial-analysis/solution-app-create-screen.png)

위의 매개 변수를 입력 한 후 **검토 + 만들기** 및 앱 만들기를 클릭 합니다.

### <a name="configure-the-app"></a>앱 구성 

설치가 완료 될 때까지 기다렸다가 Azure Portal의 리소스로 이동 합니다. **구성** 섹션으로 이동 하 여 다음 두 가지 **응용 프로그램 설정을**추가 합니다.

* `EventHubConsumerGroup` – Azure IoT Hub에서 소비자 그룹의 문자열 이름을 사용 하 여 IoT Hub에 새 소비자 그룹을 만들거나 기본 그룹을 사용할 수 있습니다. 
* `IotHubConnectionString` -Azure IoT Hub에 대 한 연결 문자열입니다 .이 문자열을 Azure IoT Hub 리소스 ![ 구성 매개 변수의 키 섹션에서 검색할 수 있습니다.](./media/spatial-analysis/solution-app-config-page.png)

이러한 두 설정이 추가 되 면 **저장**을 클릭 합니다. 왼쪽 탐색 메뉴에서 **인증/권한 부여** 를 클릭 하 고 원하는 인증 수준으로 업데이트 합니다. Azure AD (Azure Active Directory) express를 권장 합니다. 

### <a name="test-the-app"></a>앱 테스트

Azure 웹 앱으로 이동 하 여 배포가 성공 했으며 웹 앱이 실행 중인지 확인 합니다. 구성 된 url로 이동 `<yourapp>.azurewebsites.net` 하 여 실행 중인 앱을 확인 합니다.

![배포 테스트](./media/spatial-analysis/solution-app-output.png)

## <a name="next-steps"></a>다음 단계

* [공간 분석 작업 구성](./spatial-analysis-operations.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
* [영역 및 줄 배치 가이드](spatial-analysis-zone-line-placement.md)
