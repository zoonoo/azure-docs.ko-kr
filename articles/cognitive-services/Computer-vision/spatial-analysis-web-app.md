---
title: 공간 분석 웹앱 배포
titleSuffix: Azure Cognitive Services
description: 웹 애플리케이션에서 공간 분석을 사용하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: bd071fc930420a48a764eff3818580885312fde6
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746072"
---
# <a name="how-to-deploy-a-spatial-analysis-web-application"></a>방법: 공간 분석 웹 애플리케이션 배포

이 문서를 사용하여 IotHub에서 공간 분석 데이터(인사이트)를 수집하고 시각화하는 웹앱을 배포하는 방법을 알아봅니다. 다양한 시나리오와 산업에 걸쳐 유용한 애플리케이션을 제공할 수 있습니다. 예를 들어 회사에서 부동산 공간 사용을 최적화하려는 경우 다양한 시나리오로 솔루션을 신속하게 만들 수 있습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

* 공간 분석 컨테이너 배포
* 작업 및 카메라 구성
* 웹 애플리케이션에 IoT Hub 연결 구성
* 웹 애플리케이션 배포 및 테스트

이 앱은 아래 시나리오를 소개합니다.

* 공간/상점에 출입하는 사람 수
* 체크 아웃 영역/구역에 출입하는 사람 수와 체크 아웃 줄에서 소요된 시간(체류 시간)
* 얼굴 마스크를 쓴 사람 수 
* 사회적 거리두기 지침을 위반하는 사람 수

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure IoT Edge 배포 구성 및 [Azure IoT Hub](../../iot-hub/index.yml)에 대한 기본적인 이해
* 구성된 [호스트 컴퓨터](spatial-analysis-container.md).

## <a name="deploy-the-spatial-analysis-container"></a>공간 분석 컨테이너 배포

컨테이너를 실행하는 데 필요한 액세스 권한을 얻기 위해 [애플리케이션 요청](https://aka.ms/csgate)을 작성합니다. 

[호스트 컴퓨터 설정](./spatial-analysis-container.md)에 따라 호스트 컴퓨터를 구성하고 IoT Edge 디바이스를 Azure IoT Hub에 연결합니다. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>구독에 Azure IoT Hub 서비스 배포

먼저 표준 가격 책정 계층(S1) 또는 무료 계층(S0)을 사용하여 Azure IoT Hub 서비스의 인스턴스를 만듭니다. 다음 지침에 따라 Azure CLI를 사용하여 이 인스턴스를 만듭니다.

필수 매개 변수를 입력합니다.
* 구독: Azure 구독의 이름 또는 ID
* 리소스 그룹: 리소스 그룹의 이름 만들기
* IoT Hub 이름: IoT Hub의 이름 만들기
* IoTHub 이름: 만든 IoT Hub의 이름 
* Edge 디바이스 이름: 에지 디바이스의 이름 만들기

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>호스트 컴퓨터의 Azure IoT Edge에 컨테이너 배포

다음 단계에서는 Azure CLI를 사용하여 **공간 분석** 컨테이너를 호스트 컴퓨터에 IoT 모듈로 배포합니다. 배포 프로세스를 수행하려면 배포에 필요한 컨테이너, 변수 및 구성을 설명하는 배포 매니페스트 파일이 필요합니다. 샘플 배포 매니페스트는 모든 시나리오에 대해 미리 빌드된 구성을 포함하는 [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json)에서 찾을 수 있습니다.  

### <a name="set-environment-variables"></a>환경 변수 설정

IoT Edge 모듈에 대한 대부분의 **환경 변수** 는 위에 연결된 샘플 *DeploymentManifest.json* 파일에 이미 설정되어 있습니다. 이 파일에서 아래와 같이 `ENDPOINT` 및 `APIKEY` 환경 변수를 검색합니다. 값을 이전에 만든 엔드포인트 URI 및 API 키로 바꿉니다. EULA 값이 "accept"로 설정되어 있는지 확인합니다. 

```json
"EULA": { 
    "value": "accept"
},
"ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"APIKEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>작업 매개 변수 구성

필요한 모든 구성(작업, 녹화된 비디오 파일 URL 및 영역 등)이 이미 있는 샘플 [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json)을 사용하는 경우 **배포 실행** 섹션으로 건너뛸 수 있습니다.

이제 공간 분석 컨테이너의 초기 구성이 완료되었으므로 다음 단계는 작업 매개 변수를 구성하고 배포에 추가하는 것입니다. 

첫 번째 단계는 샘플 [DeploymentManifest.json](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/deployment.json)을 업데이트하고 원하는 작업을 구성하는 것입니다. 예를 들어 cognitiveservices.vision.spatialanalysis-personcount에 대한 구성은 다음과 같습니다.

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

배포 매니페스트가 업데이트되면 카메라 제조업체의 지침에 따라 카메라를 설치하고, 카메라 URL을 구성하고, 사용자 이름 및 암호를 구성합니다. 

그런 다음, `VIDEO_URL`을 카메라의 RTSP URL로 설정하고, 카메라에 연결하기 위한 자격 증명을 설정합니다.

에지 디바이스에 GPU가 둘 이상 있는 경우 이 작업을 실행할 GPU를 선택합니다. 단일 GPU에서 한 번에 실행되는 작업이 8개를 초과하지 않는 작업은 부하를 분산해야 합니다.  

다음으로, 인원수를 세려는 영역을 구성합니다. 영역 다각형을 구성하려면 먼저 제조업체의 지침에 따라 카메라에서 프레임을 검색합니다. 다각형의 각 꼭짓점을 확인하려면 프레임에서 점을 선택하고, 프레임의 왼쪽, 위쪽 모퉁이를 기준으로 하는 점의 x, y 픽셀 좌표를 사용하고, 해당 프레임 차원 수로 나눕니다. 결과를 꼭짓점의 x, y 좌표로 설정합니다. `SPACEANALYTICS_CONFIG` 필드에 영역 다각형 구성을 설정할 수 있습니다.

이는 1920/1080 크기의 프레임에 대한 꼭짓점 좌표가 계산되는 방법을 보여 주는 샘플 동영상 프레임입니다.
![샘플 동영상 프레임](./media/spatial-analysis/sample-video-frame.jpg)

검색된 인원수가 계수되고 이벤트가 생성되는 경우에 대한 신뢰도 임계값을 선택할 수도 있습니다. 모든 이벤트를 출력하려면 임계값을 0으로 설정합니다.

### <a name="execute-the-deployment"></a>배포 실행

이제 배포 매니페스트가 완료되었으므로 Azure CLI에서 이 명령을 사용하여 컨테이너를 호스트 컴퓨터에 IoT Edge 모듈로 배포합니다.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

필수 매개 변수를 입력합니다.

* IoT Hub 이름: Azure IoT Hub 이름
* DeploymentManifest.json: 배포 파일의 이름
* IoT Edge 디바이스 이름: 호스트 컴퓨터의 IoT Edge 디바이스 이름
* 구독: 구독 ID 또는 이름

이 명령은 배포를 시작하고 Azure Portal의 Azure IoT Hub 인스턴스에서 배포 상태를 볼 수 있습니다. 디바이스가 컨테이너 이미지 다운로드를 완료하고 실행을 시작할 때까지 상태가 *417 – 디바이스의 배포 구성이 설정되지 않았습니다* 라고 표시될 수 있습니다.

### <a name="validate-that-the-deployment-was-successful"></a>배포가 성공했는지 확인

Azure Portal의 IoT Hub 인스턴스에 있는 공간 분석 모듈에 대한 IoT Edge 모듈 설정에서 *런타임 상태* 를 찾습니다. *런타임 상태* 의 **원하는 값** 및 **보고된 값** 이 `Running`으로 표시되어야 합니다. Azure Portal에서 이 값이 어떻게 표시되는지 보려면 아래를 참조하세요.

![배포 확인 예](./media/spatial-analysis/deployment-verification.png)

이때 공간 분석 컨테이너는 작업을 실행하고 있습니다. 작업에 대한 AI 인사이트를 내보내고 이러한 인사이트를 원격 분석으로 Azure IoT Hub 인스턴스에 라우팅합니다. 추가 카메라를 구성하기 위해 배포 매니페스트 파일을 업데이트하고 배포를 다시 실행할 수 있습니다.

## <a name="spatial-analysis-web-application"></a>공간 분석 웹 애플리케이션

공간 분석 웹 애플리케이션을 사용하면 개발자가 샘플 웹앱을 신속하게 구성하고, 해당 Azure 환경에서 호스트하고, 앱을 사용하여 E2E 이벤트의 유효성을 검사할 수 있습니다.

## <a name="build-docker-image"></a>Docker 이미지 빌드

[가이드](https://github.com/Azure-Samples/cognitive-services-spatial-analysis/blob/main/README.md#docker-image)에 따라 이미지를 빌드하고 구독의 Azure Container Registry에 푸시합니다.

## <a name="setup-steps"></a>설정 단계

컨테이너를 설치하려면 새 Azure App Service를 만들고 필요한 매개 변수를 입력합니다. 그런 다음, **Docker** 탭으로 이동하여 **단일 컨테이너**, **Azure Container Registry** 를 차례로 선택합니다. 위의 이미지를 밀어넣은 Azure Container Registry의 인스턴스를 사용합니다.

![이미지 세부 정보 입력](./media/spatial-analysis/solution-app-create-screen.png)

위의 매개 변수를 입력한 후 **검토 + 만들기** 를 클릭하고 앱을 만듭니다.

### <a name="configure-the-app"></a>앱 구성 

설정이 완료될 때까지 기다렸다가 Azure Portal의 리소스로 이동합니다. **구성** 섹션으로 이동하여 다음 두 가지 **애플리케이션 설정** 을 추가합니다.

* `EventHubConsumerGroup` – Azure IoT Hub의 소비자 그룹 문자열 이름. IoT Hub에 새 소비자 그룹을 만들거나 기본 그룹을 사용할 수 있습니다. 
* `IotHubConnectionString` – Azure IoT Hub에 대한 연결 문자열. 이 문자열을 Azure IoT Hub 리소스 ![매개 변수 구성](./media/spatial-analysis/solution-app-config-page.png)의 키 섹션에서 검색할 수 있습니다.

이러한 2개 설정이 추가되면 **저장** 을 클릭합니다. 그런 다음, 왼쪽 탐색 메뉴에서 **인증/권한 부여** 를 클릭하고 원하는 인증 수준으로 업데이트합니다. Azure AD(Azure Active Directory) 기본을 권장합니다. 

### <a name="test-the-app"></a>앱 테스트

Azure 서비스로 이동하여 배포가 성공적으로 수행되었으며 웹앱이 실행되고 있는지 확인합니다. 구성된 URL `<yourapp>.azurewebsites.net`으로 이동하여 실행 중인 앱을 확인합니다.

![배포 테스트](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>PersonCount 소스 코드 가져오기
이 애플리케이션에 대한 소스 코드를 보거나 수정하려는 경우 [GitHub에서](https://github.com/Azure-Samples/cognitive-services-spatial-analysis) 소스 코드를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [공간 분석 작업 구성](./spatial-analysis-operations.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
* [영역 및 선 배치 가이드](spatial-analysis-zone-line-placement.md)
