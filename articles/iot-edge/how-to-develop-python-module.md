---
title: Azure IoT Edge용 Python 모듈 디버그 | Microsoft Docs
description: Visual Studio Code를 사용하여 Azure IoT Edge용 Python 모듈 개발, 빌드 및 디버그
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4393d3231ca4f0a813d4586161383bd6d61f6a49
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2018
ms.locfileid: "45552337"
---
# <a name="use-visual-studio-code-to-develop-and-debug-python-modules-for-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure IoT Edge용 Python 모듈 개발 및 디버그

비즈니스 논리를 Azure IoT Edge용 모듈로 전환할 수 있습니다. 이 문서에서는 VS Code(Visual Studio Code)를 기본 도구로 사용하여 Python 모듈을 개발하고 디버그하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건
이 아티클에서는 사용자가 Windows 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 머신으로 사용한다고 가정합니다. 또한 IoT Edge 보안 디먼을 사용하여 개발 머신에서 IoT Edge 장치를 시뮬레이트합니다.

> [!NOTE]
> 이 디버깅 아티클에서는 모듈 컨테이너에서 프로세스를 연결하고 VS Code로 디버그하는 방법을 설명합니다. Linux amd64 컨테이너에서만 Python 모듈을 디버그할 수 있습니다. Visual Studio Code의 디버깅 기능에 익숙하지 않은 경우, [디버깅](https://code.visualstudio.com/Docs/editor/debugging)에 대해 읽어 보시기 바랍니다. 

이 아티클에서 Visual Studio Code를 기본 개발 도구로 사용하기 때문에 VS Code를 설치합니다. 그런 다음, 필요한 확장을 추가합니다.
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker 확장](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* Visual Studio Code용 [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Python](https://www.python.org/downloads/).
* Python 패키지 설치를 위한 [Pip](https://pip.pypa.io/en/stable/installing/#installation)(일반적으로 Python 설치와 함께 포함됨)
* 아래 명령으로 **Cookiecutter** 설치
   
    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

모듈을 만들려면 모듈 이미지를 빌드하기 위한 Docker, 모듈 이미지를 저장하기 위한 컨테이너 레지스트리가 필요합니다.
* 개발 머신의 [Docker Community Edition](https://docs.docker.com/install/) 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다. 

장치에서 모듈을 테스트하려면 하나 이상의 IoT Edge 장치가 있는 활성 IoT 허브가 필요합니다. 컴퓨터를 IoT Edge 장치로 사용하려면 [Linux](quickstart-linux.md)용 빠른 시작의 단계에 따릅니다. 

## <a name="create-a-new-solution-template"></a>새 솔루션 템플릿 만들기

다음 단계를 따라 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 Azure IoT Python SDK를 기준으로 IoT Edge 모듈을 만듭니다. 먼저 솔루션을 만든 다음, 해당 솔루션에서 첫 번째 모듈을 생성합니다. 각 솔루션에는 둘 이상의 모듈이 포함될 수 있습니다. 

1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택합니다.

2. **보기** > **명령 팔레트**를 선택합니다. 

3. 명령 팔레트에서 **Azure IoT Edge: 새로운 IoT Edge 솔루션** 명령을 입력하고 실행합니다.

   ![New IoT Edge Solution 실행](./media/how-to-develop-csharp-module/new-solution.png)

4. 새로운 솔루션을 만들 폴더로 이동합니다. **폴더 선택**을 선택합니다. 

5. 솔루션에 대한 이름을 입력합니다. 

6. 솔루션의 첫 번째 모듈에 대한 템플릿으로 **Python 모듈**을 선택합니다.

7. 모듈의 이름을 입력합니다. 컨테이너 레지스트리 내에서 고유한 이름을 선택합니다. 

8. 모듈의 이미지 리포지토리 이름을 입력합니다. VS Code는 **localhost:5000**으로 모듈 이름을 자동으로 채웁니다. 고유한 레지스트리 정보로 바꿉니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 **localhost**를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는 **\<레지스트리 이름\>.azurecr.io**와 같이 표시됩니다. 문자열에서 localhost 부분만 바꾸고, 모듈 이름을 삭제하지 마세요. 

   ![Docker 이미지 리포지토리 제공](./media/how-to-develop-c-module/repository.png)

VS Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만든 다음, 새 창에서 로드합니다.

솔루션 내에 4개의 항목이 있습니다. 
* **.vscode** 폴더에는 디버그 구성이 들어 있습니다.
* **modules** 폴더에는 각 모듈의 하위 폴더가 있습니다. 이 시점에는 하나만 있습니다. 하지만 **Azure IoT Edge: Add IoT Edge Module** 명령을 사용하여 명령 팔레트에서 모듈을 더 추가할 수 있습니다. 
* **.env** 파일은 환경 변수를 나열합니다. Azure Container Registry가 레지스트리인 경우 거기에 Azure Container Registry 사용자 이름 및 암호가 있습니다. 

   > [!NOTE]
   > 환경 파일은 모듈에 대한 이미지 리포지토리를 제공하는 경우에만 생성됩니다. localhost 기본값을 로컬로 테스트하고 디버그하도록 수락하는 경우 환경 변수를 선언할 필요가 없습니다. 

* **deployment.template.json** 파일은 테스트에 사용할 수 있는 데이터를 시뮬레이션하는 샘플 **tempSensor** 모듈과 함께 새 모듈을 나열합니다. 배포 매니페스트 작동 방식에 대한 자세한 내용은 [배포 매니페스트를 사용하여 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요. 

## <a name="develop-your-module"></a>모듈 개발

솔루션과 함께 제공되는 기본 Python 모듈 코드는 **modules** > [모듈 이름] > **main.py**에 있습니다. 모듈 및 deployment.template.json 파일은 솔루션을 빌드하고, 컨테이너 레지스트리에 푸시하고, 장치에 배포하여 코드를 변경하지 않고 테스트를 시작하도록 설정됩니다. 모듈은 단순히 원본에서 입력을 가져오고(이 경우에 데이터를 시뮬레이션하는 tempSensor 모듈) IoT Hub로 파이핑하도록 빌드됩니다. 

고유한 코드를 사용하여 Python 템플릿을 사용자 지정할 준비가 된 경우 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하여 보안, 장치 관리 및 안정성 등 IoT 솔루션에 대한 주요 필요 사항을 해결하는 모듈을 빌드합니다. 

## <a name="build-and-deploy-your-module-for-debugging"></a>디버그를 위해 모듈 빌드 및 배포

각 모듈 폴더에는 서로 다른 컨테이너 유형에 사용되는 여러 Docker 파일이 있습니다. **.debug** 확장명으로 끝나는 이러한 파일을 사용하여 테스트할 모듈을 빌드합니다. 현재 Python 모듈은 Linux amd64 컨테이너에서만 디버깅을 지원합니다. 

1. VS Code에서 `deployment.template.json` 파일로 이동합니다. 끝에 **.debug**를 추가하여 모듈 이미지 URL을 업데이트합니다.

2. **deployment.template.json**에서 Python 모듈 createOptions를 아래 콘텐츠로 바꾸고 이 파일을 저장합니다. 
    
    ```json
    "createOptions": "{\"ExposedPorts\":{\"5678/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"5678/tcp\":[{\"HostPort\":\"5678\"}]}}}"
    ```

3. `main.py`로 이동하여 다음 코드를 import 섹션 뒤에 추가합니다.
    
    ```python
    import ptvsd
    ptvsd.enable_attach(('0.0.0.0',  5678))
    ```

4. 디버그할 콜백에 다음 단일 코드 줄을 추가합니다.

    ```python
    ptvsd.break_into_debugger()
    ```

   예를 들어 `receive_message_callback` 메서드를 디버그하려는 경우 아래와 같은 단일 코드 줄을 삽입할 수 있습니다.

    ```python
    def receive_message_callback(message, hubManager):
        ptvsd.break_into_debugger()
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

2. VS Code 명령 팔레트에서 **Azure IoT Edge: IoT Edge 솔루션 빌드 및 푸시** 명령을 입력하고 실행합니다.
3. 명령 팔레트에서 솔루션의 `deployment.template.json` 파일을 선택합니다. 
4. Azure IoT Hub Device Explorer에서 IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭합니다. 그런 후 **단일 장치용 배포 만들기**를 선택합니다. 
5. 솔루션의 **config** 폴더를 엽니다. 그런 다음, `deployment.json` 파일을 선택합니다. **Edge 배포 매니페스트 선택**을 선택합니다. 

VS Code 통합 터미널에서 배포 ID를 사용하여 생성된 배포가 표시됩니다.

VS Code Docker 탐색기를 사용하거나 터미널에서 `docker ps` 명령을 실행하여 컨테이너 상태를 확인합니다.

## <a name="start-debugging-python-module-in-vs-code"></a>VS Code에서 Python 모듈 디버깅 시작
VS Code는 작업 영역의 `.vscode` 폴더에 위치한 `launch.json` 파일에서 구성 정보를 디버깅하도록 유지합니다. 이 `launch.json` 파일은 새 IoT Edge 솔루션을 만들 때 생성되었으며, 디버깅을 지원하는 새 모듈을 추가할 때마다 업데이트됩니다. 

1. VS Code 디버그 보기로 이동합니다. 모듈에 대한 디버그 구성 파일을 선택합니다. 디버그 옵션 이름은 **ModuleName 원격 디버그(Python)** 와 비슷해야 합니다.

2. `main.c`로 이동합니다. `ptvsd.break_into_debugger()`를 넣은 콜백 메서드에 중단점을 추가합니다.

3. **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다. 연결할 프로세스를 선택합니다.

4. VS Code 디버그 보기의 왼쪽 패널에서 변수가 표시됩니다. 

앞의 예제에서는 컨테이너에서 Python IoT Edge 모듈을 디버그하는 방법을 보여 줍니다. 모듈 컨테이너 createOptions에 노출된 포트를 추가했습니다. Python 모듈의 디버그가 끝나면 프로덕션 준비가 완료된 IoT Edge 모듈을 위해 이러한 노출된 포트를 제거하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

모듈을 빌드한 후에 [Visual Studio Code에서 Azure IoT Edge 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.

IoT Edge 장치의 모듈을 개발하려면 [Azure IoT Hub SDK를 이해하고 사용](../iot-hub/iot-hub-devguide-sdks.md)합니다.
