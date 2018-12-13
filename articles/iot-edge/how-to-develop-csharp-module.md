---
title: Azure IoT Edge용 C# 모듈 디버그 | Microsoft Docs
description: Visual Studio Code를 사용하여 Azure IoT Edge용 C# 모듈 개발, 빌드 및 디버그
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 275ee95261b168b0da7f0a4638679fe38fc0581b
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443895"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure IoT Edge용 C# 모듈 개발 및 디버그

비즈니스 논리를 Azure IoT Edge용 모듈로 전환할 수 있습니다. 이 아티클에서는 VS Code(Visual Studio Code)를 기본 도구로 사용하여 C# 모듈을 개발하고 디버그하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

Windows, macOS 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 머신으로 사용할 수 있습니다. IoT Edge 장치는 다른 물리적 장치일 수 있습니다.

VS Code에서 C# 모듈을 디버그하는 방법은 두 가지가 있습니다. 한 방법은 모듈 컨테이너에서 프로세스를 연결하는 것이고, 다른 방법은 디버그 모드에서 모듈 코드를 시작하는 것입니다. Visual Studio Code의 디버깅 기능에 익숙하지 않은 경우, [디버깅](https://code.visualstudio.com/Docs/editor/debugging)에 대해 읽어 보시기 바랍니다.

먼저 Visual Studio Code를 설치한 다음, 다음과 같은 필요한 확장을 추가하세요.
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker 확장](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

모듈을 만들려면 프로젝트 폴더를 빌드하기 위한 .NET, 모듈 이미지를 빌드하기 위한 Docker, 모듈 이미지를 저장하기 위한 컨테이너 레지스트리가 필요합니다.
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* 개발 머신의 [Docker Community Edition](https://docs.docker.com/install/) 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다. 

IoT Edge 솔루션을 디버그, 실행 및 테스트하기 위한 로컬 개발 환경을 설치하려면 [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)이 필요하며, [Python(2.7/3.6) 및 Pip](https://www.python.org/)를 설치합니다. 그런 후 터미널에서 아래 명령을 실행하여 **iotedgehubdev**를 설치합니다.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

장치에서 모듈을 테스트하려면 하나 이상의 IoT Edge 장치 ID가 생성된 활성 IoT 허브가 필요합니다. 개발 머신에서 IoT Edge 디먼을 실행하는 경우 다음 단계로 이동하기 전에 EdgeHub 및 EdgeAgent를 중지해야 합니다. 

## <a name="create-a-new-solution-with-c-module"></a>C# 모듈을 사용하여 새 솔루션 만들기

다음 단계를 따라 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 .NET Core 2.1을 기반으로 IoT Edge 모듈을 만듭니다. 먼저 솔루션을 만든 다음, 해당 솔루션에서 첫 번째 모듈을 생성합니다. 각 솔루션에는 둘 이상의 모듈이 포함될 수 있습니다. 

1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택합니다.
2. **보기** > **명령 팔레트**를 선택합니다. 
3. 명령 팔레트에서 **Azure IoT Edge: 새로운 IoT Edge 솔루션** 명령을 입력하고 실행합니다.

   ![New IoT Edge Solution 실행](./media/how-to-develop-csharp-module/new-solution.png)

4. 새로운 솔루션을 만들 폴더로 이동합니다. **폴더 선택**을 선택합니다. 
5. 솔루션에 대한 이름을 입력합니다. 
6. 솔루션의 첫 번째 모듈에 대한 템플릿으로 **C# 모듈**을 선택합니다.
7. 모듈의 이름을 입력합니다. 컨테이너 레지스트리 내에서 고유한 이름을 선택합니다. 
8. 모듈의 이미지 리포지토리 이름을 입력합니다. VS Code는 **localhost:5000**으로 모듈 이름을 자동으로 채웁니다. 고유한 레지스트리 정보로 바꿉니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 **localhost**를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는 **\<레지스트리 이름\>.azurecr.io**와 같이 표시됩니다. 문자열에서 localhost 부분만 바꾸고, 모듈 이름을 삭제하지 마세요. 마지막 문자열은 \<레지스트리 이름\>.azurecr.io/\<모듈 이름\>과 같이 표시됩니다.

   ![Docker 이미지 리포지토리 제공](./media/how-to-develop-csharp-module/repository.png)

VS Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만든 다음, 새 창에서 로드합니다.

   ![IoT Edge 솔루션 보기](./media/how-to-develop-csharp-module/view-solution.png)

솔루션 내에 4개의 항목이 있습니다. 

* **.vscode** 폴더에는 디버그 구성이 들어 있습니다.
* **modules** 폴더에는 각 모듈의 하위 폴더가 있습니다. 이 시점에는 하나만 있습니다. 하지만 **Azure IoT Edge: Add IoT Edge Module** 명령을 사용하여 명령 팔레트에서 모듈을 더 추가할 수 있습니다. 
* **.env** 파일은 환경 변수를 나열합니다. Azure Container Registry가 레지스트리인 경우 거기에 Azure Container Registry 사용자 이름 및 암호가 있습니다. 

   >[!NOTE]
   >환경 파일은 모듈에 대한 이미지 리포지토리를 제공하는 경우에만 생성됩니다. localhost 기본값을 로컬로 테스트하고 디버그하도록 수락하는 경우 환경 변수를 선언할 필요가 없습니다. 

* **deployment.template.json** 파일은 테스트에 사용할 수 있는 데이터를 시뮬레이션하는 샘플 **tempSensor** 모듈과 함께 새 모듈을 나열합니다. 배포 매니페스트 작동 방식에 대한 자세한 내용은 [배포 매니페스트를 사용하여 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요. 
* **deployment.debug.template.json** 파일에는 적절한 컨테이너 옵션이 있는 디버그 버전의 모듈 이미지가 포함됩니다.


## <a name="develop-your-module"></a>모듈 개발

솔루션과 함께 제공되는 기본 C# 모듈 코드는 **modules** > ** [모듈 이름] ** > **Program.cs**에 있습니다. 모듈 및 deployment.template.json 파일은 솔루션을 빌드하고, 컨테이너 레지스트리에 푸시하고, 장치에 배포하여 코드를 변경하지 않고 테스트를 시작하도록 설정됩니다. 모듈은 단순히 원본에서 입력을 가져오고(이 경우에 데이터를 시뮬레이션하는 tempSensor 모듈) IoT Hub로 파이핑하도록 빌드됩니다. 

고유한 코드를 사용하여 C# 템플릿을 사용자 지정할 준비가 된 경우 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하여 보안, 장치 관리 및 안정성 등 IoT 솔루션에 대한 주요 요구 사항을 해결하는 모듈을 빌드합니다. 

VS Code의 C# 지원은 플랫폼 간 .NET Core 개발을 위해 최적화되어 있습니다. [VS Code에서 C#을 사용하여 작업하는 방법](https://code.visualstudio.com/docs/languages/csharp)에 대해 자세히 알아보세요.

## <a name="launch-and-debug-module-code-without-container"></a>컨테이너 없이 모듈 코드 시작 및 디버그

IoT Edge C# 모듈은 .NET Core 응용 프로그램입니다. 또한 Azure IoT C# Device SDK에 의존합니다. IoT C# 모듈에는 시작 및 실행을 위한 환경 설정이 필요하므로 기본 모듈 코드에서 환경 설정 및 입력 이름을 사용하여 **ModuleClient**를 초기화합니다. 입력 채널로 메시지를 전송하거나 라우링해야 합니다. 기본 C# 모듈은 하나의 입력 채널만 포함하며 이름은 **input1**입니다.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge 솔루션에 대한 IoT Edge 시뮬레이터 설치

개발 머신에서 IoT Edge 보안 디먼을 설치하는 대신 IoT Edge 시뮬레이터를 시작하여 IoT Edge 솔루션을 실행할 수 있습니다. 

1. 왼쪽의 장치 탐색기에서 마우스 오른쪽 단추로 IoT Edge 장치 ID를 클릭하고 **Setup IoT Edge Simulator**(IoT Edge 시뮬레이터 설치)를 선택하여 장치 연결 문자열을 사용하여 시뮬레이터를 시작합니다.

2. 통합 터미널에서 IoT Edge 시뮬레이터가 성공적으로 설치된 것을 확인할 수 있습니다.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>단일 모듈 앱에 대한 IoT Edge 시뮬레이터 설치
개발 머신에서 IoT Edge 보안 디먼을 설치하는 대신 IoT Edge 시뮬레이터를 시작하여 IoT Edge 솔루션을 실행할 수 있습니다. 

1. 왼쪽의 장치 탐색기에서 마우스 오른쪽 단추로 IoT Edge 장치 ID를 클릭하고 **Setup IoT Edge Simulator**(IoT Edge 시뮬레이터 설치)를 선택하여 장치 연결 문자열을 사용하여 시뮬레이터를 시작합니다.

2. 통합 터미널에서 IoT Edge 시뮬레이터가 성공적으로 설치된 것을 확인할 수 있습니다.

3. VS Code 명령 팔레트에서 **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module**을 입력하고 선택합니다. 단일 모듈 응용 프로그램에 대한 입력 이름도 지정해야 합니다. **input1**을 입력하고 Enter 키를 누릅니다. 이 명령은 **iotedgehubdev** CLI를 트리거하고 IoT Edge 시뮬레이터를 시작한 후 유틸리티 모듈 컨테이너를 테스트합니다. 시뮬레이터가 단일 모듈 모드에서 성공적으로 시작된 경우 통합 터미널에서 다음과 같은 출력을 볼 수 있습니다. 또한 메시지를 전송하는 데 도움이 되는 `curl` 명령도 볼 수 있습니다. 나중에 필요합니다.

   ![단일 모듈 앱에 대한 IoT Edge 시뮬레이터 설치](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Docker 탐색기로 이동하여 모듈 실행 상태를 확인할 수 있습니다.

   ![시뮬레이터 모듈 상태](media/how-to-develop-csharp-module/simulator-status.png)

   **edgeHubDev** 컨테이너는 로컬 IoT Edge 시뮬레이터의 핵심입니다. 이 컨테이너는 IoT Edge 보안 디먼 없이 개발 머신에서 실행되며, 네이티브 모듈 앱 또는 모듈 컨테이너에 대한 환경 설정을 제공할 수 있습니다. **input** 컨테이너는 메시지를 모듈의 대상 입력 채널에 브리지하는 데 도움을 주기 위해 restAPI를 노출했습니다.

4. VS Code 명령 팔레트에서 **Azure IoT Edge: Set Module Credentials to User Settings**를 입력한 후 선택하여 사용자 설정에서 모듈 환경 설정을 `azure-iot-edge.EdgeHubConnectionString` 및 `azure-iot-edge.EdgeModuleCACertificateFile`로 지정합니다. 이러한 환경 설정이 **.vscode** > **launch.json** 및 [VS Code 사용자 설정](https://code.visualstudio.com/docs/getstarted/settings)에서 참조되는 것을 확인할 수 있습니다.

### <a name="build-module-app-and-debug-in-launch-mode"></a>모듈 앱 빌드 및 실행 모드에서 디버그

1. 통합 터미널에서 디렉터리를 **CSharpModule** 폴더로 변경하고 다음 명령을 실행하여 .NET Core 응용 프로그램을 빌드합니다.

    ```cmd
    dotnet build
    ```

2. `program.cs`로 이동합니다. 이 파일에 중단점을 추가합니다.

3. VS Code 디버그 보기(View > Debug)로 이동합니다. 드롭다운에서 디버그 구성 **ModuleName 로컬 디버그(.NET Core)** 를 선택합니다. 

  ![VS Code의 디버그 모드로 전환](media/how-to-develop-csharp-module/debug-view.png)

4. **디버깅 시작**을 클릭하거나 **F5** 키를 누릅니다. 디버그 세션을 시작합니다.

   > [!NOTE]
   > .NET Core `TargetFramework`가 `launch.json`의 프로그램 경로와 일치하지 않는 경우. .csproj 파일의 `TargetFramework`를 준수하려면 `launch.json`의 프로그램 경로를 수동으로 업데이트해야 합니다. 이제 VS Code는 이 프로그램을 성공적으로 시작할 수 있습니다.

5. VS Code 통합 터미널에서 다음 명령을 실행하여 모듈에 **Hello World** 메시지를 보냅니다. 이 명령은 이전 단계에서 IoT Edge 시뮬레이터를 성공적으로 설치했을 때 표시되는 명령입니다.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows를 사용하는 경우 VS Code 통합 터미널의 셸은 **Git Bash** 또는 **WSL Bash**입니다. `curl` 명령은 PowerShell 또는 명령 프롬프트에서 실행할 수 없습니다. 
   
   > [!TIP]
   > `curl` 대신 [PostMan](https://www.getpostman.com/) 또는 다른 API 도구를 사용하여 메시지를 보낼 수도 있습니다.

6. VS Code 디버그 보기의 왼쪽 패널에서 변수가 표시됩니다. 

    ![변수 보기](media/how-to-develop-csharp-module/single-module-variables.png)

7. 디버깅 세션을 중지하려면 중지 단추를 클릭하거나 **Shift+f5**를 누릅니다. VS Code 명령 팔레트에서 **Azure IoT Edge: Stop IoT Edge Simulator**를 입력하고 선택하여 시뮬레이터를 중지하고 정리합니다.

## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>디버깅을 위한 모듈 컨테이너 빌드 및 연결 모드에서 디버그

기본 솔루션에는 2개의 모듈, 즉 시뮬레이트된 온도 센서 모듈과 C# 파이프 모듈이 포함되어 있습니다. 시뮬레이션된 온도 센서는 C# 파이프 모듈로 메시지를 계속 전송하고, 해당 메시지는 IoT Hub에 파이프됩니다. 만든 모듈 폴더에는 서로 다른 컨테이너 유형에 사용되는 여러 Docker 파일이 있습니다. **.debug** 확장명으로 끝나는 이러한 파일을 사용하여 테스트할 모듈을 빌드합니다. 기본적으로 **deployment.debug.template.json**에는 디버그 버전의 이미지가 포함됩니다. 현재 C# 모듈은 연결 모드의 Linux amd64 컨테이너에서만 디버깅을 지원합니다. VS Code 상태 표시줄에서 Azure IoT Edge 기본 플랫폼을 전환할 수 있습니다.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge 솔루션에 대한 IoT Edge 시뮬레이터 설치

개발 머신에서 IoT Edge 보안 디먼을 설치하는 대신 IoT Edge 시뮬레이터를 시작하여 IoT Edge 솔루션을 실행할 수 있습니다. 

1. 왼쪽의 장치 탐색기에서 마우스 오른쪽 단추로 IoT Edge 장치 ID를 클릭하고 **Setup IoT Edge Simulator**(IoT Edge 시뮬레이터 설치)를 선택하여 장치 연결 문자열을 사용하여 시뮬레이터를 시작합니다.

2. 통합 터미널에서 IoT Edge 시뮬레이터가 성공적으로 설치된 것을 확인할 수 있습니다.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>디버깅을 위한 컨테이너 빌드/실행 및 연결 모드에서 디버그

1. `program.cs`로 이동합니다. 이 파일에 중단점을 추가합니다.

2. VS Code 파일 탐색기에서 솔루션에 대한 `deployment.debug.template.json` 파일을 선택하고 상황에 맞는 메뉴에서 **Build and Run IoT Edge solution in Simulator**(시뮬레이터에서 IoT Edge 솔루션 빌드 및 실행)를 클릭합니다. 같은 창에서 모든 모듈 컨테이너 로그를 볼 수 있습니다. Docker 탐색기로 이동하여 컨테이너 상태를 확인할 수도 있습니다.

   ![변수 보기](media/how-to-develop-csharp-module/view-log.png)

3. VS Code 디버그 보기로 이동합니다. 모듈에 대한 디버그 구성 파일을 선택합니다. 디버그 옵션 이름은 **ModuleName 원격 디버그(.NET Core)** 와 비슷합니다.

   ![구성 선택](media/how-to-develop-csharp-module/debug-config.png)

4. **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다. 연결할 프로세스를 선택합니다.

5. VS Code 디버그 보기의 왼쪽 패널에서 변수가 표시됩니다.

6. 디버깅 세션을 중지하려면 중지 단추를 클릭하거나 **Shift+f5**를 누릅니다. VS Code 명령 팔레트에서 **Azure IoT Edge: Stop IoT Edge Simulator**를 입력하고 선택합니다.

    > [!NOTE]
    > 이 예제에서는 컨테이너에서 .NET Core IoT Edge 모듈을 디버그하는 방법을 보여 줍니다. 이 방법은 빌드하는 동안 컨테이너 이미지에 VSDBG(Visual Studio .NET Core 명령줄 디버거)를 포함하는 `Dockerfile.debug`의 디버그 버전에 기반합니다. C# 모듈을 디버그한 후에 프로덕션에서 사용할 준비가 된 IoT Edge 모듈에 VSDBG를 사용하지 않고 Dockerfile을 직접 사용하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

모듈을 빌드한 후에 [Visual Studio Code에서 Azure IoT Edge 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.

IoT Edge 장치의 모듈을 개발하려면 [Azure IoT Hub SDK를 이해하고 사용](../iot-hub/iot-hub-devguide-sdks.md)합니다.
