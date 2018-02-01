---
title: "Visual Studio Code를 사용하여 Azure IoT Edge에서 C# 모듈 디버그 | Microsoft Docs"
description: "VS Code에서 Azure IoT Edge로 C# 모듈 디버그"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 1ab67cd8aaf59cde3157fcb877ce13f10cb432bb
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2018
---
# <a name="use-visual-studio-code-to-debug-c-module-with-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure IoT Edge에서 C# 모듈 디버그
이 문서에서는 [Visual Studio Code](https://code.visualstudio.com/)를 주 개발 도구로 사용하여 IoT Edge 모듈을 디버그하기 위한 자세한 지침을 제공합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서에서는 사용자가 Windows 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 컴퓨터로 사용한다고 가정합니다. IoT Edge 장치가 다른 물리적 장치가 될 수도 있고, 개발 컴퓨터에서 IoT Edge 장치를 시뮬레이트할 수도 있습니다.

이 설명서를 시작하기 전에 다음 자습서를 완료했는지 확인합니다.
- [Visual Studio Code를 사용하여 Azure IoT Edge에서 C# 모듈 개발](how-to-vscode-develop-csharp-module.md)

이전 자습서를 완료하면 다음 항목이 준비될 것입니다.
- 개발 컴퓨터에서 실행 중인 로컬 Docker 레지스트리. 프로토타입 및 테스트 목적에는 로컬 Docker 레지스트리를 사용하는 것이 좋습니다.
- 최신 필터 모듈 코드를 포함하는 `Program.cs` 파일
- 센서 모듈 및 필터 모듈에 대한 업데이트된 `deployment.json` 파일
- 개발 컴퓨터에서 실행되는 Edge 런타임

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>디버깅을 위한 IoT Edge 모듈 빌드
1. 디버깅을 시작하려면 **dockerfile.debug**를 사용하여 docker 이미지를 다시 빌드하고 Edge 솔루션을 배포합니다. VS Code 탐색기에서 Docker 폴더를 클릭하여 엽니다. 그런 후 `linux-x64` 폴더를 클릭하고 **Dockerfile.debug**를 마우스 오른쪽 단추로 클릭한 후 **IoT Edge 모듈 Docker 이미지 빌드**를 클릭합니다.

    ![디버그 이미지 빌드](./media/how-to-debug-csharp-module/build-debug-image.png)

3. **폴더 선택** 창에서 `./bin/Debug/netcoreapp2.0/publish`로 이동하거나 이 항목을 입력합니다. **Select Folder as EXE_DIR**(EXE_DIR로 폴더 선택)을 클릭합니다.
4. VS Code 창의 맨 위에 있는 팝업 텍스트 상자에 이미지 이름을 입력합니다. 예: `<your container registry address>/filtermodule:latest` 로컬 레지스트리를 배포하는 경우 `localhost:5000/filtermodule:latest`를 입력해야 합니다.
5. Docker 리포지토리에 이미지를 푸시합니다. **Edge: IoT Edge 모듈 Docker 이미지 푸시** 명령을 사용하여 VS Code 창의 맨 위에 있는 팝업 텍스트 상자에 이미지 URL을 입력합니다. 위 단계에서 사용한 동일한 이미지 URL을 사용하세요.
6. `deployment.json`을 다시 사용하여 다시 배포할 수 있습니다. 명령 팔레트에서 **Edge: Edge 다시 시작**을 입력한 후 선택하여 필터 모듈이 디버그 버전으로 실행되도록 합니다.

## <a name="start-debugging-in-vs-code"></a>VS Code에서 디버깅 시작
1. VS Code 디버그 창으로 이동합니다. **F5** 키를 누르고 **IoT Edge(.Net Core)**를 선택합니다.

    ![F5키 누르기](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. `launch.json`에서 **IoT Edge 사용자 지정 모듈 디버그(.NET Core)** 섹션으로 이동한 후 `pipeArgs` 아래에 `<container_name>`을 입력합니다. 이것은 이 자습서에서는 `filtermodule`이어야 합니다.

    ![pipeArgs 수정](./media/how-to-debug-csharp-module/f5-debug-option.png)

3. Program.cs로 이동합니다. `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`에 중단점을 추가합니다.
4. **F5** 키를 다시 누릅니다. 그런 후 연결할 프로세스를 선택합니다. 이 자습서에서 프로세스 이름은 `FilterModule.dll`이어야 합니다.

    ![프로세스 연결](./media/how-to-debug-csharp-module/attach-process.png)

5. VS 코드 디버그 창의 왼쪽 패널에서 변수를 볼 수 있습니다. 

> [!NOTE]
> 위 예제에서는 컨테이너에서 .NET Core IoT Edge 모듈을 디버그하는 방법을 보여 줍니다. 이 방법은 빌드하는 동안 컨테이너 이미지에 VSDBG(.NET Core 명령줄 디버거)를 포함하는 디버그 버전의 `Dockerfile.debug`를 기준으로 합니다. C# 모듈의 디버그가 끝나면 프로덕션에서 사용할 준비가 완료된 IoT Edge 모듈을 얻기 위해 VSDBG를 사용하지 않고 `Dockerfile`을 직접 사용하거나 사용자 지정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디버깅을 위해 IoT Edge 모듈을 만들고 배포한 후 VS Code에서 디버깅을 시작했습니다. 다음 자습서 중 하나를 계속 진행하면 VS Code에 Azure IoT Edge를 개발하는 다른 시나리오에 대해 자세히 알아볼 수 있습니다. 

> [!div class="nextstepaction"]
> [VS Code에서 C# 모듈 개발 및 배포](how-to-vscode-develop-csharp-module.md)
