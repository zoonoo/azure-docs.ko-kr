---
title: Azure IoT Edge Python 자습서 | Microsoft Docs
description: 이 자습서에서는 Python 코드를 사용하여 IoT Edge 모듈을 만들고, 에지 장치에 배포하는 방법을 보여 줍니다.
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 42af2b5ec6b591929f37afebe6546d61b8a3a02a
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082851"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>자습서: Python IoT Edge 모듈을 개발하여 시뮬레이션된 장치에 배포

비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 센서 데이터를 필터링하는 IoT Edge 모듈을 만들고 배포하는 과정을 안내합니다. 여기서는 [Windows][lnk-quickstart-win] 또는 [Linux][lnk-quickstart-lin] 빠른 시작의 '시뮬레이션된 장치에 Azure IoT Edge 배포'에서 만든 시뮬레이션된 IoT Edge 장치를 사용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.    

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 IoT Edge Python 모듈 만들기
> * Visual Studio Code 및 Docker를 사용하여 docker 이미지를 만들어 레지스트리에 게시합니다. 
> * 모듈을 IoT Edge 장치에 배포합니다.
> * 생성된 데이터 보기


이 자습서에서 만드는 IoT Edge 모듈은 장치에서 생성된 온도 데이터를 필터링합니다. 온도가 지정된 임계값을 초과하는 경우에만 메시지 업스트림을 전송합니다. 에지에서 이 유형의 분석은 클라우드로 전송되고 저장되는 데이터 양을 줄이는 데 유용합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free)을 만듭니다.


## <a name="prerequisites"></a>필수 조건

* [Linux](quickstart-linux.md)용 빠른 시작에서 만든 Azure IoT Edge 장치.

   >[!Note]
   >Azure IoT Edge용 Python 모듈은 Windows 또는 ARM 장치를 지원하지 않습니다. 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Visual Studio Code에 대한 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Visual Studio Code용 Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* Visual Studio Code가 있는 동일한 컴퓨터의 [Docker](https://docs.docker.com/engine/installation/). 이 자습서에서는 CE(Community Edition)로 충분합니다. 
* [Python](https://www.python.org/downloads/).
* Python 패키지 설치를 위한 [Pip](https://pip.pypa.io/en/stable/installing/#installation)(일반적으로 Python 설치와 함께 포함됨)

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
다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 IoT Edge Python 모듈을 만드는 방법을 보여 줍니다.

### <a name="create-a-new-solution"></a>새 솔루션 만들기

**cookiecutter** Python 패키지를 사용하여 빌드할 수 있는 Python 솔루션 템플릿을 만듭니다. 

1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택하여 VS Code 통합 터미널을 엽니다.

2. 통합 터미널에서 다음 명령을 입력하여 VS Code에서 Edge 솔루션 템플릿을 만드는 데 사용하는 **cookiecutter**를 설치하거나 업데이트합니다.

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다. 

4. 명령 팔레트에서 **Azure: 로그인** 명령을 입력 및 실행하고, 지침에 따라 Azure 계정에 로그인합니다. 이미 로그인한 경우 이 단계는 건너뛸 수 있습니다.

5. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트에서 다음 정보를 제공하여 솔루션을 만듭니다. 

   1. 솔루션을 만들 폴더를 선택합니다. 
   2. 솔루션에 대한 이름을 제공하거나 기본 **EdgeSolution**을 그대로 적용합니다.
   3. **Python 모듈**을 모듈 템플릿으로 선택합니다. 
   4. 모듈 이름을 **PythonModule**로 지정합니다. 
   5. 이전 섹션에서 만든 Azure Container Registry를 첫 번째 모듈에 대한 이미지 리포지토리로 지정합니다. **localhost:5000**을 복사한 로그인 서버 값으로 바꿉니다. 마지막 문자열은 **\<레지스트리 이름\>.azurecr.io/pythonmodule**과 같습니다.
 
VS Code 창에서 IoT Edge 솔루션 작업 영역을 로드합니다. 여기에는 **modules** 폴더, 배포 매니페스트 템플릿 파일 및 **.env** 파일이 있습니다. 

### <a name="add-your-registry-credentials"></a>레지스트리 자격 증명 추가

환경 파일은 컨테이너 리포지토리에 대한 자격 증명을 저장하고 IoT Edge 런타임과 이를 공유합니다. 이러한 자격 증명은 런타임에서 개인 이미지를 IoT Edge 장치로 가져오기 위해 필요합니다. 

1. VS Code 탐색기에서 **.env** 파일을 엽니다. 
2. 필드를 Azure 컨테이너 레지스트리에서 복사한 **사용자 이름** 및 **암호** 값으로 업데이트합니다. 
3. 이 파일을 저장합니다. 

### <a name="update-the-module-with-custom-code"></a>사용자 지정 코드를 사용하여 모듈 업데이트

각 템플릿에는 **tempSensor** 모듈에서 센서 데이터를 시뮬레이션하고 이를 IoT Hub에 라우팅하는 샘플 코드가 포함되어 있습니다. 이 섹션에서는 메시지를 보내기 전에 pythonModule을 확장하여 메시지를 분석하는 코드를 추가합니다. 

1. VS Code 탐색기에서 **modules** > **PythonModule** > **main.py**를 차례로 엽니다.

2. **main.py** 위쪽에서 `json` 라이브러리를 가져옵니다.

    ```python
    import json
    ```

3. 전역 카운터 아래에 `TEMPERATURE_THRESHOLD` 및 `TWIN_CALLBACKS` 변수를 추가합니다. IoT Hub로 데이터를 보내려면 온도 임계값을 측정된 컴퓨터 온도에서 초과해야 하는 값으로 설정합니다.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. `receive_message_callback` 함수를 다음 코드로 바꿉니다.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. `module_twin_callback`이라는 새 함수를 추가합니다. 이 함수는 원하는 속성이 업데이트될 때 호출됩니다.

    ```python
    # module_twin_callback is invoked when twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. `HubManager` 클래스에서 방금 추가한 `module_twin_callback` 함수를 초기화하도록 `__init__` 메서드에 새 줄을 추가합니다.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. 이 파일을 저장합니다.

## <a name="build-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드

이전 섹션에서는 IoT Edge 솔루션을 만들고 PythonModule에 코드를 추가하여 보고된 컴퓨터 온도가 허용 가능한 임계값 미만인 메시지를 필터링했습니다. 이제 솔루션을 컨테이너 이미지로 빌드하고 컨테이너 레지스트리로 푸시해야 합니다. 

1. Visual Studio Code 통합 터미널에서 다음 명령을 입력하여 Docker에 로그인하면 모듈 이미지를 ACR로 푸시할 수 있습니다. 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   첫 번째 섹션에서 복사한 Azure Container Registry의 사용자 이름, 암호 및 로그인 서버를 사용합니다. 또는 Azure Portal에서 레지스트리의 **액세스 키** 섹션에서 이러한 항목을 다시 검색합니다.

2. VS Code 탐색기에서 IoT Edge 솔루션 작업 영역에 있는 **deployment.template.json** 파일을 엽니다. 

   이 파일은 장치 데이터를 시뮬레이션하는 **tempSensor**와 **PythonModule**의 두 모듈을 배포하도록 `$edgeAgent`에 지시합니다. `PythonModule.image` 값은 Linux amd64 버전의 이미지로 설정됩니다. 배포 매니페스트에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

   또한 이 파일에는 레지스트리 자격 증명도 포함되어 있습니다. 템플릿 파일에서 사용자 이름과 암호는 자리 표시자로 채워집니다. 배포 매니페스트를 생성하면 필드가 **.env**에 추가한 값으로 업데이트됩니다. 

3. PythonModule 모듈 쌍을 배포 매니페스트에 추가합니다. `$edgeHub` 모듈 쌍 뒤에 있는 `moduleContent` 섹션의 아래쪽에 다음 JSON 내용을 삽입합니다. 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. 이 파일을 저장합니다.

5. VS Code 탐색기에서 **deployment.template.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드**를 선택합니다. 

솔루션을 빌드하도록 Visual Studio Code에 지시하면 먼저 배포 템플릿의 정보를 가져와서 새 **config** 폴더에 `deployment.json` 파일을 생성합니다. 그런 다음, 통합 터미널에서 두 개의 명령, 즉 `docker build`과 `docker push`를 실행합니다. 이 두 명령은 코드를 빌드하고, Python 코드를 컨테이너화한 다음, 솔루션을 초기화할 때 지정한 컨테이너 레지스트리로 푸시합니다. 

VS Code 통합 터미널에서 실행되는 `docker build` 명령에서 태그가 포함된 전체 컨테이너 이미지 주소를 확인할 수 있습니다. 이미지 주소는 `module.json` 파일의 정보에서 **\<리포지토리\>:\<버전\>-\<플랫폼\>**  형식으로 작성됩니다. 이 자습서에서 이 주소는 **registryname.azurecr.io/pythonmodule:0.0.1-amd64**와 같습니다.

## <a name="deploy-and-run-the-solution"></a>솔루션 배포 및 실행

빠른 시작에서 수행한 대로 Azure Portal을 사용하여 Python 모듈을 IoT Edge 장치에 배포할 수 있지만, Visual Studio Code 내에서 모듈을 배포하고 모니터링할 수도 있습니다. 다음 섹션에서는 필수 조건에 나열된 VS Code용 Azure IoT Edge 확장을 사용합니다. 아직 설치되어 있지 않으면 지금 설치하세요. 

1. **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다.

2. **Azure: 로그인** 명령을 검색하여 실행합니다. 지침에 따라 Azure 계정에 로그인합니다. 

3. 명령 팔레트에서 **Azure IoT Hub: IoT Hub 선택** 명령을 검색하여 실행합니다. 

4. IoT Hub가 포함된 구독을 선택한 다음, 액세스하려는 IoT Hub를 선택합니다.

5. VS Code 탐색기에서 **Azure IoT Hub 장치** 섹션을 펼칩니다. 

6. IoT Edge 장치의 이름을 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 장치에 대한 배포 만들기**를 선택합니다. 

7. PythonModule이 포함된 솔루션 폴더로 이동합니다. **config** 폴더를 열고 **deployment.json** 파일을 선택합니다. **에지 배포 매니페스트 선택**을 클릭합니다.

8. **Azure IoT Hub 장치** 섹션을 새로 고칩니다. **TempSensor** 모듈과 **$edgeAgent** 및 **$edgeHub**와 함께 실행되는 새 **PythonModule**이 표시됩니다. 

## <a name="view-generated-data"></a>생성된 데이터 보기

1. IoT Hub에 도달하는 데이터를 모니터링하려면 **...** 를 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.
2. 특정 장치에 대한 D2C 메시지를 모니터링하려면 목록에서 해당 장치를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.
3. 데이터 모니터링을 중지하려면 명령 팔레트에서 **Azure IoT Hub: D2C 메시지 모니터링 중지** 명령을 실행합니다. 
4. 모듈 쌍을 보거나 업데이트하려면 목록에서 해당 모듈을 마우스 오른쪽 단추로 클릭하고 **모듈 쌍 편집**을 선택합니다. 모듈 쌍을 업데이트하려면 쌍 JSON 파일을 저장하고, 편집기 영역을 마우스 오른쪽 단추로 클릭하고, **모듈 쌍** 업데이트를 선택합니다.
5. Docker 로그를 보려면 VS Code용 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)를 설치하고 Docker 탐색기에서 실행 중인 모듈을 로컬로 찾을 수 있습니다. 통합 터미널에서 보려면 상황에 맞는 메뉴에서 **로그 표시**를 클릭합니다. 

## <a name="clean-up-resources"></a>리소스 정리 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

권장되는 다음 아티클을 계속 진행하는 경우 지금까지 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다.

그렇지 않으면 요금이 부과되지 않도록 이 아티클에서 만든 로컬 구성과 Azure 리소스를 삭제할 수 있습니다. 

> [!IMPORTANT]
> Azure 리소스와 리소스 그룹을 삭제하면 되돌릴 수 없습니다. 일단 삭제되면 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 보관할 리소스가 포함된 기존 리소스 그룹 내에 IoT Hub를 만든 경우 리소스 그룹을 삭제하지 말고 IoT Hub 리소스만 삭제하면 됩니다.
>

IoT Hub만 삭제하려면 허브 이름과 리소스 그룹 이름을 사용하여 다음 명령을 실행합니다.

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


이름으로 전체 리소스 그룹을 삭제하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

2. **이름을 기준으로 필터링...** 텍스트 상자에 IoT Hub가 들어 있는 리소스 그룹의 이름을 입력합니다. 

3. 결과 목록의 리소스 그룹 오른쪽에서 **...** 를 클릭한 다음, **리소스 그룹 삭제**를 클릭합니다.

4. 리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 다시 입력하여 확인한 다음, **삭제**를 클릭합니다. 잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치에서 생성된 원시 데이터를 필터링하는 코드가 포함된 IoT Edge 모듈을 만들었습니다. Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다음 자습서 중 하나를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Functions를 모듈로 배포](tutorial-deploy-function.md)
> [Azure Stream Analytics를 모듈로 배포](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
