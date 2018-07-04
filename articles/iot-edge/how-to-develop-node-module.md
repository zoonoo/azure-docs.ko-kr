---
title: Azure IoT Edge용 Node.js 모듈 디버그 | Microsoft Docs
description: Visual Studio Code를 사용하여 Azure IoT Edge용 Node.js 모듈 개발 및 디버그
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6b217690b88f303268f5abe66abb7868711d3125
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045095"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Visual Studio Code용 Azure IoT Edge를 사용하여 Node.js 모듈 개발 및 디버그

Azure IoT Edge용 모듈로 전환하여 비즈니스 논리를 에지에서 작동하도록 전송할 수 있습니다. 이 문서에서는 VS Code(Visual Studio Code)를 주 개발 도구로 사용하여 C# 모듈을 개발하기 위한 자세한 지침을 제공합니다.

## <a name="prerequisites"></a>필수 조건
이 아티클에서는 사용자가 Windows 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 컴퓨터로 사용한다고 가정합니다. IoT Edge 장치가 다른 물리적 장치가 될 수도 있고, 개발 컴퓨터에서 IoT Edge 장치를 시뮬레이트할 수도 있습니다.

> [!NOTE]
> 이 디버깅 자습서는 모듈 컨테이너에서 프로세스를 연결하고 VS Code로 디버그하는 방법을 설명합니다. linux-amd64, windows 및 arm32 컨테이너에서 Node.js 모듈을 디버그할 수 있습니다. Visual Studio Code의 디버깅 기능에 익숙하지 않은 경우, [디버깅](https://code.visualstudio.com/Docs/editor/debugging)에 대해 읽어 보시기 바랍니다. 

이 문서에서는 Visual Studio Code를 주 개발 도구로 사용하므로 VS Code를 설치하고 필요한 확장을 추가합니다.
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker 확장](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

모듈을 만들려면 프로젝트 폴더를 빌드하기 위한 npm이 포함된 Node.js, 모듈 이미지를 빌드하기 위한 Docker, 모듈 이미지를 저장하기 위한 컨테이너 레지스트리가 필요합니다.
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다. 

장치에서 모듈을 테스트하려면 하나 이상의 IoT Edge 장치가 있는 활성 IoT Hub가 필요합니다. 사용자 컴퓨터를 IoT Edge 장치로 사용하려는 경우, [Windows](quickstart.md) 또는 [Linux](quickstart-linux.md)에 대한 자습서 단계를 따르면 됩니다. 

## <a name="create-a-new-solution-template"></a>새 솔루션 템플릿 만들기

다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 .NET Core 2.0을 기반으로 IoT Edge 모듈을 만드는 방법을 보여 줍니다. 먼저 솔루션을 만들고 해당 솔루션에 첫 번째 모듈을 생성합니다. 각 솔루션은 여러 모듈을 포함할 수 있습니다. 

1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택합니다.
2. 통합 터미널에서 다음 명령을 입력하여 Node.js용 Azure IoT Edge 모듈 템플릿의 최신 버전을 설치(또는 업데이트)합니다.

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. Visual Studio Code에서 **보기** > **명령 팔레트**를 선택합니다. 
4. 명령 팔레트에서 **Azure IoT Edge: New IoT Edge Solution** 명령을 입력하고 실행합니다.

   ![New IoT Edge Solution 실행](./media/how-to-develop-csharp-module/new-solution.png)

5. 새 솔루션을 만들려는 폴더로 이동한 다음, **폴더 선택**을 클릭합니다. 
6. 솔루션의 이름을 입력합니다. 
7. 솔루션의 첫 번째 모듈에 대한 템플릿으로 **Node.js 모듈**을 선택합니다.
8. 모듈의 이름을 입력합니다. 컨테이너 레지스트리 내에서 고유한 이름을 선택합니다. 
9. 모듈의 이미지 리포지토리를 입력합니다. VS Code에서 모듈 이름을 자동으로 채우기 때문에 **localhost:5000**을 고유한 레지스트리 정보로 바꾸기만 하면 됩니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우, localhost를 사용해도 됩니다. Azure Container Registry를 사용하는 경우, 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는 **\<레지스트리 이름\>.azurecr.io**와 같이 표시됩니다.

VS Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만들고 새 창에서 로드합니다.

솔루션에는 다음 세 개의 항목이 있습니다. 
* **.vscode** 폴더에는 디버그 구성이 들어 있습니다.
* **modules** 폴더에는 각 모듈의 하위 폴더가 들어 있습니다. 지금은 모듈이 하나뿐이지만, **Azure IoT Edge: Add IoT Edge Module** 명령을 사용하여 명령 팔레트에서 모듈을 더 추가할 수 있습니다. 
* **.env** 파일은 환경 변수를 나열합니다. 레지스트리로 ACR을 사용하는 경우, 지금은 ACR 사용자 이름과 암호가 들어 있습니다. 
* **deployment.template.json** 파일은 테스트에 사용할 수 있는 데이터를 시뮬레이트하는 샘플 **tempSensor** 모듈과 함께 새 모듈을 나열합니다. 배포 매니페스트의 작동 방식에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

## <a name="build-and-deploy-your-module-for-debugging"></a>디버그를 위해 모듈 빌드 및 배포

각 모듈 폴더에는 서로 다른 컨테이너 유형에 사용되는 여러 Docker 파일이 있습니다. **.debug** 확장명으로 끝나는 이러한 파일을 사용하여 테스트할 모듈을 빌드할 수 있습니다. 현재, C# 모듈은 linux-amd64 컨테이너의 디버깅만 지원합니다.

1. VS Code에서 `deployment.template.json` 파일로 이동합니다. **deployment.template.json**에서 Node.js 모듈의 createOptions를 아래 내용으로 바꾸고 이 파일을 저장합니다. 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. VS Code 명령 팔레트에서 **Azure IoT Edge: Build IoT Edge solution** 명령을 입력하고 실행합니다.
3. 명령 팔레트에서 솔루션의 `deployment.template.json` 파일을 선택합니다. 
4. Azure IoT Hub 장치 탐색기에서 IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 장치에 대한 배포 만들기**를 선택합니다. 
5. 솔루션의 **구성** 폴더를 열고 `deployment.json` 파일을 선택합니다. **Edge 배포 매니페스트 선택**을 클릭합니다. 

그런 다음, VS Code 통합 터미널에서 배포 ID로 배포가 생성되었는지 확인할 수 있습니다.

VS Code Docker 탐색기에서 또는 터미널에서 `docker ps` 명령을 실행하여 컨테이너 상태를 확인할 수 있습니다.

## <a name="start-debugging-nodejs-module-in-vs-code"></a>VS Code에서 Node.Js 모듈 디버깅 시작

VS Code는 작업 영역의 `.vscode` 폴더에 위치한 `launch.json` 파일에서 구성 정보를 디버깅하도록 유지합니다. 이 `launch.json` 파일은 새 IoT Edge 솔루션을 만들 때 생성되었으며, 디버깅을 지원하는 새 모듈을 추가할 때마다 업데이트됩니다. 

1. VS Code 디버그 보기로 이동하여 모듈의 디버그 구성 파일을 선택합니다.

2. `app.js`로 이동합니다. 이 파일에 중단점을 추가합니다.

3. **디버깅 시작** 단추를 클릭하거나 **F5** 키를 누르고, 연결할 프로세스를 선택합니다.

4. VS Code 디버그 보기의 왼쪽 패널에서 변수를 볼 수 있습니다. 

앞의 예제에서는 컨테이너에서 Node.Js IoT Edge 모듈을 디버그하는 방법을 보여 줍니다. 모듈 컨테이너 createOptions에 노출된 포트를 추가했습니다. Node.Js 모듈의 디버그가 끝나면 프로덕션에서 사용할 준비가 완료된 IoT Edge 모듈을 얻기 위해 이러한 노출된 포트를 제거하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

모듈이 빌드되면 [Visual Studio Code에서 Azure IoT Edge 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.

