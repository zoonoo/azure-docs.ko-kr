---
title: Develop and debug modules in Visual Studio - Azure IoT Edge | Microsoft Docs
description: Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 2f8b0fe83e10beb3b65dca08e18b03f4fc11947e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457099"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge

비즈니스 논리를 Azure IoT Edge용 모듈로 전환할 수 있습니다. This article shows you how to use Visual Studio 2019 as the main tool to develop and debug modules.

Visual Studio 용 Azure IoT Edge 도구는 다음과 같은 이점을 제공합니다.

- 로컬 개발 컴퓨터에서 Azure IoT Edge 솔루션 및 모듈을 생성, 편집, 빌드, 실행 및 디버그합니다.
- Azure IoT Hub를 통해 Azure IoT Edge 디바이스에 Azure IoT Edge 솔루션을 배포합니다.
- Code your Azure IoT modules in C or C# while having all of the benefits of Visual Studio development.
- Azure IoT Edge 디바이스 및 모듈을 UI로 관리합니다.

This article shows you how to use the Azure IoT Edge Tools for Visual Studio 2019 to develop your IoT Edge modules. Azure IoT Edge 디바이스에 프로젝트를 배포하는 방법도 알아봅니다. Currently, Visual Studio 2019 provides support for modules written in C and C#. The supported device architectures are Windows X64 and Linux X64 or ARM32. For more information about supported operating systems, languages, and architectures, see [Language and architecture support](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>전제 조건

이 문서에서는 Windows를 실행하는 컴퓨터 또는 가상 머신을 개발 머신으로 사용한다고 가정합니다. On Windows computers you can develop either Windows or Linux modules. To develop Windows modules, use a Windows computer running version 1809/build 17763 or newer. To develop Linux modules, use a Windows computer that meets the [requirements for Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Because this article uses Visual Studio 2019 as the main development tool, install Visual Studio. Make sure you include the **Azure development** and **Desktop development with C++** workloads in your Visual Studio 2019 installation. You can [Modify Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) to add the required workloads.

After your Visual Studio 2019 is ready, you also need the following tools and components:

- Download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) from the Visual Studio marketplace to create an IoT Edge project in Visual Studio 2019.

> [!TIP]
> If you are using Visual Studio 2017, please download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for VS 2017 from the Visual Studio marketplace

- [Docker Community Edition](https://docs.docker.com/install/)을 개발 머신에 다운로드 및 설치하여 모듈 이미지를 빌드하고 실행합니다. Linux 컨테이너 모드 또는 Windows 컨테이너 모드에서 실행할 Docker CE를 설정해야 합니다.

- [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)을 설치하여 IoT Edge 솔루션을 디버그, 실행 및 테스트할 로컬 개발 환경을 설정합니다. Install [Python (2.7/3.6+) and Pip](https://www.python.org/) and then install the **iotedgehubdev** package by running the following command in your terminal. Azure IoT EdgeHub Dev Tool 버전이 0.3.0보다 높아야 합니다.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clone the repository and install the Vcpkg library manager, and then install the **azure-iot-sdk-c package** for Windows.

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

- 디바이스에서 모듈을 테스트하려면 하나 이상의 IoT Edge 디바이스가 있는 활성 IoT 허브가 필요합니다. 컴퓨터를 IoT Edge 디바이스로 사용하려면 [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)용 빠른 시작의 단계에 따릅니다. 개발 머신에서 IoT Edge 디먼을 실행할 경우, Visual Studio에서 개발을 시작하기 전 EdgeHub 및 EdgeAgent를 중지해야 할 수도 있습니다.

### <a name="check-your-tools-version"></a>도구 버전 확인

1. **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다. **설치됨 > 도구**를 펼치면 **Azure IoT Edge**와 **Visual Studio용 클라우드 탐색기**가 보입니다.

1. 설치된 버전을 적어둡니다. 이 버전을 Visual Studio Marketplace([클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))의 최신 버전과 비교할 수 있습니다.

1. Visual Studio Marketplace에서 제공되는 것보다 버전이 낮을 경우, 다음 섹션에 나오는 대로 Visual Studio에서 도구를 업데이트합니다.

### <a name="update-your-tools"></a>도구 업데이트

1. **확장 및 업데이트** 대화 상자에서 **업데이트 > Visual Studio Marketplace**를 펼치고, **Azure IoT Edge 도구** 또는 **Visual Studio용 클라우드 탐색기**를 선택하고 **업데이트**를 선택합니다.

1. 도구 업데이트를 다운로드한 후에 Visual Studio를 닫고 VSIX 설치 관리자를 사용하여 도구 업데이트를 트리거합니다.

1. 설치 관리자에서 **확인**을 선택하여 시작한 다음, **수정**을 선택하여 도구를 업데이트합니다.

1. 업데이트가 완료되면 **닫기**를 선택하고 Visual Studio를 다시 시작합니다.

### <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge 프로젝트 만들기

Visual Studio의 Azure IoT Edge 프로젝트 템플릿은 Azure IoT Hub의 Azure IoT Edge 디바이스에 배포할 수 있는 프로젝트를 만듭니다. First you create an Azure IoT Edge solution, and then you generate the first module in that solution. 각 IoT Edge 솔루션에는 둘 이상의 모듈이 포함될 수 있습니다.

> [!TIP]
> Visual Studio에서 만든 IoT Edge 프로젝트 구조는 Visual Studio Code의 구조와는 다릅니다.

1. In Visual Studio new project dialog, search and select **Azure IoT Edge** project and click **Next**. In project configuration window, enter a name for your project and specify the location, and then select **Create**. 기본 프로젝트 이름은 **AzureIoTEdgeApp1**입니다.

   ![새 프로젝트 만들기](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. In the **Add IoT Edge Application and Module** window, select either **C# Module** or **C Module** and then specify your module name and module image repository. Visual Studio는 **localhost:5000/<모듈 이름\>** 으로 모듈 이름을 자동으로 채웁니다. 고유한 레지스트리 정보로 바꿉니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 **localhost**를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. The login server looks like **_\<registry name\>_ .azurecr.io**. Only replace the **localhost:5000** part of the string so that the final result looks like **\<*registry name*\>.azurecr.io/ _\<your module name\>_** . The default module name is **IotEdgeModule1**

   ![Add Application and Module](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Select **OK** to create the Azure IoT Edge solution with a module that uses either C# or C.

Now you have an **AzureIoTEdgeApp1.Linux.Amd64** project or an **AzureIoTEdgeApp1.Windows.Amd64** project, and also an **IotEdgeModule1** project in your solution. Each **AzureIoTEdgeApp1** project has a `deployment.template.json` file, which defines the modules you want to build and deploy for your IoT Edge solution, and also defines the routes between modules. The default solution has a **SimulatedTemperatureSensor** module and a **IotEdgeModule1** module. The **SimulatedTemperatureSensor** module generates simulated data to the **IotEdgeModule1** module, while the default code in the **IotEdgeModule1** module directly pipes received messages to Azure IoT Hub.

The **IotEdgeModule1** project is a .NET Core 2.1 console application if it's a C# module. 여기에는 IoT Edge 디바이스를 Windows 컨테이너나 Linux 컨테이너로 실행할 때 필요한 필수 Docker 파일이 포함되어 있습니다. The `module.json` file describes the metadata of a module. The actual module code, which takes Azure IoT Device SDK as a dependency, is found in the `Program.cs` or `main.c` file.

## <a name="develop-your-module"></a>모듈 개발

The default module code that comes with the solution is located at **IotEdgeModule1** > **Program.cs** (for C#) or **main.c** (C). The module and the `deployment.template.json` file are set up so that you can build the solution, push it to your container registry, and deploy it to a device to start testing without touching any code. The module is built to take input from a source (in this case, the **SimulatedTemperatureSensor** module that simulates data) and pipe it to Azure IoT Hub.

When you're ready to customize the module template with your own code, use the [Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md) to build modules that address the key needs for IoT solutions such as security, device management, and reliability.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>IoT Edge 디바이스 연결 문자열로 iotegehubdev 초기화

1. Visual Studio 클라우드 탐색기의 **기본 연결 문자열**에서 모든 IoT Edge 디바이스의 연결 문자열을 복사합니다. IoT Edge 디바이스의 아이콘은 non-Edge 디바이스의 아이콘과 다르므로 non-Edge 디바이스의 연결 문자열을 복사해서는 안 됩니다.

   ![Edge 디바이스 연결 문자열 복사](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Go to **Tools** > **Azure IoT Edge Tools** > **Setup IoT Edge Simulator**, paste the connection string and click **OK**.

   ![Edge 장치 연결 문자열 설정 창 열기](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 첫 번째 단계에서 연결 문자열을 입력하고 **확인**을 선택합니다.

> [!NOTE]
> 모든 후속 Azure IoT Edge 솔루션에 결과가 자동으로 적용되기 때문에 이 단계들은 개발 컴퓨터에서 한 번만 수행해야 합니다. 다른 연결 스트링으로 변경해야 할 경우 이 과정을 다시 밟으면 됩니다.

## <a name="build-and-debug-single-module"></a>Build and debug single module

여러 모듈이 포함된 전체 솔루션 내에서 실행하기 전데 모듈을 각각 테스트 및 디버그하는 것이 일반적입니다.

1. Right-click **IotEdgeModule1** and select **Set as StartUp Project** from the context menu.

   ![시작 프로젝트 설정](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. **F5** 키를 누르거나 아래 버튼을 클릭하여 모듈을 실행합니다. 처음에는 10&ndash;20초 정도 걸립니다.

   ![모듈 실행](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 모듈이 성공적으로 초기화된 경우 .NET Core 콘솔 앱이 시작된 것을 볼 수 있습니다.

   ![실행 중인 모듈](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. If developing in C#, set a breakpoint in the `PipeMessage()` function in **Program.cs**; if using C, set a breakpoint in the `InputQueue1Callback()` function in **main.c**. You can then test it by sending a message by running the following command in a **Git Bash** or **WSL Bash** shell. (PowerShell 또는 명령 프롬프트에서는 `curl` 명령을 실행할 수 없습니다.)

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

1. Add a second module to the solution by right-clicking **AzureIoTEdgeApp1** and selecting **Add** > **New IoT Edge Module**. The default name of the second module is **IotEdgeModule2** and will act as another pipe module.

1. Open the file `deployment.template.json` and you'll see **IotEdgeModule2** has been added in the **modules** section. **routes** 섹션을 다음으로 바꿉니다. 모듈 이름을 사용자 지정한 경우에는 해당 이름을 업데이트하여 맞춰 주어야 합니다.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. **AzureIoTEdgeApp1**을 오른쪽 클릭하여 바로 가기 메뉴에서 **시작 프로젝트로 설정**을 선택합니다.

1. 중단점을 만든 다음 **F5**를 눌러 여러 개의 모듈을 동시에 실행 및 디버그합니다. You should see multiple .NET Core console app windows, which each window representing a different module.

   ![여러 모듈 디버그](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. **Ctrl + F5**를 누르거나 중지 버튼을 클릭하여 디버깅을 중지합니다.

## <a name="build-and-push-images"></a>이미지 빌드 및 푸시

1. **AzureIoTEdgeApp1**이 시작 프로젝트여야 합니다. 모듈 이미지를 빌드할 구성으로 **디버그**와 **릴리스** 중 하나를 선택합니다.

    > [!NOTE]
    > **디버그**를 선택하면, Visual Studio가 `Dockerfile.(amd64|windows-amd64).debug`을(를) 사용하여 Docker 이미지를 빌드합니다. 이미지를 빌드하는 동안 컨테이너 이미지의 .NET Core 명령 줄 디버거 VSDBG가 포함됩니다. 프로덕션이 준비된 IoT Edge 모듈의 경우, VSDBG 없이 `Dockerfile.(amd64|windows-amd64)`을(를) 사용하는 **릴리스** 구성을 권장합니다.

1. Azure Container Registry 같은 프라이빗 레지스트리를 사용할 경우, 다음 Docker 명령을 사용하여 로그인합니다. 로컬 레지스트리를 사용할 경우 [로컬 레지스트리를 실행](https://docs.docker.com/registry/deploying/#run-a-local-registry)할 수 있습니다.

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

1. **AzureIoTEdgeApp1**을 오른쪽 클릭한 다음, **Edge Solution 빌드 및 푸시**를 선택하여 각 모듈의 Docker 이미지를 빌드 및 푸시합니다.

   ![이미지 빌드 및 푸시](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>솔루션 배포

IoT Edge 디바이스를 설정할 때 사용한 빠른 시작 문서에서는 Azure Portal을 사용하여 모듈을 배포했습니다. Visual Studio 용 클라우드 탐색기를 사용하여 모듈을 배포할 수도 있습니다. 시나리오의 배포 매니페스트인 `deployment.json` 파일이 이미 있으므로 배포를 수신할 디바이스만 선택하면 됩니다.

1. **보기** > **클라우드 탐색기**를 클릭하여 **클라우드 탐색기**를 엽니다. Make sure you've logged in to Visual Studio 2019.

1. **클라우드 탐색기**에서 구독을 펼쳐서 배포하려는 Azure IoT Hub 및 Azure IoT Edge 디바이스를 찾습니다.

1. IoT Edge 디바이스를 오른쪽 클릭하여 배포를 만듭니다. `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` 아래에 있는 배포 매니페스트 파일을 선택해야 합니다.

   > [!NOTE]
   > `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`을 선택하면 안됩니다.

1. Click the refresh button to see the new modules running along with the **SimulatedTemperatureSensor** module and **$edgeAgent** and **$edgeHub**.

## <a name="view-generated-data"></a>생성된 데이터 보기

1. To monitor the D2C message for a specific device, select the device in the list and then click **Start Monitoring Built-in Event Endpoint** in the **Action** window.

1. To stop monitoring data, select the device in the list and then select **Stop Monitoring Built-in Event Endpoint** in the **Action** window.

## <a name="next-steps"></a>다음 단계

IoT Edge 디바이스의 사용자 지정 모듈을 개발하려면 [Azure IoT Hub SDK에 대해 숙지하고 사용](../iot-hub/iot-hub-devguide-sdks.md)하십시오.
