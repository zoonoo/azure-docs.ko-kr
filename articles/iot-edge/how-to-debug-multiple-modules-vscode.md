---
title: VS Code에서 Azure IoT Edge용 여러 모듈 디버그 | Microsoft Docs
description: Visual Studio Code를 사용하여 Azure IoT Edge에서 여러 모듈 디버그
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049594"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure IoT Edge에서 여러 모듈 디버그
이 문서에서는 [VS(Visual Studio) Code](https://code.visualstudio.com/)를 사용하여 IoT Edge에서 여러 모듈을 디버그하기 위한 자세한 지침을 제공합니다.

## <a name="prerequisites"></a>필수 조건
[Visual Studio Code에서 여러 모듈을 사용하여 IoT Edge 솔루션 개발](tutorial-multiple-modules-in-vscode.md) 자습서를 완료하고 IoT Edge 장치에서 두 개 이상의 모듈이 실행되고 있는지 확인합니다.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>VS Code의 다중 대상 및 원격 디버깅
VS Code 및 Azure IoT Edge 확장을 사용하는 경우, 컨테이너가 개발 머신에서 실행 중인지 또는 원격 물리적 IoT Edge 디바이스에서 실행 중인지에 관계없이 컨테이너에 모듈 프로세스를 연결할 수 있습니다. 컨테이너에서 실행되는 여러 모듈 디버그는 실제로 개별 컨테이너에 두 개 이상의 프로세스를 연결하는 것입니다. VS Code [다중 대상 디버깅](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging)은 여러 모듈을 디버그할 때 사용할 수 있습니다.

   > [!TIP]
   > 모듈 컨테이너가 원격 물리적 IoT Edge 디바이스에서 실행 중인 경우, 개발 머신의 Docker 엔진이 원격 Docker 호스트와 통신할 수 있도록 [Docker 머신](https://docs.docker.com/machine/overview/)를 설정해야 할 수 있습니다.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>디버깅을 위한 IoT Edge 모듈 빌드
1. 여러 모듈 디버깅을 시작하려면 **Dockerfile.amd64.debug**를 사용하여 Docker 이미지를 다시 빌드하고 Edge 솔루션을 다시 배포해야 합니다. VS Code 탐색기에서 `deployment.template.json` 파일로 이동합니다. 끝에 `.debug`를 추가하여 이미지 URL을 업데이트합니다. `.debug`가 있는 모듈 이미지가 두 개 이상 필요합니다. 이전 자습서의 솔루션에서 작업 중인 경우, C# 함수 모듈과 C# 모듈이 있어야 합니다. 끝에 `.debug`를 추가하여 이러한 두 이미지 URL을 업데이트하고 이 파일을 저장합니다. 
2. 솔루션을 다시 빌드합니다. VS Code 명령 팔레트에서 **Azure IoT Edge: Build IoT Edge solution** 명령을 입력하고 실행합니다.
3. Azure IoT Hub 디바이스 탐색기에서 IoT Edge 디바이스 ID를 마우스 오른쪽 단추로 클릭한 다음, **Edge 디바이스에 대한 배포 만들기**를 선택합니다. `config` 폴더 아래에서 `deployment.json` 파일을 선택합니다. 그런 다음, VS Code 통합 터미널에서 배포 ID로 배포가 생성되었는지 확인할 수 있습니다.

VS Code Docker 탐색기를 사용하거나 터미널에서 `docker ps` 명령을 실행하여 컨테이너 상태를 확인할 수 있습니다.

### <a name="start-debugging-c-function-in-vs-code"></a>VS Code에서 C# 함수 디버깅 시작
1. VS Code는 작업 영역의 `.vscode` 폴더에 위치한 `launch.json` 파일에서 구성 정보를 디버깅하도록 유지합니다. 이 `launch.json` 파일은 새 IoT Edge 솔루션을 만들 때 생성되었으며, 디버깅을 지원하는 새 모듈을 추가할 때마다 업데이트됩니다. 디버그 보기로 이동한 다음, C# 함수 모듈 원격 디버깅에 해당하는 디버그 구성 파일을 선택합니다.
2. `run.csx`로 이동합니다. 함수에 중단점을 추가합니다.
3. **디버깅 시작** 단추를 클릭하거나 **F5** 키를 누르고, 연결할 프로세스를 선택합니다.
4. VS Code 디버그 보기의 왼쪽 패널에서 변수를 볼 수 있습니다. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>VS Code에서 동시에 C# 모듈 디버깅 시작
1. VS Code 명령 팔레트에서 “Workspace: Duplicate Workspace in New Window” 명령을 입력하고 실행합니다. 새 VS Code 창이 동일한 작업 영역에서 시작됩니다.
2. 디버그 보기로 이동한 다음, C# 모듈 원격 디버깅에 해당하는 디버그 구성 파일을 선택합니다.
3. `program.cs`로 이동합니다. C# 모듈에 중단점을 추가합니다.
4. **디버깅 시작** 단추를 클릭하거나 **F5** 키를 누르고, 연결할 프로세스를 선택합니다.
5. VS Code 디버그 보기의 왼쪽 패널에서 변수를 볼 수 있습니다. 

### <a name="see-variables-in-multiple-debugging-windows"></a>다중 디버깅 창에서 변수 확인
1. 이제 두 개의 VS Code 창에서 두 개 이상의 디버깅 세션이 실행되고 있습니다. 중단점 중 하나가 적중되어야 합니다.
2. `F10` 키를 누르거나, **디버그 도구 모음**에서 프로시저 단위 실행 단추를 클릭합니다.
3. 다른 VS Code 창의 중단점이 적중되어야 합니다. 
4. 위의 두 단계를 계속하면 여러 VS Code 디버깅 창에서 여러 모듈의 변수를 볼 수 있습니다.

> [!NOTE]
> 위 예제에서는 Azure IoT Edge를 사용하여 여러 모듈을 디버그하는 방법을 보여 줍니다. 이 방법은 빌드하는 동안 컨테이너 이미지에 VSDBG(.NET Core 명령줄 디버거)를 포함하는 디버그 버전의 `Dockerfile.amd64.debug`를 기준으로 합니다. C# 함수의 디버그가 끝나면 프로덕션에서 사용할 준비가 완료된 IoT Edge 함수를 얻기 위해 VSDBG를 사용하지 않고 `Dockerfile`을 직접 사용하거나 사용자 지정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

모듈이 빌드되면 [Visual Studio Code에서 Azure IoT Edge 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.
