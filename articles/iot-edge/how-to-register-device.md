---
title: 새 Azure IoT Edge 디바이스 등록 | Microsoft Docs
description: Azure CLI용 IoT 확장을 사용하여 새 IoT Edge 디바이스를 등록하고 연결 문자열 검색
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2ac6a38d99f588c9dbab7a40bc092aa01b27649a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726012"
---
# <a name="register-an-azure-iot-edge-device"></a>Azure IoT Edge 디바이스 등록

Azure IoT Edge에서 IoT 디바이스를 사용하려면 먼저 IoT Hub에 등록해야 합니다. 디바이스가 등록되면 연결 문자열을 검색하여 IoT Edge 워크로드에 대한 디바이스를 설정할 수 있습니다.

다음 도구 중 하나를 사용하여 등록을 선택할 수 있습니다.

* 그래픽 사용자 인터페이스를 사용하여 Azure 리소스를 만들고, 보고, 관리하려면 [Azure Portal에 디바이스를 등록합니다](#register-in-the-azure-portal).
* IoT 솔루션을 개발하는 동일한 위치에서 Azure IoT 리소스를 관리하려면 [Visual Studio Code에 디바이스를 등록합니다](#register-with-visual-studio-code).
* Azure 리소스를 관리하는 명령줄 도구를 선호하거나 작업을 자동화하려는 경우 [Azure CLI에 디바이스를 등록합니다](#register-with-the-azure-cli).

## <a name="register-in-the-azure-portal"></a>Azure Portal에 등록

Azure Portal에서 모든 등록 작업을 수행할 수 있습니다.

### <a name="prerequisites-for-the-azure-portal"></a>Azure Portal의 필수 조건

Azure 구독의 무료 또는 표준 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Azure Portal에서 IoT Edge 디바이스 만들기

Azure Portal의 IoT Hub에서 IoT Edge 디바이스는 에지를 사용할 수 없는 IOT 디바이스와 별도로 만들어지고 관리됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
2. 왼쪽 창의 메뉴에서 **IoT Edge**를 선택합니다.
3. **IoT Edge 디바이스 추가**를 선택합니다.
4. 설명이 포함된 디바이스 ID를 제공합니다. 인증 키를 자동으로 생성하고 허브에 새 디바이스를 연결하려면 기본 설정을 사용합니다.
5. **저장**을 선택합니다.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Azure Portal에서 IoT Edge 디바이스 보기

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

![IoT Hub에서 모든 IoT Edge 디바이스 보기](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Azure Portal에서 연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. 포털에서 **IoT Edge** 페이지의 IoT Edge 디바이스 목록에서 디바이스 ID를 클릭합니다.
2. **기본 연결 문자열** 또는 **보조 연결 문자열** 중 하나의 값을 복사합니다.

## <a name="register-with-visual-studio-code"></a>Visual Studio Code에 등록

VS Code에서 대부분의 작업을 수행하는 방법에는 여러 가지가 있습니다. 이 문서에서는 탐색기를 사용하지만, 명령 팔레트를 사용하여 단계를 실행할 수도 있습니다.

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code의 필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

Visual Studio Code용 Azure IoT 확장을 사용하여 IoT Hub에 대한 작업을 수행할 수 있습니다. 이러한 작업이 수행되려면 Azure 계정에 로그인하고 IoT Hub를 선택해야 합니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.
1. 탐색기의 맨 아래에서 **Azure IoT Hub** 섹션을 확장합니다.

   ![Azure IoT Hub 디바이스 확장 섹션](./media/how-to-register-device/azure-iot-hub-devices.png)

1. **Azure IoT Hub** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다.
1. **IoT Hub 선택**을 선택합니다.
1. Azure 계정에 로그인하지 않은 경우, 프롬프트를 따라 로그인합니다.
1. Azure 구독을 선택합니다.
1. IoT Hub를 선택합니다.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Visual Studio Code를 사용하여 IoT Edge 디바이스 만들기

1. VS Code Explorer에서 **Azure IoT Hub 디바이스** 섹션을 확장합니다.
1. **Azure IoT Hub 디바이스** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다.
1. **IoT Edge 디바이스 만들기**를 선택합니다.
1. 열리는 텍스트 상자에 디바이스 ID를 입력합니다.

출력 화면에 명령의 결과가 표시됩니다. 디바이스 정보가 인쇄되며, 여기에는 입력한 **deviceId**와 물리적 디바이스를 IoT Hub에 연결하는 데 사용할 수 있는 **connectionString**이 포함됩니다.

출력 화면에 명령의 결과가 표시됩니다. 디바이스 정보가 인쇄되며, 여기에는 입력한 **deviceId**와 물리적 디바이스를 IoT Hub에 연결하는 데 사용할 수 있는 **connectionString**이 포함됩니다.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visual Studio Code를 사용하여 IoT Edge 디바이스 보기

IoT Hub에 연결하는 모든 디바이스는 Visual Studio Code Explorer의 **Azure IoT Hub** 섹션에 나열됩니다. IoT Edge 디바이스는 아이콘이 다르고 **$edgeAgent** 및 **$edgeHub** 모듈이 각 IoT Edge 디바이스에 배포된다는 점에서 비 Edge 디바이스와 구분됩니다.

![IoT Hub에서 모든 IoT Edge 디바이스 보기](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Visual Studio Code를 사용하여 연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. **Azure IoT Hub** 섹션에서 디바이스 ID를 마우스 오른쪽 단추로 클릭합니다.
1. **디바이스 연결 문자열 복사**를 선택합니다.

   연결 문자열이 클립보드에 복사됩니다.

오른쪽 클릭 메뉴에서 **디바이스 정보 가져오기**를 선택하여 출력 창에서 연결 문자열을 비롯한 모든 디바이스 정보를 볼 수도 있습니다.

## <a name="register-with-the-azure-cli"></a>Azure CLI에 등록

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)는 IoT Edge 같은 Azure 리소스를 관리하기 위한 오픈 소스 교차 플랫폼 명령줄 도구입니다. 이 기능을 사용하면 Azure IoT Hub 리소스, 디바이스 프로비전 서비스 인스턴스 및 연결된 허브를 즉시 관리할 수 있습니다. IoT 확장은 디바이스 관리 및 전체 IoT Edge 같은 기능으로 Azure CLI를 강화합니다.

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI의 필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md)
* 사용자 환경의 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Azure CLI 버전이 2.0.70 이상이어야 합니다. `az --version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다.
* [Azure CLI용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Azure CLI에서 IoT Edge 디바이스 만들기

다음 [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-create) 명령을 사용하여 IoT 허브에서 새 디바이스 ID를 만듭니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

이 명령에는 세 개의 매개 변수가 포함됩니다.

* **device-id**: IoT Hub에 고유한 설명 이름을 제공합니다.
* **hub-name**: IoT Hub의 이름을 제공합니다.
* **edge-enabled**: 이 매개 변수는 디바이스를 IoT Edge에서 사용한다고 선언합니다.

   ![az iot hub device-identity create output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLI를 사용하여 IoT Edge 디바이스 보기

[az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-list) 명령을 사용하여 IoT 허브의 모든 디바이스를 볼 수 있습니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true**로 설정됩니다.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Azure CLI를 사용하여 연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다. [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) 명령을 사용하여 단일 디바이스에 대한 연결 문자열을 반환합니다.

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 매개 변수의 값은 대소문자를 구분합니다. 연결 문자열 앞뒤의 따옴표를 복사하지 마세요.

## <a name="next-steps"></a>다음 단계

이제 IoT 허브에 디바이스 ID가 등록되었으므로 디바이스에 IoT Edge 런타임을 설치할 준비가 되었습니다. 디바이스의 운영 체제에 따라 런타임을 설치합니다.

* [Windows에 Azure IoT Edge 설치](how-to-install-iot-edge-windows.md)
* [Linux에 Azure IoT Edge 런타임 설치](how-to-install-iot-edge-linux.md)
