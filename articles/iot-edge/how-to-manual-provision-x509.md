---
title: X.509 인증서를 사용 하 여 프로 비전-Azure IoT Edge | Microsoft Docs
description: 설치 후 장치 id 인증서를 사용 하 여 IoT Edge 장치를 프로 비전 하 고 IoT Hub에 인증 합니다.
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: abb3aa9ca7c9697fef1cf456964154249f0d69f3
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913979"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>X.509 인증서 인증을 사용 하 여 Azure IoT Edge 장치 설정

이 문서에서는 IoT Hub에 새 IoT Edge 장치를 등록 하 고 x.509 인증서를 사용 하 여 인증 하도록 장치를 구성 하는 단계를 제공 합니다.

이 문서의 단계에서는 각 장치를 IoT hub에 수동으로 연결 하는 수동 프로 비전 이라는 프로세스를 안내 합니다. 대안은 프로 비전 할 장치가 많은 경우 도움이 되는 IoT Hub Device Provisioning Service를 사용 하는 자동 프로 비전입니다.

<!--TODO: Add auto-provision info/links-->

수동 프로 비전을 위해 IoT Edge 장치를 인증 하는 두 가지 옵션이 있습니다.

* **대칭 키** : IoT Hub에서 새 장치 id를 만들면 서비스에서 두 개의 키를 만듭니다. 장치에 키 중 하나를 추가 하 고 인증할 때 IoT Hub 키를 제공 합니다.

  이 인증 방법은 시작 하기 빠르지만 안전 하지 않습니다.

* **X.509 자체 서명** : 두 개의 x.509 id 인증서를 만들어 장치에 저장 합니다. IoT Hub에서 새 장치 id를 만들 때 두 인증서의 지문을 모두 제공 합니다. 장치는 IoT Hub을 인증 하는 경우 인증서를 제공 하 고, IoT Hub 지문이 일치 하는지 확인할 수 있습니다.

  이 인증 방법은 더 안전 하며 프로덕션 시나리오에 권장 됩니다.

이 문서에서는 x.509 인증서 인증을 사용 하 여 등록 및 프로 비전 프로세스를 안내 합니다. 대칭 키를 사용 하 여 장치를 설정 하는 방법을 알아보려면 [대칭 키 인증을 사용 하 여 Azure IoT Edge 장치 설정](how-to-manual-provision-symmetric-key.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 수행 하기 전에 IoT Edge 런타임이 설치 된 장치가 있어야 합니다. 그렇지 않으면 [Azure IoT Edge 런타임 설치 또는 제거](how-to-install-iot-edge.md)의 단계를 따릅니다.

X.509 인증서를 사용 하 여 수동으로 프로 비전 하려면 IoT Edge 버전 1.0.10 이상이 필요 합니다.

## <a name="create-certificates-and-thumbprints"></a>인증서 및 지문 만들기

장치 id 인증서는 신뢰의 인증서 체인을 통해 상위 x.509 CA (인증 기관) 인증서에 연결 하는 리프 인증서입니다. 장치 id 인증서에는 장치를 IoT hub에 포함 하려는 장치 ID로 설정 된 CN (일반 이름)이 있어야 합니다.

장치 id 인증서는 IoT Edge 장치를 프로 비전 하 고 Azure IoT Hub 장치를 인증 하는 데만 사용 됩니다. 이러한 인증서는 IoT Edge 장치가 모듈에 제공 하는 CA 인증서 또는 확인을 위한 리프 장치에 대 한 것과 달리 인증서에 서명 하지 않습니다. 자세한 내용은 [Azure IoT Edge 인증서 사용 세부](iot-edge-certs.md)정보를 참조 하세요.

장치 id 인증서를 만든 후에는 인증서의 공개 부분을 포함 하는 .cer 또는. i d 파일, 인증서의 개인 키가 포함 된 .cer 또는. d i d 파일의 두 파일이 있어야 합니다.

X.509를 사용한 수동 프로 비전에는 다음 파일이 필요 합니다.

* 두 개의 장치 id 인증서 및 개인 키 인증서 집합 하나의 인증서/키 파일 집합이 IoT Edge 런타임에 제공 됩니다.
* 장치 id 인증서에서 가져온 지문입니다. 지문 값은 sha-1 해시의 경우 40-16 진수 문자이 고, SHA-256 해시의 경우 64입니다. 장치 등록 시 IoT Hub 하기 위해 두 지문이 모두 제공 됩니다.

사용할 수 있는 인증서가 없는 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다. 이 문서의 지침에 따라 인증서 생성 스크립트를 설정 하 고 루트 CA 인증서를 만든 다음 두 개의 IoT Edge 장치 id 인증서를 만듭니다.

인증서에서 지문을 검색 하는 한 가지 방법은 다음 openssl 명령을 사용 하는 것입니다.

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-a-new-device"></a>새 장치 등록

IoT Hub에 연결 하는 모든 장치에는 클라우드-장치 또는 장치-클라우드 통신을 추적 하는 데 사용 되는 장치 ID가 있습니다. IoT hub 호스트 이름, 장치 ID 및 장치에서 IoT Hub을 인증 하는 데 사용 하는 정보를 포함 하는 연결 정보를 사용 하 여 장치를 구성 합니다.

X.509 인증서 인증의 경우이 정보는 장치 id 인증서에서 가져온 *지문* 형태로 제공 됩니다. 이러한 지문을 장치 등록 시 IoT Hub에 제공 되므로 서비스가 연결 될 때 장치를 인식할 수 있습니다.

여러 도구를 사용 하 여 IoT Hub에 새 IoT Edge 장치를 등록 하 고 해당 인증서 지문을 업로드할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Azure Portal의 필수 조건

Azure 구독의 무료 또는 표준 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Azure Portal에서 IoT Edge 디바이스 만들기

Azure Portal의 IoT Hub에서 IoT Edge 디바이스는 에지를 사용할 수 없는 IOT 디바이스와 별도로 만들어지고 관리됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택한 다음 **IoT Edge 장치 추가** 를 선택 합니다.

   ![Azure Portal에서 IoT Edge 장치 추가](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. **장치 만들기** 페이지에서 다음 정보를 제공 합니다.

   * 설명 장치 ID를 만듭니다. 다음 섹션에서 사용할 것 처럼이 장치 ID를 기록해 둡니다.
   * 인증 유형으로 **X.509 자체 서명** 을 선택합니다.
   * 기본 및 보조 id 인증서 지문을 제공 합니다. 지문 값은 sha-1 해시의 경우 40-16 진수 문자이 고, SHA-256 해시의 경우 64입니다.

1. **저장** 을 선택합니다.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Azure Portal에서 IoT Edge 디바이스 보기

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

![IoT Hub에서 모든 IoT Edge 디바이스 보기](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI의 필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md)
* 사용자 환경의 [Azure CLI](/cli/azure/install-azure-cli). Azure CLI 버전이 2.0.70 이상이어야 합니다. `az --version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다.
* [Azure CLI용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Azure CLI에서 IoT Edge 디바이스 만들기

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

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLI를 사용하여 IoT Edge 디바이스 보기

[az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) 명령을 사용하여 IoT 허브의 모든 디바이스를 볼 수 있습니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

`--edge-enabled`IoT hub에서 플래그를 추가 하거나 `--ee` IoT Edge 장치만 나열 합니다.

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true** 로 설정됩니다.

--- 

## <a name="configure-an-iot-edge-device"></a>IoT Edge 디바이스 구성

IoT Edge 장치에 IoT Hub의 id가 있으면 해당 클라우드 id 뿐만 아니라 해당 id 인증서를 사용 하 여 장치를 구성 해야 합니다.

Linux 장치에서는 config.xml 파일을 편집 하 여이 정보를 제공 합니다. Windows 장치에서 PowerShell 스크립트를 실행 하 여이 정보를 제공 합니다.

# <a name="linux"></a>[Linux](#tab/linux)

1. IoT Edge 장치에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 파일의 프로 비전 구성 섹션을 찾습니다. 

1. **연결 문자열 섹션을 사용 하 여 수동 프로 비전 구성을** 주석 처리 합니다.

1. **X.509 id 인증서 섹션을 사용 하 여 수동 프로 비전 구성** 의 주석 처리를 제거 합니다. **프로 비전:** 줄에 앞에 공백이 없고 중첩 된 항목이 두 개의 공백으로 들여쓰기 되는지 확인 합니다.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. 다음 필드를 업데이트 합니다.

   * **iothub_hostname** : 장치가 연결 될 IoT hub의 호스트 이름입니다. 예: `{IoT hub name}.azure-devices.net`.
   * **device_id** : 장치를 등록할 때 제공한 id입니다.
   * **identity_cert** : 장치의 id 인증서에 대 한 URI입니다. 예: `file:///path/identity_certificate.pem`.
   * **identity_pk** : 제공 된 id 인증서의 개인 키 파일에 대 한 URI입니다. 예: `file:///path/identity_key.pem`.

1. 파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

1. 구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. IoT Edge 장치에서 관리자 권한으로 PowerShell을 실행 합니다.

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 명령을 사용 하 여 컴퓨터에서 IoT Edge 런타임을 구성 합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Linux 컨테이너를 사용 하는 경우 `-ContainerOs` 플래그에 매개 변수를 추가 합니다. 이전에 실행 한 명령을 사용 하 여 선택한 컨테이너 옵션과 일치 해야 `Deploy-IoTEdge` 합니다.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * 오프 라인 또는 특정 버전 설치를 위해 IoTEdgeSecurityDaemon.ps1 스크립트를 장치에 다운로드 한 경우 스크립트의 로컬 복사본을 참조 해야 합니다.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 메시지가 표시되면 다음 정보를 제공합니다.

   * **IotHubHostName** : 장치가 연결 될 IoT hub의 호스트 이름입니다. 예: `{IoT hub name}.azure-devices.net`.
   * **DeviceId** : 장치를 등록할 때 제공한 ID입니다.
   * **X509IdentityCertificate** : 장치에서 id 인증서의 절대 경로입니다. 예: `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey** : 제공 된 id 인증서의 개인 키 파일에 대 한 절대 경로입니다. 예: `C:\path\identity_key.pem`.

수동으로 장치를 프로 비전 할 때 추가 매개 변수를 사용 하 여 다음을 포함 하 여 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 특정 edgeAgent 컨테이너 이미지를 선언 하 고 개인 레지스트리에 있는 경우 자격 증명을 제공 합니다.

이러한 추가 매개 변수에 대 한 자세한 내용은 [Windows의 IoT Edge에 대 한 PowerShell 스크립트](reference-windows-scripts.md)를 참조 하세요.

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>다음 단계

장치에 모듈을 배포 하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md) 를 계속 진행 하세요.