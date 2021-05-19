---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 5a72409880953570163728d1e8e943ebdbeb1ba0
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109518302"
---
## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독이 아직 없는 경우 시작하기 전에 [무료 구독을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio(Community, Professional 또는 Enterprise) 2019](https://visualstudio.microsoft.com/downloads/).
- [C#(.NET) GitHub 리포지토리에 대한 Microsoft Azure IoT 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp)의 로컬 복사본입니다. [ZIP 다운로드](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)를 통해 리포지토리의 복사본을 다운로드하고 추출합니다.
- Azure CLI. 이 빠른 시작에서 Azure CLI 명령을 실행하기 위한 두 가지 옵션이 있습니다.
    - 브라우저에서 CLI 명령을 실행하는 대화형 셸인 Azure Cloud Shell을 사용합니다. 이 옵션은 아무 것도 설치할 필요가 없으므로 권장됩니다. 처음으로 Cloud Shell을 사용하는 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다. [Cloud Shell 빠른 시작](/azure/cloud-shell/quickstart)의 단계를 따라 **Cloud Shell을 시작하고** **Bash 환경을 선택합니다**.
    - 선택적으로 로컬 컴퓨터에서 Azure CLI를 실행합니다. 빠른 시작에는 Azure CLI 버전 2.0.76 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드하여 실행하고 로그인하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)의 단계를 수행합니다. 메시지가 표시되면 처음 사용할 때 Azure CLI 확장을 설치합니다.

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>시뮬레이트된 디바이스 실행
이 섹션에서는 로컬 환경을 구성하고 시뮬레이션된 온도 컨트롤러를 만드는 샘플을 실행합니다.

Visual Studio에서 샘플 애플리케이션을 실행하려면 다음을 수행합니다.

1. C#에 대한 Azure IoT 샘플의 압축을 해제한 폴더에 Visual Studio의 *azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln* 솔루션 파일을 엽니다. 

1. **솔루션 탐색기** 에서 **PnpDeviceSamples > TemperatureController** 프로젝트 파일을 선택하고 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정** 을 선택합니다.

1. **TemperatureController** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하고 **디버그** 탭을 선택한 뒤 다음 환경 변수를 프로젝트에 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | *connectionString* |
    | IOTHUB_DEVICE_CONNECTION_STRING | 이전에 저장한 연결 문자열. |

1. 업데이트된 **TemperatureController** 프로젝트 파일을 저장합니다.

1. CLI 앱에서 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 명령을 실행하여 시뮬레이션된 IoT 디바이스의 이벤트 모니터링을 시작합니다.  이벤트 메시지가 도착하면 터미널에 출력됩니다.

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Visual Studio에서 Ctrl + F5를 눌러 샘플을 실행합니다.

    시뮬레이션된 디바이스가IoT Central 애플리케이션에 연결되면 원격 분석을 보내기 시작합니다. 연결 세부 정보 및 원격 분석 출력이 콘솔에 나타납니다. 
    
    ```output
    Starting event monitor, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: myDevice
      payload:
        temperature: 39.8
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: myDevice
      payload:
        temperature: 36.7
    ```