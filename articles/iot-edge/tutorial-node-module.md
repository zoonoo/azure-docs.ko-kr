---
title: Azure IoT Edge Node.js 자습서 | Microsoft Docs
description: 이 자습서에서는 Node.js 코드를 사용하여 IoT Edge 모듈을 만들고, 에지 장치에 배포하는 방법을 보여 줍니다.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 40fa0357245ad77fbdb08c5dbb4839d69322954f
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566795"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>자습서: Node.js IoT Edge 모듈을 개발하여 시뮬레이션된 장치에 배포

비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 센서 데이터를 필터링하는 IoT Edge 모듈을 만들고 배포하는 과정을 안내합니다. 빠른 시작에서 만든 시뮬레이션된 IoT Edge 장치를 사용하겠습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.    

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 IoT Edge Node.js 모듈 만들기
> * Visual Studio Code 및 Docker를 사용하여 docker 이미지를 만들어 레지스트리에 게시합니다. 
> * 모듈을 IoT Edge 장치에 배포합니다.
> * 생성된 데이터 보기


이 자습서에서 만드는 IoT Edge 모듈은 장치에서 생성된 온도 데이터를 필터링합니다. 온도가 지정된 임계값을 초과하는 경우에만 메시지 업스트림을 전송합니다. 에지에서 이 유형의 분석은 클라우드로 전송되고 저장되는 데이터 양을 줄이는 데 유용합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 장치:

* [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)의 빠른 시작에 설명된 단계에 따라 개발 머신 또는 가상 머신을 Edge 장치로 사용할 수 있습니다.

클라우드 리소스:

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md). 

개발 리소스:

* [Visual Studio Code](https://code.visualstudio.com/) 
* Visual Studio Code에 대한 [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Node.js 및 npm](https://nodejs.org). npm 패키지는 Node.js와 함께 배포됩니다. 즉 Node.js를 다운로드하면 npm도 자동으로 컴퓨터에 설치됩니다.

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기
이 자습서에서는 VS Code용 Azure IoT Edge 확장을 사용하여 모듈을 빌드하고 파일에서 **컨테이너 이미지**를 만듭니다. 그런 후 이미지를 저장하고 관리하는 **레지스트리**에 이 이미지를 푸시합니다. 마지막으로 IoT Edge 장치에서 실행되도록 레지스트리의 이미지를 배포합니다.  

이 자습서에서는 Docker 호환 레지스트리를 사용할 수 있습니다. 클라우드에서 사용 가능한 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 자습서에서는 Azure Container Registry를 사용합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **컨테이너** > **Azure Container Registry**를 선택합니다.
2. 레지스트리에 이름을 지정하고, 구독을 선택하고, 리소스 그룹을 선택하고, SKU를 **기본**으로 설정합니다. 
3. **만들기**를 선택합니다.
4. 컨테이너 레지스트리를 만든 후에는 해당 레지스트리로 이동하고 **액세스 키**를 선택합니다. 
5. **관리 사용자**를 **사용**으로 전환합니다.
6. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 복사합니다. 자습서의 뒷부분에서 이러한 값을 사용합니다. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge 모듈 프로젝트 만들기
다음 단계에서는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 IoT Edge Node.js 모듈을 만드는 방법을 보여 줍니다.

### <a name="create-a-new-solution"></a>새 솔루션 만들기

**npm**을 사용하여 빌드할 수 있는 Node.js 솔루션 템플릿을 만듭니다. 

1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택하여 VS Code 통합 터미널을 엽니다.

2. 통합 터미널에서 다음 명령을 입력하여 Node.js Azure IoT Edge 모듈의 **yeoman** 및 생성기를 설치합니다. 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다. 

3. 명령 팔레트에서 **Azure: 로그인** 명령을 입력 및 실행하고, 지침에 따라 Azure 계정에 로그인합니다. 이미 로그인한 경우 이 단계는 건너뛸 수 있습니다.

4. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트에서 다음 정보를 제공하여 솔루션을 만듭니다. 

   1. 솔루션을 만들 폴더를 선택합니다. 
   2. 솔루션에 대한 이름을 제공하거나 기본 **EdgeSolution**을 그대로 적용합니다.
   3. **Node.js Module**을 모듈 템플릿으로 선택합니다. 
   4. 모듈 이름을 **NodeModule**로 지정합니다. 
   5. 이전 섹션에서 만든 Azure Container Registry를 첫 번째 모듈에 대한 이미지 리포지토리로 지정합니다. **localhost:5000**을 복사한 로그인 서버 값으로 바꿉니다. 마지막 문자열은 **\<레지스트리 이름\>.azurecr.io/nodemodule**과 같습니다.

   ![Docker 이미지 리포지토리 제공](./media/tutorial-node-module/repository.png)

VS Code 창에서 IoT Edge 솔루션 작업 영역을 로드합니다. 솔루션 작업 영역에는 최상위 구성 요소 5개가 포함됩니다. 이 자습서에서는 **\.vscode** 폴더 또는 **\.gitignore** 파일을 편집하지 않습니다. **modules** 폴더에는 모듈에 대한 Node.js 코드와 모듈을 컨테이너 이미지로 빌드하기 위한 Dockerfile이 포함되어 있습니다. **\.env** 파일은 컨테이너 레지스트리 자격 증명을 저장합니다. **deployment.template.json** 파일에는 IoT Edge 런타임에서 장치에 모듈을 배포하는 데 사용하는 정보가 포함되어 있습니다. 

솔루션을 만들 때 컨테이너 레지스트리를 지정하지 않았지만 기본값인 localhost:5000을 수락한 경우에는 \.env 파일이 없습니다. 

   ![Node.js 솔루션 작업 영역](./media/tutorial-node-module/workspace.png)

### <a name="add-your-registry-credentials"></a>레지스트리 자격 증명 추가

환경 파일은 컨테이너 리포지토리에 대한 자격 증명을 저장하고 IoT Edge 런타임과 이를 공유합니다. 이러한 자격 증명은 런타임에서 개인 이미지를 IoT Edge 장치로 가져오기 위해 필요합니다. 

1. VS Code 탐색기에서 **.env** 파일을 엽니다. 
2. 필드를 Azure 컨테이너 레지스트리에서 복사한 **사용자 이름** 및 **암호** 값으로 업데이트합니다. 
3. 이 파일을 저장합니다. 

### <a name="update-the-module-with-custom-code"></a>사용자 지정 코드를 사용하여 모듈 업데이트

각 템플릿에는 **tempSensor** 모듈에서 센서 데이터를 시뮬레이션하고 이를 IoT Hub에 라우팅하는 샘플 코드가 포함되어 있습니다. 이 섹션에서는 NodeModule에서 메시지를 보내기 전에 분석하는 코드를 추가합니다. 

1. VS Code 탐색기에서 **modules** > **NodeModule** > **app.js**를 차례로 엽니다.

5. 필요한 노드 모듈 아래에 온도 임계값 변수를 추가합니다. 온도 임계값은 IoT Hub로 데이터를 전송하기 위해 측정된 온도가 초과해야 하는 값을 설정합니다.

    ```javascript
    var temperatureThreshold = 25;
    ```

6. `PipeMessage` 함수 전체를 `FilterMessage` 함수로 바꿉니다.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. `client.on()` 함수에서 `pipeMessage` 함수 이름을 `filterMessage`로 바꿉니다.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. `else` 문 내에서 `client.on()` 뒤의 `client.open()` 함수 콜백에 다음 코드 조각을 복사합니다. 이 함수는 desired 속성이 업데이트될 때 호출됩니다.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. 이 파일을 저장합니다.

## <a name="build-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드

이전 섹션에서는 IoT Edge 솔루션을 만들고 NodeModule에 코드를 추가하여 보고된 컴퓨터 온도가 허용 가능한 임계값 미만인 메시지를 필터링했습니다. 이제 솔루션을 컨테이너 이미지로 빌드하고 컨테이너 레지스트리로 푸시해야 합니다. 

1. Visual Studio Code 통합 터미널에서 다음 명령을 입력하여 Docker에 로그인하면 모듈 이미지를 ACR로 푸시할 수 있습니다. 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   첫 번째 섹션에서 복사한 Azure Container Registry의 사용자 이름, 암호 및 로그인 서버를 사용합니다. 또는 Azure Portal에서 레지스트리의 **액세스 키** 섹션에서 이러한 항목을 다시 검색합니다.

2. VS Code 탐색기에서 IoT Edge 솔루션 작업 영역에 있는 **deployment.template.json** 파일을 엽니다. 

   이 파일은 장치 데이터를 시뮬레이션하는 **tempSensor**와 **NodeModule**의 두 모듈을 배포하도록 `$edgeAgent`에 지시합니다. `NodeModule.image` 값은 Linux amd64 버전의 이미지로 설정됩니다. 배포 매니페스트에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

   또한 이 파일에는 레지스트리 자격 증명도 포함되어 있습니다. 템플릿 파일에서 사용자 이름과 암호는 자리 표시자로 채워집니다. 배포 매니페스트를 생성하면 필드가 **.env**에 추가한 값으로 업데이트됩니다. 

4. NodeModule 모듈 쌍을 배포 매니페스트에 추가합니다. `$edgeHub` 모듈 쌍 뒤에 있는 `moduleContent` 섹션의 아래쪽에 다음 JSON 내용을 삽입합니다. 
    ```json
        "NodeModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```
5. 이 파일을 저장합니다.
6. VS Code 탐색기에서 **deployment.template.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다. 

솔루션을 빌드하도록 Visual Studio Code에 지시하면 먼저 배포 템플릿의 정보를 가져와서 새 **config** 폴더에 `deployment.json` 파일을 생성합니다. 그런 다음, 통합 터미널에서 두 개의 명령, 즉 `docker build`과 `docker push`를 실행합니다. 이 두 명령은 코드를 빌드하고, Node.js 코드를 컨테이너화한 다음, 솔루션을 초기화할 때 지정한 컨테이너 레지스트리로 푸시합니다. 

VS Code 통합 터미널에서 실행되는 `docker build` 명령에서 태그가 포함된 전체 컨테이너 이미지 주소를 확인할 수 있습니다. 이미지 주소는 `module.json` 파일의 정보에서 **\<리포지토리\>:\<버전\>-\<플랫폼\>**  형식으로 작성됩니다. 이 자습서에서 이 주소는 **registryname.azurecr.io/nodemodule:0.0.1-amd64**와 같습니다.

## <a name="deploy-and-run-the-solution"></a>솔루션 배포 및 실행

IoT Edge 장치를 설정할 때 사용한 빠른 시작 문서에서는 Azure Portal을 사용하여 모듈을 배포했습니다. Visual Studio Code용 Azure IoT 도구 키트 확장을 사용하여 모듈을 배포할 수도 있습니다. 여러분의 시나리오를 위한 배포 매니페스트인 **deployment.json** 파일이 이미 준비되어 있습니다. 이제 배포를 받을 장치를 선택하기만 하면 됩니다.

1. VS Code 명령 팔레트에서 **Azure IoT Hub: IoT Hub 선택**을 실행합니다. 

2. 구성하려는 IoT Edge 장치가 포함된 구독 및 IoT Hub를 선택합니다. 

3. VS Code 탐색기에서 **Azure IoT Hub 장치** 섹션을 펼칩니다. 

4. IoT Edge 장치의 이름을 마우스 오른쪽 단추로 클릭한 다음, **단일 장치용 배포 만들기**를 선택합니다. 

   ![단일 장치용 배포 만들기](./media/tutorial-node-module/create-deployment.png)

5. **config** 폴더에서 **deployment.json** 파일을 선택한 다음, **에지 배포 매니페스트 선택**을 클릭합니다. deployment.template.json 파일을 사용하지 마세요. 

6. 새로고침 단추를 클릭합니다. **TempSensor** 모듈과 **$edgeAgent** 및 **$edgeHub**와 함께 실행되는 새 **NodeModule**이 표시됩니다. 


## <a name="view-generated-data"></a>생성된 데이터 보기

IoT Edge 장치에 배포 매니페스트를 적용한 후에는 장치의 IoT Edge 런타임이 새 배포 정보를 수집하고 그에 따라 실행하기 시작합니다. 배포 매니페스트에 포함되지 않는 장치에서 실행되는 모든 모듈은 중지됩니다. 장치에서 누락된 모든 모듈이 시작됩니다. 

Visual Studio Code Explorer의 **Azure IoT Hub 장치** 섹션을 통해 IoT Edge 장치 상태를 확인할 수 있습니다. 배포되어 실행 중인 모듈의 목록을 보려면 장치 상세 정보를 확장합니다. 

IoT Edge 장치 자체에서 `iotedge list` 명령을 사용하여 배포 모듈 상태를 확인할 수 있습니다. 두 IoT Edge 런타임 모듈과 tempSensor, 이 자습서에서 만든 사용자 지정 모듈 등, 4개 모듈이 표시됩니다. 모든 모듈이 시작되려면 몇 분 정도 걸릴 수 있으므로 처음에 일부가 표시되지 않는다면 명령을 다시 실행합니다. 

모든 모듈에서 생성되는 메시지를 보려면 `iotedge logs <module name>` 명령을 사용합니다. 

메시지가 IoT 허브에 도착하면 Visual Studio Code를 사용하여 메시지를 볼 수 있습니다. 

1. IoT Hub에 도달하는 데이터를 모니터링하려면 **...** 를 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.
2. 특정 장치에 대한 D2C 메시지를 모니터링하려면 목록에서 해당 장치를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.
3. 데이터 모니터링을 중지하려면 명령 팔레트에서 **Azure IoT Hub: D2C 메시지 모니터링 중지** 명령을 실행합니다. 
4. 모듈 쌍을 보거나 업데이트하려면 목록에서 해당 모듈을 마우스 오른쪽 단추로 클릭하고 **모듈 쌍 편집**을 선택합니다. 모듈 쌍을 업데이트하려면 쌍 JSON 파일을 저장하고, 편집기 영역을 마우스 오른쪽 단추로 클릭하고, **모듈 쌍** 업데이트를 선택합니다.
5. Docker 로그를 보려면 VS Code용 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)를 설치하고 Docker 탐색기에서 실행 중인 모듈을 로컬로 찾을 수 있습니다. 통합 터미널에서 보려면 상황에 맞는 메뉴에서 **로그 표시**를 클릭합니다. 

## <a name="clean-up-resources"></a>리소스 정리 

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 장치와 동일한 IoT Edge 장치를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치에서 생성된 원시 데이터를 필터링하는 코드가 포함된 IoT Edge 모듈을 만들었습니다. Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다음 자습서 중 하나를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Functions를 모듈로 배포](tutorial-deploy-function.md)
> [Azure Stream Analytics를 모듈로 배포](tutorial-deploy-stream-analytics.md)

