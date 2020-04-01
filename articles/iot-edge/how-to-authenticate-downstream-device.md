---
title: 다운스트림 장치 인증 - Azure IoT 에지 | 마이크로 소프트 문서
description: 다운스트림 장치 또는 리프 디바이스를 IoT Hub로 인증하고 Azure IoT Edge 게이트웨이 장치를 통해 연결을 라우팅하는 방법
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 999204cf2fc8ce18b42f873b9d34af4e6c08052b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411507"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Azure IoT Hub에 다운스트림 디바이스 인증

투명한 게이트웨이 시나리오에서 다운스트림 장치(리프 장치 또는 자식 장치라고도 함)는 다른 장치와 마찬가지로 IoT Hub에 ID가 필요합니다. 이 문서에서는 IoT Hub에 다운스트림 장치를 인증하기 위한 옵션을 살펴보고 게이트웨이 연결을 선언하는 방법을 설명합니다.

성공적인 투명 게이트웨이 연결을 설정하는 세 가지 일반적인 단계가 있습니다. 이 문서에서는 두 번째 단계를 다룹니다.

1. 게이트웨이 장치는 다운스트림 장치에 안전하게 연결하고, 다운스트림 장치에서 통신을 수신하고, 메시지를 적절한 대상으로 라우팅할 수 있어야 합니다. 자세한 내용은 [투명한 게이트웨이 역할을 하는 IoT Edge 장치 구성을](how-to-create-transparent-gateway.md)참조하십시오.
2. **다운스트림 장치는 IoT Hub로 인증할 수 있고 게이트웨이 장치를 통해 통신할 수 있는 장치 ID가 있어야 합니다.**
3. 다운스트림 장치는 게이트웨이 장치에 안전하게 연결해야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

다운스트림 장치는 대칭 키(공유 액세스 키라고도 함), X.509 자체 서명 인증서 또는 Ca(인증서)의 세 가지 방법 중 하나를 사용하여 IoT Hub를 사용하여 인증할 수 있습니다. 인증 단계는 IoT Hub를 사용하여 IoT-Edge가 아닌 장치를 설정하는 데 사용되는 단계와 유사하며 게이트웨이 관계를 선언하는 데 는 약간의 차이가 있습니다.

이 문서의 단계는 Azure IoT Hub 장치 프로비저닝 서비스(DPS)를 통한 자동 프로비저닝이 아닌 수동 장치 프로비저닝을 보여 준다. DPS를 사용 하 고 다운스트림 장치를 프로비전 지원 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

투명한 게이트웨이 [역할을 할 IoT Edge 장치 구성의](how-to-create-transparent-gateway.md)단계를 완료합니다. 다운스트림 장치에 X.509 인증을 사용하는 경우 투명 게이트웨이 문서에서 설정한 것과 동일한 인증서 생성 스크립트를 사용해야 합니다.

이 문서에서는 여러 지점에서 *게이트웨이 호스트 이름을* 참조합니다. 게이트웨이 호스트 이름은 IoT Edge 게이트웨이 장치의 config.yaml 파일의 **호스트 이름** 매개 변수에 선언됩니다. 다운스트림 장치의 연결 문자열에서 언급됩니다. 게이트웨이 호스트 이름은 DNS 또는 호스트 파일 항목을 사용하여 IP 주소로 확인할 수 있어야 합니다.

## <a name="register-device-symmetric-key"></a>레지스터 장치(대칭 키)

대칭 키 인증 또는 공유 액세스 키 인증은 IoT Hub를 사용하여 인증하는 가장 간단한 방법입니다. 대칭 키 인증을 사용하면 base64 키가 IoT Hub의 IoT 장치 ID와 연결됩니다. IoT Hub에 연결할 때 장치가 표시할 수 있도록 IoT 응용 프로그램에 해당 키를 포함합니다.

### <a name="create-the-device-identity"></a>장치 ID 만들기

Azure 포털, Azure CLI 또는 Visual Studio 코드에 대한 IoT 확장을 사용하여 IoT 허브에 새 IoT 장치를 추가합니다. 다운스트림 장치는 IoT Hub에서 IoT Edge 장치가 아닌 일반 IoT 장치로 식별해야 합니다.

새 장치 ID를 만들 때 다음 정보를 제공합니다.

* 기기의 ID를 만듭니다.

* **대칭 키를** 인증 유형으로 선택합니다.

* 선택적으로 상위 **장치 설정을** 선택하고 이 다운스트림 장치가 연결하는 IoT Edge 게이트웨이 장치를 선택합니다. 이 단계는 대칭 키 인증에 대한 선택 사항이지만 상위 장치를 설정하면 다운스트림 장치에 대한 [오프라인 기능을](offline-capabilities.md) 사용할 수 있으므로 권장됩니다. 언제든지 장치 세부 정보를 업데이트하여 나중에 부모를 추가하거나 변경할 수 있습니다.

   ![포털에서 대칭 키 인증으로 장치 ID 만들기](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

[Azure CLI에 대한 IoT 확장을](https://github.com/Azure/azure-iot-cli-extension) 사용하여 동일한 작업을 완료할 수 있습니다. 다음 예제는 대칭 키 인증을 사용한 새 IoT 장치를 만들고 상위 장치를 할당합니다.

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

장치 만들기 및 부모/자식 관리를 위한 Azure CLI 명령에 대한 자세한 내용은 [az iot 허브 장치 ID](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령에 대한 참조 콘텐츠를 참조합니다.


그런 다음 장치가 게이트웨이를 통해 연결할 수 있도록 [연결 문자열을 검색하고 수정합니다.](#retrieve-and-modify-connection-string)

## <a name="register-device-x509-self-signed"></a>등록 장치(X.509 자체 서명)

지문 인증이라고도 하는 X.509 자체 서명 인증의 경우 IoT 장치에 배치할 새 인증서를 만들어야 합니다. 이러한 인증서에는 인증을 위해 IoT Hub와 공유하는 지문이 있습니다.

X.509 인증서를 만들 수 있는 인증서 권한이 없는 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다. 다운스트림 장치에 대한 테스트 인증서를 생성할 때 게이트웨이 장치에 대한 인증서를 생성한 것과 동일한 루트 CA 인증서를 사용합니다.

1. CA 인증서를 사용하여 다운스트림 장치에 대해 두 개의 장치 인증서(기본 및 보조 인증서)를 만듭니다.

   장치 인증서에는 Azure IoT Hub에서 IoT 장치를 등록할 때 사용할 장치 ID로 설정된 주체 이름이 있어야 합니다. 이 설정은 인증에 필요합니다.

2. 40 헥사데피만 문자 문자열인 각 인증서에서 SHA1 지문(IoT Hub 인터페이스의 지문이라고 함)을 검색합니다. 다음 openssa 명령을 사용하여 인증서를 보고 지문을 찾습니다.

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   이 명령을 두 번, 기본 인증서에 대해 한 번, 보조 인증서에 대해 한 번 실행합니다. 자체 서명된 X.509 인증서를 사용하여 새 IoT 장치를 등록할 때 두 인증서 모두에 대한 지문을 제공합니다.

3. Azure 포털에서 IoT 허브로 이동하여 다음 값을 사용하여 새 IoT 장치 ID를 만듭니다.

   * 장치 인증서의 제목 이름과 일치하는 **장치 ID를** 제공합니다.
   * 인증 유형으로 **X.509 자체 서명을** 선택합니다.
   * 장치의 기본 및 보조 인증서에서 복사한 육각형 문자열을 붙여넣습니다.
   * **상위 장치 설정을** 선택하고 이 다운스트림 장치가 연결할 IoT Edge 게이트웨이 장치를 선택합니다. 다운스트림 장치의 X.509 인증에는 상위 장치가 필요합니다.

   ![포털에서 X.509 자체 서명 인증으로 장치 ID 만들기](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 장치 인증서 및 키를 다운스트림 장치의 모든 위치에 복사합니다. 또한 게이트웨이 장치 인증서와 다운스트림 장치 인증서를 모두 생성한 공유 루트 CA 인증서의 복사본을 이동합니다.

   IoT Hub에 연결하는 리프 장치 응용 프로그램에서 이러한 파일을 참조합니다. [Azure Key Vault와](https://docs.microsoft.com/azure/key-vault) 같은 서비스 또는 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용하여 인증서 파일을 이동할 수 있습니다.

5. 선호하는 언어에 따라 IoT 애플리케이션에서 X.509 인증서를 참조할 수 있는 방법에 대한 샘플을 검토합니다.

   * C#: [Azure IoT 허브에서 X.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * 노드.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * 자바: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * 파이썬: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

[Azure CLI에 대한 IoT 확장을](https://github.com/Azure/azure-iot-cli-extension) 사용하여 동일한 장치 만들기 작업을 완료할 수 있습니다. 다음 예제는 X.509 자체 서명된 인증을 통해 새 IoT 장치를 만들고 상위 장치를 할당합니다.

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

장치 만들기, 인증서 생성 및 부모 및 자식 관리를 위한 Azure CLI 명령에 대한 자세한 내용은 [az iot 허브 장치 ID](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령에 대한 참조 콘텐츠를 참조하십시오.

그런 다음 장치가 게이트웨이를 통해 연결할 수 있도록 [연결 문자열을 검색하고 수정합니다.](#retrieve-and-modify-connection-string)

## <a name="register-device-x509-ca-signed"></a>등록 장치(X.509 CA 서명)

Ca(인증)에 서명한 X.509 인증 기관의 경우 IoT 장치에 대한 인증서에 서명하는 데 사용하는 IoT Hub에 등록된 루트 CA 인증서가 필요합니다. 루트 CA 인증서 또는 중간 인증서에 의해 문제가 된 인증서를 사용하는 모든 장치는 인증할 수 있습니다.

이 섹션에서는 [Azure IoT 허브에서 X.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md)문서에서 자세히 설명하는 지침을 기반으로 합니다. 이 섹션의 단계를 수행하여 게이트웨이를 통해 연결되는 다운스트림 장치를 설정하는 데 사용할 값을 알 수 있습니다.

X.509 인증서를 만들 수 있는 인증서 권한이 없는 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다. 다운스트림 장치에 대한 테스트 인증서를 생성할 때 게이트웨이 장치에 대한 인증서를 생성한 것과 동일한 루트 CA 인증서를 사용합니다.

1. Azure IoT 허브에서 *X.509 보안 설정의*IoT 허브 섹션에 [대한 등록 X.509 CA 인증서의](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) 지침을 따릅니다. 이 섹션에서는 다음 단계를 수행합니다.

   1. 루트 CA 인증서를 업로드합니다. 데모 인증서를 사용하는 경우 루트 CA는 ** \<경로>/certs/azure-iot-test-only.root.ca.cert.pem**입니다.

   2. 해당 루트 CA 인증서를 소유하고 있는지 확인합니다.

2. Azure IoT 허브에서 X.509 보안 설정의 [IoT 허브에 대한 X.509 장치 만들기](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) 섹션의 지침을 *따릅니다.* 이 섹션에서는 다음 단계를 수행합니다.

   1. 새 장치를 추가합니다. **장치 ID에**대한 소문자 이름을 제공하고 인증 유형 **X.509 CA 서명을**선택합니다.
   2. 상위 장치를 설정합니다. 다운스트림 장치의 경우 **상위 장치 설정을** 선택하고 IoT Hub에 대한 연결을 제공하는 IoT Edge 게이트웨이 장치를 선택합니다.

3. 다운스트림 장치에 대한 인증서 체인을 만듭니다. IoT Hub에 업로드한 것과 동일한 루트 CA 인증서를 사용하여 이 체인을 만듭니다. 포털에서 장치 ID에 제공한 것과 동일한 소문자 장치 ID를 사용합니다.

4. 장치 인증서 및 키를 다운스트림 장치의 모든 위치에 복사합니다. 또한 게이트웨이 장치 인증서와 다운스트림 장치 인증서를 모두 생성한 공유 루트 CA 인증서의 복사본을 이동합니다.

   IoT Hub에 연결하는 리프 장치 응용 프로그램에서 이러한 파일을 참조합니다. [Azure Key Vault와](https://docs.microsoft.com/azure/key-vault) 같은 서비스 또는 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용하여 인증서 파일을 이동할 수 있습니다.

5. 선호하는 언어에 따라 IoT 애플리케이션에서 X.509 인증서를 참조할 수 있는 방법에 대한 샘플을 검토합니다.

   * C#: [Azure IoT 허브에서 X.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * 노드.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * 자바: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * 파이썬: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

[Azure CLI에 대한 IoT 확장을](https://github.com/Azure/azure-iot-cli-extension) 사용하여 동일한 장치 만들기 작업을 완료할 수 있습니다. 다음 예제는 X.509 CA 서명 된 인증으로 새 IoT 장치를 만들고 상위 장치를 할당합니다.

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

자세한 내용은 [az iot 허브 장치 ID](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령에 대한 Azure CLI 참조 콘텐츠를 참조하십시오.

그런 다음 장치가 게이트웨이를 통해 연결할 수 있도록 [연결 문자열을 검색하고 수정합니다.](#retrieve-and-modify-connection-string)

## <a name="retrieve-and-modify-connection-string"></a>연결 문자열 검색 및 수정

포털에서 IoT 장치 ID를 만든 후 기본 또는 보조 키를 검색할 수 있습니다. 이러한 키 중 하나는 응용 프로그램이 IoT Hub와 통신하는 데 사용하는 연결 문자열에 포함되어야 합니다. 대칭 키 인증의 경우 IoT Hub는 사용자의 편의를 위해 장치 세부 정보에 완전히 형성된 연결 문자열을 제공합니다. 게이트웨이 장치에 대한 추가 정보를 연결 문자열에 추가해야 합니다.

다운스트림 장치의 연결 문자열에는 다음 구성 요소가 필요합니다.

* 장치가 연결하는 IoT 허브는 다음에 연결됩니다.`Hostname={iothub name}.azure-devices.net`
* 허브에 등록된 장치 ID:`DeviceID={device ID}`
* 기본 키 또는 보조 키:`SharedAccessKey={key}`
* 장치가 연결되는 게이트웨이 장치입니다. IoT Edge 게이트웨이 장치의 config.yaml 파일에서 **호스트 이름** 값을 제공합니다.`GatewayHostName={gateway hostname}`

모두 함께, 전체 연결 문자열처럼 보인다 :

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

이 다운스트림 장치에 대한 부모/자식 관계를 설정한 경우 게이트웨이를 연결 호스트로 직접 호출하여 연결 문자열을 단순화할 수 있습니다. X.509 인증에는 부모/자식 관계가 필요하지만 대칭 키 인증에는 선택 사항입니다. 다음은 그 예입니다.

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

이 시점에서 IoT Edge 장치를 게이트웨이로 등록하고 구성해야 합니다. 또한 다운스트림 IoT 장치가 등록되어 게이트웨이 장치를 가리킵니다. 마지막 단계는 게이트웨이에 안전하게 연결할 수 있도록 다운스트림 장치에 인증서를 배치하는 것입니다.

게이트웨이 시리즈의 다음 문서로 계속, [다운스트림 장치를 Azure IoT Edge 게이트웨이에 연결합니다.](how-to-connect-downstream-device.md)

## <a name="next-steps"></a>다음 단계

이 문서를 작성하면 투명한 게이트웨이로 작동하는 IoT Edge 장치와 IoT 허브에 등록된 다운스트림 장치가 있어야 합니다. 다음으로 게이트웨이 장치를 신뢰하고 안전하게 연결하도록 다운스트림 장치를 구성해야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.
