---
title: Azure IoT Edge용 Java 모듈 디버그 | Microsoft Docs
description: Visual Studio Code를 사용하여 Azure IoT Edge용 Java 모듈 개발 및 디버그
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a5e86b7c93344a6c61fecaa41b98bc1daa20f36f
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423224"
---
# <a name="use-visual-studio-code-to-develop-and-debug-java-modules-for-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure IoT Edge용 Java 모듈 개발 및 디버그

Azure IoT Edge용 모듈로 전환하여 비즈니스 논리를 에지에서 작동하도록 전송할 수 있습니다. 이 문서에서는 VS Code(Visual Studio Code)를 주 개발 도구로 사용하여 Java 모듈을 개발 및 디버그하기 위한 자세한 지침을 제공합니다.

## <a name="prerequisites"></a>필수 조건
이 아티클에서는 사용자가 Windows, macOS 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 머신으로 사용한다고 가정합니다. IoT Edge 장치는 다른 물리적 장치일 수 있습니다.

> [!NOTE]
> 이 디버깅 문서에서는 VS Code에서 Java 모듈을 디버그하는 두 가지 일반적인 방법을 보여 줍니다. 한 가지 방법은 모듈 컨테이너에서 프로세스를 연결하는 것이고, 다른 하나는 디버그 모드에서 모듈 코드를 시작하는 것입니다. Visual Studio Code의 디버깅 기능에 익숙하지 않은 경우, [디버깅](https://code.visualstudio.com/Docs/editor/debugging)에 대해 읽어 보시기 바랍니다.

이 문서에서는 Visual Studio Code를 주 개발 도구로 사용하므로 VS Code를 설치하고 필요한 확장을 추가합니다.
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Visual Studio Code용 Java 확장 팩](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
* [Docker 확장](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

모듈을 만들려면 모듈 코드를 실행하기 위한 Java 및 Maven, 모듈 이미지를 빌드하기 위한 Docker, 모듈 이미지를 저장하기 위한 컨테이너 레지스트리가 필요합니다.
* [Java SE Development Kit 10](http://www.oracle.com/technetwork/java/javase/downloads/index.html)(JDK 설치를 가리키도록 [`JAVA_HOME` 환경 변수](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) 설정)
* [Maven](https://maven.apache.org/)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다. 

IoT Edge 솔루션을 디버그, 실행 및 테스트하기 위한 로컬 개발 환경을 설치하려면 [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)이 필요하며, [Python(2.7/3.6) 및 Pip](https://www.python.org/)를 설치합니다. 그런 후 터미널에서 아래 명령을 실행하여 **iotedgehubdev**를 설치합니다.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

장치에서 모듈을 테스트하려면 하나 이상의 IoT Edge 장치 ID가 생성된 활성 IoT 허브가 필요합니다. 개발 머신에서 IoT Edge 디먼을 실행하는 경우 다음 단계로 이동하기 전에 EdgeHub 및 EdgeAgent를 중지해야 합니다. 


## <a name="create-a-new-solution-template"></a>새 솔루션 템플릿 만들기

다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 Java를 기반으로 IoT Edge 모듈을 만드는 방법을 보여 줍니다. 먼저 솔루션을 만들고 해당 솔루션에 첫 번째 모듈을 생성합니다. 각 솔루션은 여러 모듈을 포함할 수 있습니다. 

1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택합니다.

3. Visual Studio Code에서 **보기** > **명령 팔레트**를 선택합니다. 
4. 명령 팔레트에서 **Azure IoT Edge: New IoT Edge Solution** 명령을 입력하고 실행합니다.

   ![New IoT Edge Solution 실행](./media/how-to-develop-csharp-module/new-solution.png)

5. 새 솔루션을 만들려는 폴더로 이동한 다음, **폴더 선택**을 클릭합니다. 
6. 솔루션의 이름을 입력합니다. 
7. 솔루션의 첫 번째 모듈에 대한 템플릿으로 **Java 모듈**을 선택합니다.
8. 모듈의 이름을 입력합니다. 컨테이너 레지스트리 내에서 고유한 이름을 선택합니다. 
8. groupId 값을 제공하거나 기본값인 **com.edgemodule**을 그대로 적용합니다.
9. 모듈의 이미지 리포지토리를 입력합니다. VS Code에서 모듈 이름을 자동으로 채우기 때문에 **localhost:5000**을 고유한 레지스트리 정보로 바꾸기만 하면 됩니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 localhost를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는 **\<레지스트리 이름\>.azurecr.io**와 같이 표시됩니다. 문자열에서 localhost 부분만 바꾸고, 모듈 이름을 삭제하지 마세요.

   ![Docker 이미지 리포지토리 제공](./media/how-to-develop-node-module/repository.png)

VS Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만들고 새 창에서 로드합니다.

솔루션에는 다음 세 개의 항목이 있습니다. 
* **.vscode** 폴더에는 디버그 구성이 들어 있습니다.
* **modules** 폴더에는 각 모듈의 하위 폴더가 들어 있습니다. 지금은 모듈이 하나뿐이지만, **Azure IoT Edge: Add IoT Edge Module** 명령을 사용하여 명령 팔레트에서 모듈을 더 추가할 수 있습니다. 
* **.env** 파일은 환경 변수를 나열합니다. Azure Container Registry가 레지스트리인 경우 거기에 Azure Container Registry 사용자 이름 및 암호가 있습니다.

   >[!NOTE]
   >환경 파일은 모듈에 대한 이미지 리포지토리를 제공하는 경우에만 생성됩니다. localhost 기본값을 로컬로 테스트하고 디버그하도록 수락하는 경우 환경 변수를 선언할 필요가 없습니다. 

* **deployment.template.json** 파일은 테스트에 사용할 수 있는 데이터를 시뮬레이트하는 샘플 **tempSensor** 모듈과 함께 새 모듈을 나열합니다. 배포 매니페스트의 작동 방식에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

## <a name="develop-your-module"></a>모듈 개발

솔루션과 함께 제공되는 기본 Java 코드는 **modules > [모듈 이름] > src > main > java > com > edgemodulemodules > App.java**에 있습니다. 모듈 및 deployment.template.json 파일은 솔루션을 빌드하고, 컨테이너 레지스트리에 푸시하고, 장치에 배포하여 코드를 변경하지 않고 테스트를 시작하도록 설정됩니다. 모듈은 단순히 원본에서 입력을 가져오고(이 경우에 데이터를 시뮬레이션하는 tempSensor 모듈) IoT Hub로 파이핑하도록 빌드됩니다. 

고유한 코드를 사용하여 Java 템플릿을 사용자 지정할 준비가 된 경우 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하여 보안, 장치 관리 및 안정성 등 IoT 솔루션에 대한 주요 필요 사항을 해결하는 모듈을 빌드합니다. 

Visual Studio Code는 Java를 지원합니다. [VS Code에서 Java로 작업하는 방법](https://code.visualstudio.com/docs/java/java-tutorial)에 대해 자세히 알아보세요.

## <a name="launch-and-debug-module-code-without-container"></a>컨테이너 없이 모듈 코드 시작 및 디버그
IoT Edge Java 모듈은 Azure IoT Java 장치 SDK에 따라 달라집니다. 기본 모듈 코드에서 환경 설정 및 입력 이름을 사용하여 **ModuleClient**를 초기화합니다. 즉, IoT Edge Java 모듈에는 시작 및 실행을 위한 환경 설정이 필요하므로 입력 채널로 메시지를 전송하거나 라우팅해야 합니다. 기본 Java 모듈은 하나의 입력 채널만 포함하며 이름은 **input1**입니다.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>단일 모듈 앱에 대한 IoT Edge 시뮬레이터 설치

1. 이 시뮬레이터를 설치하고 시작하려면 VS Code 명령 팔레트에서 **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module**을 입력하고 선택합니다. 단일 모듈 응용 프로그램에 대한 입력 이름도 지정해야 합니다. **input1**을 입력하고 Enter 키를 누릅니다. 이 명령은 **iotedgehubdev** CLI를 트리거하고 IoT Edge 시뮬레이터를 시작한 후 유틸리티 모듈 컨테이너를 테스트합니다. 시뮬레이터가 단일 모듈 모드에서 성공적으로 시작된 경우 통합 터미널에서 다음과 같은 출력을 볼 수 있습니다. 또한 메시지를 전송하는 데 도움이 되는 `curl` 명령도 볼 수 있습니다. 나중에 필요합니다.

   ![단일 모듈 앱에 대한 IoT Edge 시뮬레이터 설치](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Docker 탐색기로 이동하여 모듈 실행 상태를 확인할 수 있습니다.

   ![시뮬레이터 모듈 상태](media/how-to-develop-csharp-module/simulator-status.png)

   **edgeHubDev** 컨테이너는 로컬 IoT Edge 시뮬레이터의 핵심입니다. 이 컨테이너는 IoT Edge 보안 디먼 없이 개발 머신에서 실행되며, 네이티브 모듈 앱 또는 모듈 컨테이너에 대한 환경 설정을 제공할 수 있습니다. **input** 컨테이너는 메시지를 모듈의 대상 입력 채널에 브리지하는 데 도움을 주기 위해 restAPI를 노출했습니다.

2. VS Code 명령 팔레트에서 **Azure IoT Edge: Set Module Credentials to User Settings**를 입력한 후 선택하여 사용자 설정에서 모듈 환경 설정을 `azure-iot-edge.EdgeHubConnectionString` 및 `azure-iot-edge.EdgeModuleCACertificateFile`로 지정합니다. 이러한 환경 설정이 **.vscode** > **launch.json** 및 [VS Code 사용자 설정](https://code.visualstudio.com/docs/getstarted/settings)에서 참조되는 것을 확인할 수 있습니다.

### <a name="debug-java-module-in-launch-mode"></a>시작 모드에서 Java 모듈 디버그

2. `App.java`로 이동합니다. 이 파일에 중단점을 추가합니다.

3. VS Code 디버그 보기로 이동합니다. 디버그 구성 **ModuleName Local Debug(java)** 를 선택합니다. 

4. **디버깅 시작**을 클릭하거나 **F5** 키를 누릅니다. 디버그 세션을 시작합니다.

5. VS Code 통합 터미널에서 다음 명령을 실행하여 모듈에 **Hello World** 메시지를 보냅니다. 이 명령은 이전 단계에서 IoT Edge 시뮬레이터를 성공적으로 설치했을 때 표시되는 명령입니다. 현재 통합 터미널이 차단된 경우 다른 통합 터미널을 만들거나 전환해야 할 수 있습니다.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows를 사용하는 경우 VS Code 통합 터미널의 셸은 **Git Bash** 또는 **WSL Bash**입니다. `curl` 명령은 PowerShell 또는 명령 프롬프트에서 실행할 수 없습니다. 
   
   > [!TIP]
   > `curl` 대신 [PostMan](https://www.getpostman.com/) 또는 다른 API 도구를 사용하여 메시지를 보낼 수도 있습니다.

6. VS Code 디버그 보기의 왼쪽 패널에서 변수가 표시됩니다. 

7. 디버깅 세션을 중지하려면 중지 단추를 클릭하거나 **Shift+f5**를 누릅니다. VS Code 명령 팔레트에서 **Azure IoT Edge: Stop IoT Edge Simulator**를 입력하고 선택하여 시뮬레이터를 중지하고 정리합니다.


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>디버깅을 위한 모듈 컨테이너 빌드 및 연결 모드에서 디버그

기본 솔루션에는 2개의 모듈, 즉 시뮬레이션된 온도 센서 모듈과 Java 파이프 모듈이 포함됩니다. 시뮬레이션된 온도 센서는 Java 파이프 모듈로 메시지를 계속 전송합니다.그러면 해당 메시지가 IoT Hub에 파이프됩니다. 만든 모듈 폴더에는 서로 다른 컨테이너 유형에 사용되는 여러 Docker 파일이 있습니다. **.debug** 확장명으로 끝나는 이러한 파일을 사용하여 테스트할 모듈을 빌드합니다. 현재, Java 모듈은 linux-amd64 및 linux-arm32v7 컨테이너에서만 디버깅을 지원합니다.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge 솔루션에 대한 IoT Edge 시뮬레이터 설치

개발 머신에서 IoT Edge 보안 디먼을 설치하는 대신 IoT Edge 시뮬레이터를 시작하여 IoT Edge 솔루션을 실행할 수 있습니다. 

1. 왼쪽의 장치 탐색기에서 마우스 오른쪽 단추로 IoT Edge 장치 ID를 클릭하고 **Setup IoT Edge Simulator**(IoT Edge 시뮬레이터 설치)를 선택하여 장치 연결 문자열을 사용하여 시뮬레이터를 시작합니다.

2. 통합 터미널에서 IoT Edge 시뮬레이터가 성공적으로 설치된 것을 확인할 수 있습니다.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>디버깅을 위한 컨테이너 빌드/실행 및 연결 모드에서 디버그

1. VS Code에서 `deployment.template.json` 파일로 이동합니다. 끝에 **.debug**를 추가하여 모듈 이미지 URL을 업데이트합니다.

2. **deployment.template.json**에서 Java 모듈 createOptions를 아래 콘텐츠로 바꾸고 이 파일을 저장합니다. 
    ```json
    "createOptions":"{\"HostConfig\":{\"PortBindings\":{\"5005/tcp\":[{\"HostPort\":\"5005\"}]}}}"
    ```

5. VS Code 디버그 보기로 이동합니다. 모듈에 대한 디버그 구성 파일을 선택합니다. 디버그 옵션 이름은 **ModuleName 원격 디버그(Java)** 와 비슷해야 합니다.

6. **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다. 연결할 프로세스를 선택합니다.

7. VS Code 디버그 보기의 왼쪽 패널에서 변수가 표시됩니다.

8. 디버깅 세션을 중지하려면 중지 단추를 클릭하거나 **Shift+f5**를 누릅니다. VS Code 명령 팔레트에서 **Azure IoT Edge: Stop IoT Edge Simulator**를 입력하고 선택합니다.

> [!NOTE]
> 앞의 예제에서는 컨테이너에서 Java IoT Edge 모듈을 디버그하는 방법을 보여 줍니다. 모듈 컨테이너 createOptions에 노출된 포트를 추가했습니다. Java 모듈의 디버그가 끝나면 프로덕션 준비가 완료된 IoT Edge 모듈을 위해 이러한 노출된 포트를 제거하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

모듈이 빌드되면 [Visual Studio Code에서 Azure IoT Edge 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.

IoT Edge 장치의 모듈을 개발하려면 [Azure IoT Hub SDK를 이해하고 사용](../iot-hub/iot-hub-devguide-sdks.md)합니다.
