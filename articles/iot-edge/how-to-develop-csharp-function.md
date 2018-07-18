---
title: Azure IoT Edge에서 Functions 모듈 디버그 | Microsoft Docs
description: Visual Studio Code를 사용하여 Azure IoT Edge에서 C# Azure Functions를 디버그합니다.
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ee5d368bde0f176442a01ba266791f8a9c2cbc3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052830"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure IoT Edge용 Azure Functions 개발 및 배포

이 문서에서는 [VS(Visual Studio) 코드](https://code.visualstudio.com/)를 사용하여 IoT Edge에서 Azure Functions를 디버그하기 위한 지침에 대해 자세히 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 아티클에서는 사용자가 Windows 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 컴퓨터로 사용한다고 가정합니다. IoT Edge 장치가 다른 물리적 장치가 될 수도 있고, 개발 컴퓨터에서 IoT Edge 장치를 시뮬레이트할 수도 있습니다.

> [!NOTE]
> 이 디버깅 자습서는 모듈 컨테이너에서 프로세스를 연결하고 VS Code로 디버그하는 방법을 설명합니다. C# 모듈은 linux-amd64 컨테이너에서만 디버그할 수 있습니다. Visual Studio Code의 디버깅 기능에 익숙하지 않은 경우 [디버깅](https://code.visualstudio.com/Docs/editor/debugging)에 대해 읽어 보시기 바랍니다. 

이 문서에서는 Visual Studio Code를 주 개발 도구로 사용합니다. VS Code를 설치한 후 필요한 확장을 추가합니다. 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker 확장](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

모듈을 만들려면 프로젝트 폴더를 빌드하기 위한 .NET, 모듈 이미지를 빌드하기 위한 Docker, 모듈 이미지를 저장하기 위한 컨테이너 레지스트리가 필요합니다.
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* 개발 컴퓨터의 [Docker CE](https://docs.docker.com/install/) 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다. 

장치에서 모듈을 테스트하려면 하나 이상의 IoT Edge 장치가 있는 활성 IoT 허브가 필요합니다. 사용자 컴퓨터를 IoT Edge 장치로 사용하려는 경우 [Windows](quickstart.md) 또는 [Linux](quickstart-linux.md)에 대한 자습서 단계를 따르면 됩니다. 

## <a name="create-a-new-solution-template"></a>새 솔루션 템플릿 만들기

다음 단계는 하나의 C# Function 모듈을 포함하는 IoT Edge 솔루션을 만드는 방법을 보여 줍니다. 각 솔루션은 여러 모듈을 포함할 수 있습니다.

1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택합니다.
3. **보기** > **명령 팔레트**를 선택합니다.
4. 명령 팔레트에서 **Azure IoT Edge: New IoT Edge Solution** 명령을 입력하고 실행합니다. 

   ![New IoT Edge Solution 실행](./media/how-to-develop-csharp-module/new-solution.png)

5. 새 솔루션을 만들려는 폴더로 이동한 다음, **폴더 선택**을 클릭합니다. 
6. 솔루션의 이름을 입력합니다. 
7. 솔루션의 첫 번째 모듈에 대한 템플릿으로 **Azure Functions - C#** 을 선택합니다.
8. 모듈의 이름을 입력합니다. 컨테이너 레지스트리 내에서 고유한 이름을 선택합니다. 
9. 모듈의 이미지 리포지토리를 입력합니다. VS Code에서 모듈 이름을 자동으로 채우기 때문에 **localhost:5000**을 고유한 레지스트리 정보로 바꾸기만 하면 됩니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 localhost를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는 **\<레지스트리 이름\>.azurecr.io**와 같이 표시됩니다.

VS Code는 입력한 정보를 사용하고, Function 프로젝트로 IoT Edge 솔루션을 만들고, 새 창에서 로드합니다.

솔루션에는 다음 세 개의 항목이 있습니다. 

* 디버그 구성이 들어 있는 **.vscode** 폴더
* 각 모듈의 하위 폴더가 들어 있는 **modules** 폴더 지금은 모듈이 하나뿐이지만, **Azure IoT Edge: Add IoT Edge Module** 명령을 사용하여 명령 팔레트에서 모듈을 더 추가할 수 있습니다.
* **.env** 파일은 환경 변수를 나열합니다. 레지스트리로 ACR을 사용하는 경우 지금은 ACR 사용자 이름과 암호가 들어 있습니다. 
* **deployment.template.json** 파일은 테스트에 사용할 수 있는 데이터를 시뮬레이트하는 샘플 **tempSensor** 모듈과 함께 새 모듈을 나열합니다. 배포 매니페스트의 작동 방식에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>디버깅을 위한 IoT Edge 함수 모듈 빌드
1. 디버깅을 시작하려면 **Dockerfile.amd64.debug**를 사용하여 Docker 이미지를 다시 빌드하고 Edge 솔루션을 다시 배포해야 합니다. VS Code 탐색기에서 `deployment.template.json` 파일로 이동합니다. 끝에 `.debug`를 추가하여 함수 이미지 URL을 업데이트합니다.

    ![디버그 이미지 빌드](./media/how-to-debug-csharp-function/build-debug-image.png)

2. 솔루션을 다시 빌드합니다. VS Code 명령 팔레트에서 **Azure IoT Edge: Build IoT Edge solution** 명령을 입력하고 실행합니다.
3. Azure IoT Hub 장치 탐색기에서 IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭한 다음, **Edge 장치에 대한 배포 만들기**를 선택합니다. `config` 폴더 아래에서 `deployment.json` 파일을 선택합니다. 그런 다음, VS Code 통합 터미널에서 배포 ID로 배포가 생성되었는지 확인할 수 있습니다.

VS Code Docker 탐색기를 사용하거나 터미널에서 `docker images` 명령을 실행하여 컨테이너 상태를 확인할 수 있습니다.

## <a name="start-debugging-c-function-in-vs-code"></a>VS Code에서 C# 함수 디버깅 시작
1. VS Code는 작업 영역의 `.vscode` 폴더에 위치한 `launch.json` 파일에서 구성 정보를 디버깅하도록 유지합니다. 이 `launch.json` 파일은 새 IoT Edge 솔루션을 만들 때 생성되었으며, 디버깅을 지원하는 새 모듈을 추가할 때마다 업데이트됩니다. 디버그 보기를 찾아서 해당 디버그 구성 파일을 선택합니다. 디버그 옵션 이름은 **ModuleName 원격 디버그(.NET Core)** ![디버그 구성 선택](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)과 같아야 합니다.

2. `run.csx`로 이동합니다. 함수에 중단점을 추가합니다.
3. **디버깅 시작** 단추를 클릭하거나 **F5** 키를 누르고, 연결할 프로세스를 선택합니다.
4. VS Code 디버그 보기의 왼쪽 패널에서 변수를 볼 수 있습니다. 


> [!NOTE]
> 위 예제에서는 컨테이너에서 .NET Core IoT Edge 함수를 디버그하는 방법을 보여 줍니다. 이 방법은 빌드하는 동안 컨테이너 이미지에 VSDBG(.NET Core 명령줄 디버거)를 포함하는 디버그 버전의 `Dockerfile.amd64.debug`를 기준으로 합니다. C# 함수의 디버그가 끝나면 프로덕션에서 사용할 준비가 완료된 IoT Edge 함수를 얻기 위해 VSDBG를 사용하지 않고 `Dockerfile`을 직접 사용하거나 사용자 지정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

모듈이 빌드되면 [Visual Studio Code에서 Azure IoT Edge 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.
