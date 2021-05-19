---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 3e5dadcd61246e68cb90ed6d115e7d10bebda92f
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109518299"
---
## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독이 아직 없는 경우 시작하기 전에 [무료 구독을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Git](https://git-scm.com/downloads)
- [Python](https://www.python.org/downloads/) 버전 3.7 이상 Python 버전을 확인하려면 `python --version`을 실행합니다.
- Azure CLI. 이 빠른 시작에서 Azure CLI 명령을 실행하기 위한 두 가지 옵션이 있습니다.
    - 브라우저에서 CLI 명령을 실행하는 대화형 셸인 Azure Cloud Shell을 사용합니다. 이 옵션은 아무 것도 설치할 필요가 없으므로 권장됩니다. 처음으로 Cloud Shell을 사용하는 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다. [Cloud Shell 빠른 시작](/azure/cloud-shell/quickstart)의 단계를 따라 **Cloud Shell을 시작하고** **Bash 환경을 선택합니다**.
    - 선택적으로 로컬 컴퓨터에서 Azure CLI를 실행합니다. 빠른 시작에는 Azure CLI 버전 2.0.76 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드하여 실행하고 로그인하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)의 단계를 수행합니다. 메시지가 표시되면 처음 사용할 때 Azure CLI 확장을 설치합니다.

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>시뮬레이트된 디바이스 실행
이 섹션에서는 Python SDK를 사용하여 시뮬레이션된 디바이스에서 IoT 허브로 메시지를 보냅니다.

1. 새 콘솔 창을 엽니다. 이 콘솔을 사용하여 Python SDK를 설치하고 Python 샘플 코드로 작업합니다. 이제 두 개의 콘솔 창이 열려 있습니다. 방금 연 것과, 이전에 CLI 명령을 입력하기 위해 사용한 Cloud Shell 또는 CLI 콘솔입니다.

1. Python 콘솔에서 [Azure IoT Python SDK 디바이스 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)을 로컬 컴퓨터에 복제합니다.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. 샘플 디렉터리로 이동합니다.

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Azure IoT Python SDK를 설치합니다.

    ```console
    pip3 install azure-iot-device
    ```
1. 시뮬레이션된 디바이스에서 Azure IoT에 연결할 수 있도록 다음 환경 변수를 설정합니다.
    * `IOTHUB_DEVICE_CONNECTION_STRING`이라는 환경 변수를 설정합니다. 변수 값의 경우 이전 섹션에서 저장한 디바이스 연결 문자열을 사용합니다.
    * `IOTHUB_DEVICE_SECURITY_TYPE`이라는 환경 변수를 설정합니다. 변수의 경우 리터럴 문자열 값 `connectionString`을 사용합니다.

    **Windows(cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > Windows CMD의 경우 각 변수의 문자열 값을 묶는 따옴표가 없습니다.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash(Linux 또는 Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. CLI 앱에서 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 명령을 실행하여 시뮬레이션된 IoT 디바이스의 이벤트 모니터링을 시작합니다.  이벤트 메시지가 도착하면 터미널에 출력됩니다.

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Python 콘솔에서 다음 샘플 파일의 코드를 실행합니다. 샘플은 자동 온도 조절기 센서가 있는 시뮬레이션된 온도 컨트롤러를 만듭니다.

    ```console
    python temp_controller_with_thermostats.py
    ```
    > [!NOTE]
    > 이 코드 샘플에서는 수동 구성 없이 솔루션에 스마트 디바이스를 통합할 수 있도록 하는 Azure IoT 플러그 앤 플레이를 사용합니다.  기본적으로 이 설명서에 있는 대부분의 샘플은 IoT 플러그 앤 플레이를 사용합니다. IoT PnP의 장점과 사용 여부에 대한 자세한 내용은 [IoT 플러그 앤 플레이란?](../articles/iot-pnp/overview-iot-plug-and-play.md)을 참조하세요.

 Python 코드가 디바이스에서 IoT 허브로 메시지를 보내면 이벤트를 모니터링하는 CLI 앱에 메시지가 나타납니다.

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: thermostat1
  interface: dtmi:com:example:TemperatureController;2
  module: ''
  origin: myDevice
  payload:
    temperature: 29

event:
  component: thermostat2
  interface: dtmi:com:example:TemperatureController;2
  module: ''
  origin: myDevice
  payload:
    temperature: 48
```

이제 디바이스가 안전하게 연결되어 Azure IoT Hub로 원격 분석을 보냅니다.