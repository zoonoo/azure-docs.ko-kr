---
title: 자습서 - 디바이스에 Custom Vision 분류자 배포 - Azure IoT Edge | Microsoft Docs
description: 이 자습서에서는 Custom Vision 및 IoT Edge를 사용하여 컴퓨터 비전 모델을 컨테이너로 실행하는 방법을 알아봅니다.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: a0530739428e18d01209f94345ae53dfb743d80b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239679"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>자습서: Custom Vision Service를 사용하여 에지에서 이미지 분류 수행

Azure IoT Edge를 통해 워크로드를 클라우드에서 에지로 이동하여 IoT 솔루션의 효율성을 높일 수 있습니다. 이 기능은 컴퓨터 비전 모델과 같은 많은 데이터를 처리하는 서비스에 적합합니다. [Custom Vision Service](../cognitive-services/custom-vision-service/home.md)를 통해 사용자 지정 이미지 분류자를 빌드하고 컨테이너로 디바이스에 배포할 수 있습니다. 이러한 두 서비스를 함께 사용하여 먼저 모든 데이터를 사이트에서 전송하지 않고도 이미지 또는 비디오 스트림에서 정보를 찾을 수 있습니다. Custom Vision은 이미지를 정보를 생성하는 학습된 모델과 비교하는 분류자를 제공합니다. 

예를 들어 IoT Edge 디바이스의 Custom Vision은 고속도로에서 정상보다 높거나 낮은 트래픽이 발생하는지 여부 또는 주차장에 사용 가능한 주차 공간이 연달아 있는지 여부를 확인할 수 있습니다. 작업을 수행하기 위해 다른 서비스와 이러한 정보를 공유할 수 있습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Custom Vision을 사용하여 이미지 분류자 빌드
> * 디바이스에서 Custom Vision 웹 서버를 쿼리하는 IoT Edge 모듈 개발
> * IoT Hub에 이미지 분류자의 결과 전송

<center>

![다이어그램 - 자습서 아키텍처, 분류자 준비 및 배포](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하려면 이전 자습서를 진행하여 Linux 컨테이너 개발을 위한 개발 환경이 설정되어 있어야 합니다. [Linux 디바이스를 위한 IoT Edge 모듈을 개발합니다](tutorial-develop-for-linux.md). 이 자습서를 완료하여 다음과 같은 필수 구성 요소를 갖추어야 합니다. 

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* [Azure IoT Edge를 실행하는 Linux 디바이스](quickstart-linux.md)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)와 같은 컨테이너 레지스트리
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)를 사용하여 구성된 [Visual Studio Code](https://code.visualstudio.com/)
* Linux 컨테이너를 실행하도록 구성된 [Docker CE](https://docs.docker.com/install/)

Custom Vision 서비스를 사용하여 IoT Edge 모듈을 개발하려면 다음과 같은 추가 필수 구성 요소를 개발 머신에 설치합니다. 

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* Visual Studio Code용 [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 확장

## <a name="build-an-image-classifier-with-custom-vision"></a>Custom Vision을 사용하여 이미지 분류자 빌드

이미지 분류자를 빌드하려면 Custom Vision 프로젝트를 만들고 교육 이미지를 제공해야 합니다. 이 섹션에서 수행하는 단계에 대한 자세한 내용은 [Custom Vision을 사용하여 분류자를 빌드하는 방법](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md)을 참조하세요.

이미지 분류자를 빌드 및 학습하면 Docker 컨테이너로 내보내고 IoT Edge 디바이스에 배포할 수 있습니다. 

### <a name="create-a-new-project"></a>새 프로젝트 만들기

1. 웹 브라우저에서 [Custom Vision 웹 페이지](https://customvision.ai/)로 이동합니다.

2. **로그인**을 선택하고 Azure 리소스에 액세스하는 데 사용하는 동일한 계정으로 로그인합니다. 

3. **새 프로젝트**를 선택합니다.

4. 다음 값을 사용하여 프로젝트를 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | Name | **EdgeTreeClassifier**와 같은 프로젝트의 이름을 제공합니다. |
   | 설명 | 선택적인 프로젝트 설명입니다. |
   | 리소스 그룹 | 기본값 **제한된 평가판**을 적용합니다. |
   | 프로젝트 형식 | **분류** |
   | 분류 형식 | **다중 클래스(이미지당 단일 태그)** | 
   | 도메인 | **일반(압축)** |

5. **프로젝트 만들기**를 선택합니다.

### <a name="upload-images-and-train-your-classifier"></a>이미지 업로드 및 분류자 학습

이미지 분류자를 만들려면 학습 이미지 집합 뿐만 아니라 테스트 이미지도 필요합니다. 

1. [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) 리포지토리의 샘플 이미지를 로컬 개발 머신으로 복제 또는 다운로드합니다. 

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Custom Vision 프로젝트로 돌아가고 **이미지 추가**를 선택합니다. 

3. 로컬로 복제한 git 리포지토리를 찾고, 첫 번째 이미지 폴더, **Cognitive-CustomVision-Windows / Samples / Images / Hemlock**으로 이동합니다. 폴더에서 10개의 모든 이미지를 선택한 다음, **열기**를 선택합니다. 

4. 태그 **hemlock**을 이 이미지 그룹에 추가하고 **입력** 키를 눌러 태그를 적용합니다. 

5. **10개의 파일 업로드**를 선택합니다. 

   ![Custom Vision에 hemlock 태그가 지정된 파일 업로드](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. 이미지가 성공적으로 업로드되면 **완료**를 선택합니다.

7. **이미지 추가**를 다시 선택합니다.

8. 두 번째 이미지 폴더, **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry**를 찾습니다. 폴더에서 10개의 모든 이미지를 선택한 다음, **열기**를 선택합니다. 

9. 태그 **japanese cherry**를 이 이미지 그룹에 추가하고 **입력** 키를 눌러 태그를 적용합니다. 

10. **10개의 파일 업로드**를 선택합니다. 이미지가 성공적으로 업로드되면 **완료**를 선택합니다. 

11. 두 개의 이미지 집합이 태그가 지정되고 업로드되면 **학습**을 선택하여 분류자를 학습합니다. 

### <a name="export-your-classifier"></a>분류자 내보내기

1. 분류자를 학습한 후 분류자의 성능 페이지에서 **내보내기**를 선택합니다. 

   ![학습된 이미지 분류자 내보내기](./media/tutorial-deploy-custom-vision/export.png)

2. 플랫폼에 대해 **DockerFile**을 선택합니다. 

3. 버전에 대해 **Linux**를 선택합니다.  

4. **내보내기**를 선택합니다. 

   ![Linux 컨테이너를 사용하여 DockerFile로 내보내기](./media/tutorial-deploy-custom-vision/export-2.png)

5. 내보내기가 완료되면 **다운로드**를 선택하고 컴퓨터에 .zip 패키지를 로컬로 저장합니다. 패키지에서 모든 파일을 추출합니다. 이러한 파일을 사용하여 이미지 분류 서버를 포함하는 IoT Edge 모듈을 만듭니다. 

이 시점에 도달하면 Custom Vision 프로젝트 만들기 및 학습을 완료했습니다. 다음 섹션에서는 내보낸 파일을 사용하지만 Custom Vision 웹 페이지 사용은 완료했습니다. 

## <a name="create-an-iot-edge-solution"></a>IoT Edge 솔루션 만들기

이제 개발 머신에 이미지 분류자의 컨테이너 버전에 대한 파일이 있습니다. 이 섹션에서는 이미지 분류자 컨테이너를 IoT Edge 모듈로 실행하도록 구성합니다. 또한 이미지 분류자와 함께 배포되는 두 번째 모듈을 만듭니다. 두 번째 모듈은 분류자에 대한 요청을 게시하고 IoT Hub에 메시지로 결과를 보냅니다. 

### <a name="create-a-new-solution"></a>새 솔루션 만들기

솔루션은 단일 IoT Edge 배포에 대해 여러 모듈을 개발 및 구성하는 논리적 방법입니다. 솔루션은 하나 이상의 모듈에 대한 코드 및 IoT Edge 디바이스에서 구성하는 방법을 선언하는 배포 매니페스트를 포함합니다. 

1. Visual Studio Code에서 **보기** > **터미널**을 선택하여 VS Code 통합 터미널을 엽니다.

2. 통합 터미널에서 다음 명령을 입력하여 VS Code에서 IoT Edge python 모듈 템플릿을 만드는 데 사용하는 **cookiecutter**를 설치하거나 업데이트합니다.

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >명령 프롬프트에서 호출할 수 있도록 cookiecutter를 설치할 위치가 환경의 `Path`에 있는지 확인합니다.

3. **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다. 

4. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트에서 다음 정보를 제공하여 솔루션을 만듭니다. 

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | VS Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | **CustomVisionSolution**과 같은 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값을 적용합니다. |
   | 모듈 템플릿 선택 | **Python 모듈**을 선택합니다. |
   | 모듈 이름 제공 | 모듈 이름을 **classifier**로 지정합니다.<br><br>이 모듈 이름은 반드시 소문자여야 합니다. IoT Edge는 모듈을 참조할 때 대/소문자를 구분하고 이 솔루션에서는 소문자로 모든 요청을 서식 지정하는 라이브러리를 사용합니다. |
   | 모듈의 Docker 이미지 리포지토리 제공 | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 마지막 단계에서 미리 채워져 있습니다. **localhost:5000**을 Azure 컨테이너 레지스트리의 로그인 서버 값으로 바꿉니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다. 마지막 문자열은 \<registry name\>.azurecr.io/classifier 형식입니다. |
 
   ![Docker 이미지 리포지토리 제공](./media/tutorial-deploy-custom-vision/repository.png)

Visual Studio Code 창에서 IoT Edge 솔루션 작업 영역을 로드합니다.

### <a name="add-your-registry-credentials"></a>레지스트리 자격 증명 추가

환경 파일은 컨테이너 레지스트리의 자격 증명을 저장하고 IoT Edge 런타임과 공유합니다. 이러한 자격 증명은 런타임에서 프라이빗 이미지를 IoT Edge 디바이스로 가져오기 위해 필요합니다.

1. VS Code 탐색기에서 .env 파일을 엽니다.
2. 필드를 Azure 컨테이너 레지스트리에서 복사한 **사용자 이름** 및 **암호** 값으로 업데이트합니다.
3. 이 파일을 저장합니다.

### <a name="select-your-target-architecture"></a>대상 아키텍처 선택

현재, Visual Studio Code에서는 Linux AMD64 및 Linux ARM32v7 디바이스용 모듈을 개발할 수 있습니다. 컨테이너는 아키텍처 유형별로 다르게 빌드되고 실행되므로 각 솔루션에서 대상으로 지정할 대상 아키텍처를 선택해야 합니다. 기본값은 Linux AMD64입니다. 

1. 명령 팔레트를 열고 **Azure IoT Edge: 에지 솔루션용 기본 대상 플랫폼 설정**을 검색하거나 창의 맨 아래에 있는 사이드바에서 바로 가기 아이콘을 선택합니다. 

2. 명령 팔레트의 옵션 목록에서 대상 아키텍처를 선택합니다. 이 자습서에서는 Ubuntu 가상 머신을 IoT Edge 디바이스로 사용할 예정이므로 기본값인 **amd64**를 그대로 둡니다. 

### <a name="add-your-image-classifier"></a>이미지 분류자 추가

Visual Studio 코드의 Python 모듈 템플릿은 IoT Edge를 테스트하도록 실행할 수 있는 몇 가지 샘플 코드를 포함합니다. 이 시나리오에서는 해당 코드를 사용하지 않습니다. 대신 이 섹션의 단계를 사용하여 샘플 코드를 이전에 내보낸 이미지 분류자 컨테이너로 바꿉니다. 

1. 파일 탐색기에서 다운로드 및 추출한 Custom Vision 패키지를 찾습니다. 추출된 패키지에서 모든 콘텐츠를 복사합니다. 두 개의 폴더, **app** 및 **azureml**과 두 개의 파일, **Dockerfile** 및 **README**여야 합니다. 

2. 파일 탐색기에서 Visual Studio Code에서 IoT Edge 솔루션을 만들도록 설정한 디렉터리를 찾습니다. 

3. 분류자 모듈 폴더를 엽니다. 이전 섹션에서 제안된 이름을 사용한 경우 폴더 구조는 **CustomVisionSolution / modules / classifier**와 같습니다. 

4. 파일을 **분류자** 폴더로 붙여넣습니다. 

5. Visual Studio Code 창으로 돌아갑니다. 솔루션 작업 영역은 이제 모듈 폴더에 이미지 분류자 파일을 표시해야 합니다. 

   ![이미지 분류자 파일이 있는 솔루션 작업 영역](./media/tutorial-deploy-custom-vision/workspace.png)

6. 분류자 폴더에서 **module.json** 파일을 엽니다. 

7. **플랫폼** 매개 변수를 추가한 새 Dockerfile을 가리키도록 업데이트하고, 현재 Custom Vision 모듈에 대해 지원되지 않는 ARM32 아키텍처 및 AMD64.debug 옵션을 제거합니다. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. 변경 내용을 저장합니다. 

### <a name="create-a-simulated-camera-module"></a>시뮬레이션된 카메라 모듈 만들기

실제 Custom Vision 배포에는 실시간 이미지 또는 비디오 스트림을 제공하는 카메라가 있습니다. 이 시나리오의 경우 이미지 분류자에 테스트 이미지를 전송하는 모듈을 작성하여 카메라를 시뮬레이션합니다. 

#### <a name="add-and-configure-a-new-module"></a>새 모듈 추가 및 구성

이 섹션에서는 동일한 CustomVisionSolution에 새 모듈을 추가하고 시뮬레이션된 카메라를 만드는 코드를 제공합니다. 

1. 동일한 Visual Studio Code 창에서 명령 팔레트를 사용하여 **Azure IoT Edge: IoT Edge 모듈 추가**를 실행합니다. 명령 팔레트에서 새 모듈에 대한 다음 정보를 제공합니다. 

   | prompt | 값 | 
   | ------ | ----- |
   | 배포 템플릿 파일 선택 | CustomVisionSolution 폴더에서 deployment.template.json 파일을 선택합니다. |
   | 모듈 템플릿 선택 | **Python 모듈** 선택 |
   | 모듈 이름 제공 | 모듈의 이름을 **cameraCapture**로 지정 |
   | 모듈의 Docker 이미지 리포지토리 제공 | **localhost:5000**을 Azure 컨테이너 레지스트리의 로그인 서버 값으로 바꿉니다. 마지막 문자열은 **\<registryname\>.azurecr.io/cameracapture** 형식입니다. |

   VS Code 창은 솔루션 작업 영역에서 새 모듈을 로드하고, deployment.template.json 파일을 업데이트합니다. 이제 두 개의 모듈 폴더, classifier 및 cameraCapture가 표시됩니다. 

2. **modules** / **cameraCapture** 폴더에서 **main.py** 파일을 엽니다. 

3. 전체 파일을 다음 코드로 바꿉니다. 이 샘플 코드는 분류자 모듈에서 실행되는 이미지 처리 서비스에 POST 요청을 보냅니다. 이 모듈 컨테이너를 요청에 사용할 샘플 이미지로 제공합니다. 그런 다음, IoT Hub 메시지로 응답을 패키지하고 출력 큐에 보냅니다.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json

    import iothub_client
    # pylint: disable=E0611
    from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError
    # pylint: disable=E0401

    # messageTimeout - the maximum time in milliseconds until a message times out.
    # The timeout period starts at IoTHubModuleClient.send_event_async.
    MESSAGE_TIMEOUT = 10000

    # Choose HTTP, AMQP or MQTT as transport protocol.  
    PROTOCOL = IoTHubTransportProvider.MQTT

    # global counters
    SEND_CALLBACKS = 0

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = IoTHubMessage(bytearray(strMessage, 'utf8'))
        hubManager.send_event_to_output("output1", message, 0)

    # Callback received when the message that we send to IoT Hub is processed.
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        SEND_CALLBACKS += 1
        print ( "Confirmation received for message with result = %s" % result )
        print ( "   Total calls confirmed: %d \n" % SEND_CALLBACKS )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("Response from classification service: (" + str(response.status_code))

        return json.dumps(response.json())

    class HubManager(object):
        def __init__(self, protocol, message_timeout):
            self.client_protocol = protocol
            self.client = IoTHubModuleClient()
            self.client.create_from_environment(protocol)
            # set the time until a message times out
            self.client.set_option("messageTimeout", message_timeout)
            
        # Sends a message to an output queue, to be routed by IoT Edge hub. 
        def send_event_to_output(self, outputQueueName, event, send_context):
            self.client.send_event_async(
                outputQueueName, event, send_confirmation_callback, send_context)

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global hubManager 
                hubManager = HubManager(PROTOCOL, MESSAGE_TIMEOUT)
            except IoTHubError as iothub_error:
                print ( "Unexpected error %s from IoTHub" % iothub_error )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. **main.py** 파일을 저장합니다. 

5. **requrements.txt** 파일을 엽니다. 

6. 컨테이너에 포함할 라이브러리에 대한 새 줄을 추가합니다.

   ```Text
   requests
   ```

7. **requirements.txt** 파일을 저장합니다.


#### <a name="add-a-test-image-to-the-container"></a>컨테이너에 테스트 이미지 추가

이 시나리오에 대한 이미지 피드를 제공하기 위해 실제 카메라를 사용하는 대신 단일 테스트 이미지를 사용합니다. 테스트 이미지는 이 자습서의 앞부분에서 학습 이미지에 대해 다운로드한 GitHub 리포지토리에 포함되어 있습니다. 

1. **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**에 위치한 테스트 이미지로 이동합니다. 

2. **test_image.jpg** 복사 

3. IoT Edge 솔루션 디렉터리를 찾고 **modules** / **cameraCapture** 폴더에 테스트 이미지를 붙여넣습니다. 이미지는 이전 섹션에서 편집한 main.py 파일과 동일한 폴더에 있어야 합니다. 

3. Visual Studio Code에서 cameraCapture 모듈에 대한 **Dockerfile.amd64** 파일을 엽니다. (ARM32는 현재 Custom Vision 모듈에서 지원되지 않습니다.) 

4. 작업 디렉터리, `WORKDIR /app`을 설정하는 줄 뒤에 다음 코드 줄을 추가합니다. 

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

5. Dockerfile을 저장합니다. 

### <a name="prepare-a-deployment-manifest"></a>배포 매니페스트 준비

지금까지 이 자습서에서는 트리의 이미지를 분류하는 Custom Vision 모델을 학습하고, IoT Edge 모듈로 모델을 패키지했습니다. 그런 다음, 이미지 분류 서버를 쿼리하고 IoT Hub에 결과를 다시 보고할 수 있는 두 번째 모듈을 만들었습니다. 이제 이러한 두 모듈을 함께 시작하고 실행하는 방법을 IoT Edge 디바이스를 알려주는 배포 매니페스트를 만들 준비가 되었습니다. 

Visual Studio Code용 IoT Edge 확장은 배포 매니페스트를 만들 수 있도록 각 IoT Edge 솔루션에서 템플릿을 제공합니다. 

1. 솔루션 폴더에서 **deployment.template.json** 파일을 엽니다. 

2. 만든 classifier 및 cameraCapture의 두 개와 기본적으로 포함된 tempSensor로 세 개의 모듈을 포함해야 하는 **모듈** 섹션을 찾습니다. 

3. 모든 해당 매개 변수를 사용하여 **tempSensor** 모듈을 삭제합니다. 이 모듈은 테스트 시나리오에 대한 샘플 데이터를 제공하기 위해 포함되지만 이 배포에 필요하지 않습니다. 

4. 이미지 분류 모듈을 **classifier** 이외의 이름으로 지정한 경우 이름을 확인하고 모두 소문자인지 확인합니다. cameraCapture 모듈은 소문자로 모든 요청을 서식 지정하는 요청 라이브러리를 사용하여 분류자 모듈을 호출하며, IoT Edge는 대/소문자를 구분합니다. 

5. cameraCapture 모듈에 대한 **createOptions** 매개 변수를 다음 JSON으로 업데이트합니다. 이 정보는 main.py 프로세스에서 검색되는 모듈 컨테이너에 환경 변수를 만듭니다. 배포 매니페스트에 이 정보를 포함하여 모듈 이미지를 다시 빌드하지 않고도 이미지 또는 엔드포인트를 변경할 수 있습니다. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Custom Vision 모듈을 *classifier* 이외의 이름으로 지정한 경우 이미지 처리 엔드포인트 값을 일치하도록 업데이트합니다. 

5. 파일의 맨 아래에서 $edgeHub 모듈에 대한 **경로** 매개 변수를 업데이트합니다. cameraCapture의 예측 결과를 IoT Hub로 라우팅하려고 합니다. 

    ```json
        "routes": {
          "CameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    두 번째 모듈을 *cameraCapture* 이외의 이름으로 지정한 경우 경로 값을 일치하도록 업데이트합니다. 

7. **deployment.template.json** 파일을 저장합니다.

## <a name="build-and-deploy-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드 및 배포

모듈이 생성되고 배포 매니페스트 템플릿이 구성되면 컨테이너 이미지를 빌드하고 컨테이너 레지스트리에 푸시할 준비가 되었습니다. 

이미지가 레지스트리에 있으면 솔루션을 IoT Edge 디바이스에 배포할 수 있습니다. IoT Hub를 통해 디바이스에서 모듈을 설정할 수 있지만 Visual Studio Code를 통해 IoT Hub 및 디바이스에 액세스할 수도 있습니다. 이 섹션에서는 IoT Hub에 대한 액세스 권한을 설정한 다음, VS Code를 사용하여 IoT Edge 디바이스에 솔루션을 배포합니다.

먼저 솔루션을 컨테이너 레지스트리에 빌드 및 푸시합니다. 

1. VS Code 탐색기에서 **deployment.template.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다. VS Code의 통합 터미널에서 이 작업의 진행률을 볼 수 있습니다. 
2. 새 폴더가 솔루션, **config**에 추가되었습니다. 이 파일을 확장하고 **deployment.json** 파일을 엽니다.
3. deployment.json 파일에서 정보를 검토합니다. 구성한 배포 템플릿 파일 및 .env 파일과 module.json 파일을 포함하는 솔루션의 정보를 기반으로 deployment.json 파일이 자동으로 생성됩니다(또는 업데이트됨). 

다음으로 디바이스를 선택하고 솔루션을 배포합니다.

1. VS Code 탐색기에서 **Azure IoT Hub 디바이스** 섹션을 펼칩니다. 
2. 배포에서 대상으로 지정하려는 디바이스를 마우스 오른쪽 단추로 클릭하고, **단일 디바이스 배포 만들기**를 선택합니다. 
3. 파일 탐색기에서는 솔루션 내의 **config** 폴더로 이동하고, **deployment.json**을 선택합니다. **에지 배포 매니페스트 선택**을 클릭합니다. 

배포가 성공하는 경우 VS Code 출력에 확인 메시지가 출력됩니다. VS Code 탐색기에서 이 배포에 사용한 IoT Edge 디바이스에 대한 세부 정보를 확장합니다. 커서로 **Azure IoT Hub 디바이스** 헤더를 가리켜 모듈이 즉시 표시되지 않는 경우 새로 고침 단추를 활성화합니다. 모듈을 시작하고 IoT Hub에 다시 보고하는 데 몇 분 정도 걸릴 수 있습니다. 

모든 모듈이 디바이스 자체에서 실행되는지를 확인할 수도 있습니다. IoT Edge 디바이스에서 다음 명령을 실행하여 모듈의 상태를 확인합니다. 모듈을 시작하는 데 몇 분 정도 걸릴 수 있습니다.

   ```bash
   iotedge list
   ```

## <a name="view-classification-results"></a>분류 결과 보기

모듈의 결과를 보는 두 가지 방법이 있습니다. 메시지가 생성 및 전송되는 대로 디바이스 자체에서 보거나 메시지가 IoT Hub에 도착하는 대로 Visual Studio Code에서 볼 수 있습니다. 

디바이스에서 cameraCapture 모듈의 로그를 확인하여 전송되는 메시지 및 IoT Hub에서 수신했음을 확인합니다. 

   ```bash
   iotedge logs cameraCapture
   ```

Visual Studio Code에서 IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택합니다. 

cameraCapture 모듈에서 메시지로 전송되는 Custom Vision 모듈의 결과는 이미지가 hemlock 또는 cherry 트리일 가능성을 포함합니다. 이미지는 hemlock이므로 확률은 1.0으로 표시됩니다. 


## <a name="clean-up-resources"></a>리소스 정리

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 디바이스와 동일한 IoT Edge 디바이스를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금이 발생하지 않도록 이 문서에서 사용한 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Custom Vision 모델을 학습하고 이를 모듈로 IoT Edge 디바이스에 배포했습니다. 그런 다음, 이미지 분류 서비스를 쿼리하고 IoT Hub에 결과를 다시 보고할 수 있는 모듈을 빌드했습니다. 

라이브 카메라 피드를 사용하여 이 시나리오의 보다 심층적인 버전을 시도하려는 경우 [Raspberry Pi 3의 Custom Vision 및 Azure IoT Edge](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) GitHub 프로젝트를 참조하세요. 

Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [SQL Server 데이터베이스로 에지에 데이터 저장](tutorial-store-data-sql-server.md)
