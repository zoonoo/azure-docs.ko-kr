---
title: Azure IoT Edge에서 Azure Function 모듈 디버그 | Microsoft Docs
description: Visual Studio Code를 사용하여 Azure IoT Edge에서 C# Azure Functions 디버그
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bf2d1af66cc3ecc35dafe3bcd43bf10399d71641
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346718"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure IoT Edge용 Azure Functions 개발 및 배포

이 아티클에서는 [VS Code(Visual Studio Code)](https://code.visualstudio.com/)를 사용하여 Azure IoT Edge에서 Azure Functions를 디버그하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건
이 아티클에서는 사용자가 Windows 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 머신으로 사용한다고 가정합니다. IoT Edge 장치는 다른 물리적 장치일 수 있습니다. 또는 개발 머신에서 IoT Edge 장치를 시뮬레이션할 수 있습니다.

> [!NOTE]
> 이 디버깅 아티클에서는 모듈 컨테이너에서 프로세스를 연결하고 VS Code로 디버그하는 방법을 설명합니다. C# 함수는 Linux amd64 컨테이너에서만 디버그할 수 있습니다. Visual Studio Code의 디버깅 기능에 익숙하지 않은 경우, [디버깅](https://code.visualstudio.com/Docs/editor/debugging)에 대해 읽어 보시기 바랍니다. 

이 문서에서는 Visual Studio Code를 주 개발 도구로 사용합니다. VS Code를 설치합니다. 그런 다음, 필요한 확장을 추가합니다. 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker 확장](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

모듈을 만들려면 프로젝트 폴더를 빌드하기 위한 .NET, 모듈 이미지를 빌드하기 위한 Docker, 모듈 이미지를 저장하기 위한 컨테이너 레지스트리가 필요합니다.
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
* 개발 머신의 [Docker Community Edition](https://docs.docker.com/install/) 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다. 

장치에서 모듈을 테스트하려면 하나 이상의 IoT Edge 장치가 있는 활성 IoT 허브가 필요합니다. 컴퓨터를 IoT Edge 장치로 사용하려면 [Windows](quickstart.md) 또는 [Linux](quickstart-linux.md)용 빠른 시작의 단계에 따릅니다. 

## <a name="create-a-new-solution-template"></a>새 솔루션 템플릿 만들기

다음 단계를 수행하여 하나의 C# 함수 모듈이 포함된 IoT Edge 솔루션을 만듭니다. 각 솔루션에는 여러 모듈이 있을 수 있습니다.

1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택합니다.
3. **보기** > **명령 팔레트**를 선택합니다.
4. 명령 팔레트에서 **Azure IoT Edge: 새로운 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 

   ![새로운 IoT Edge 솔루션 실행](./media/how-to-develop-csharp-module/new-solution.png)

5. 새로운 솔루션을 만들 폴더로 이동합니다. **폴더 선택**을 선택합니다. 
6. 솔루션에 대한 이름을 입력합니다. 
7. 솔루션의 첫 번째 모듈에 대한 템플릿으로 **Azure Functions - C#** 을 선택합니다.
8. 모듈의 이름을 입력합니다. 컨테이너 레지스트리 내에서 고유한 이름을 선택합니다. 
9. 모듈의 이미지 리포지토리를 입력합니다. VS Code는 **localhost:5000**으로 모듈 이름을 자동으로 채웁니다. 고유한 레지스트리 정보로 바꿉니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 **localhost**를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는 **\<레지스트리 이름\>.azurecr.io**와 같이 표시됩니다.

VS Code는 입력한 정보를 사용하고, Azure Functions 프로젝트로 IoT Edge 솔루션을 만든 다음, 새 창에서 로드합니다.

솔루션 내에 4개의 항목이 있습니다. 

* **.vscode** 폴더에는 디버그 구성이 들어 있습니다.
* **modules** 폴더에는 각 모듈의 하위 폴더가 있습니다. 이 시점에는 하나만 있습니다. 하지만 **Azure IoT Edge: Add IoT Edge Module** 명령을 사용하여 명령 팔레트에서 모듈을 더 추가할 수 있습니다.
* **.env** 파일은 환경 변수를 나열합니다. Azure Container Registry가 레지스트리인 경우 거기에 Azure Container Registry 사용자 이름 및 암호가 있습니다. 

   >[!NOTE]
   >환경 파일은 모듈에 대한 이미지 리포지토리를 제공하는 경우에만 생성됩니다. localhost 기본값을 로컬로 테스트하고 디버그하도록 수락하는 경우 환경 변수를 선언할 필요가 없습니다. 

* **deployment.template.json** 파일은 테스트에 사용할 수 있는 데이터를 시뮬레이션하는 샘플 **tempSensor** 모듈과 함께 새 모듈을 나열합니다. 배포 매니페스트 작동 방식에 대한 자세한 내용은 [배포 매니페스트를 사용하여 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요.

## <a name="develop-your-module"></a>모듈 개발

솔루션과 함께 제공되는 기본 Azure Function 코드는 **모듈** > **\<모듈 이름\>** > **EdgeHubTrigger-Csharp** > **run.csx**에 위치합니다. 모듈 및 deployment.template.json 파일은 솔루션을 빌드하고, 컨테이너 레지스트리에 푸시하고, 장치에 배포하여 코드를 변경하지 않고 테스트를 시작하도록 설정됩니다. 모듈은 단순히 원본에서 입력을 가져오고(이 경우에 데이터를 시뮬레이션하는 tempSensor 모듈) IoT Hub로 파이핑하도록 빌드됩니다. 

고유한 코드를 사용하여 Azure Function 템플릿을 사용자 지정할 준비가 된 경우 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하여 보안, 장치 관리 및 안정성 등 IoT 솔루션에 대한 주요 요구 사항을 해결하는 모듈을 빌드합니다. 

## <a name="build-your-module-for-debugging"></a>디버깅을 위한 모듈 빌드
1. 디버깅을 시작하려면 **Dockerfile.amd64.debug**를 사용하여 Docker 이미지를 다시 빌드하고 Edge 솔루션을 배포합니다. VS Code 탐색기에서 `deployment.template.json` 파일로 이동합니다. 끝에 `.debug`를 추가하여 함수 이미지 URL을 업데이트합니다.

    ![디버그 이미지 빌드](./media/how-to-debug-csharp-function/build-debug-image.png)

2. 솔루션을 다시 빌드합니다. VS Code 명령 팔레트에서 **Azure IoT Edge: IoT Edge 솔루션 빌드** 명령을 입력하고 실행합니다.
3. Azure IoT Hub 장치 탐색기에서 IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭한 다음, **Edge 장치에 대한 배포 만들기**를 선택합니다. `config` 폴더에서 `deployment.json` 파일을 선택합니다. VS Code 통합 터미널에서 배포 ID를 사용하여 생성된 배포가 표시됩니다.

VS Code Docker 탐색기를 사용하거나 터미널에서 `docker images` 명령을 실행하여 컨테이너 상태를 확인합니다.

## <a name="start-debugging-c-functions-in-vs-code"></a>VS Code에서 C# 함수 디버깅 시작
1. VS Code는 작업 영역의 `.vscode` 폴더에 위치한 `launch.json` 파일에서 구성 정보를 디버깅하도록 유지합니다. 이 `launch.json` 파일은 새 IoT Edge 솔루션을 만들 때 생성되었으며, 디버깅을 지원하는 새 모듈을 추가할 때마다 업데이트됩니다. 디버그 보기로 이동합니다. 해당하는 디버그 구성 파일을 선택합니다. 디버그 옵션 이름은 **ModuleName 원격 디버그(.NET Core)** 와 비슷합니다.

   ![디버그 구성 선택](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. `run.csx`로 이동합니다. 함수에 중단점을 추가합니다.
3. **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다. 연결할 프로세스를 선택합니다.
4. VS Code 디버그 보기의 왼쪽 패널에서 변수가 표시됩니다. 


> [!NOTE]
> 이 예제에서는 컨테이너에서 .NET Core IoT Edge 함수를 디버그하는 방법을 보여줍니다. 이 방법은 빌드하는 동안 컨테이너 이미지에 VSDBG(.NET Core 명령줄 디버거)를 포함하는 디버그 버전의 `Dockerfile.amd64.debug`에 기반합니다. C# 함수를 디버그한 후에 프로덕션에서 사용할 준비가 완료된 IoT Edge 함수를 얻기 위해 VSDBG를 사용하지 않고 `Dockerfile`을 직접 사용하거나 사용자 지정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

모듈을 빌드한 후에 [Visual Studio Code에서 Azure IoT Edge 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.

IoT Edge 장치의 모듈을 개발하려면 [Azure IoT Hub SDK를 이해하고 사용](../iot-hub/iot-hub-devguide-sdks.md)합니다.
