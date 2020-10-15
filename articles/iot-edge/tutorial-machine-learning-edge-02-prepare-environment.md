---
title: '자습서: 환경 설정 - Azure IoT Edge의 Machine Learning'
description: '자습서: 에지에서 기계 학습에 사용할 모듈의 개발 및 배포 환경을 준비합니다.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 99a0805239ee2437ad6ec9ceb40cf45496a07850
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047661"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>자습서: IoT Edge의 기계 학습에 사용할 환경 설정

> [!NOTE]
> 이 문서는 IoT Edge에서 Azure Machine Learning을 사용하는 방법에 대한 자습서 시리즈의 일부입니다. 이 문서로 바로 이동한 경우에는 학습 효과를 극대화할 수 있도록 시리즈의 [첫 번째 문서](tutorial-machine-learning-edge-01-intro.md)부터 시작하는 것이 좋습니다.

이 문서는 개발 및 배포 환경을 준비하는 데 도움이 됩니다. 먼저, 필요한 모든 도구를 사용하여 개발 머신을 설정합니다. 그런 다음, Azure에서 필요한 클라우드 리소스를 만듭니다.

## <a name="set-up-the-development-vm"></a>개발 VM 설정

이 단계는 일반적으로 클라우드 개발자가 수행합니다. 일부 소프트웨어는 데이터 과학자도 유용하게 사용할 수 있습니다.

많은 필수 구성 요소가 이미 구성된 Azure 가상 머신을 만드는 PowerShell 스크립트를 만들었습니다. 우리가 만드는 VM은 [중첩 가상화](../virtual-machines/windows/nested-virtualization.md)를 처리할 수 있어야 하므로 [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) 머신 크기를 선택했습니다.

개발 VM을 설정하려면 다음이 필요합니다.

* 윈도우 10
* [Chocolatey](https://chocolatey.org/)
* [Windows용 Docker Desktop](https://www.docker.com/products/docker-desktop)
* [Windows용 Git](https://gitforwindows.org/)
* [Windows용 Git Credential Manager](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/?view=azps-1.1.0)
* [VS Code 확장](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

개발자 VM이 꼭 필요한 것은 아닙니다. 모든 개발 도구를 로컬 머신에서 실행할 수 있습니다. 그러나 공평한 경쟁이 이루어지도록 VM을 사용할 것을 강력하게 권장합니다.

가상 머신을 만들고 구성하는 데 약 30분 정도 걸립니다.

1. [Machine Learning 및 IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) 샘플 리포지토리를 로컬 컴퓨터에 복제하거나 다운로드합니다.

1. PowerShell을 관리자 권한으로 열고 코드를 다운로드한 루트 디렉터리 아래에 있는 **\IoTEdgeAndMlSample\DevVM** 디렉터리로 이동합니다. 원본의 루트 디렉터리를 `srcdir`로 참조할 것입니다.

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   DevVM 디렉터리에는 이 자습서를 완료하는 데 적합한 Azure 가상 머신을 만들려면 필요한 파일이 포함되어 있습니다.

1. 다음 명령을 실행하여 스크립트 실행을 허용합니다. 메시지가 표시되면 **모두 예**를 선택합니다.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Create-AzureDevVM.ps1을 실행합니다.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    메시지가 표시되면 다음 정보를 제공합니다.

    * **Azure 구독 ID**: 포털의 [Azure 구독](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 찾을 수 있는 구독 ID입니다.
    * **리소스 그룹 이름**: Azure의 새 리소스 그룹 또는 기존 리소스 그룹의 이름입니다.
    * **위치**: 가상 머신이 만들어지는 Azure 위치를 선택합니다. 예를 들어 '미국 서부 2' 또는 '북유럽'입니다. 자세한 내용은 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.
    * **사용자 이름**: VM의 관리자 계정에 대한 기억하기 쉬운 이름을 제공합니다.
    * **암호**: VM의 관리자 계정에 대한 암호를 설정합니다.

   스크립트가 몇 분 정도 실행되면서 다음 단계를 수행합니다.

    1. [Azure PowerShell Az 모듈](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)을 설치합니다.
    1. Azure에 로그인하라는 메시지가 표시됩니다.
    1. VM을 만드는 데 필요한 정보를 확인합니다. **y** 또는 **Enter** 키를 눌러 계속 진행합니다.
    1. 없는 경우 리소스 그룹을 만듭니다.
    1. 가상 머신을 배포합니다.
    1. VM에서 Hyper-V를 사용하도록 설정합니다.
    1. 개발에 필요한 소프트웨어를 설치하고 샘플 리포지토리를 복제합니다.
    1. VM을 다시 시작합니다.
    1. VM에 연결하기 위한 RDP 파일을 데스크톱에 만듭니다.

   VM을 다시 시작하기 위해 VM 이름을 요구하는 메시지가 표시되면 스크립트 출력에서 해당 이름을 복사할 수 있습니다. 출력에는 VM에 연결하기 위한 RDP 파일의 경로도 표시됩니다.

### <a name="set-auto-shutdown-schedule"></a>자동 종료 일정 설정

비용을 줄이기 위해 자동 종료 일정이 1900 PST로 설정된 개발 VM을 만들었습니다. 사용자의 위치와 일정에 따라 이 설정을 업데이트해야 할 수도 있습니다. 종료 일정을 업데이트하려면 다음을 수행합니다.

1. Azure Portal에서 스크립트에서 만든 VM으로 이동합니다.

1. 왼쪽 창 메뉴의 **작업** 아래에서 **자동 종료**를 선택합니다.

1. 원하는 대로 **예약된 종료** 및 **표준 시간대**를 조정하고 **저장**을 선택합니다.

## <a name="connect-to-the-development-vm"></a>개발 VM에 연결

VM을 만들었으므로, 자습서를 완료하는 데 필요한 소프트웨어 설치를 마무리해야 합니다.

1. 스크립트에서 데스크톱에 만든 RDP 파일을 두 번 클릭합니다.

1. 원격 연결의 게시자를 알 수 없다는 대화 상자가 나타날 것입니다. 이는 허용되므로 **연결**을 선택합니다.

1. VM을 만들기 위해 제공한 관리자 암호를 제공하고 **확인**을 클릭합니다.

1. VM 인증서를 수락하라는 메시지가 표시됩니다. **예**를 선택합니다.

## <a name="install-visual-studio-code-extensions"></a>Visual Studio Code 확장 설치

개발 머신에 연결했으므로, 이제 개발 환경을 보다 쉽게 사용할 수 있도록 Visual Studio Code에 몇 가지 유용한 확장을 추가하겠습니다.

1. 개발 VM에 연결하고, PowerShell 창을 열고, **C:\source\IoTEdgeAndMlSample\DevVM** 디렉터리로 이동합니다. 이 디렉터리는 VM을 만든 스크립트를 통해 만들어졌습니다.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. 다음 명령을 실행하여 스크립트 실행을 허용합니다. 메시지가 표시되면 **모두 예**를 선택합니다.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Visual Studio Code 확장 스크립트를 실행합니다.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. 이 스크립트는 몇 분 동안 실행되면서 VS Code 확장을 설치합니다.

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub 및 스토리지 설정

이 단계는 일반적으로 클라우드 개발자가 수행합니다.

Azure IoT Hub는 IoT 디바이스와 클라우드 간의 보안 통신을 처리하므로 IoT 애플리케이션의 핵심입니다. IoT Edge 기계 학습 솔루션을 작동하기 위해 조정이 이루어지는 주요 요소입니다.

* IoT Hub는 경로를 사용하여 IoT 디바이스에서 들어오는 데이터를 다른 다운스트림 서비스로 보냅니다. IoT Hub 경로를 활용하여 디바이스 데이터를 Azure Storage로 보냅니다. Azure Storage에서 Azure Machine Learning을 통해 디바이스 데이터를 사용하여 RUL(잔여 수명) 분류자를 학습시킵니다.

* 이 자습서의 뒷부분에서는 IoT Hub를 사용하여 Azure IoT Edge 디바이스를 구성하고 관리하겠습니다.

이 섹션에서는 스크립트를 사용하여 Azure IoT허브 및 Azure Storage 계정을 만듭니다. 그런 다음, Azure Portal에서 허브를 통해 받은 데이터를 Azure Storage 컨테이너에 전달하는 경로를 구성합니다. 이러한 단계를 완료하는 데 약 10분 정도 걸립니다.

1. 개발 VM에 연결하고, PowerShell 창을 열고, **IoTHub** 디렉터리로 이동합니다.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. 생성 스크립트를 실행합니다. 개발 VM을 만들 때 사용한 것과 똑같은 값을 구독 ID, 위치 및 리소스 그룹에 사용합니다.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Azure에 로그인하라는 메시지가 표시됩니다.
    * 스크립트가 허브 및 스토리지 계정을 만들기 위한 정보를 확인합니다. **y** 또는 **Enter** 키를 눌러 계속 진행합니다.

스크립트를 실행하는 데 약 2분이 걸립니다. 완료되면 스크립트에서 IoT 허브 및 스토리지 계정의 이름을 출력합니다.

## <a name="review-route-to-storage-in-iot-hub"></a>IoT Hub에서 스토리지 경로 검토

IoT Hub 만들기의 일환으로, 우리가 이전 섹션에서 실행한 스크립트 역시 사용자 지정 엔드포인트와 경로를 만들었습니다. IoT Hub 경로는 쿼리 식과 엔드포인트로 구성됩니다. 메시지가 식과 일치하는 경우 데이터가 경로를 따라 관련 엔드포인트로 전송됩니다. 엔드포인트는 Event Hubs, Service Bus 큐 또는 토픽입니다. 이 경우 엔드포인트는 스토리지 계정의 Blob 컨테이너입니다. Azure Portal을 사용하여 스크립트에서 만든 경로를 검토하겠습니다.

1. [Azure Portal](https://portal.azure.com)을 열고, 이 자습서에서 사용하는 리소스 그룹으로 이동합니다.

1. 리소스 목록에서 스크립트에서 만든 IoT Hub를 선택합니다. `IotEdgeAndMlHub-jrujej6de6i7w`와 같이 임의의 문자로 끝나는 이름이 있습니다.

1. 왼쪽 창 메뉴의 **메시지** 아래에서 **메시지 라우팅**을 선택합니다.

1. **메시지 라우팅** 페이지에서 **사용자 지정 엔드포인트** 탭을 선택합니다.

1. **스토리지** 섹션을 펼칩니다.

   ![turbofanDeviceStorage가 사용자 지정 엔드포인트 목록에 있는지 확인](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   사용자 지정 엔드포인트 목록에 **turbofanDeviceStorage**가 표시됩니다. 이 엔드포인트에 대한 다음 특성에 유의하세요.

   * **컨테이너 이름**에서 표시한 대로 `devicedata`라는 Blob 스토리지 컨테이너를 가리킵니다.
   * **파일 이름 형식**에는 partition이 이름의 마지막 요소로 있습니다. 이 형식은 자습서의 뒷부분에서 Azure Notebooks로 파일 작업을 수행할 때 편리합니다.
   * **상태**가 정상이어야 합니다.

1. **경로** 탭을 선택합니다.

1. **turbofanDeviceDataToStorage** 경로를 선택합니다.

1. **경로 세부 정보** 페이지에서 경로의 엔드포인트는  **turbofanDeviceStorage** 엔드포인트입니다.

   ![turbofanDeviceDataToStorage 경로에 대한 세부 정보 검토](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. **true**로 설정된 **라우팅 쿼리**를 확인합니다. 이 설정은 모든 디바이스 원격 분석 메시지가 이 경로와 일치함을 의미합니다. 따라서 모든 메시지는 **turbofanDeviceStorage** 엔드포인트로 보내집니다.

1. 편집되지 않았으므로 이 페이지를 닫기만 하면 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT Hub를 만들고 Azure Storage 계정의 경로를 구성했습니다. 다음으로, IoT Hub를 통해 데이터를 시뮬레이션된 디바이스 세트에서 스토리지 계정으로 보냅니다. 이 자습서의 뒷부분에서 IoT Edge 디바이스 및 모듈을 구성한 후 경로를 다시 방문하여 라우팅 쿼리를 좀 더 살펴보겠습니다.

IoT Edge의 Machine Learning에 대한 자습서 파트에서 다루는 단계에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure IoT 기본 사항](../iot-fundamentals/index.yml)
* [IoT Hub로 메시지 라우팅 구성](../iot-hub/tutorial-routing.md)
* [Azure Portal을 사용하여 IoT Hub 만들기](../iot-hub/iot-hub-create-through-portal.md)

다음 문서로 넘어가서 모니터링할 시뮬레이션된 디바이스 만들기를 진행하세요.

> [!div class="nextstepaction"]
> [디바이스 데이터 생성](tutorial-machine-learning-edge-03-generate-data.md)