---
title: 다운스트림 장치 인증-Azure IoT Edge | Microsoft Docs
description: IoT Hub에 대 한 다운스트림 장치 또는 리프 장치를 인증 하 고 Azure IoT Edge 게이트웨이 장치를 통해 연결을 라우팅하는 방법입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ca066729a81ff51d87c5d8063c94be86366811c
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548767"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Azure IoT Hub에 다운스트림 디바이스 인증

투명 게이트웨이 시나리오에서 다운스트림 장치 (리프 장치 또는 자식 장치 라고도 함)는 다른 장치와 마찬가지로 IoT Hub id가 필요 합니다. 이 문서에서는 IoT Hub에 대해 다운스트림 장치를 인증 하는 옵션을 안내 하 고 게이트웨이 연결을 선언 하는 방법을 보여 줍니다.

성공적인 투명 게이트웨이 연결을 설정 하는 세 가지 일반적인 단계가 있습니다. 이 문서에서는 두 번째 단계에 대해 설명 합니다.

1. 게이트웨이 장치는 다운스트림 장치에 안전 하 게 연결 하 고, 다운스트림 장치에서 통신을 수신 하 고, 메시지를 적절 한 대상으로 라우팅할 수 있어야 합니다. 자세한 내용은 [투명 한 게이트웨이로 작동 하도록 IoT Edge 장치 구성](how-to-create-transparent-gateway.md)을 참조 하세요.
2. **다운스트림 장치는 IoT Hub을 사용 하 여 인증 하 고 게이트웨이 장치를 통해 통신 하는 것을 알 수 있도록 장치 id가 있어야 합니다.**
3. 다운스트림 장치는 게이트웨이 장치에 안전 하 게 연결 해야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

다운스트림 장치는 대칭 키 (공유 액세스 키 라고도 함), x.509 자체 서명 된 인증서 또는 x.509 CA (인증 기관) 서명 된 인증서의 세 가지 방법 중 하나를 사용 하 여 IoT Hub 인증할 수 있습니다. 인증 단계는 IoT Hub를 사용 하 여 비 IoT Edge 장치를 설정 하는 데 사용 되는 단계와 비슷하며,이는 게이트웨이 관계를 선언 하는 데 약간의 차이가 있습니다.

이 문서의 단계에서는 DPS (Azure IoT Hub 장치 프로 비전 서비스)를 사용 하 여 자동으로 프로 비전 하는 것이 아니라 수동 장치 프로 비전을 보여 줍니다. DPS로 다운스트림 장치를 프로 비전 하는 것은 지원 되지 않습니다.

## <a name="prerequisites"></a>필수 조건

[투명 게이트웨이 역할을 하도록 IoT Edge 장치 구성](how-to-create-transparent-gateway.md)의 단계를 완료 합니다. 다운스트림 장치에 대해 x.509 인증을 사용 하는 경우 투명 게이트웨이 문서에서 설정한 것과 동일한 인증서 생성 스크립트를 사용 해야 합니다.

이 문서는 여러 지점에서 *게이트웨이 호스트 이름을* 참조 합니다. 게이트웨이 호스트 이름은 IoT Edge 게이트웨이 장치에서 config.xml 파일의 **hostname** 매개 변수에 선언 됩니다. 다운스트림 장치의 연결 문자열에서 참조 됩니다. IP 주소는 DNS 또는 호스트 파일 항목을 사용 하 여 게이트웨이 호스트 이름을 확인할 수 있어야 합니다.

## <a name="register-device-symmetric-key"></a>장치 등록 (대칭 키)

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
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

장치 만들기와 부모/자식 관리를 위한 Azure CLI 명령에 대 한 자세한 내용은 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령에 대 한 참조 콘텐츠를 참조 하세요.


그런 다음 장치에서 게이트웨이를 통해 연결을 인식 하도록 [연결 문자열을 검색 하 고 수정](#retrieve-and-modify-connection-string) 합니다.

## <a name="register-device-x509-self-signed"></a>장치 등록 (x.509 자체 서명 됨)

X.509 자체 서명 된 인증의 경우 (지문 인증이 라고도 함) IoT 장치에 새 인증서를 만들어야 합니다. 이러한 인증서에는 인증을 위해 IoT Hub와 공유 하는 지문이 있습니다.

X.509 인증서를 만들 인증 기관이 없는 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다. 다운스트림 장치에 대 한 테스트 인증서를 생성할 때 게이트웨이 장치에 대 한 인증서를 생성 한 동일한 루트 CA 인증서를 사용 합니다.

1. CA 인증서를 사용 하 여 다운스트림 장치에 대 한 두 개의 장치 인증서 (기본 및 보조)를 만듭니다.

   장치 인증서의 주체 이름은 Azure IoT Hub IoT 장치를 등록할 때 사용할 장치 ID로 설정 해야 합니다. 이 설정은 인증에 필요 합니다.

2. 40 16 진수 문자열의 각 인증서에서 SHA1 지문 (IoT Hub 인터페이스에서 지문 이라고 함)을 검색 합니다. 다음 openssl 명령을 사용 하 여 인증서를 확인 하 고 지문을 찾습니다.

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   기본 인증서에 대해 한 번, 보조 인증서의 경우이 명령을 두 번 실행 합니다. 자체 서명 된 x.509 인증서를 사용 하 여 새 IoT 장치를 등록할 때 두 인증서에 대해 지문을 제공 합니다.

3. Azure Portal에서 IoT hub로 이동 하 고 다음 값을 사용 하 여 새 IoT 장치 id를 만듭니다.

   * 장치 인증서의 주체 이름과 일치 하는 **장치 ID** 를 제공 합니다.
   * 인증 유형으로 **X.509 자체 서명** 을 선택 합니다.
   * 장치의 기본 인증서 및 보조 인증서에서 복사한 16 진수 문자열을 붙여 넣습니다.
   * **부모 장치 설정** 을 선택 하 고이 다운스트림 장치에서 연결할 IoT Edge 게이트웨이 장치를 선택 합니다. 부모 장치는 다운스트림 장치의 x.509 인증에 필요 합니다.

   ![X.509 자체 서명 된 인증을 사용 하 여 포털에서 장치 ID 만들기](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 장치 인증서 및 키를 다운스트림 장치의 임의의 위치에 복사 합니다. 또한 게이트웨이 장치 인증서와 다운스트림 장치 인증서를 모두 생성 한 공유 루트 CA 인증서의 복사본을 이동 합니다.

   IoT Hub에 연결 하는 리프 장치 응용 프로그램에서 이러한 파일을 참조 합니다. [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 와 같은 서비스를 사용 하거나 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용 하 여 인증서 파일을 이동할 수 있습니다.

5. 선호 하는 언어에 따라 IoT 응용 프로그램에서 x.509 인증서를 참조할 수 있는 방법에 대 한 샘플을 검토 합니다.

   * C#: [Azure IoT hub에서 x.509 보안을 설정 합니다](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates) .
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509 py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

[Azure CLI에 대 한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 을 사용 하 여 동일한 장치 만들기 작업을 완료할 수 있습니다. 다음 예제에서는 x.509 자체 서명 된 인증을 사용 하 여 새 IoT 장치를 만들고 부모 장치를 할당 합니다.

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

장치 만들기, 인증서 생성, 부모 및 자식 관리를 위한 Azure CLI 명령에 대 한 자세한 내용은 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령에 대 한 참조 콘텐츠를 참조 하세요.

그런 다음 장치에서 게이트웨이를 통해 연결을 인식 하도록 [연결 문자열을 검색 하 고 수정](#retrieve-and-modify-connection-string) 합니다.

## <a name="register-device-x509-ca-signed"></a>장치 등록 (x.509 CA 서명 됨)

X.509 CA (인증 기관) 서명 된 인증의 경우 IoT 장치에 대 한 인증서에 서명 하는 데 사용 하는 IoT Hub에 등록 된 루트 CA 인증서가 필요 합니다. 루트 CA 인증서 또는 중간 인증서에 의해 발급 된 인증서를 사용 하는 모든 장치는 인증을 허용 합니다.

이 섹션은 [Azure IoT Hub에서 x.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md)IoT Hub 문서에 설명 된 지침을 기반으로 합니다. 이 섹션의 단계에 따라 게이트웨이를 통해 연결 되는 다운스트림 장치를 설정 하는 데 사용할 값을 확인 합니다.

X.509 인증서를 만들 인증 기관이 없는 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다. 다운스트림 장치에 대 한 테스트 인증서를 생성할 때 게이트웨이 장치에 대 한 인증서를 생성 한 동일한 루트 CA 인증서를 사용 합니다.

1. *Azure iot hub에서 x.509 보안 설정*의 [IoT HUB에 X.509 CA 인증서 등록](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) 섹션의 지침을 따릅니다. 해당 섹션에서 다음 단계를 수행 합니다.

   1. 루트 CA 인증서를 업로드 합니다. 데모 인증서를 사용 하는 경우 루트 CA는 **/certs/azure-iot-test-only.root.ca.cert.pem > 경로를\<** 합니다.

   2. 해당 루트 CA 인증서를 소유 하 고 있는지 확인 합니다.

2. *Azure iot hub에서 x.509 보안 설정*의 [IoT hub에 대 한 x.509 장치 만들기](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) 섹션의 지침을 따릅니다. 해당 섹션에서 다음 단계를 수행 합니다.

   1. 새 장치를 추가 합니다. **장치 ID**에 대해 소문자 이름을 제공 하 고 인증 유형으로 **서명 된 x.509 CA**를 선택 합니다.
   2. 부모 장치를 설정 합니다. 다운스트림 장치에서 **부모 장치 설정** 을 선택 하 고 IoT Hub 연결을 제공할 IoT Edge 게이트웨이 장치를 선택 합니다.

3. 다운스트림 장치에 대 한 인증서 체인을 만듭니다. IoT Hub 위해 업로드 한 것과 동일한 루트 CA 인증서를 사용 하 여이 체인을 만듭니다. 포털에서 장치 id에 제공한 것과 동일한 소문자 장치 ID를 사용 합니다.

4. 장치 인증서 및 키를 다운스트림 장치의 임의의 위치에 복사 합니다. 또한 게이트웨이 장치 인증서와 다운스트림 장치 인증서를 모두 생성 한 공유 루트 CA 인증서의 복사본을 이동 합니다.

   IoT Hub에 연결 하는 리프 장치 응용 프로그램에서 이러한 파일을 참조 합니다. [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 와 같은 서비스를 사용 하거나 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용 하 여 인증서 파일을 이동할 수 있습니다.

5. 선호 하는 언어에 따라 IoT 응용 프로그램에서 x.509 인증서를 참조할 수 있는 방법에 대 한 샘플을 검토 합니다.

   * C#: [Azure IoT hub에서 x.509 보안을 설정 합니다](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates) .
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509 py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

[Azure CLI에 대 한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 을 사용 하 여 동일한 장치 만들기 작업을 완료할 수 있습니다. 다음 예제에서는 x.509 CA 서명 인증을 사용 하 여 새 IoT 장치를 만들고 부모 장치를 할당 합니다.

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

자세한 내용은 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령의 Azure CLI 참조 콘텐츠를 참조 하세요.

그런 다음 장치에서 게이트웨이를 통해 연결을 인식 하도록 [연결 문자열을 검색 하 고 수정](#retrieve-and-modify-connection-string) 합니다.

## <a name="retrieve-and-modify-connection-string"></a>연결 문자열 검색 및 수정

포털에서 IoT 장치 id를 만든 후에는 기본 또는 보조 키를 검색할 수 있습니다. 이러한 키 중 하나는 응용 프로그램에서 IoT Hub와 통신 하는 데 사용 하는 연결 문자열에 포함 해야 합니다. 대칭 키 인증의 경우 사용자 편의를 위해 IoT Hub 장치 세부 정보에 완전히 구성 된 연결 문자열을 제공 합니다. 게이트웨이 장치에 대 한 추가 정보를 연결 문자열에 추가 해야 합니다.

다운스트림 장치에 대 한 연결 문자열에는 다음 구성 요소가 필요 합니다.

* 장치를 연결 하는 IoT hub: `Hostname={iothub name}.azure-devices.net`
* 허브에 등록 된 장치 ID: `DeviceID={device ID}`
* 기본 또는 보조 키 중 하나: `SharedAccessKey={key}`
* 장치에서 연결 하는 게이트웨이 장치입니다. IoT Edge 게이트웨이 장치의 구성 파일에서 **호스트 이름** 값을 제공 합니다. yaml 파일: `GatewayHostName={gateway hostname}`

전체 연결 문자열은 다음과 같습니다.

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

이 다운스트림 장치에 대 한 부모/자식 관계를 설정한 경우 게이트웨이를 연결 호스트로 직접 호출 하 여 연결 문자열을 단순화할 수 있습니다. 부모/자식 관계는 x.509 인증에 필요 하지만 대칭 키 인증의 경우 선택 사항입니다. 예:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

이 시점에서 IoT Edge 장치가 등록 되어 게이트웨이로 구성 되어 있어야 합니다. 또한 다운스트림 IoT 장치를 등록 하 고 게이트웨이 장치를 가리킵니다. 최종 단계는 게이트웨이에 안전 하 게 연결할 수 있도록 다운스트림 장치에 인증서를 저장 하는 것입니다.

게이트웨이 시리즈의 다음 문서를 계속 진행 하 여 [다운스트림 장치를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)합니다.

## <a name="next-steps"></a>다음 단계

이 문서를 완료 하면 투명 게이트웨이 및 IoT hub에 등록 된 다운스트림 장치로 작동 하는 IoT Edge 장치가 있어야 합니다. 그런 다음 게이트웨이 장치를 신뢰 하 고 안전 하 게 연결 하도록 다운스트림 장치를 구성 해야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.
