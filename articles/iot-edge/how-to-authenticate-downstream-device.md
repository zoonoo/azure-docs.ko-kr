---
title: 다운스트림 장치 인증-Azure IoT Edge | Microsoft Docs
description: IoT Hub에 대 한 다운스트림 장치 또는 리프 장치를 인증 하 고 Azure IoT Edge 게이트웨이 장치를 통해 연결을 라우팅하는 방법입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1634d7cd3dfe8d118e220fa8620ef6467c15ea2c
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69983015"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Azure IoT Hub에 다운스트림 디바이스 인증

투명 게이트웨이 시나리오에서 다운스트림 장치 (리프 장치 또는 자식 장치 라고도 함)는 다른 장치와 마찬가지로 IoT Hub id가 필요 합니다. 이 문서에서는 IoT Hub에 대해 다운스트림 장치를 인증 하는 옵션을 안내 하 고 게이트웨이 연결을 선언 하는 방법을 보여 줍니다.

성공적인 투명 게이트웨이 연결을 설정 하는 세 가지 일반적인 단계가 있습니다. 이 문서에서는 두 번째 단계에 대해 설명 합니다.

1. 게이트웨이 장치는 다운스트림 장치에 안전 하 게 연결 하 고, 다운스트림 장치에서 통신을 수신 하 고, 메시지를 적절 한 대상으로 라우팅할 수 있어야 합니다. 자세한 내용은 [투명 한 게이트웨이로 작동 하도록 IoT Edge 장치 구성](how-to-create-transparent-gateway.md)을 참조 하세요.
2. **다운스트림 장치는 IoT Hub을 사용 하 여 인증 하 고 게이트웨이 장치를 통해 통신 하는 것을 알 수 있도록 장치 id가 있어야 합니다.**
3. 다운스트림 장치는 게이트웨이 장치에 안전 하 게 연결할 수 있어야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

다운스트림 장치는 대칭 키 (공유 액세스 키 라고도 함), x.509 자체 서명 된 인증서 또는 x.509 CA (인증 기관) 서명 된 인증서의 세 가지 방법 중 하나를 사용 하 여 IoT Hub 인증할 수 있습니다. 인증 단계는 IoT Hub를 사용 하 여 비 IoT Edge 장치를 설정 하는 데 사용 되는 단계와 비슷하며,이는 게이트웨이 관계를 선언 하는 데 약간의 차이가 있습니다.

이 문서의 단계에서는 Azure IoT Hub 장치 프로 비전 서비스를 사용 하 여 자동으로 프로 비전 하는 것이 아니라 수동 장치 프로 비전을 보여 줍니다. 

## <a name="prerequisites"></a>필수 구성 요소

[투명 게이트웨이 역할을 하도록 IoT Edge 장치 구성](how-to-create-transparent-gateway.md)의 단계를 완료 합니다.

이 문서는 여러 지점에서 *게이트웨이 호스트 이름을* 참조 합니다. 게이트웨이 호스트 이름은 IoT Edge 게이트웨이 장치에서 config.xml 파일의 **hostname** 매개 변수에 선언 됩니다. 이 문서에서 인증서를 만드는 데 사용 되며 다운스트림 장치의 연결 문자열에서 참조 됩니다. IP 주소는 DNS 또는 호스트 파일 항목을 사용 하 여 게이트웨이 호스트 이름을 확인할 수 있어야 합니다.

## <a name="symmetric-key-authentication"></a>대칭 키 인증

대칭 키 인증 또는 공유 액세스 키 인증은 IoT Hub를 사용 하 여 인증 하는 가장 간단한 방법입니다. 대칭 키 인증을 사용 하 여 base64 키는 IoT Hub의 IoT 장치 ID와 연결 됩니다. 장치가 IoT Hub에 연결할 때 해당 키를 제공할 수 있도록 IoT 응용 프로그램에 해당 키를 포함 합니다. 

### <a name="create-the-device-identity"></a>장치 id 만들기 

Visual Studio Code에 대 한 Azure Portal, Azure CLI 또는 IoT 확장을 사용 하 여 IoT hub에 새 IoT 장치를 추가 합니다. 다운스트림 장치는 IoT Edge 장치가 아닌 일반 IoT 장치로 IoT Hub에서 식별 해야 합니다. 

새 장치 id를 만들 때 다음 정보를 제공 합니다. 

* 장치에 대 한 ID를 만듭니다.

* 인증 유형으로 **대칭 키** 를 선택 합니다. 

* 필요한 경우 **부모 장치를 설정** 하 고이 다운스트림 장치에서 연결할 IoT Edge 게이트웨이 장치를 선택 합니다. 이 단계는 대칭 키 인증의 경우 선택 사항 이지만 부모 장치를 설정 하면 다운스트림 장치에 [오프 라인 기능](offline-capabilities.md) 을 사용할 수 있으므로 권장 됩니다. 언제 든 지 장치 세부 정보를 업데이트 하 여 나중에 부모를 추가 하거나 변경할 수 있습니다. 

   ![포털에서 대칭 키 인증을 사용 하 여 장치 ID 만들기](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

[Azure CLI에 대 한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 을 사용 하 여 동일한 작업을 완료할 수 있습니다. 다음 예제에서는 대칭 키 인증을 사용 하 여 새 IoT 장치를 만들고 부모 장치를 할당 합니다. 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

장치 만들기와 부모/자식 관리를 위한 Azure CLI 명령에 대 한 자세한 내용은 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령에 대 한 참조 콘텐츠를 참조 하세요.

### <a name="connect-to-iot-hub-through-a-gateway"></a>게이트웨이를 통해 IoT Hub에 연결

동일한 프로세스는 대칭 키를 사용 하 여 IoT Hub에 대 한 일반 IoT 장치를 인증 하는 데에도 다운스트림 장치에 적용 됩니다. 유일한 차이점은 게이트웨이 장치에 대 한 포인터를 추가 하 여 연결을 라우팅하거나 오프 라인 시나리오에서 IoT Hub를 대신 하 여 인증을 처리 해야 한다는 것입니다. 

대칭 키 인증의 경우 장치에서 IoT Hub 인증을 위해 수행 해야 하는 추가 단계가 없습니다. 다운스트림 장치를 [Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)에 설명 된 대로 다운스트림 장치를 게이트웨이 장치에 연결할 수 있도록 여전히 인증서를 준비 해야 합니다.

포털에서 IoT 장치 id를 만든 후에는 기본 또는 보조 키를 검색할 수 있습니다. 이러한 키 중 하나를 IoT Hub와 통신 하는 모든 응용 프로그램에 포함 하는 연결 문자열에 포함 해야 합니다. 대칭 키 인증의 경우 사용자 편의를 위해 IoT Hub 장치 세부 정보에 완전히 구성 된 연결 문자열을 제공 합니다. 게이트웨이 장치에 대 한 추가 정보를 연결 문자열에 추가 해야 합니다. 

다운스트림 장치에 대 한 대칭 키 연결 문자열에는 다음 구성 요소가 필요 합니다. 

* 장치를 연결 하는 IoT hub:`Hostname={iothub name}.azure-devices.net`
* 허브에 등록 된 장치 ID:`DeviceID={device ID}`
* 기본 키 또는 보조 키:`SharedAccessKey={key}`
* 장치에서 연결 하는 게이트웨이 장치입니다. IoT Edge 게이트웨이 장치의 config.xml 파일에서 **호스트 이름** 값을 제공 합니다.`GatewayHostName={gateway hostname}`

전체 연결 문자열은 다음과 같습니다.

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

이 다운스트림 장치에 대 한 부모/자식 관계를 설정한 경우 게이트웨이를 연결 호스트로 직접 호출 하 여 연결 문자열을 단순화할 수 있습니다. 예: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509 인증 

X.509 인증서를 사용 하 여 IoT 장치를 인증 하는 방법에는 두 가지가 있습니다. 인증을 선택 하는 방법에 관계 없이 장치를 IoT Hub에 연결 하는 단계는 동일 합니다. 인증을 위해 자체 서명 된 인증서 또는 CA 서명 인증서를 선택한 다음 계속 IoT Hub에 연결 하는 방법을 알아봅니다. 

IoT Hub에서 x.509 인증을 사용 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요. 
* [X.509 CA 인증서를 사용 하는 장치 인증](../iot-hub/iot-hub-x509ca-overview.md)
* [IoT 업계의 x.509 CA 인증서에 대 한 개념 이해](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>X.509 자체 서명 된 인증서를 사용 하 여 장치 id 만들기

X.509 자체 서명 된 인증의 경우 (지문 인증이 라고도 함) IoT 장치에 새 인증서를 만들어야 합니다. 이러한 인증서에는 인증을 위해 IoT Hub와 공유 하는 지문이 있습니다. 

이 시나리오를 테스트 하는 가장 쉬운 방법은에서 인증서를 만드는 데 사용한 것과 동일한 컴퓨터를 사용 하 여 [투명 한 게이트웨이로 작동 하도록 IoT Edge 장치를 구성](how-to-create-transparent-gateway.md)하는 것입니다. IoT 장치 인증서를 만들려면 올바른 도구, 루트 CA 인증서 및 중간 CA 인증서를 사용 하 여 해당 컴퓨터를 이미 설정 해야 합니다. 나중에 다운스트림 장치에 최종 인증서와 해당 개인 키를 복사할 수 있습니다. 게이트웨이 문서의 단계를 수행 하 여 컴퓨터에 openssl를 설정 하 고, IoT Edge 리포지토리를 복제 하 여 인증서 생성 스크립트에 액세스 합니다. 그런 다음  **\<WRKDIR >** 호출 하는 작업 디렉터리를 만들어 인증서를 보관 합니다. 기본 인증서는 개발 및 테스트를 위한 것 이므로 최근 30 일입니다. 루트 CA 인증서와 중간 인증서를 만들어야 합니다. 

1. Bash 또는 PowerShell 창에서 작업 디렉터리로 이동 합니다. 

2. 다운스트림 장치에 대해 두 개의 인증서 (기본 및 보조)를 만듭니다. 장치 이름과 기본 또는 보조 레이블을 제공 합니다. 이 정보는 여러 장치에 대 한 인증서를 추적할 수 있도록 파일의 이름을 지정할 때 사용 됩니다. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. 40 16 진수 문자열의 각 인증서에서 SHA1 지문 (IoT Hub 인터페이스에서 지문 이라고 함)을 검색 합니다. 다음 openssl 명령을 사용 하 여 인증서를 확인 하 고 지문을 찾습니다.

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Azure Portal에서 IoT hub로 이동 하 고 다음 값을 사용 하 여 새 IoT 장치 id를 만듭니다. 

   * 인증 유형으로 **X.509 자체 서명** 을 선택 합니다.
   * 장치의 기본 인증서 및 보조 인증서에서 복사한 16 진수 문자열을 붙여 넣습니다.
   * **부모 장치 설정** 을 선택 하 고이 다운스트림 장치에서 연결할 IoT Edge 게이트웨이 장치를 선택 합니다. 부모 장치는 다운스트림 장치의 x.509 인증에 필요 합니다. 

   ![X.509 자체 서명 된 인증을 사용 하 여 포털에서 장치 ID 만들기](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. 다운스트림 장치의 임의 디렉터리에 다음 파일을 복사 합니다.

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   IoT Hub에 연결 하는 리프 장치 응용 프로그램에서 이러한 파일을 참조 합니다. [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 와 같은 서비스를 사용 하거나 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용 하 여 인증서 파일을 이동할 수 있습니다.

[Azure CLI에 대 한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 을 사용 하 여 동일한 장치 만들기 작업을 완료할 수 있습니다. 다음 예제에서는 x.509 자체 서명 된 인증을 사용 하 여 새 IoT 장치를 만들고 부모 장치를 할당 합니다. 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

장치 만들기, 인증서 생성, 부모 및 자식 관리를 위한 Azure CLI 명령에 대 한 자세한 내용은 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령에 대 한 참조 콘텐츠를 참조 하세요.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>X.509 CA 서명 인증서를 사용 하 여 장치 id 만들기

X.509 CA (인증 기관) 서명 된 인증의 경우 IoT 장치에 대 한 인증서에 서명 하는 데 사용 하는 IoT Hub에 등록 된 루트 CA 인증서가 필요 합니다. 루트 CA 인증서 또는 중간 인증서에 의해 발급 된 인증서를 사용 하는 모든 장치는 인증을 허용 합니다. 

이 섹션은 [Azure IoT Hub에서 x.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md)IoT Hub 문서에 설명 된 지침을 기반으로 합니다. 이 섹션의 단계에 따라 게이트웨이를 통해 연결 되는 다운스트림 장치를 설정 하는 데 사용할 값을 확인 합니다. 

이 시나리오를 테스트 하는 가장 쉬운 방법은에서 인증서를 만드는 데 사용한 것과 동일한 컴퓨터를 사용 하 여 [투명 한 게이트웨이로 작동 하도록 IoT Edge 장치를 구성](how-to-create-transparent-gateway.md)하는 것입니다. IoT 장치 인증서를 만들려면 올바른 도구, 루트 CA 인증서 및 중간 CA 인증서를 사용 하 여 해당 컴퓨터를 이미 설정 해야 합니다. 나중에 다운스트림 장치에 최종 인증서와 해당 개인 키를 복사할 수 있습니다. 게이트웨이 문서의 단계를 수행 하 여 컴퓨터에 openssl를 설정 하 고, IoT Edge 리포지토리를 복제 하 여 인증서 생성 스크립트에 액세스 합니다. 그런 다음  **\<WRKDIR >** 호출 하는 작업 디렉터리를 만들어 인증서를 보관 합니다. 기본 인증서는 개발 및 테스트를 위한 것 이므로 최근 30 일입니다. 루트 CA 인증서와 중간 인증서를 만들어야 합니다. 

1. *Azure iot hub에서 x.509 보안 설정*의 [IoT HUB에 X.509 CA 인증서 등록](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) 섹션의 지침을 따릅니다. 해당 섹션에서 다음 단계를 수행 합니다. 

   1. 루트 CA 인증서를 업로드 합니다. 투명 게이트웨이 문서에서 만든 인증서를 사용 하는 경우 루트 인증서 파일로  **\<WRKDIR >/certs/azure-iot-test-only.root.ca.cert.pem** 를 업로드 합니다. 
   2. 해당 루트 CA 인증서를 소유 하 고 있는지 확인 합니다. WRKDIR >에서 \<인증서 도구를 사용 하 여 소유권을 확인할 수 있습니다. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. *Azure iot hub에서 x.509 보안 설정*의 [IoT hub에 대 한 x.509 장치 만들기](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) 섹션의 지침을 따릅니다. 해당 섹션에서 다음 단계를 수행 합니다. 

   1. 새 장치를 추가 합니다. **장치 ID**에 대해 소문자 이름을 제공 하 고 인증 유형으로 **서명 된 x.509 CA**를 선택 합니다. 
   2. 부모 장치를 설정 합니다. 다운스트림 장치에서 **부모 장치 설정** 을 선택 하 고 IoT Hub 연결을 제공할 IoT Edge 게이트웨이 장치를 선택 합니다. 

3. 다운스트림 장치에 대 한 인증서 체인을 만듭니다. IoT Hub 위해 업로드 한 것과 동일한 루트 CA 인증서를 사용 하 여이 체인을 만듭니다. 포털에서 장치 id에 제공한 것과 동일한 소문자 장치 ID를 사용 합니다.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. 다운스트림 장치의 임의 디렉터리에 다음 파일을 복사 합니다. 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   IoT Hub에 연결 하는 리프 장치 응용 프로그램에서 이러한 파일을 참조 합니다. [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 와 같은 서비스를 사용 하거나 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용 하 여 인증서 파일을 이동할 수 있습니다.

[Azure CLI에 대 한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 을 사용 하 여 동일한 장치 만들기 작업을 완료할 수 있습니다. 다음 예제에서는 x.509 CA 서명 인증을 사용 하 여 새 IoT 장치를 만들고 부모 장치를 할당 합니다. 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

장치 만들기와 부모/자식 관리를 위한 Azure CLI 명령에 대 한 자세한 내용은 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령에 대 한 참조 콘텐츠를 참조 하세요.


### <a name="connect-to-iot-hub-through-a-gateway"></a>게이트웨이를 통해 IoT Hub에 연결

각 Azure IoT SDK는 x.509 인증을 약간 다르게 처리 합니다. 그러나 동일한 프로세스를 사용 하 여 x.509 인증서를 사용 IoT Hub 하 여 일반 IoT 장치를 인증 합니다 .이는 다운스트림 장치에도 적용 됩니다. 유일한 차이점은 게이트웨이 장치에 대 한 포인터를 추가 하 여 연결을 라우팅하거나 오프 라인 시나리오에서 IoT Hub를 대신 하 여 인증을 처리 해야 한다는 것입니다. 일반적으로 모든 IoT Hub 장치에 대해 동일한 x.509 인증 단계를 수행 하 고, 연결 문자열의 **호스트 이름** 값을 게이트웨이 장치의 호스트 이름으로 바꿀 수 있습니다. 

다음 섹션에서는 다양 한 SDK 언어에 대 한 몇 가지 예를 보여 줍니다. 

>[!IMPORTANT]
>다음 샘플에서는 IoT Hub Sdk에서 인증서를 사용 하 여 장치를 인증 하는 방법을 보여 줍니다. 프로덕션 배포에서는 개인 또는 SAS 키와 같은 모든 비밀을 하드웨어 보안 모듈 (HSM)에 저장 해야 합니다. 

#### <a name="net"></a>.NET

X.509 인증서를 사용 하 C# 여 IoT Hub을 인증 하는 프로그램의 예는 [Azure IoT Hub에서 x.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)을 참조 하세요. 이 샘플의 주요 줄 중 일부는 인증 프로세스를 보여 주기 위해 여기에 포함 되어 있습니다.

DeviceClient 인스턴스에 대 한 호스트 이름을 선언 하는 경우 IoT Edge 게이트웨이 장치의 호스트 이름을 사용 합니다. 호스트 이름은 게이트웨이 장치의 구성. yaml 파일에서 찾을 수 있습니다. 

IoT Edge git 리포지토리에서 제공 하는 테스트 인증서를 사용 하는 경우 인증서에 대 한 키는 **1234**입니다.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

X.509 인증서를 사용 하 여 IoT Hub을 인증 하는 C 프로그램의 예는 C IoT SDK의 [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) 샘플을 참조 하세요. 이 샘플의 주요 줄 중 일부는 인증 프로세스를 보여 주기 위해 여기에 포함 되어 있습니다.

다운스트림 장치에 대 한 연결 문자열을 정의 하는 경우 **hostname** 매개 변수에 대 한 IoT Edge 게이트웨이 장치의 호스트 이름을 사용 합니다. 호스트 이름은 게이트웨이 장치의 구성. yaml 파일에서 찾을 수 있습니다. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

X.509 인증서를 사용 하 여 IoT Hub을 인증 하는 node.js 프로그램의 예는 node.js IoT SDK의 [simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) 샘플을 참조 하세요. 이 샘플의 주요 줄 중 일부는 인증 프로세스를 보여 주기 위해 여기에 포함 되어 있습니다.

다운스트림 장치에 대 한 연결 문자열을 정의 하는 경우 **hostname** 매개 변수에 대 한 IoT Edge 게이트웨이 장치의 호스트 이름을 사용 합니다. 호스트 이름은 게이트웨이 장치의 구성. yaml 파일에서 찾을 수 있습니다. 

IoT Edge git 리포지토리에서 제공 하는 테스트 인증서를 사용 하는 경우 인증서에 대 한 키는 **1234**입니다.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

X.509 인증서를 사용 하 여 IoT Hub을 인증 하는 Python 프로그램의 예는 Java IoT SDK의 [iothub_client_sample_x509 py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) 샘플을 참조 하세요. 이 샘플의 주요 줄 중 일부는 인증 프로세스를 보여 주기 위해 여기에 포함 되어 있습니다.

다운스트림 장치에 대 한 연결 문자열을 정의 하는 경우 **hostname** 매개 변수에 대 한 IoT Edge 게이트웨이 장치의 호스트 이름을 사용 합니다. 호스트 이름은 게이트웨이 장치의 구성. yaml 파일에서 찾을 수 있습니다. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)


def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

X.509 인증서를 사용 하 여 IoT Hub을 인증 하는 Java 프로그램의 예는 Java IoT SDK의 [SendEventX509](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) 샘플을 참조 하세요. 이 샘플의 주요 줄 중 일부는 인증 프로세스를 보여 주기 위해 여기에 포함 되어 있습니다.

다운스트림 장치에 대 한 연결 문자열을 정의 하는 경우 **hostname** 매개 변수에 대 한 IoT Edge 게이트웨이 장치의 호스트 이름을 사용 합니다. 호스트 이름은 게이트웨이 장치의 구성. yaml 파일에서 찾을 수 있습니다. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>다음 단계

이 문서를 완료 하면 투명 게이트웨이 및 IoT hub에 등록 된 다운스트림 장치로 작동 하는 IoT Edge 장치가 있어야 합니다. 그런 다음 게이트웨이 장치를 신뢰 하 고 메시지를 보내도록 다운스트림 장치를 구성 해야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.
