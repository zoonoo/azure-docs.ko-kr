---
title: 비주얼 스튜디오 - Azure IoT Edge에서 모듈 개발 및 디버깅
description: Azure IoT 도구와 함께 Visual Studio를 사용하여 C 또는 C# IoT Edge 모듈을 개발하고 배포 매니페스트에 의해 구성된 IoT Hub에서 IoT 장치로 푸시합니다.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384860"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio 2019를 사용하여 Azure IoT Edge용 모듈 개발 및 디버깅

비즈니스 논리를 Azure IoT Edge용 모듈로 전환할 수 있습니다. 이 문서에서는 Visual Studio 2019를 모듈을 개발하고 디버깅하는 주요 도구로 사용하는 방법을 보여 주며 설명합니다.

Visual Studio 용 Azure IoT Edge 도구는 다음과 같은 이점을 제공합니다.

- 로컬 개발 컴퓨터에서 Azure IoT Edge 솔루션 및 모듈을 생성, 편집, 빌드, 실행 및 디버그합니다.
- Azure IoT Hub를 통해 Azure IoT Edge 디바이스에 Azure IoT Edge 솔루션을 배포합니다.
- Visual Studio 개발의 모든 이점을 누리면서 Azure IoT 모듈을 C 또는 C#로 코딩합니다.
- Azure IoT Edge 디바이스 및 모듈을 UI로 관리합니다.

이 문서에서는 Visual Studio 2019용 Azure IoT 에지 도구를 사용하여 IoT Edge 모듈을 개발하는 방법을 보여 줍니다. Azure IoT Edge 디바이스에 프로젝트를 배포하는 방법도 알아봅니다. 현재 Visual Studio 2019는 C 및 C#으로 작성된 모듈에 대한 지원을 제공합니다. 지원되는 장치 아키텍처는 Windows X64 및 Linux X64 또는 ARM32입니다. 지원되는 운영 체제, 언어 및 아키텍처에 대한 자세한 내용은 [언어 및 아키텍처 지원을](module-development.md#language-and-architecture-support)참조하십시오.
  
## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Windows를 실행하는 컴퓨터 또는 가상 머신을 개발 머신으로 사용한다고 가정합니다. Windows 컴퓨터에서는 Windows 또는 Linux 모듈을 개발할 수 있습니다. Windows 모듈을 개발하려면 버전 1809/빌드 17763 또는 최신 버전을 실행하는 Windows 컴퓨터를 사용합니다. Linux 모듈을 개발하려면 [Docker 데스크톱에 대한 요구 사항을](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)충족하는 Windows 컴퓨터를 사용합니다.

이 문서에서는 Visual Studio 2019를 주요 개발 도구로 사용하기 때문에 Visual Studio를 설치합니다. Visual Studio 2019 설치에 **C++** 워크로드가 포함된 **Azure 개발** 및 데스크톱 개발을 포함해야 합니다. Visual [Studio 2019를 수정하여](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) 필요한 워크로드를 추가할 수 있습니다.

Visual Studio 2019가 준비되면 다음 도구와 구성 요소도 필요합니다.

- Visual Studio 마켓플레이스에서 [Azure IoT Edge 도구를](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 다운로드하여 설치하여 Visual Studio 2019에서 IoT Edge 프로젝트를 만듭니다.

> [!TIP]
> Visual Studio 2017을 사용하는 경우 Visual Studio 마켓플레이스에서 VS 2017용 [Azure IoT 에지 도구를](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) 다운로드하여 설치하십시오.

- [Docker Community Edition](https://docs.docker.com/install/)을 개발 머신에 다운로드 및 설치하여 모듈 이미지를 빌드하고 실행합니다. Linux 컨테이너 모드 또는 Windows 컨테이너 모드에서 실행할 Docker CE를 설정해야 합니다.

- [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)을 설치하여 IoT Edge 솔루션을 디버그, 실행 및 테스트할 로컬 개발 환경을 설정합니다. [파이썬 (2.7 / 3.6+)과 핍을](https://www.python.org/) 설치한 다음 터미널에서 다음 명령을 실행하여 **iotedgehubdev** 패키지를 설치하십시오. Azure IoT EdgeHub Dev Tool 버전이 0.3.0보다 높아야 합니다.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- 리포지토리를 복제하고 Vcpkg 라이브러리 관리자를 설치한 다음 Windows용 **azure-iot-sdk-c 패키지를** 설치합니다.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다.

- 디바이스에서 모듈을 테스트하려면 하나 이상의 IoT Edge 디바이스가 있는 활성 IoT 허브가 필요합니다. 컴퓨터를 IoT Edge 디바이스로 사용하려면 [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)의 빠른 시작에서 단계를 수행합니다. 개발 머신에서 IoT Edge 디먼을 실행할 경우, Visual Studio에서 개발을 시작하기 전 EdgeHub 및 EdgeAgent를 중지해야 할 수도 있습니다.

### <a name="check-your-tools-version"></a>도구 버전 확인

1. **확장** 메뉴에서 확장 **관리를 선택합니다.** **설치 > 도구를** 확장하면 Visual **Studio용 Azure IoT 에지 도구** 및 Visual **Studio용 클라우드 탐색기를**찾을 수 있습니다.

1. 설치된 버전을 적어둡니다. 이 버전을 Visual Studio Marketplace([클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))의 최신 버전과 비교할 수 있습니다.

1. Visual Studio Marketplace에서 제공되는 것보다 버전이 낮을 경우, 다음 섹션에 나오는 대로 Visual Studio에서 도구를 업데이트합니다.

### <a name="update-your-tools"></a>도구 업데이트

1. 확장 **관리** 창에서 **업데이트를 > Visual Studio 마켓플레이스로** **확장하고, Visual Studio에 대한** Azure **IoT 에지 도구** 또는 클라우드 탐색기를 선택하고 **업데이트를**선택합니다.

1. 도구 업데이트를 다운로드한 후에 Visual Studio를 닫고 VSIX 설치 관리자를 사용하여 도구 업데이트를 트리거합니다.

1. 설치 관리자에서 **확인**을 선택하여 시작한 다음, **수정**을 선택하여 도구를 업데이트합니다.

1. 업데이트가 완료되면 **닫기**를 선택하고 Visual Studio를 다시 시작합니다.

### <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge 프로젝트 만들기

Visual Studio의 Azure IoT Edge 프로젝트 템플릿은 Azure IoT Hub의 Azure IoT Edge 디바이스에 배포할 수 있는 프로젝트를 만듭니다. 먼저 Azure IoT Edge 솔루션을 만든 다음 해당 솔루션에서 첫 번째 모듈을 생성합니다. 각 IoT Edge 솔루션에는 둘 이상의 모듈이 포함될 수 있습니다.

> [!TIP]
> Visual Studio에서 만든 IoT Edge 프로젝트 구조는 Visual Studio Code의 구조와는 다릅니다.

1. Visual Studio 새 프로젝트 대화 상자에서 **Azure IoT Edge** 프로젝트를 검색하고 선택하고 다음 을 **클릭합니다.** 프로젝트 구성 창에서 프로젝트의 이름을 입력하고 위치를 지정한 다음 **만들기를**선택합니다. 기본 프로젝트 이름은 **AzureIoTEdgeApp1**입니다.

   ![새 프로젝트 만들기](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. **IoT 에지 응용 프로그램 및 모듈 추가** 창에서 **C# 모듈** 또는 C **모듈** 중 하나를 선택한 다음 모듈 이름 및 모듈 이미지 리포지토리를 지정합니다. Visual Studio는 **localhost:5000/<모듈 이름\>** 으로 모듈 이름을 자동으로 채웁니다. 고유한 레지스트리 정보로 바꿉니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 **localhost는** 괜찮습니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는 ** _ \<레지스트리 이름\>_.azurecr.io**. 최종 결과가 ** \<모듈_\<이름\>_.azurecr.io/ *레지스트리 이름처럼*\>** 보이게 하려면 **문자열의 localhost:5000** 부분만 바꿉니다. 기본 모듈 이름은 **IotEdgeModule1입니다.**

   ![응용 프로그램 및 모듈 추가](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. C# 또는 C를 사용하는 모듈을 사용하여 Azure IoT Edge 솔루션을 **만들려면 확인을** 선택합니다.

이제 **AzureIoTEdgeApp1.Linux.Amd64** 프로젝트 또는 **AzureIoTEdgeApp1.Windows.Amd64** 프로젝트, 그리고 솔루션에 **IotEdgeModule1** 프로젝트가 있습니다. 각 **AzureIoTEdgeApp1** 프로젝트에는 IoT Edge 솔루션에 대해 빌드및 배포할 모듈을 정의하고 모듈 간의 경로를 정의하는 `deployment.template.json` 파일이 있습니다. 기본 솔루션에는 **시뮬레이션온도 센서** 모듈과 **IotEdgeModule1** 모듈이 있습니다. **시뮬레이션된TemperatureSensor** 모듈은 **IotEdgeModule1** 모듈에 시뮬레이션된 데이터를 생성하는 반면, **IotEdgeModule1** 모듈의 기본 코드는 수신된 메시지를 Azure IoT Hub에 직접 파이프합니다.

시뮬레이션된 온도 센서의 작동 방식을 보려면 [시뮬레이션온도Sensor.csproj 소스 코드를](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)참조하십시오.

**IotEdgeModule1** 프로젝트는 C# 모듈인 경우 .NET 코어 2.1 콘솔 응용 프로그램입니다. 여기에는 IoT Edge 디바이스를 Windows 컨테이너나 Linux 컨테이너로 실행할 때 필요한 필수 Docker 파일이 포함되어 있습니다. 파일은 `module.json` 모듈의 메타데이터를 설명합니다. Azure IoT 장치 SDK를 종속성으로 하는 실제 모듈 코드가 `Program.cs` 또는 `main.c` 파일에서 찾을 수 있습니다.

## <a name="develop-your-module"></a>모듈 개발

솔루션과 함께 제공되는 기본 모듈 코드는 **IotEdgeModule1** > Program.cs(C#) 또는 **main.c(C)에** 있습니다.**Program.cs** 솔루션을 빌드하고 `deployment.template.json` 컨테이너 레지스트리로 푸시한 다음 장치에 배포하여 코드를 건드리지 않고 테스트를 시작할 수 있도록 모듈과 파일이 설정됩니다. 이 모듈은 소스(이 경우 데이터를 시뮬레이션하는 **SimulatedTemperatureSensor** 모듈)의 입력을 가져와 Azure IoT Hub에 파이프하도록 제작되었습니다.

고유한 코드로 모듈 템플릿을 사용자 지정할 준비가 되면 [Azure IoT Hub SDK를](../iot-hub/iot-hub-devguide-sdks.md) 사용하여 보안, 장치 관리 및 안정성과 같은 IoT 솔루션의 주요 요구 사항을 해결하는 모듈을 빌드합니다.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>IoT Edge 디바이스 연결 문자열로 iotegehubdev 초기화

1. Visual Studio 클라우드 탐색기의 **기본 연결 문자열**에서 모든 IoT Edge 디바이스의 연결 문자열을 복사합니다. IoT Edge 디바이스의 아이콘은 non-Edge 디바이스의 아이콘과 다르므로 non-Edge 디바이스의 연결 문자열을 복사해서는 안 됩니다.

   ![Edge 디바이스 연결 문자열 복사](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. **도구** 메뉴에서 **Azure IoT 에지 도구** > **설정 IoT 에지 시뮬레이터를**선택하고 연결 문자열을 붙여 넣은 다음 **확인을**클릭합니다.

   ![Edge 장치 연결 문자열 설정 창 열기](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 첫 번째 단계에서 연결 문자열을 입력하고 **확인**을 선택합니다.

> [!NOTE]
> 모든 후속 Azure IoT Edge 솔루션에 결과가 자동으로 적용되기 때문에 이 단계들은 개발 컴퓨터에서 한 번만 수행해야 합니다. 다른 연결 스트링으로 변경해야 할 경우 이 과정을 다시 밟으면 됩니다.

## <a name="build-and-debug-single-module"></a>단일 모듈 빌드 및 디버깅

여러 모듈이 포함된 전체 솔루션 내에서 실행하기 전데 모듈을 각각 테스트 및 디버그하는 것이 일반적입니다.

1. **솔루션 탐색기에서** **IotEdgeModule1을** 마우스 오른쪽 단추로 클릭하고 컨텍스트 메뉴에서 **시작 프로젝트로 설정을** 선택합니다.

   ![시작 프로젝트 설정](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. **F5** 키를 누르거나 아래 버튼을 클릭하여 모듈을 실행합니다. 처음에는 10&ndash;20초 정도 걸립니다.

   ![모듈 실행](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 모듈이 성공적으로 초기화된 경우 .NET Core 콘솔 앱이 시작된 것을 볼 수 있습니다.

   ![실행 중인 모듈](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. C#에서 개발하는 경우 **Program.cs**함수의 `PipeMessage()` 중단점을 설정합니다. C를 사용하는 경우 `InputQueue1Callback()` **main.c.c의**함수에 중단점을 설정합니다. 그런 다음 **Git Bash** 또는 **WSL Bash** 셸에서 다음 명령을 실행하여 메시지를 전송하여 테스트할 수 있습니다. (PowerShell 또는 명령 프롬프트에서는 `curl` 명령을 실행할 수 없습니다.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![단일 모듈 디버그](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    중단점이 트리거됩니다. Visual Studio **로컬** 창에서 변수를 확인할 수 있습니다.

   > [!TIP]
   > `curl` 대신 [PostMan](https://www.getpostman.com/) 또는 다른 API 도구를 사용하여 메시지를 보낼 수도 있습니다.

1. **Ctrl + F5**를 누르거나 중지 버튼을 클릭하여 디버깅을 중지합니다.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>여러 모듈로 IoT Edge 솔루션 빌드 및 디버그

단일 모듈 개발을 마친 후 여러 모듈이 있는 전체 솔루션을 실행하고 디버그합니다.

1. **솔루션 탐색기에서** **AzureIoTEdgeApp1을** 마우스 오른쪽 단추로 클릭하고**새 IoT 에지 모듈** **추가를** > 선택하여 솔루션에 두 번째 모듈을 추가합니다. 두 번째 모듈의 기본 이름은 **IotEdgeModule2이며** 다른 파이프 모듈로 작동합니다.

1. 파일을 `deployment.template.json` 열면 모듈 섹션에 **IotEdgeModule2가** 추가된 것을 볼 수 **있습니다.** **경로** 섹션을 다음으로 바꿉습니다. 모듈 이름을 사용자 지정한 경우에는 해당 이름을 업데이트하여 맞춰 주어야 합니다.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. **AzureIoTEdgeApp1**을 오른쪽 클릭하여 바로 가기 메뉴에서 **시작 프로젝트로 설정**을 선택합니다.

1. 중단점을 만든 다음 **F5**를 눌러 여러 개의 모듈을 동시에 실행 및 디버그합니다. 각 창이 다른 모듈을 나타내는 여러 개의 .NET Core 콘솔 앱 창이 표시됩니다.

   ![여러 모듈 디버그](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. **Ctrl + F5**를 누르거나 중지 버튼을 클릭하여 디버깅을 중지합니다.

## <a name="build-and-push-images"></a>이미지 빌드 및 푸시

1. **AzureIoTEdgeApp1**이 시작 프로젝트여야 합니다. 모듈 이미지를 빌드할 구성으로 **디버그**와 **릴리스** 중 하나를 선택합니다.

    > [!NOTE]
    > **디버그**를 선택하면, Visual Studio가 `Dockerfile.(amd64|windows-amd64).debug`을(를) 사용하여 Docker 이미지를 빌드합니다. 이미지를 빌드하는 동안 컨테이너 이미지의 .NET Core 명령 줄 디버거 VSDBG가 포함됩니다. 프로덕션이 준비된 IoT Edge 모듈의 경우, VSDBG 없이 `Dockerfile.(amd64|windows-amd64)`을(를) 사용하는 **릴리스** 구성을 권장합니다.

1. Azure 컨테이너 레지스트리(ACR)와 같은 개인 레지스트리를 사용하는 경우 다음 Docker 명령을 사용하여 로그인합니다.  Azure 포털에서 레지스트리의 액세스 **키** 페이지에서 사용자 이름과 암호를 얻을 수 있습니다. 로컬 레지스트리를 사용할 경우 [로컬 레지스트리를 실행](https://docs.docker.com/registry/deploying/#run-a-local-registry)할 수 있습니다.

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Azure Container Registry 같은 프라이빗 레지스트리를 사용할 경우, `deployment.template.json` 파일에 있는 런타임 설정에 레지스트리 로그인 정보를 추가해야 합니다. 자리 표시자를 실제 ACR 관리 사용자 이름, 암호 및 레지스트리 이름으로 바꿉니다.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. **솔루션 탐색기에서** **AzureIoTEdgeApp1을** 마우스 오른쪽 단추로 클릭하고 **빌드 및 푸시 IoT 에지 모듈을** 선택하여 각 모듈에 대한 Docker 이미지를 빌드하고 푸시합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

IoT Edge 디바이스를 설정할 때 사용한 빠른 시작 문서에서는 Azure Portal을 사용하여 모듈을 배포했습니다. Visual Studio 용 클라우드 탐색기를 사용하여 모듈을 배포할 수도 있습니다. 시나리오의 배포 매니페스트인 `deployment.json` 파일이 이미 있으므로 배포를 수신할 디바이스만 선택하면 됩니다.

1. **보기** > **클라우드 탐색기**를 클릭하여 **클라우드 탐색기**를 엽니다. Visual Studio 2019에 로그인했는지 확인합니다.

1. **클라우드 탐색기**에서 구독을 펼쳐서 배포하려는 Azure IoT Hub 및 Azure IoT Edge 디바이스를 찾습니다.

1. IoT Edge 장치를 마우스 오른쪽 단추로 클릭하여 배포를 만듭니다. 와 같은 `deployment.arm32v7.json`Visual Studio 솔루션의 **구성** 폴더에 있는 플랫폼에 대해 구성된 배포 매니페스트로 이동합니다.

1. 새로 고침 버튼을 클릭하여 **시뮬레이션된TemperatureSensor** 모듈과 함께 실행되는 새 모듈을 확인하고 **$edgeAgent** **$edgeHub.**

## <a name="view-generated-data"></a>생성된 데이터 보기

1. 특정 IoT-Edge 장치에 대한 D2C 메시지를 모니터링하려면 **클라우드 탐색기의** IoT 허브에서 메시지를 선택한 다음 **작업** 창에서 기본 제공 **이벤트 끝점 모니터링 시작을** 클릭합니다.

1. 데이터 모니터링을 중지하려면 **작업** 창에서 **기본 제공 이벤트 끝점 모니터링 중지를** 선택합니다.

## <a name="next-steps"></a>다음 단계

IoT Edge 디바이스의 사용자 지정 모듈을 개발하려면 [Azure IoT Hub SDK에 대해 숙지하고 사용](../iot-hub/iot-hub-devguide-sdks.md)하십시오.
