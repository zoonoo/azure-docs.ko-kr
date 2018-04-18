---
title: Visual Studio Code에서 여러 IoT Edge 모듈 사용 | Microsoft Docs
description: Azure Machine Learning을 Edge 장치에 모듈로 배포
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6c94701507f86f6ecab2875f952215cc3e4cc719
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Visual Studio Code - 미리 보기의 여러 모듈을 사용하여 IoT Edge 솔루션 개발
Visual Studio Code의 여러 모듈을 사용하여 IoT Edge 솔루션을 개발할 수 있습니다. 이 문서에서는 Visual Studio Code에서 시뮬레이션된 IoT Edge 장치의 센서 데이터를 파이프하는 IoT Edge 솔루션을 만들고 업데이트 및 배포하는 과정을 안내합니다. 이 문서에서는 다음 방법을 설명합니다.

* Visual Studio Code를 사용하여 IoT Edge 솔루션 만들기
* VS Code를 사용하여 작업 중인 IoT Edge 솔루션에 새 모듈을 추가할 수 있습니다. 
* IoT Edge 장치에 IoT Edge 솔루션(여러 모듈) 배포
* 생성된 데이터 보기

## <a name="prerequisites"></a>필수 조건
* 아래 자습서 완료
  * [C# 모듈 배포](tutorial-csharp-module.md)
  * [C# 함수 배포](tutorial-deploy-function.md)
  * [Python 모듈 배포](tutorial-python-module.md)
* 이미지 및 컨테이너 관리를 위한 탐색기가 통합된 [Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)


## <a name="prepare-your-first-iot-edge-solution"></a>첫 번째 IoT Edge 솔루션 준비
1. VS Code 명령 팔레트에서 **Edge: New IoT Edge solution** 명령을 입력하고 실행합니다. 작업 영역 폴더를 선택하고 솔루션 이름(기본 이름은 **EdgeSolution**임)을 지정한 다음, 이 솔루션의 첫 번째 사용자 모듈로 C# 모듈(**SampleModule**)을 만듭니다. 첫 번째 모듈의 Docker 이미지 리포지토리도 지정해야 합니다. 기본 이미지 리포지토리는 로컬 Docker 레지스트리(`localhost:5000/<first module name>`)를 기반으로 합니다. Azure Container Registry 또는 Docker 허브로 변경할 수도 있습니다.

> [!NOTE]
> 로컬 Docker 레지스트리를 사용하는 경우 콘솔 창에서 `docker run -d -p 5000:5000 --restart=always --name registry registry:2` 명령을 입력하여 레지스트리가 실행 중인지 확인합니다.

2. VS Code 창에서 IoT Edge 솔루션 작업 영역이 로드됩니다. 루트 폴더에 `modules` 폴더, `.vscode` 폴더 및 배포 매니페스트 템플릿 파일이 있습니다. `.vscode` 폴더에서 디버그 구성을 확인할 수 있습니다. 모든 사용자 모듈 코드는 `modules` 폴더 아래의 하위 폴더입니다. `deployment.template.json`은 배포 매니페스트 템플릿입니다. 이 파일의 일부 매개 변수는 모든 모듈 폴더에 존재하는 `module.json`에서 구문 분석됩니다.

3. 이 솔루션 프로젝트에 두 번째 모듈을 추가합니다. 이번에는 **Edge: Add IoT Edge module**을 입력 및 실행하고 업데이트할 배포 템플릿 파일을 선택합니다. 그런 다음, 이름이 **SampleFunction**인 **Azure 함수 - C#** 및 추가할 Docker 이미지 리포지토리를 선택합니다.

4. 이제 두 개의 기본 모듈이 있는 첫 번째 IoT Edge 솔루션이 준비되었습니다. 기본 C# 모듈은 파이프 메시지 모듈 역할을 하는 반면, C# 함수는 파이프 메시지 함수 역할을 합니다. `deployment.template.json`에서 이 솔루션에 세 개의 모듈이 포함된 것을 확인할 수 있습니다. `tempSensor` 모듈에서 메시지가 생성되어 `SampleModule` 및 `SampleFunction`을 통해 직접 파이프된 다음 IoT 허브로 전송됩니다. 이러한 모듈의 경로를 아래 콘텐츠로 업데이트합니다. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. 이 파일을 저장합니다.

## <a name="build-and-deploy-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드 및 배포
1. VS Code 명령 팔레트에서 **Edge: Build IoT Edge solution** 명령을 입력하고 실행합니다. 각 모듈 폴더에 있는 `module.json` 파일에 따라 이 명령은 각 모듈 Docker 이미지를 확인하고 작성, 컨테이너화 및 푸시하기 시작합니다. 그런 다음, 필수 값을 `deployment.template.json`으로 구문 분석하고 `config` 폴더 아래의 실제 값으로 `deployment.json`을 생성합니다. VS Code 통합 터미널에서 빌드 진행 상황을 확인할 수 있습니다.

2. Azure IoT Hub 장치 탐색기에서 IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭한 다음, **Edge 장치에 대한 배포 만들기**를 선택합니다. `config` 폴더 아래에서 `deployment.json`을 선택합니다. 그런 다음, VS Code 통합 터미널에서 배포 ID로 배포가 생성되었는지 확인할 수 있습니다.

3. 개발 컴퓨터에서 [IoT Edge 장치를 시뮬레이션](tutorial-simulate-device-linux.md)하는 경우. 모든 모듈 이미지 컨테이너가 몇 분 후에 시작됨을 확인할 수 있습니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

1. IoT Hub에 도착하는 데이터를 모니터링하려면 **보기** > **명령 팔레트...**를 선택하고 **IoT: D2C 메시지 모니터링 시작**을 검색합니다. 
2. 데이터 모니터링을 중지하려면 명령 팔레트에서 **IoT: D2C 메시지 모니터링 중지** 명령을 사용합니다. 

## <a name="next-steps"></a>다음 단계

다음 문서 중 하나를 계속 진행하면 Visual Studio Code에서 Azure IoT Edge를 개발하는 다른 시나리오에 대해 자세히 알아볼 수 있습니다.

* [VS Code에서 C# 모듈 디버그](how-to-vscode-debug-csharp-module.md)
* [VS Code에서 C# 함수 디버그](how-to-vscode-debug-azure-function.md)