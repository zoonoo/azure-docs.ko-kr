---
title: 다운스트림 디바이스 인증 - Azure IoT Edge | Microsoft Docs
description: 다운스트림 디바이스 또는 리프 디바이스를 IoT Hub에 인증하고 Azure IoT Edge 게이트웨이 디바이스를 통해 연결을 라우팅하는 방법입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a9d2116062dc45f3602bf5ee0efba31ad815c0c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447850"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Azure IoT Hub에 다운스트림 디바이스 인증

투명 게이트웨이 시나리오에서 다운스트림 디바이스(리프 디바이스 또는 자식 디바이스라고도 함)는 다른 디바이스와 마찬가지로 IoT Hub ID가 필요합니다. 이 문서에서는 다운스트림 디바이스를 IoT Hub에 인증하는 옵션을 소개하고 게이트웨이 연결을 선언하는 방법을 보여 줍니다.

성공적인 투명 게이트웨이 연결을 설정하기 위한 세 가지 일반적인 단계가 있습니다. 이 문서에서는 두 번째 단계에 대해 설명합니다.

1. 다운스트림 장치를 안전 하 게 연결할 수 있도록 게이트웨이 장치를 서버로 구성 합니다. 다운스트림 장치에서 메시지를 수신 하 고 적절 한 대상으로 라우팅하는 게이트웨이를 설정 합니다. 자세한 내용은 [투명 게이트웨이로 작동하도록 IoT Edge 디바이스 구성](how-to-create-transparent-gateway.md)을 참조하세요.
2. **IoT Hub를 사용 하 여 인증할 수 있도록 다운스트림 장치에 대 한 장치 id를 만듭니다. 게이트웨이 장치를 통해 메시지를 보내도록 다운스트림 장치를 구성 합니다.**
3. 다운스트림 장치를 게이트웨이 장치에 연결 하 고 메시지 보내기를 시작 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

다운스트림 디바이스는 대칭 키(공유 액세스 키라고도 함), X.509 자체 서명된 인증서, X.509 CA(인증 기관) 서명된 인증서 등 세 가지 방법 중 하나를 사용하여 IoT Hub에서 인증할 수 있습니다. 인증 단계는 IoT Hub를 사용하여 비-IoT Edge 디바이스를 설정하는 데 사용되는 단계와 비슷하지만, 게이트웨이 관계를 선언하는 데 있어 약간의 차이가 있습니다.

이 문서의 단계는 수동 장치 프로 비전을 보여 줍니다. Azure IoT Hub 장치 프로 비전 서비스 (DPS)를 사용 하는 자동 프로 비전 다운스트림 장치는 지원 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

[투명 게이트웨이로 작동하도록 IoT Edge 디바이스 구성](how-to-create-transparent-gateway.md)의 단계를 완료합니다.

X.509 인증을 사용 하는 경우 다운스트림 장치에 대 한 인증서를 생성 합니다. 불투명 게이트웨이 문서에 사용한 것과 동일한 루트 CA 인증서 및 인증서 생성 스크립트를 다시 사용할 수 있도록 합니다.

이 문서에서는 여러 위치에서 게이트웨이 호스트 이름을 참조합니다. 게이트웨이 호스트 이름은 IoT Edge 게이트웨이 디바이스에서 config.yaml 파일의 **hostname** 매개 변수에 선언됩니다. 그리고 다운스트림 디바이스의 연결 문자열에서 참조됩니다. 다운스트림 장치에서 DNS 또는 호스트 파일 항목을 사용 하 여 게이트웨이 호스트 이름을 IP 주소로 확인할 수 있어야 합니다.

## <a name="register-device-with-iot-hub"></a>IoT Hub를 사용 하 여 장치 등록

다운스트림 장치에서 IoT Hub 인증 하는 방법을 선택 합니다.

* [대칭 키 인증](#symmetric-key-authentication): IoT Hub 다운스트림 장치에 배치 하는 키를 만듭니다. 장치가 인증 되 면 IoT Hub는 두 키가 일치 하는지 확인 합니다. 대칭 키 인증을 사용 하기 위해 추가 인증서를 만들 필요가 없습니다.
* [X.509 자체 서명 인증](#x509-self-signed-authentication): 장치 x.509 인증서의 지문을 IoT Hub와 공유 하므로 지문 인증이 라고도 합니다.
* [X.509 CA 서명 인증](#x509-ca-signed-authentication): IoT Hub에 루트 ca 인증서를 업로드 합니다. 장치에서 인증을 위해 x.509 인증서를 제공 하는 경우 IoT Hub는 동일한 루트 CA 인증서로 서명 된 신뢰 체인에 속하는지 확인 합니다.

이 세 가지 방법 중 하나를 사용 하 여 장치를 등록 한 후에는 다음 섹션으로 이동 하 여 다운스트림 장치에 대 한 [연결 문자열을 검색 하 고 수정](#retrieve-and-modify-connection-string) 합니다.

### <a name="symmetric-key-authentication"></a>대칭 키 인증

대칭 키 인증 또는 공유 액세스 키 인증은 IoT Hub를 사용하여 인증하는 가장 간단한 방법입니다. 대칭 키 인증에서는 base64 키가 IoT Hub의 IoT 디바이스 ID와 연결됩니다. 디바이스가 IoT Hub에 연결할 때 제시할 수 있도록 IoT 애플리케이션에 이 키를 포함시킵니다.

Visual Studio Code용 Azure Portal, Azure CLI 또는 IoT 확장을 사용하여 IoT 허브에 새 IoT 디바이스를 추가합니다. 다운스트림 디바이스는 IoT Hub에서 IoT Edge 디바이스가 아닌 일반 IoT 디바이스로 식별해야 합니다.

새 디바이스 ID를 만들 때 다음 정보를 제공합니다.

* 디바이스의 ID를 만듭니다.

* 인증 유형으로 **대칭 키**를 선택합니다.

* 필요에 따라 **부모 디바이스 설정**을 선택하고 이 다운스트림 디바이스가 통과할 IoT Edge 게이트웨이 디바이스를 선택합니다. 이 단계는 대칭 키 인증 시 선택 사항이지만, 부모 디바이스를 설정하면 다운스트림 디바이스에 [오프라인 기능](offline-capabilities.md)을 사용할 수 있으므로 권장됩니다. 언제든지 디바이스 세부 정보를 업데이트하여 나중에 부모를 추가하거나 변경할 수 있습니다.

   ![포털에서 대칭 키 인증을 사용하여 디바이스 ID 만들기](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

[Azure CLI에 대 한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 을 사용 하 여 동일한 작업을 완료할 수도 있습니다. 다음 예제에서는 대칭 키 인증을 사용하여 새 IoT 디바이스를 만들고 부모 디바이스를 할당합니다.

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

디바이스 만들기 및 부모/자식 관리를 위한 Azure CLI 명령에 대한 자세한 내용은 [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) 명령에 대한 참조 콘텐츠를 참조하세요.

그런 다음 디바이스에서 해당 게이트웨이를 통한 연결을 인식하도록 [연결 문자열을 검색 및 수정](#retrieve-and-modify-connection-string)합니다.

### <a name="x509-self-signed-authentication"></a>X.509 자체 서명 된 인증

X.509 자체 서명 된 인증의 경우 (지문 인증이 라고도 함) 다운스트림 장치에 저장할 인증서를 만들어야 합니다. 이러한 인증서에는 인증을 위해 IoT Hub와 공유하는 지문이 있습니다.

1. CA 인증서를 사용하여 다운스트림 디바이스에 대해 두 개의 디바이스 인증서(기본 및 보조)를 만듭니다.

   X.509 인증서를 만들 인증 기관이 없는 경우 IoT Edge 데모 인증서 스크립트를 사용 하 여 [다운스트림 장치 인증서를 만들](how-to-create-test-certificates.md#create-downstream-device-certificates)수 있습니다. 자체 서명 된 인증서를 만드는 단계를 수행 합니다. 게이트웨이 장치에 대 한 인증서를 생성 한 동일한 루트 CA 인증서를 사용 합니다.

   사용자 고유의 인증서를 만드는 경우 장치 인증서의 주체 이름이 Azure IoT Hub에서 IoT 장치를 등록할 때 사용 하는 장치 ID로 설정 되어 있는지 확인 합니다. 이 설정은 인증에 필요합니다.

2. 16진수 문자 40개로 구성된 문자열인 각 인증서에서 SHA1 지문(IoT Hub 인터페이스에서 지문이라고 함)을 검색합니다. 다음 openssl 명령을 사용하여 인증서를 확인하고 지문을 찾습니다.

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   이 명령을 기본 인증서 및 보조 인증서에 대해 한 번씩 두 번 실행합니다. 자체 서명된 X.509 인증서를 사용하여 새 IoT 디바이스를 등록할 때 두 인증서의 지문을 제공합니다.

3. Azure Portal에서 IoT 허브로 이동하고 다음 값을 사용하여 새 IoT 디바이스 ID를 만듭니다.

   * 디바이스 인증서의 주체 이름과 일치하는 **디바이스 ID**를 제공합니다.
   * 인증 유형으로 **X.509 자체 서명**을 선택합니다.
   * 디바이스의 기본 인증서 및 보조 인증서에서 복사한 16진수 문자열을 붙여넣습니다.
   * **부모 디바이스 설정**을 선택하고 이 다운스트림 디바이스가 통과할 IoT Edge 게이트웨이 디바이스를 선택합니다. 부모 디바이스는 다운스트림 디바이스의 X.509 인증에 필요합니다.

   ![포털에서 X.509 자체 서명된 인증을 사용하여 디바이스 ID 만들기](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 기본 및 보조 장치 인증서와 해당 키를 모두 다운스트림 장치의 임의의 위치에 복사 합니다. 또한 게이트웨이 디바이스 인증서와 다운스트림 디바이스 인증서를 모두 생성한 공유 루트 CA 인증서의 복사본을 이동합니다.

   IoT Hub에 연결 하는 다운스트림 장치의 모든 응용 프로그램에서 이러한 인증서 파일을 참조 합니다. [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 또는 [보안 복사 프로토콜](https://www.ssh.com/ssh/scp/) 같은 기능을 사용하여 인증서 파일을 이동할 수 있습니다.

5. 선호하는 언어에 따라 IoT 애플리케이션에서 X.509 인증서를 참조할 수 있는 방법에 대한 샘플을 검토합니다.

   * C#: [Azure IoT Hub의 X.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

또한 [Azure CLI 용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 을 사용 하 여 동일한 장치 만들기 작업을 완료할 수 있습니다. 다음 예제에서는 X.509 자체 서명 인증을 사용하여 새 IoT 디바이스를 만들고 부모 디바이스를 할당합니다.

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

디바이스 만들기, 인증서 생성 및 부모/자식 관리를 위한 Azure CLI 명령에 대한 자세한 내용은 [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) 명령에 대한 참조 콘텐츠를 참조하세요.

그런 다음 디바이스에서 해당 게이트웨이를 통한 연결을 인식하도록 [연결 문자열을 검색 및 수정](#retrieve-and-modify-connection-string)합니다.

### <a name="x509-ca-signed-authentication"></a>X.509 CA 서명 인증

X.509 CA (인증 기관) 서명 된 인증의 경우 다운스트림 장치에 대 한 인증서에 서명 하는 데 사용 하는 IoT Hub에 등록 된 루트 CA 인증서가 필요 합니다. 루트 CA 인증서 또는 해당 중간 인증서에 의해 발급된 인증서를 사용하는 모든 디바이스는 인증이 허용됩니다.

이 섹션은 IoT Hub 문서 [Azure IoT Hub에서 X.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md)에 설명된 지침을 기반으로 합니다.

1. CA 인증서를 사용하여 다운스트림 디바이스에 대해 두 개의 디바이스 인증서(기본 및 보조)를 만듭니다.

   X.509 인증서를 만들 인증 기관이 없는 경우 IoT Edge 데모 인증서 스크립트를 사용 하 여 [다운스트림 장치 인증서를 만들](how-to-create-test-certificates.md#create-downstream-device-certificates)수 있습니다. CA 서명 인증서를 만드는 단계를 수행 합니다. 게이트웨이 장치에 대 한 인증서를 생성 한 동일한 루트 CA 인증서를 사용 합니다.

2. Azure IoT Hub에서 X.509 보안 설정의 [IoT 허브에 X.509 CA 인증서 등록](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) 섹션에 설명된 지침을 따릅니다. 이 섹션에서는 다음 단계를 수행합니다.

   1. 루트 CA 인증서를 업로드합니다. 데모 인증서를 사용 하는 경우 루트 CA는 ** \<path> /certs/azure-iot-test-only.root.ca.cert.pem**입니다.

   2. 해당 루트 CA 인증서를 소유하고 있는지 확인합니다.

3. Azure IoT Hub에서 X.509 보안 설정의 [IoT 허브용 X.509 디바이스 만들기](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) 섹션에 설명된 지침을 따릅니다. 이 섹션에서는 다음 단계를 수행합니다.

   1. 새 디바이스를 추가합니다. **디바이스 ID**에 소문자 이름을 입력하고 인증 유형 **X.509 CA 서명**을 선택합니다.

   2. 부모 디바이스를 설정합니다. 다운스트림 디바이스의 경우 **부모 디바이스 설정**을 선택하고 IoT Hub에 대한 연결을 제공하는 IoT Edge 게이트웨이 디바이스를 선택합니다.

4. 다운스트림 디바이스에 대한 인증서 체인을 만듭니다. IoT Hub에 업로드한 것과 동일한 루트 CA 인증서를 사용하여 이 체인을 만듭니다. 포털에서 디바이스 ID에 제공한 것과 동일한 소문자 디바이스 ID를 사용합니다.

5. 디바이스 인증서 및 키를 다운스트림 디바이스에서 임의의 위치에 복사합니다. 또한 게이트웨이 디바이스 인증서와 다운스트림 디바이스 인증서를 모두 생성한 공유 루트 CA 인증서의 복사본을 이동합니다.

   IoT Hub에 연결 하는 다운스트림 장치의 모든 응용 프로그램에서 이러한 파일을 참조 합니다. [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 또는 [보안 복사 프로토콜](https://www.ssh.com/ssh/scp/) 같은 기능을 사용하여 인증서 파일을 이동할 수 있습니다.

6. 선호하는 언어에 따라 IoT 애플리케이션에서 X.509 인증서를 참조할 수 있는 방법에 대한 샘플을 검토합니다.

   * C#: [Azure IoT Hub의 X.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

또한 [Azure CLI 용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 을 사용 하 여 동일한 장치 만들기 작업을 완료할 수 있습니다. 다음 예제에서는 X.509 CA 서명 인증을 사용하여 새 IoT 디바이스를 만들고 부모 디바이스를 할당합니다.

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

자세한 내용은 [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) 명령에 대한 Azure CLI 참조 콘텐츠를 참조하세요.

그런 다음 디바이스에서 해당 게이트웨이를 통한 연결을 인식하도록 [연결 문자열을 검색 및 수정](#retrieve-and-modify-connection-string)합니다.

## <a name="retrieve-and-modify-connection-string"></a>연결 문자열 검색 및 수정

포털에서 IoT 디바이스 ID를 만든 후에는 기본 또는 보조 키를 검색할 수 있습니다. 이러한 키 중 하나는 애플리케이션에서 IoT Hub와 통신하는 데 사용하는 연결 문자열에 포함해야 합니다. 대칭 키 인증의 경우 사용자 편의를 위해 IoT Hub가 디바이스 세부 정보에 완전히 구성된 연결 문자열을 제공합니다. 게이트웨이 디바이스에 대한 추가 정보를 연결 문자열에 추가해야 합니다.

다운스트림 디바이스의 연결 문자열에는 다음 구성 요소가 필요합니다.

* 디바이스가 연결하는 IoT 허브: `Hostname={iothub name}.azure-devices.net`
* 허브에 등록된 디바이스 ID: `DeviceID={device ID}`
* 인증 방법 (대칭 키 또는 x.509 인증서에 상관 없음)
  * 대칭 키 인증을 사용 하는 경우 기본 키 또는 보조 키를 제공 합니다. `SharedAccessKey={key}`
  * X.509 인증서 인증을 사용 하는 경우 플래그를 제공 합니다. `x509=true`
* 디바이스가 통과하는 게이트웨이 디바이스. IoT Edge 게이트웨이 디바이스의 config.yaml 파일 에서 **호스트 이름** 값을 제공합니다. `GatewayHostName={gateway hostname}`

최종적으로 전체 연결 문자열은 다음과 같습니다.

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

또는

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

이 다운스트림 디바이스에 부모/자식 관계를 설정한 경우 게이트웨이를 연결 호스트로 직접 호출하여 연결 문자열을 단순화할 수 있습니다. 부모/자식 관계는 X.509 인증에서는 필수이지만 대칭 키 인증의 경우 선택 사항입니다. 다음은 그 예입니다.

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

이 수정 된 연결 문자열은 투명 게이트웨이 시리즈의 다음 문서에 사용 됩니다.

## <a name="next-steps"></a>다음 단계

이 시점에서 IoT Edge 장치가 IoT hub에 등록 되 고 투명 게이트웨이로 구성 됩니다. IoT hub에 등록 하 고 게이트웨이 장치를 가리키는 다운스트림 장치도 있습니다.

이 문서의 단계에서는 다운스트림 장치를 설정 하 여 IoT Hub에 인증 합니다. 그런 다음 게이트웨이 장치를 신뢰 하 고 안전 하 게 연결 하도록 다운스트림 장치를 구성 해야 합니다. 투명 게이트웨이 시리즈의 다음 문서를 계속 진행 하 여 [다운스트림 장치를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)합니다.
