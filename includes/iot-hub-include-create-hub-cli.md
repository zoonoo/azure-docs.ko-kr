---
title: 포함 파일
description: 포함 파일
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 45a9a0821dec98637ade4940bee915483d5e9aa1
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112042037"
---
## <a name="create-an-iot-hub"></a>IoT Hub 만들기
이 섹션에서는 Azure CLI를 사용하여 IoT 허브 및 리소스 그룹을 만듭니다.  Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. IoT 허브는 IoT 애플리케이션과 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다.

IoT 허브 및 리소스 그룹을 만들려면 다음을 수행합니다.

1. CLI 앱을 시작합니다.  이 빠른 시작의 나머지 부분에서 CLI 명령을 실행하려면 명령 구문을 복사하고 CLI 애플리케이션에 붙여넣은 후 변수 값을 편집하고 Enter 키를 누릅니다.
    - Cloud Shell을 사용하려면 CLI 명령에서 **사용해 보기** 단추를 선택하여 분할 브라우저 창에서 Cloud Shell을 시작합니다. 또는 별도의 브라우저 탭에서 [Cloud Shell](https://shell.azure.com/bash)을 열 수 있습니다.
    - Azure CLI를 로컬에서 사용하고 있는 경우 CLI 콘솔 앱을 시작하고 Azure CLI에 로그인합니다.

1. [az extension add](/cli/azure/extension?view=azure-cli-latest#az_extension_add)를 실행하여 *azure-iot* 확장을 현재 버전으로 설치하거나 업그레이드합니다.

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. CLI 앱에서 [az group create](/cli/azure/group#az_group_create)를 실행하여 리소스 그룹을 만듭니다. 다음 명령은 *eastus* 위치에 *MyResourceGroup* 이라는 리소스 그룹을 만듭니다. 
    >[!NOTE]
    > 선택적으로 대체 위치를 설정할 수 있습니다. 사용 가능한 위치를 보려면 `az account list-locations`를 실행합니다. 이 자습서에서는 예제 명령에 표시된 대로 *eastus* 를 사용합니다. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```

1. [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 명령을 사용하여 IoT Hub를 만듭니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. 

    *YourIotHubName*. IoT 허브에 대해 선택한 이름을 사용하여 다음 명령에서 이 자리 표시자와 주변 중괄호를 바꿉니다. IoT Hub 이름은 Azure에서 전역적으로 고유해야 합니다. 자리 표시자가 표시될 때마다 이 빠른 시작의 나머지 부분에서 IoT 허브 이름을 사용합니다.

    ```azurecli-interactive
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```
    > [!TIP]
    > IoT 허브를 만든 후 빠른 시작의 나머지 부분에서 CLI 명령을 계속 사용하여 허브와 상호 작용할 수 있습니다. 선택적으로 CLI 명령 대신 Azure IoT Explorer를 사용할 수 있습니다. IoT Explorer는 기존 IoT 허브에 연결하고 디바이스를 추가, 관리 및 모니터링할 수 있는 GUI 애플리케이션입니다. 자세히 알아보려면 [Azure IoT 탐색기 설치 및 사용](../articles/iot-pnp/howto-use-iot-explorer.md)을 참조하세요.

## <a name="create-a-simulated-device"></a>시뮬레이션된 디바이스 만들기
이 섹션에서는 IoT 허브에 연결되어 시뮬레이션된 IoT 디바이스를 만듭니다. 

시뮬레이션된 디바이스를 만들려면 다음을 수행합니다.
1. CLI 셸에서 [az iot hub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) 명령을 실행합니다. 시뮬레이트된 디바이스 ID가 만들어집니다. 

    *YourIotHubName*. 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다. 

    *myDevice*. 이 문서의 나머지 부분에서 시뮬레이트된 디바이스 ID에 이 이름을 직접 사용할 수 있습니다. 필요에 따라 다른 이름을 사용합니다. 

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  [Az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string#az_iot_hub_device_identity_connection_string_show) 명령을 실행합니다. 

    ```azurecli-interactive
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    연결 문자열 출력은 다음 형식입니다.

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. 연결 문자열을 안전한 위치에 저장합니다. 

> [!NOTE]
> CLI 앱을 열어 둡니다. 이후 단계에서 사용합니다.