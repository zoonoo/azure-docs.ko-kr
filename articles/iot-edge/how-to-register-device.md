---
title: 새 장치 등록-Azure IoT Edge | Microsoft Docs
description: 대칭 키 또는 x.509 인증서를 사용 하 여 수동 프로 비전을 위해 IoT Hub에 단일 IoT Edge 장치를 등록 합니다.
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: d75f184a324a9d418b0af2e3cf5790205af0fa42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200710"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>IoT Hub에 IoT Edge 장치 등록

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

이 문서에서는 IoT Hub에 새 IoT Edge 장치를 등록 하는 단계를 제공 합니다.

IoT hub에 연결 하는 모든 장치에는 클라우드-장치 또는 장치-클라우드 통신을 추적 하는 데 사용 되는 장치 ID가 있습니다. IoT hub 호스트 이름, 장치 ID 및 장치에서 IoT Hub 인증 하는 데 사용 하는 정보를 포함 하는 연결 정보를 사용 하 여 장치를 구성 합니다.

이 문서의 단계는 단일 장치를 IoT hub에 연결 하는 수동 프로 비전 이라는 프로세스를 안내 합니다. 수동 프로 비전을 위해 IoT Edge 장치를 인증 하는 두 가지 옵션이 있습니다.

* **대칭 키**: IoT Hub에서 새 장치 id를 만들면 서비스에서 두 개의 키를 만듭니다. 장치에 키 중 하나를 추가 하 고 인증할 때 IoT Hub 키를 제공 합니다.

  이 인증 방법은 시작 하기 빠르지만 안전 하지 않습니다.

* **X.509 자체 서명**: 두 개의 x.509 id 인증서를 만들어 장치에 저장 합니다. IoT Hub에서 새 장치 id를 만들 때 두 인증서의 지문을 모두 제공 합니다. 장치는 IoT Hub을 인증 하는 경우 인증서 하나를 표시 하 고 인증서가 해당 지문과 일치 하는지 확인 IoT Hub 합니다.

  이 인증 방법은 더 안전 하며 프로덕션 시나리오에 권장 됩니다.

이 문서에서는 두 인증 방법에 대해 설명 합니다.

설정할 장치가 많고 각 장치를 수동으로 프로 비전 하지 않으려는 경우 다음 문서 중 하나를 사용 하 여 IoT Edge IoT Hub Device Provisioning Service에서 작동 하는 방법을 알아보세요.

* [X.509 인증서를 사용 하 여 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-x509-certs.md)
* [TPM을 사용 하 여 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-simulated-device-linux.md)
* [대칭 키를 사용 하 여 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>필수 구성 요소

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 구독의 무료 또는 표준 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure 구독의 무료 또는 표준 [IoT hub](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure 구독의 무료 또는 표준 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md).
* 사용자 환경의 [Azure CLI](/cli/azure/install-azure-cli). 최소한 Azure CLI 버전은 2.0.70 이상 이어야 합니다. `az --version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다.

---

## <a name="option-1-register-with-symmetric-keys"></a>옵션 1: 대칭 키를 사용 하 여 등록

사용자의 기본 설정에 따라 여러 도구를 사용 하 여 IoT Hub에 새 IoT Edge 장치를 등록 하 고 해당 연결 문자열을 검색할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal의 IoT hub에서 IoT Edge 장치는 Edge를 사용 하도록 설정 되지 않은 IoT 장치와 별도로 만들어지고 관리 됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택한 다음 **IoT Edge 장치 추가** 를 선택 합니다.

   ![Azure Portal에서 IoT Edge 장치 추가](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. **장치 만들기** 페이지에서 다음 정보를 제공 합니다.

   * 설명 장치 ID를 만듭니다.
   * 인증 유형으로 **대칭 키** 를 선택합니다.
   * 인증 키를 자동으로 생성하고 허브에 새 디바이스를 연결하려면 기본 설정을 사용합니다.

1. **저장** 을 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

Visual Studio Code용 Azure IoT 확장을 사용하여 IoT Hub에 대한 작업을 수행할 수 있습니다. 이러한 작업을 수행 하려면 Azure 계정에 로그인 하 고 허브를 선택 해야 합니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.
1. 탐색기의 맨 아래에서 **Azure IoT Hub** 섹션을 확장합니다.

   ![Azure IoT Hub 디바이스 확장 섹션](./media/how-to-register-device/azure-iot-hub-devices.png)

1. **Azure IoT Hub** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다.
1. **IoT Hub 선택** 을 선택합니다.
1. Azure 계정에 로그인하지 않은 경우, 프롬프트를 따라 로그인합니다.
1. Azure 구독을 선택합니다.
1. IoT Hub를 선택합니다.

### <a name="register-a-new-device-with-visual-studio-code"></a>Visual Studio Code를 사용 하 여 새 장치 등록

1. Visual Studio Code 탐색기에서 **Azure IoT Hub** 섹션을 확장 합니다.
1. **Azure IoT Hub** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다.
1. **IoT Edge 디바이스 만들기** 를 선택합니다.
1. 열리는 텍스트 상자에 디바이스 ID를 입력합니다.

출력 화면에 명령의 결과가 표시됩니다. 디바이스 정보가 인쇄되며, 여기에는 입력한 **deviceId** 와 물리적 디바이스를 IoT Hub에 연결하는 데 사용할 수 있는 **connectionString** 이 포함됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 명령을 사용하여 IoT 허브에서 새 디바이스 ID를 만듭니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

이 명령에는 세 개의 매개 변수가 포함됩니다.

* `--device-id` 또는 `-d` : IoT hub 내에서 고유한 설명이 포함 된 이름을 제공 합니다.
* `--hub-name` 또는 `-n` : IoT hub의 이름을 제공 합니다.
* `--edge-enabled` 또는 `--ee` : 장치가 IoT Edge 장치 임을 선언 합니다.

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

이제 IoT Hub에 등록 된 장치가 있으므로 IoT Edge 런타임의 설치 및 프로 비전을 완료 하는 데 사용 하는 연결 문자열을 검색 합니다. 이 문서의 뒷부분에 나오는 단계에 따라 [등록 된 장치를 확인 하 고 연결 문자열을 검색](#view-registered-devices-and-retrieve-connection-strings)합니다.

## <a name="option-2-register-with-x509-certificates"></a>옵션 2: x.509 인증서를 사용 하 여 등록

X.509 인증서를 사용 하 여 수동으로 프로 비전 하려면 IoT Edge 버전 1.0.10 이상이 필요 합니다.

X.509 인증서 인증의 경우 각 장치의 인증 정보는 장치 id 인증서에서 가져온 *지문* 형태로 제공 됩니다. 이러한 지문을 장치 등록 시 IoT Hub에 제공 되므로 서비스가 연결 될 때 장치를 인식할 수 있습니다.

### <a name="create-certificates-and-thumbprints"></a>인증서 및 지문 만들기

X.509 인증서를 사용 하 여 IoT Edge 장치를 프로 비전 할 때 *장치 id 인증서* 를 사용 합니다. 이 인증서는 IoT Edge 장치를 프로 비전 하 고 Azure IoT Hub 장치를 인증 하는 데만 사용 됩니다. 다른 인증서를 서명 하지 않는 리프 인증서입니다. 장치 id 인증서는 IoT Edge 장치가 확인을 위해 모듈 또는 다운스트림 장치에 제공 하는 CA (인증 기관) 인증서와는 별개입니다. IoT Edge 장치에서 CA 인증서를 사용 하는 방법에 대 한 자세한 내용은 [Azure IoT Edge 인증서를 사용 하는 방법 이해](iot-edge-certs.md)를 참조 하세요.

X.509를 사용한 수동 프로 비전에는 다음 파일이 필요 합니다.

* .Cer 또는. p a. 형식의 일치 하는 개인 키 인증서를 포함 하는 두 개의 장치 id 인증서

  하나의 인증서/키 파일 집합이 IoT Edge 런타임에 제공 됩니다. 장치 id 인증서를 만들 때 IoT hub에 장치를 포함할 장치 ID로 인증서 CN (일반 이름)을 설정 합니다.

* 장치 id 인증서에서 가져온 지문입니다.

  지문 값은 sha-1 해시의 경우 40-16 진수 문자이 고, SHA-256 해시의 경우 64입니다. 장치 등록 시 IoT Hub 하기 위해 두 지문이 모두 제공 됩니다.

사용할 수 있는 인증서가 없는 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다. 이 문서의 지침에 따라 인증서 생성 스크립트를 설정 하 고 루트 CA 인증서를 만든 다음 두 개의 IoT Edge 장치 id 인증서를 만듭니다.

인증서에서 지문을 검색 하는 한 가지 방법은 다음 openssl 명령을 사용 하는 것입니다.

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>새 장치 등록

여러 도구를 사용 하 여 IoT Hub에 새 IoT Edge 장치를 등록 하 고 해당 인증서 지문을 업로드할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal의 IoT hub에서 IoT Edge 장치는 Edge를 사용 하도록 설정 되지 않은 IoT 장치와 별도로 만들어지고 관리 됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택한 다음 **IoT Edge 장치 추가** 를 선택 합니다.

   ![Azure Portal에서 IoT Edge 장치 추가](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. **장치 만들기** 페이지에서 다음 정보를 제공 합니다.

   * 설명 장치 ID를 만듭니다. 다음 섹션에서 사용할 것 처럼이 장치 ID를 기록해 둡니다.
   * 인증 유형으로 **X.509 자체 서명** 을 선택합니다.
   * 기본 및 보조 id 인증서 지문을 제공 합니다. 지문 값은 sha-1 해시의 경우 40-16 진수 문자이 고, SHA-256 해시의 경우 64입니다.

1. **저장** 을 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

현재 Visual Studio Code 용 Azure IoT 확장은 x.509 인증서를 사용 하 여 장치 등록을 지원 하지 않습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 명령을 사용하여 IoT 허브에서 새 디바이스 ID를 만듭니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

이 명령에는 여러 매개 변수가 포함 됩니다.

* `--device-id` 또는 `-d` : IoT hub에 고유한 설명이 포함 된 이름을 제공 합니다. 다음 섹션에서 사용할 것 처럼이 장치 ID를 기록해 둡니다.
* `hub-name` 또는 `-n` : IoT hub의 이름을 제공 합니다.
* `--edge-enabled` 또는 `--ee` : 장치가 IoT Edge 장치 임을 선언 합니다.
* `--auth-method` 또는 `--am` : 장치에서 사용 하려는 권한 부여 유형을 선언 합니다. 이 경우에는 x.509 인증서 지문을 사용 하 고 있습니다.
* `--primary-thumbprint` 또는 `--ptp` : 기본 키로 사용할 x.509 인증서 지문을 제공 합니다.
* `--secondary-thumbprint` 또는 `--stp` : 보조 키로 사용할 x.509 인증서 지문을 제공 합니다.

---

이제 IoT Hub에 등록 된 장치가 있으므로 장치에 IoT Edge 런타임을 설치 하 고 프로 비전 할 준비가 되었습니다. X.509 인증서를 사용 하 여 인증 하는 장치는 연결 문자열을 사용 하지 않으므로 다음 단계를 계속 진행할 수 있습니다. IoT Edge

* [Linux 용 Azure IoT Edge 설치 또는 제거](how-to-install-iot-edge.md)
* [Windows 용 Azure IoT Edge 설치 또는 제거](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>등록 된 장치 보기 및 연결 문자열 검색

대칭 키 인증을 사용 하는 장치에는 IoT Edge 런타임의 설치 및 프로 비전을 완료 하기 위해 연결 문자열이 필요 합니다.

X.509 인증서 인증을 사용 하는 장치에는 연결 문자열이 필요 하지 않습니다. 대신, 이러한 장치에는 IoT hub 이름, 해당 장치 이름 및 인증서 파일이 있어야 IoT Edge 런타임의 설치 및 프로 비전을 완료할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

![Azure Portal를 사용 하 여 IoT hub의 모든 IoT Edge 장치를 볼 수 있습니다.](./media/how-to-register-device/portal-view-devices.png)

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

대칭 키를 사용 하 여 인증 하는 장치에는 포털에서 복사할 수 있는 연결 문자열이 있습니다.

1. 포털에서 **IoT Edge** 페이지의 IoT Edge 디바이스 목록에서 디바이스 ID를 클릭합니다.
2. **기본 연결 문자열** 또는 **보조 연결 문자열** 중 하나의 값을 복사합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visual Studio Code를 사용하여 IoT Edge 디바이스 보기

IoT Hub에 연결하는 모든 디바이스는 Visual Studio Code Explorer의 **Azure IoT Hub** 섹션에 나열됩니다. IoT Edge 디바이스는 아이콘이 다르고 **$edgeAgent** 및 **$edgeHub** 모듈이 각 IoT Edge 디바이스에 배포된다는 점에서 비 Edge 디바이스와 구분됩니다.

![VS Code를 사용 하 여 IoT hub의 모든 IoT Edge 장치 보기](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Visual Studio Code를 사용하여 연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. **Azure IoT Hub** 섹션에서 디바이스 ID를 마우스 오른쪽 단추로 클릭합니다.
1. **디바이스 연결 문자열 복사** 를 선택합니다.

   연결 문자열이 클립보드에 복사됩니다.

오른쪽 클릭 메뉴에서 **디바이스 정보 가져오기** 를 선택하여 출력 창에서 연결 문자열을 비롯한 모든 디바이스 정보를 볼 수도 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLI를 사용하여 IoT Edge 디바이스 보기

[az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) 명령을 사용하여 IoT 허브의 모든 디바이스를 볼 수 있습니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true** 로 설정됩니다.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Azure CLI를 사용하여 연결 문자열 검색

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다. [Az iot hub device-identity connection-string show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) 명령을 사용 하 여 단일 장치에 대 한 연결 문자열을 반환 합니다.

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>`connection-string show`이 명령은 Azure IoT 확장의 버전 0.9.8에서 도입 되었으며 사용 되지 않는 명령을 대체 합니다 `show-connection-string` . 이 명령을 실행 하는 동안 오류가 발생 하면 확장 버전이 0.9.8 이상으로 업데이트 되었는지 확인 합니다. 자세한 내용과 최신 업데이트에 대 한 자세한 내용은 [Microsoft Azure IoT extension for Azure CLI](https://github.com/Azure/azure-iot-cli-extension)를 참조 하세요.

`device-id` 매개 변수의 값은 대소문자를 구분합니다.

장치에서 사용할 연결 문자열을 복사 하는 경우 연결 문자열 주위에 따옴표를 포함 하지 마십시오.

---

## <a name="next-steps"></a>다음 단계

이제 IoT Hub에 등록 된 장치가 있으므로 장치에 IoT Edge 런타임을 설치 하 고 프로 비전 할 준비가 되었습니다.

* [Linux 용 Azure IoT Edge 설치 또는 제거](how-to-install-iot-edge.md)
* [Windows 용 Azure IoT Edge 설치 또는 제거](how-to-install-iot-edge-windows-on-windows.md)