---
title: Visual Studio 2017에서 Azure IoT Edge용 C# 모듈 개발 및 디버그 | Microsoft Docs
description: Visual Studio 2017을 사용하여 Azure IoT Edge용 C# 모듈 개발 및 디버그
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ab4dd1186715fde51fbf188ace902c8092d192d0
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647190"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Visual Studio 2017을 사용하여 Azure IoT Edge용 C# 모듈 개발 및 디버그(미리 보기)

비즈니스 논리를 Azure IoT Edge용 모듈로 전환할 수 있습니다. 이 문서에서는 Visual Studio 2017을 기본 도구로 사용하여 C# 모듈을 개발하고 디버그하는 방법을 보여줍니다.

Visual Studio 용 Azure IoT Edge 도구는 다음과 같은 이점을 제공합니다.

- 로컬 개발 컴퓨터에서 Azure IoT Edge 솔루션 및 모듈을 생성, 편집, 빌드, 실행 및 디버그합니다.
- Azure IoT Hub를 통해 Azure IoT Edge 장치에 Azure IoT Edge 솔루션을 배포합니다.
- C#으로 Azure IoT 모듈을 코딩하면서 Visual Studio 개발 환경의 모든 이점을 활용할 수 있습니다.
- Azure IoT Edge 장치 및 모듈을 UI로 관리합니다. 

이 문서에서는 Visual Studio 2017용 Azure IoT Edge 도구를 사용하여 C#에서 IoT Edge 모듈을 개발하는 방법을 설명합니다. Azure IoT Edge 장치에 프로젝트를 배포하는 방법도 알아봅니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 Windows를 실행하는 컴퓨터 또는 가상 머신을 개발 머신으로 사용한다고 가정합니다. IoT Edge 장치는 다른 물리적 장치일 수 있습니다.

이 문서에서는 Visual Studio 2017을 기본 개발 도구로 사용하기 때문에 Visual Studio를 설치해야 합니다. Visual Studio 2017 설치에 **Azure 개발 워크로드**를 포함해야 합니다. [Visual Studio 2017을 수정](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017)하고 Azure 개발 워크로드를 추가할 수 있습니다.

Visual Studio 2017이 준비되면 다음을 수행해야 합니다.

- Visual Studio 2017에서 IoT Edge 프로젝트를 만들 수 있도록 Visual Studio Marketplace에서 [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)을 다운로드하여 설치합니다.
- 모듈 이미지를 만들고 실행하기 위해 [Docker Community Edition](https://docs.docker.com/install/)을 개발 머신에 설치합니다. Linux 컨테이너 모드 또는 Windows 컨테이너 모드에서 실행 중인 Docker CE를 올바르게 설정해야 합니다.
- IoT Edge 솔루션을 디버그, 실행 및 테스트하기 위한 로컬 개발 환경을 설정하려면 [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)이 필요합니다. [Python(2.7/3.6) 및 Pip](https://www.python.org/)를 설치합니다. 그런 후 터미널에서 아래 명령을 실행하여 **iotedgehubdev**를 설치합니다. Azure IoT EdgeHub Dev Tool 버전이 0.3.0보다 높아야 합니다.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   - 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다. 

- 모듈을 테스트하려면 하나 이상의 IoT Edge 장치 ID가 생성된 활성 IoT 허브가 필요합니다. 개발 머신에서 IoT Edge 보안 디먼을 실행하는 경우 Visual Studio에서 개발을 시작하기 전에 EdgeHub 및 EdgeAgent를 중지해야 할 수도 있습니다. 

### <a name="check-your-tools-version"></a>도구 버전 확인

1. **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다. **설치됨 > 도구**를 펼치면 **Azure IoT Edge**와 **클라우드 탐색기**가 보입니다.

2. 설치된 버전을 적어둡니다. 이 버전을 Visual Studio Marketplace([클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools))의 최신 버전과 비교할 수 있습니다.

3. 최신 버전이 아닌 경우 다음 섹션에 표시된 대로 Visual Studio에서 도구를 업데이트합니다.

### <a name="update-your-tools"></a>도구 업데이트

1. **확장 및 업데이트** 대화 상자에서 **업데이트 > Visual Studio Marketplace**를 펼치고, **Azure IoT Edge** 또는 **클라우드 탐색기**를 선택하고 **업데이트**를 선택합니다.

2. 도구 업데이트를 다운로드한 후에 Visual Studio를 닫고 VSIX 설치 관리자를 사용하여 도구 업데이트를 트리거합니다.

3. 설치 관리자에서 **확인**을 선택하여 시작한 다음, 수정을 선택하여 도구를 업데이트합니다.

4. 업데이트가 완료되면 닫기를 선택하고 Visual Studio를 다시 시작합니다.

### <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge 프로젝트 만들기

Visual Studio의 Azure IoT Edge 프로젝트 템플릿은 Azure IoT Hub의 Azure IoT Edge 장치에 배포할 수 있는 프로젝트를 만듭니다. 먼저 Azure IoT Edge 솔루션을 만든 다음, 이 솔루션에서 첫 번째 C# 모듈을 생성합니다. 각 IoT Edge 솔루션에는 둘 이상의 모듈이 포함될 수 있습니다. 

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.

2. **새 프로젝트** 대화 상자에서 **설치됨**을 선택하고 **Visual C# > 클라우드**를 펼치고, **Azure IoT Edge**를 선택하고, 프로젝트에 대한 **이름**을 입력하고 위치를 지정한 다음, **확인**을 클릭합니다. 기본 프로젝트 이름은 **AzureIoTEdgeApp1**입니다. 

   ![새 프로젝트](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. **IoT Edge 모듈 구성** 창에서 **C# 모듈** 유형을 선택하고 모듈 이름과 모듈 이미지 리포지토리를 지정합니다.  VS에서 모듈 이름이 **localhost:5000**으로 자동으로 채워집니다. 고유한 레지스트리 정보로 바꿉니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 localhost를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는 **<registry name>.azurecr.io**와 같은 모양입니다. 문자열에서 localhost 부분만 바꾸고, 모듈 이름을 삭제하지 마세요. 기본 모듈 이름은 **IoTEdgeModule1**입니다.

4. **확인**을 클릭하여 C# 모듈로 Azure IoT Edge 프로젝트를 만듭니다.

이제 솔루션에 **AzureIoTEdgeApp1** 프로젝트와 **IoTEdgeModule1** 프로젝트가 준비되었습니다. **AzureIoTEdgeApp1** 프로젝트에는 **deployment.template.json** 파일이 있습니다. 이 파일은 IoT Edge 솔루션에 대해 빌드하고 배포할 모듈을 정의하고 모듈 간의 경로를 정의합니다. 기본 솔루션에는 **tempSensor** 모듈과 **IoTEdgeModule1** 모듈이 있습니다. **tempSensor** 모듈은 **IoTEdgeModule1** 모듈에 시뮬레이션 데이터를 생성하는 반면 **IoTEdgeModule1** 모듈의 기본 코드는 파이프 메시지 모듈이며, 수신 메시지를 IoT Hub로 직접 파이프합니다.

**IoTEdgeModule1** 프로젝트는 .Net Core 2.1 콘솔 응용 프로그램입니다. 여기에는 Windows 컨테이너 또는 Linux 컨테이너에서 실행 중인 IoT Edge 장치에 필요한 필수 **Dockerfiles**가 포함되어 있습니다. **module.json** 파일은 모듈의 메타데이터를 설명합니다. **program.cs**는 실제 모듈 코드이며, Azure IoT 장치 SDK를 종속성으로 사용합니다.

## <a name="develop-your-module"></a>모듈 개발

솔루션과 함께 제공되는 기본 C# 모듈 코드는 **IoTEdgeModule1** > **Program.cs**에 있습니다. 모듈 및 deployment.template.json 파일은 솔루션을 빌드하고, 컨테이너 레지스트리에 푸시하고, 장치에 배포하여 코드를 변경하지 않고 테스트를 시작하도록 설정됩니다. 모듈은 단순히 원본에서 입력을 가져오고(이 경우에 데이터를 시뮬레이션하는 tempSensor 모듈) IoT Hub로 파이핑하도록 빌드됩니다. 

고유한 코드를 사용하여 C# 템플릿을 사용자 지정할 준비가 된 경우 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하여 보안, 장치 관리 및 안정성 등 IoT 솔루션에 대한 주요 요구 사항을 해결하는 모듈을 빌드합니다. 

## <a name="initialize-iotegehubdev-with-iot-edge-device-connection-string"></a>IoT Edge 장치 연결 문자열을 사용하여 **iotegehubdev** 초기화

1. IoT Edge 장치의 연결 문자열을 가져오려면 다음과 같이 Visual Studio 2017의 클라우드 탐색기에서 "기본 연결 문자열"의 값을 복사할 수 있습니다. Edge가 아닌 장치의 연결 문자열을 복사하지 마세요. IoT Edge 장치의 아이콘은 Edge가 아닌 장치의 아이콘과 다릅니다.

   ![Edge 장치 연결 문자열 복사](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

2. **AzureIoTEdgeApp1** 프로젝트를 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 연 다음, **Edge 장치 연결 문자열 설정**을 클릭하여 Azure IoT Edge 설정 창을 표시합니다.

   ![Edge 장치 연결 문자열 설정 창 열기](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

3. 설정 창에서 첫 번째 단계에서 얻은 연결 문자열을 입력하고, **확인** 단추를 클릭합니다.

>[!NOTE]
>이 작업은 일회성 작업이므로 한 머신에서 이 단계를 한 번만 실행하면 모든 후속 Azure IoT Edge 솔루션이 추가 비용 없이 제공됩니다. 물론 연결 문자열이 유효하지 않거나 다른 연결 문자열로 변경해야 하는 경우 이 단계를 다시 실행할 수 있습니다.

## <a name="build-and-debug-single-c-module"></a>단일 C# 모듈 빌드 및 디버그

여러 모듈이 포함된 전체 솔루션 내에서 모듈을 실행하기 전에 각 모듈을 테스트/디버그하는 것이 일반적입니다.

1. 상황에 맞는 메뉴에서 **IoTEdgeModule1**을 시작 프로젝트로 선택합니다.

   ![시작 프로젝트 설정](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. **F5** 키를 누르거나 아래 단추를 클릭하여 모듈을 실행합니다. 처음에는 10~20초 정도가 걸립니다.

   ![모듈 실행](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. 모듈이 성공적으로 초기화된 경우 .Net Core 콘솔 앱이 시작된 것을 볼 수 있습니다.

   ![실행 중인 모듈](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. 이제 **Program.cs**의 **PipeMessage**에서 중단점을 설정한 다음, **Git Bash**나 **WSL Bash**에서 다음 명령을 실행하여 메시지를 보낼 수 있습니다. (CMD 또는 Powershell에서 실행하지 마십시오.) (이 명령은 VS 출력 창에서도 찾을 수 있습니다.)

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![단일 모듈 디버그](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    중단점이 트리거됩니다. Visual Studio 로컬 창에서 변수를 볼 수 있습니다.

   > [!TIP]
   > `curl` 대신 [PostMan](https://www.getpostman.com/) 또는 다른 API 도구를 사용하여 메시지를 보낼 수도 있습니다.

5. **Ctrl + F5**를 누르거나 중지 버튼을 클릭하여 디버깅을 중지합니다.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>여러 모듈로 IoT Edge 솔루션 빌드 및 디버그

단일 모듈 개발을 마친 후에는, 여러 모듈이 포함된 전체 솔루션을 실행하고 디버그합니다.

1. 두 번째 C# 모듈을 솔루션에 추가합니다. **AzureIoTEdgeApp1**을 마우스 오른쪽 단추로 클릭하고 **추가** -> **새 IoT Edge 모듈**을 선택합니다. 두 번째 모듈의 기본 이름은 **IoTEdgeModule2**이며 여전히 파이프 모듈입니다.

2. **deployment.template.json**으로 이동합니다. **IoTEdgeModule2**가 **modules** 섹션에 추가된 것을 볼 수 있습니다. **routes** 섹션을 다음으로 바꿉니다. 모듈 이름을 사용자 지정한 경우에는 바꾼 후에 이름을 다음과 같이 업데이트해야 합니다.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. 상황에 맞는 메뉴에서 **AzureIoTEdgeApp1** 프로젝트를 시작 프로젝트로 설정합니다.

4. 중단점을 설정하고 F5 키를 누르면 모듈 여러 개를 동시에 실행하고 디버그할 수 있습니다. .Net Core 콘솔 앱 창을 여러 개 볼 수 있으며 각 창은 C# 모듈을 나타냅니다. 

   ![여러 모듈 디버그](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. **Ctrl + F5**를 누르거나 중지 버튼을 클릭하여 디버깅을 중지합니다.

## <a name="build-and-push-images"></a>이미지 빌드 및 푸시

1. **AzureIoTEdgeApp1**이 시작 프로젝트여야 합니다. 빌드할 모듈 이미지에 대해 **디버그**나 **릴리스** 구성을 선택합니다.

    > [!NOTE]
    > **디버그**를 선택하면, VS에서 `Dockerfile.debug`가 사용되어 Docker 이미지가 빌드됩니다. 이미지를 빌드하는 동안 컨테이너 이미지의 .NET Core 명령 줄 디버거 VSDBG가 포함됩니다. **릴리스** 구성을 사용하는 것이 좋습니다. 그러면 프로덕션이 준비된 IoT Edge 모듈에 대해 VSDBG 없이 `Dockerfile`이 사용됩니다.

2. Azure Container Registry와 같은 개인 레지스트리를 사용하는 경우 터미널에서 다음 명령을 사용하여 Docker 로그인을 실행합니다. 로컬 레지스트리를 사용하는 경우 [로컬 레지스트리를 실행](https://docs.docker.com/registry/deploying/#run-a-local-registry)할 수 있습니다.

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Azure Container Registry와 같은 개인 레지스트리를 사용하는 경우 `deployment.template.json`에 다음 콘텐츠를 사용하여 런타임 설정 아래 레지스트리 사용자 이름과 암호를 입력해야 합니다. 자리 표시자를 실제 관리자 사용자 이름과 암호로 바꿔야 합니다.

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

4. **AzureIoTEdgeApp1**을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴 항목에서 **Build and Push Edge Solution**(Edge 솔루션 빌드 및 푸시)을 선택하면 각 모듈에 대한 Docker 이미지가 빌드되고 푸시됩니다.

   ![이미지 빌드 및 푸시](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>솔루션 배포

IoT Edge 장치를 설정할 때 사용한 빠른 시작 문서에서는 Azure Portal을 사용하여 모듈을 배포했습니다. Visual Studio 용 클라우드 탐색기를 사용하여 모듈을 배포할 수도 있습니다. 시나리오에 대해 준비된 배포 매니페스트(`deployment.json` 파일)가 이미 있습니다. 이제 배포를 받을 장치를 선택하기만 하면 됩니다.

1. **보기** > **클라우드 탐색기**를 클릭하여 **클라우드 탐색기**를 엽니다. 자신의 계정으로 Visual Studio 2017에 로그인되어 있어야 합니다.

2. **클라우드 탐색기**에서 구독을 펼쳐서 배포하려는 Azure IoT Hub 및 Azure IoT Edge 장치를 찾습니다.

3. IoT Edge 장치를 마우스 오른쪽 단추로 클릭하여 배포를 작성합니다. `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` 아래에서 배포 매니페스트 파일을 선택해야 합니다.

>>[!NOTE]
>>`$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`을 선택하면 안됩니다.

4. 새로고침 단추를 클릭합니다. **TempSensor** 모듈과 함께 실행되는 새 모듈과 **$edgeAgent** 및 **$edgeHub**가 표시됩니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

1. 특정 장치에 대한 D2C 메시지를 모니터링하려면 목록에서 해당 장치를 클릭하고 **작업** 창에서 **D2C 메시지 모니터링 시작**을 클릭합니다.

2. 데이터 모니터링을 중지하려면 목록에서 장치를 클릭하고 **작업** 창에서 **D2C 메시지 모니터링 중지**를 선택합니다.

## <a name="next-steps"></a>다음 단계

IoT Edge 장치의 모듈을 개발하려면 [Azure IoT Hub SDK를 이해하고 사용](../iot-hub/iot-hub-devguide-sdks.md)합니다.
