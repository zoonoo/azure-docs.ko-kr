---
title: 환경 설정 - Azure IoT Edge의 Machine Learning | Microsoft Docs
description: 에지에서 기계 학습에 사용할 모듈의 개발 및 배포 환경을 준비합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fd3b5766ec2bd8d1babf847598f1fbe5b6511ce7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432840"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>자습서: IoT Edge의 기계 학습에 사용할 환경 설정

> [!NOTE]
> 이 문서는 IoT Edge에서 Azure Machine Learning을 사용하는 방법에 대한 자습서 시리즈의 일부입니다. 이 문서로 바로 이동한 경우 학습 효과를 극대화할 수 있도록 이 시리즈의 [첫 번째 문서](tutorial-machine-learning-edge-01-intro.md)부터 시작할 것을 권장합니다.

IoT Edge의 엔드투엔드 Azure Machine Learning에 대한 자습서의 이 문서에서는 개발 및 배포 환경을 준비하는 방법을 안내합니다. 먼저, 필요한 모든 도구를 사용하여 개발 머신을 설정합니다. 그런 다음, Azure에서 필요한 클라우드 리소스를 만듭니다.

## <a name="set-up-a-development-machine"></a>개발 머신 설정

이 단계는 일반적으로 클라우드 개발자가 수행합니다. 일부 소프트웨어는 데이터 과학자도 유용하게 사용할 수 있습니다.

이 문서의 과정에서는 IoT Edge 모듈과 IoT 디바이스의 코딩, 컴파일, 구성 및 배포를 포함하여 다양한 개발자 작업을 수행합니다. 사용 편의를 위해, 여러 필수 구성 요소가 이미 구성되어 있는 Azure 가상 머신을 만드는 PowerShell 스크립트를 미리 작성해 두었습니다. 우리가 만드는 VM은 [중첩 가상화](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)를 처리할 수 있어야 하므로 DS8V3 머신 크기를 선택했습니다.

개발 VM을 설정하려면 다음이 필요합니다.

* 윈도우 10
* [Chocolatey](https://chocolatey.org/)
* [Windows용 Docker Desktop](https://www.docker.com/products/docker-desktop)
* [Windows용 Git](https://gitforwindows.org/)
* [Windows용 Git Credential Manager](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.Net Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS Code 확장](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

개발자 VM이 꼭 필요한 것은 아닙니다. 모든 개발 도구를 로컬 머신에서 실행할 수 있습니다. 그러나 공평한 경쟁이 이루어지도록 VM을 사용할 것을 강력하게 권장합니다.

가상 머신을 만들고 구성하는 데 약 30분 정도 걸립니다.

### <a name="get-the-script"></a>스크립트 가져오기

[Machine Learning 및 IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) 샘플 리포지토리에서 PowerShell 스크립트를 복제하거나 다운로드합니다.

### <a name="create-an-azure-virtual-machine"></a>Azure 가상 머신 만들기

DevVM 디렉터리에는 이 자습서를 완료하는 데 적합한 Azure 가상 머신을 만들려면 필요한 파일이 포함되어 있습니다.

1. 관리자 권한으로 Powershell을 열고 코드를 다운로드한 디렉터리로 이동합니다. 원본의 루트 디렉터리를 `<srcdir>`로 참조할 것입니다.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. 다음 명령을 실행하여 스크립트 실행을 허용합니다. 메시지가 표시되면 **모두 예**를 선택합니다.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. 이 디렉터리에서 Create-AzureDevVM.ps1을 실행합니다.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * 메시지가 표시되면 다음 정보를 제공합니다.
      * **Azure 구독 ID**: Azure Portal에서 찾을 수 있는 구독 ID
      * **리소스 그룹 이름**: Azure의 새 또는 기존 리소스 그룹 이름
      * **위치**: 가상 머신이 만들어지는 Azure 위치를 선택합니다. 예: westus2 또는 northeurope. 자세한 내용은 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.
      * **AdminUsername**: 만들어서 가상 머신에 사용하려는 관리 계정의 기억하기 쉬운 이름을 입력합니다.
      * **AdminPassword**: 가상 머신의 관리자 계정 암호를 설정합니다.

    * Azure PowerShell이 설치되지 않은 경우 스크립트가 [Azure PowerShell Az 모듈](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)을 설치합니다.

    * Azure에 로그인하라는 메시지가 표시됩니다.

    * 스크립트가 VM을 만들기 위한 정보를 확인합니다. `y` 또는 `Enter` 키를 눌러 계속합니다.

스크립트가 몇 분 정도 실행되면서 다음 단계를 수행합니다.

* 리소스 그룹이 없으면 리소스 그룹 생성
* 가상 머신 배포
* VM에서 Hyper-V를 사용하도록 설정
* 개발에 필요한 소프트웨어를 설치하고 샘플 리포지토리 복제
* VM을 다시 시작합니다.
* VM에 연결하는 RDP 파일을 데스크톱에 만들기

### <a name="set-auto-shutdown-schedule"></a>자동 종료 일정 설정

비용을 줄이기 위해, VM을 만들 때 자동 종료 일정이 1900 PST로 설정됩니다. 위치 및 일정에 따라 이 타이밍을 업데이트해야 하는 경우가 있습니다. 종료 일정을 업데이트하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 이전 섹션에서 입력한 리소스 그룹의 가상 머신으로 이동합니다.

3. 탐색 창에서 **자동 종료**를 선택합니다.

4. **예약된 종료**에 새 종료 시간을 입력하거나 **표준 시간대**를 변경하고 **저장**을 클릭합니다.

### <a name="connect-and-configure-development-machine"></a>개발 머신 연결 및 구성

VM을 만들었으므로, 자습서를 완료하는 데 필요한 소프트웨어 설치를 마무리해야 합니다.

#### <a name="start-a-remote-desktop-session"></a>원격 데스크톱 세션 시작

1. VM 생성 스크립트가 데스크톱에 RDP 파일을 만들었습니다.

2. 이름이 **\<Azure VM name\>.rdp**인 파일을 두 번 클릭합니다.

3. 원격 연결의 게시자를 알 수 없다는 대화 상자가 나타날 것입니다. **Don’t ask me again for connections to this computer**(다시는 이 컴퓨터에 연결하라고 요청 안 함) 확인란을 클릭한 다음, **연결**을 선택합니다.

4. 메시지가 표시되면 스크립트를 실행하여 VM을 설정할 때 사용한 AdminPassword를 입력하고 **확인**을 클릭합니다.

5. VM 인증서를 수락하라는 메시지가 표시됩니다. **Don’t ask me again for connections to this computer**(다시는 이 컴퓨터에 연결하라고 요청 안 함)를 선택하고 **예**를 선택합니다.

#### <a name="install-visual-studio-code-extensions"></a>Visual Studio Code 확장 설치

개발 머신에 연결했으므로, 이제 개발 환경을 보다 쉽게 사용할 수 있도록 Visual Studio Code에 몇 가지 유용한 확장을 추가하겠습니다.

1. PowerShell 창에서 **C:\\source\\IoTEdgeAndMlSample\\DevVM**으로 이동합니다.

2. 다음 명령을 입력하여 가상 머신에서 스크립트를 실행하도록 허용합니다.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. 스크립트를 실행합니다.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. 이 스크립트는 몇 분 동안 실행되면서 VS Code 확장을 설치합니다.

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub 및 스토리지 설정

이 단계는 일반적으로 클라우드 개발자가 수행합니다.

Azure IoT Hub는 IoT 애플리케이션의 핵심으로써 IoT 디바이스와 클라우드 간의 보안 통신을 처리하며, IoT Edge 기계 학습 솔루션을 작동하기 위해 조정이 이루어지는 주요 요소입니다.

* IoT Hub는 경로를 사용하여 IoT 디바이스에서 들어오는 데이터를 다른 다운스트림 서비스로 보냅니다. 여기서는 IoT Hub 경로를 활용하여 Azure Storage로 디바이스 데이터를 전송하겠습니다. 전송된 데이터는 Azure Machine Learning에서 RUL(잔여 수명) 분류자를 학습하는 데 사용할 수 있습니다.

* 이 자습서의 뒷부분에서는 IoT Hub를 사용하여 Azure IoT Edge 디바이스를 구성하고 관리하겠습니다.

이 섹션에서는 스크립트를 사용하여 Azure IoT허브 및 Azure Storage 계정을 만듭니다. 그런 다음, Azure Portal을 사용하여 허브에서 받은 데이터를 Azure Storage Blob 컨테이너에 전달하는 경로를 구성합니다. 이러한 단계를 완료하는 데 약 10분 정도 걸립니다.

### <a name="create-cloud-resources"></a>클라우드 리소스 만들기

1. 개발 머신에서 PowerShell 창을 엽니다.

1. IoTHub 디렉터리로 변경합니다.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. 생성 스크립트를 실행합니다. 개발 VM을 만들 때 사용한 것과 똑같은 값을 구독 ID, 위치 및 리소스 그룹에 사용합니다.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Azure에 로그인하라는 메시지가 표시됩니다.
    * 스크립트가 허브 및 스토리지 계정을 만들기 위한 정보를 확인합니다. `y` 또는 `Enter` 키를 눌러 계속합니다.

스크립트를 실행하는 데 약 2분이 걸립니다. 실행이 완료되면 스크립트는 허브 및 스토리지 계정 이름을 출력합니다.

### <a name="review-route-to-storage-in-iot-hub"></a>IoT Hub에서 스토리지 경로 검토

IoT Hub 만들기의 일환으로, 우리가 이전 섹션에서 실행한 스크립트 역시 사용자 지정 엔드포인트와 경로를 만들었습니다. IoT Hub 경로는 쿼리 식과 엔드포인트로 구성됩니다. 메시지가 식과 일치하는 경우 데이터가 경로를 따라 관련 엔드포인트로 전송됩니다. 엔드포인트는 Event Hubs, Service Bus 큐 또는 토픽입니다. 이 예에서는 엔드포인트가 스토리지 계정의 Blob 컨테이너입니다. Azure Portal을 사용하여 스크립트에서 만든 경로를 검토하겠습니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.

1. 왼쪽 탐색 창에서 모든 서비스를 선택하고, 검색창에 IoT를 입력하고, **IoT Hub**를 선택합니다.

1. 이전 단계에서 만든 IoT Hub를 선택합니다.

1. IoT Hub 쪽 탐색기에서 **메시지 라우팅**을 선택합니다.

1. 메시지 라우팅 페이지에는 **경로** 탭과 **사용자 지정 엔드포인트** 탭이 있습니다. **사용자 지정 엔드포인트** 탭을 선택합니다.

1. **Blob 스토리지** 아래에서 **turbofanDeviceStorage**를 선택합니다.

1. 이 엔드포인트는 직전 단계에서 만든 스토리지 계정의 **devicedata**라는 Blob 컨테이너를 가리키며, 이름에 **iotedgeandml\<고유 접미사\>** 가 붙습니다.

1. 또한 **Blob 파일 이름 형식**이 기본 형식에서 이름의 마지막 요소로 파티션을 배치하도록 변경되었습니다. 이 형식은 자습서의 뒷부분에서 Azure Notebooks로 파일 작업을 수행할 때 편리합니다.

1. 엔드포인트 세부 정보 블레이드를 닫고 **메시지 라우팅** 페이지로 돌아갑니다.

1. **경로** 탭을 선택합니다.

1. **turbofanDeviceDataToStorage** 경로를 선택합니다.

1. 경로의 엔드포인트는 **turbofanDeviceStorage** 사용자 지정 엔드포인트입니다.

1. 라우팅 쿼리를 보면, **true**로 설정되어 있습니다. 즉, 모든 디바이스 원격 분석 메시지가 이 경로와 일치하므로 모든 메시지가 **turbofanDeviceStorage** 엔드포인트로 전송됩니다.

1. 경로 세부 정보를 닫습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT Hub를 만들고 Azure Storage 계정의 경로를 구성했습니다. 다음 문서에서는 시뮬레이션된 디바이스 세트의 데이터를 IoT Hub를 통해 스토리지 계정으로 전송하겠습니다. 이 자습서의 뒷부분에서 IoT Edge 디바이스 및 모듈을 구성한 후 경로를 다시 방문하여 라우팅 쿼리를 좀 더 살펴보겠습니다.

IoT Edge의 Machine Learning에 대한 자습서 파트에서 다루는 단계에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure IoT 기본 사항](https://docs.microsoft.com/azure/iot-fundamentals/)
* [IoT Hub로 메시지 라우팅 구성](../iot-hub/tutorial-routing.md)
* [Azure Portal을 사용하여 IoT Hub 만들기](../iot-hub/iot-hub-create-through-portal.md)

다음 문서로 넘어가서 모니터링할 시뮬레이션된 디바이스 만들기를 진행하세요.

> [!div class="nextstepaction"]
> [디바이스 데이터 생성](tutorial-machine-learning-edge-03-generate-data.md)
