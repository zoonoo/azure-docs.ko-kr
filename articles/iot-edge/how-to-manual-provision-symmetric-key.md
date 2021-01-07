---
title: 대칭 키를 사용 하 여 프로 비전-Azure IoT Edge | Microsoft Docs
description: 설치 후에는 해당 연결 문자열을 사용 하 여 대칭 키를 사용 하 여 IoT Edge 장치를 프로 비전 하 고에 인증 IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: f5371539c1b45c14b519729c7c07003bf74847a0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043870"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>대칭 키 인증을 사용 하 여 Azure IoT Edge 장치 설정

이 문서에서는 IoT Hub에 새 IoT Edge 장치를 등록 하 고 대칭 키를 사용 하 여 인증 하도록 장치를 구성 하는 단계를 제공 합니다.

이 문서의 단계에서는 각 장치를 IoT hub에 수동으로 연결 하는 수동 프로 비전 이라는 프로세스를 안내 합니다. 대안은 프로 비전 할 장치가 많은 경우 도움이 되는 IoT Hub Device Provisioning Service를 사용 하는 자동 프로 비전입니다.

<!--TODO: Add auto-provision info/links-->

수동 프로 비전을 위해 IoT Edge 장치를 인증 하는 두 가지 옵션이 있습니다.

* **대칭 키**: IoT Hub에서 새 장치 id를 만들면 서비스에서 두 개의 키를 만듭니다. 장치에 키 중 하나를 추가 하 고 인증할 때 IoT Hub 키를 제공 합니다.

  이 인증 방법은 시작 하기 빠르지만 안전 하지 않습니다.

* **X.509 자체 서명**: 두 개의 x.509 id 인증서를 만들어 장치에 저장 합니다. IoT Hub에서 새 장치 id를 만들 때 두 인증서의 지문을 모두 제공 합니다. 장치는 IoT Hub을 인증 하는 경우 인증서를 제공 하 고, IoT Hub 지문이 일치 하는지 확인할 수 있습니다.

  이 인증 방법은 더 안전 하며 프로덕션 시나리오에 권장 됩니다.

이 문서에서는 대칭 키 인증을 사용 하 여 등록 및 프로 비전 프로세스를 안내 합니다. X.509 인증서를 사용 하 여 장치를 설정 하는 방법을 알아보려면 [x.509 인증서 인증을 사용 하 여 Azure IoT Edge 장치 설정](how-to-manual-provision-x509.md)을 참조 하세요.

## <a name="prerequisites"></a>전제 조건

이 문서의 단계를 수행 하기 전에 IoT Edge 런타임이 설치 된 장치가 있어야 합니다. 그렇지 않으면 [Azure IoT Edge 런타임 설치 또는 제거](how-to-install-iot-edge.md)의 단계를 따릅니다.

## <a name="register-a-new-device"></a>새 장치 등록

IoT Hub에 연결 하는 모든 장치에는 클라우드-장치 또는 장치-클라우드 통신을 추적 하는 데 사용 되는 장치 ID가 있습니다. IoT hub 호스트 이름, 장치 ID 및 장치에서 IoT Hub 인증 하는 데 사용 하는 정보를 포함 하는 연결 정보를 사용 하 여 장치를 구성 합니다.

대칭 키 인증의 경우이 정보는 IoT Hub에서 검색 한 다음 IoT Edge 장치에 저장할 수 있는 *연결 문자열* 에 수집 됩니다.

사용자의 기본 설정에 따라 여러 도구를 사용 하 여 IoT Hub에 새 IoT Edge 장치를 등록 하 고 해당 연결 문자열을 검색할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Azure Portal의 필수 조건

Azure 구독의 무료 또는 표준 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Azure Portal에서 IoT Edge 디바이스 만들기

Azure Portal의 IoT Hub에서 IoT Edge 디바이스는 에지를 사용할 수 없는 IOT 디바이스와 별도로 만들어지고 관리됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택한 다음 **IoT Edge 장치 추가**를 선택 합니다.

   ![Azure Portal에서 IoT Edge 장치 추가](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. **장치 만들기** 페이지에서 다음 정보를 제공 합니다.

   * 설명 장치 ID를 만듭니다.
   * 인증 유형으로 **대칭 키**를 선택합니다.
   * 인증 키를 자동으로 생성하고 허브에 새 디바이스를 연결하려면 기본 설정을 사용합니다.

1. **저장**을 선택합니다.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Azure Portal에서 IoT Edge 디바이스 보기

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

![Azure Portal를 사용 하 여 IoT hub의 모든 IoT Edge 장치를 볼 수 있습니다.](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Azure Portal에서 연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. 포털에서 **IoT Edge** 페이지의 IoT Edge 디바이스 목록에서 디바이스 ID를 클릭합니다.
2. **기본 연결 문자열** 또는 **보조 연결 문자열** 중 하나의 값을 복사합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code의 필수 조건

* Azure 구독의 무료 또는 표준 [IoT hub](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

Visual Studio Code용 Azure IoT 확장을 사용하여 IoT Hub에 대한 작업을 수행할 수 있습니다. 이러한 작업이 수행되려면 Azure 계정에 로그인하고 IoT Hub를 선택해야 합니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.
1. 탐색기의 맨 아래에서 **Azure IoT Hub** 섹션을 확장합니다.

   ![Azure IoT Hub 디바이스 확장 섹션](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

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

![VS Code를 사용 하 여 IoT hub의 모든 IoT Edge 장치 보기](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Visual Studio Code를 사용하여 연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. **Azure IoT Hub** 섹션에서 디바이스 ID를 마우스 오른쪽 단추로 클릭합니다.
1. **디바이스 연결 문자열 복사**를 선택합니다.

   연결 문자열이 클립보드에 복사됩니다.

오른쪽 클릭 메뉴에서 **디바이스 정보 가져오기**를 선택하여 출력 창에서 연결 문자열을 비롯한 모든 디바이스 정보를 볼 수도 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI의 필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md)
* 사용자 환경의 [Azure CLI](/cli/azure/install-azure-cli). 최소한 Azure CLI 버전은 2.0.70 이상 이어야 합니다. `az --version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다.
* [Azure CLI용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Azure CLI에서 IoT Edge 디바이스 만들기

다음 [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 명령을 사용하여 IoT 허브에서 새 디바이스 ID를 만듭니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

이 명령에는 세 개의 매개 변수가 포함됩니다.

* `--device-id` 또는 `-d` : IoT hub에 고유한 설명이 포함 된 이름을 제공 합니다.
* `hub-name` 또는 `-n` : IoT hub의 이름을 제공 합니다.
* `--edge-enabled` 또는 `--ee` : 장치가 IoT Edge 장치 임을 선언 합니다.

   ![az iot hub device-identity create output](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLI를 사용하여 IoT Edge 디바이스 보기

[az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) 명령을 사용하여 IoT 허브의 모든 디바이스를 볼 수 있습니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true**로 설정됩니다.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Azure CLI를 사용하여 연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다. [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) 명령을 사용하여 단일 디바이스에 대한 연결 문자열을 반환합니다.

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 매개 변수의 값은 대소문자를 구분합니다. 연결 문자열 앞뒤의 따옴표를 복사하지 마세요.

---

## <a name="provision-an-iot-edge-device"></a>IoT Edge 디바이스 프로비전

IoT Edge 장치에 IoT Hub의 id와 인증에 사용할 수 있는 연결 문자열이 있는 경우이 정보를 사용 하 여 장치 자체를 프로 비전 해야 합니다.

Linux 장치에서는 config.xml 파일을 편집 하 여 연결 문자열을 제공 합니다. Windows 장치에서 PowerShell 스크립트를 실행 하 여 연결 문자열을 제공 합니다.

# <a name="linux"></a>[Linux](#tab/linux)

IoT Edge 장치에서 구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로 비전 구성을 찾고 **연결 문자열 섹션을 사용 하 여 수동 프로 비전 구성** 의 주석 처리를 제거 합니다. 

**device_connection_string**의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다. 다른 프로 비전 섹션이 주석 처리 되었는지 확인 합니다. **프로 비전:** 줄에 앞에 공백이 없고 중첩 된 항목이 두 개의 공백으로 들여쓰기 되는지 확인 합니다.

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

클립보드 내용을 Nano에 붙여넣거나 `Shift+Right Click` 키를 누릅니다 `Shift+Insert` .

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. IoT Edge 장치에서 관리자 권한으로 PowerShell을 실행 합니다.

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 명령을 사용 하 여 컴퓨터에서 IoT Edge 런타임을 구성 합니다. 이 명령은 Windows 컨테이너를 통한 수동 프로비저닝으로 기본 설정됩니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Linux 컨테이너를 사용 하는 경우 `-ContainerOs` 플래그에 매개 변수를 추가 합니다. 이전에 실행 한 명령을 사용 하 여 선택한 컨테이너 옵션과 일치 해야 `Deploy-IoTEdge` 합니다.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * 오프 라인 또는 특정 버전 설치를 위해 IoTEdgeSecurityDaemon.ps1 스크립트를 장치에 다운로드 한 경우 스크립트의 로컬 복사본을 참조 해야 합니다.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 메시지가 표시 되 면 이전 섹션에서 검색 한 장치 연결 문자열을 제공 합니다. 장치 연결 문자열은 물리적 장치를 IoT Hub의 장치 ID와 연결 하 고 인증 정보를 제공 합니다.

   장치 연결 문자열은 다음 형식을 사용 하며 따옴표를 포함 하지 않아야 합니다. `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

수동으로 장치를 프로 비전 할 때 추가 매개 변수를 사용 하 여 다음을 포함 하 여 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 특정 edgeAgent 컨테이너 이미지를 선언 하 고 개인 레지스트리에 있는 경우 자격 증명을 제공 합니다.

이러한 추가 매개 변수에 대 한 자세한 내용은 [Windows의 IoT Edge에 대 한 PowerShell 스크립트](reference-windows-scripts.md)를 참조 하세요.

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>다음 단계

장치에 모듈을 배포 하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md) 를 계속 진행 하세요.