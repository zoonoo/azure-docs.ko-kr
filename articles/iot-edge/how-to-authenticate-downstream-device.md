---
title: 다운스트림 장치-Azure IoT Edge 인증 | Microsoft Docs
description: 다운스트림 장치 또는 리프 장치와 IoT Hub로 인증 하 여 Azure IoT Edge 게이트웨이 장치를 통해 해당 연결을 라우팅하는 방법입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082392"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Azure IoT Hub에 다운스트림 장치를 인증 합니다.

투명 한 게이트웨이 시나리오 (리프 장치 또는 자식 장치 라고도 함) 하는 다운스트림 장치는 다른 장치와 같은 IoT Hub의 id를 해야 합니다. 이 문서에서는 IoT Hub에는 다운스트림 장치를 인증 하기 위한 옵션을 안내 하 고 게이트웨이 연결을 선언 하는 방법을 보여 줍니다.

성공적으로 투명 한 게이트웨이 연결을 설정 하려면 세 가지 일반적인 단계가 있습니다. 이 문서에서는 두 번째 단계를 다룹니다.

1. 게이트웨이 장치를 안전 하 게 연결 하 여 다운스트림 장치, 커뮤니케이션 다운스트림 장치에서 메시지를 적절 한 대상으로 라우팅할 수 있어야 합니다. 자세한 내용은 [투명 한 게이트웨이로 작동 하도록 IoT Edge 장치를 구성](how-to-create-transparent-gateway.md)합니다.
2. **다운스트림 장치는 장치 id를 IoT Hub를 사용 하 여 인증 하 고 해당 게이트웨이 장치를 통해 통신을 할 수 있어야 합니다.**
3. 다운스트림 장치는 게이트웨이 장치에 안전 하 게 연결할 수 있어야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

다운스트림 장치는 세 가지 방법 중 하나를 사용 하 여 IoT Hub를 사용 하 여 인증할 수 있습니다: 대칭 키 (공유 액세스 키 라고도 함), X.509 자체 서명 된 인증서 또는 CA (인증 기관) 인증서를 서명 하는 X.509 인증서입니다. 인증 단계 게이트웨이 관계를 선언 하는 작은 차이점으로 IoT Hub를 사용 하 여 모든 비 IoT Edge 장치를 설정 하는 단계와 비슷합니다.

이 문서의 단계를 보여 줍니다 수동 장치 프로 비전을 하지 자동 Azure IoT Hub Device Provisioning Service를 사용 하 여 프로 비전. 

## <a name="symmetric-key-authentication"></a>대칭 키 인증

대칭 키 인증 또는 공유 액세스 키 인증을 간단 하 게 IoT Hub를 사용 하 여 인증 됩니다. 대칭 키 인증을 사용 하 여 base64 키를 IoT Hub에서 IoT 장치 ID와 연결 됩니다. 장치의 IoT Hub에 연결할 때이 제공할 수 있도록 IoT 응용 프로그램에서 해당 키를 포함 합니다. 

### <a name="create-the-device-identity"></a>장치 id 만들기 

Visual Studio Code에 대 한 IoT 확장 또는 Azure portal, Azure CLI를 사용 하 여 IoT hub에서 새 IoT 장치를 추가 합니다. 다운스트림 장치를 IoT Edge 장치가 아닌 일반 IoT 장치가 IoT Hub에서 식별할 수 해야 한다는 것을 기억 합니다. 

새 장치 id를 만들 때 다음 정보를 제공 합니다. 

* 장치에 대 한 ID를 만듭니다.

* 선택 **대칭 키** 인증 유형으로 합니다. 

* 필요에 따라 하기로 **부모 장치 설정** 이 다운스트림 장치를 통해 연결 하는 IoT Edge 게이트웨이 장치를 선택 합니다. 이 단계는 대칭 키 인증에 대해 선택적 이지만 부모 장치 수 있도록 설정 이기 때문에 것이 좋습니다 [오프 라인 기능](offline-capabilities.md) 다운스트림 장치에 대 한 합니다. 항상 추가 하거나 나중에 부모를 변경 하려면 장치 세부 정보를 업데이트할 수 있습니다. 

   ![포털에서 대칭 키 인증을 사용 하 여 장치 ID 만들기](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

사용할 수는 [Azure CLI에 대 한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 동일한 작업을 완료 합니다. 다음 예에서는 대칭 키 인증을 사용 하 여 새 IoT 장치를 만들고 하 고 부모 장치를 할당 합니다. 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

장치 만들기 및 부모/자식 관리에 대 한 Azure CLI 명령에 대 한 자세한 내용은 참조에 대 한 참조 콘텐츠 [az iot hub 장치 id](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령입니다.

### <a name="connect-to-iot-hub-through-a-gateway"></a>게이트웨이 통해 IoT Hub에 연결

일반 IoT 인증에 동일한 프로세스를 사용 하는 대칭 키를 사용 하 여 IoT Hub에 장치 다운스트림 장치에도 적용 됩니다. 유일한 차이점은 연결을 라우팅하는 게이트웨이 장치 또는 IoT Hub를 대신 하 여 인증을 처리 하려면 오프 라인 시나리오에 대 한 포인터를 추가 해야 합니다. 

대칭 키 인증을 위해 IoT Hub를 사용 하 여 인증에 대 한 장치에서 수행 해야 하는 추가 단계 없이 있습니다. 여전히 필요한 위치에 있는 인증서는 다운스트림 장치는 게이트웨이 장치에 연결할 수 있도록에 설명 된 대로 [다운스트림 장치는 Azure IoT Edge 게이트웨이 연결할](how-to-connect-downstream-device.md)합니다.

포털에서 IoT 장치 id를 만들면 해당 주 또는 보조 키를 검색할 수 있습니다. 이러한 키 중 하나는 IoT Hub와 통신 하는 모든 응용 프로그램에서 사용 하는 연결 문자열에 포함 해야 합니다. 대칭 키 인증을 위해 IoT Hub는 사용자 편의 위해 장치 세부 정보에서 완전 한 형식의 연결 문자열을 제공합니다. 연결 문자열에 게이트웨이 장치에 대 한 추가 정보를 추가 해야 합니다. 

다운스트림 장치에 대 한 대칭 키 연결 문자열에는 다음 구성 요소가 필요합니다. 

* IoT hub에 연결 하는 장치는: `Hostname={iothub name}.azure-devices.net`
* 허브에 등록 된 장치 ID: `DeviceID={device ID}`
* 두 기본 또는 보조 키: `SharedAccessKey={key}`
* 게이트웨이 장치를 통해 장치를 연결 합니다. 제공 된 **hostname** IoT Edge 게이트웨이 장치 config.yaml 파일에서 값: `GatewayHostName={gateway hostname}`

모두를 함께 전체 연결 문자열을 다음과 같습니다.

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

이 다운스트림 장치에 대 한 부모/자식 관계를 설정, 연결 호스트로 직접 게이트웨이 호출 하 여 연결 문자열을 간소화할 수 있습니다. 예를 들면 다음과 같습니다. 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509 인증 

X.509 인증서를 사용 하 여 IoT 장치를 인증 하는 방법은 두 가지가 있습니다. 어떤 방식으로 인증 하려는, IoT Hub에 장치를 연결 하는 단계는 동일 합니다. 인증에 대 한 자체 서명 또는 CA 서명 인증서를 선택 하 고 IoT Hub에 연결 하는 방법을 알아보려면 계속 합니다. 

IoT Hub X.509 인증을 사용 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요. 
* [X.509 CA 인증서를 사용 하 여 장치 인증](../iot-hub/iot-hub-x509ca-overview.md)
* [IoT 업계의 X.509 CA 인증서에 대 한 개념적 이해](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>X.509 자체 서명 된 인증서를 사용 하 여 장치 id 만들기

지문 인증 라고도 하는 X.509 자체 서명 된 인증을 위해 IoT 장치에 적용할 새 인증서를 만드는 해야 합니다. 이러한 인증서 인증을 위해 IoT Hub를 사용 하 여 공유 하는에 지문을 포함 합니다. 

이 시나리오를 테스트 수행 하는 가장 쉬운 방법은에서 인증서를 만드는 데는 동일한 컴퓨터를 사용 하는 것 [투명 한 게이트웨이로 작동 하도록 IoT Edge 장치를 구성](how-to-create-transparent-gateway.md)합니다. 해당 컴퓨터 이미를 설정 해야 적절 한 도구, 루트 CA 인증서 및 중간 CA 인증서를 사용 하 여 IoT 장치 인증서를 만들어야 합니다. 복사할 수는 있지만 최종 인증서와 개인 키를 통해 다운스트림 장치에 나중에 합니다. 게이트웨이 문서의 단계를 다음 컴퓨터에 openssl을 설정한 다음 액세스 인증서 생성 스크립트에 IoT Edge 리포지토리를 복제 합니다. 라고 하는 작업 디렉터리를 변경한 다음,  **\<WRKDIR >** 인증서를 저장할 수 있습니다. 기본 인증서는 개발 및 테스트, 따라서 마지막 30 일에 대 한 것입니다. 루트 CA 인증서 및 중간 인증서가 만들어져 있어야 합니다. 

1. Bash 또는 PowerShell 창에서 작업 디렉터리로 이동 합니다. 

2. 다운스트림 장치에 대 한 두 개의 인증서 (기본 및 보조)를 만듭니다. 장치 이름 및 다음 기본 또는 보조 레이블을 제공 합니다. 이 정보는 파일 이름을 지정 하 여 수를 추적할 수 여러 장치에 대 한 인증서에 사용 됩니다. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. 각 인증서는 40 16 진수 문자열에서 SHA1 지문을 (지문을 IoT Hub 인터페이스에서)를 검색 합니다. 지문을 찾아 인증서 확인 하려면 다음 openssl 명령을 사용 합니다.

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Azure portal에서 IoT hub로 이동 하 고 다음 값을 사용 하 여 새 IoT 장치 id를 만듭니다. 

   * 선택 **자체 서명 된 X.509** 인증 유형으로 합니다.
   * 장치의 기본 및 보조 인증서에서 복사 하는 16 진수 문자열을 붙여 넣습니다.
   * 선택 **부모 장치 설정** IoT Edge를 통해이 다운스트림 장치 연결 하는 게이트웨이 장치를 선택 합니다. 부모 장치는 다운스트림 장치의 X.509 인증을 위해 필요 합니다. 

   ![포털에서 X.509 자체 서명 된 인증을 사용 하 여 장치 ID 만들기](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. 다운스트림 장치에서 모든 디렉터리에 다음 파일을 복사 합니다.

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   IoT Hub에 연결 하는 리프 장치 응용 프로그램에서 이러한 파일을 참조할 수 있습니다. 와 같은 서비스를 사용할 수 있습니다 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 등의 기능이 나 [안전한 복사 두](https://www.ssh.com/ssh/scp/) 인증서 파일을 이동 합니다.

사용할 수는 [Azure CLI에 대 한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 동일한 장치 만들기 작업을 완료 합니다. 다음 예제에서는 새 IoT 장치를 만들고 X.509 자체 서명 된 인증을 사용 하 여 부모 장치 할당: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

장치를 만들기, 인증서 생성 및 부모 및 자식 관리에 대 한 Azure CLI 명령에 대 한 자세한 내용은 참조에 대 한 참조 콘텐츠 [az iot hub 장치 id](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령입니다.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>장치를 만들 서명 된 인증서를 X.509 CA를 사용 하 여 id

X.509 인증서 기관 (CA) 서명 된 인증, 루트 CA 인증서 IoT 장치에 대 한 인증서를 서명 하는 데 사용 하는 IoT Hub에 등록 해야 합니다. 루트 CA 인증서 또는 중간 인증서로 문제는 인증서를 사용 하 여 모든 장치 인증 하도록 허용 됩니다. 

이 섹션에서는 IoT Hub 문서에 자세히 설명 된 지침에 따라 됩니다 [Azure IoT hub의 X.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md)합니다. 게이트웨이 통해 연결 하는 다운스트림 장치를 설정 하는 데 값을 알면이 섹션의 단계를 수행 합니다. 

이 시나리오를 테스트 하는 가장 쉬운 방법은에서 인증서를 만드는 데는 동일한 컴퓨터를 사용 하는 것 [투명 한 게이트웨이로 작동 하도록 IoT Edge 장치를 구성](how-to-create-transparent-gateway.md)합니다. 해당 컴퓨터 이미를 설정 해야 적절 한 도구, 루트 CA 인증서 및 중간 CA 인증서를 사용 하 여 IoT 장치 인증서를 만들어야 합니다. 복사할 수는 있지만 최종 인증서와 개인 키를 통해 다운스트림 장치에 나중에 합니다. 게이트웨이 문서의 단계를 다음 컴퓨터에 openssl을 설정한 다음 액세스 인증서 생성 스크립트에 IoT Edge 리포지토리를 복제 합니다. 라고 하는 작업 디렉터리를 변경한 다음,  **\<WRKDIR >** 인증서를 저장할 수 있습니다. 기본 인증서는 개발 및 테스트, 따라서 마지막 30 일에 대 한 것입니다. 루트 CA 인증서 및 중간 인증서가 만들어져 있어야 합니다. 

1. 지침에 따라 합니다 [IoT hub에 X.509 CA 등록 인증서](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) 부분 *Azure IoT hub의 X.509 보안 설정*합니다. 이 섹션에서 다음 단계를 수행합니다. 

   1. 루트 CA 인증서를 업로드 합니다. 투명 게이트웨이 문서에서 만든 인증서를 사용 하는 경우 업로드  **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem** 루트 인증서 파일입니다. 
   2. 해당 루트 CA 인증서를 소유 했는지 확인 합니다. 인증서 도구를 사용 하 여 소유를 확인할 수 있습니다 \<WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. 지침에 따라 합니다 [IoT hub에 X.509 장치 만들기](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) 부분 *Azure IoT hub의 X.509 보안 설정*합니다. 이 섹션에서 다음 단계를 수행합니다. 

   1. 새 장치를 추가 합니다. 에 대 한 이름을 소문자로 입력 **장치 ID**, 선택한 인증 유형을 **X.509 CA Signed**합니다. 
   2. 부모 장치를 설정 합니다. 다운스트림 장치에 대 한 선택 **부모 장치 설정** IoT Edge는 IoT Hub에 연결을 제공 하는 게이트웨이 장치를 선택 합니다. 

3. 다운스트림 장치에 대 한 인증서 체인을 만듭니다. 이 체인을 확인 하려면 IoT Hub에 업로드 한 동일한 루트 CA 인증서를 사용 합니다. 포털에서 장치 id에 지정한 동일한 소문자 장치 ID를 사용 합니다.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. 다운스트림 장치에서 모든 디렉터리에 다음 파일을 복사 합니다. 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   IoT Hub에 연결 하는 리프 장치 응용 프로그램에서 이러한 파일을 참조할 수 있습니다. 와 같은 서비스를 사용할 수 있습니다 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 등의 기능이 나 [안전한 복사 두](https://www.ssh.com/ssh/scp/) 인증서 파일을 이동 합니다.

사용할 수는 [Azure CLI에 대 한 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 동일한 장치 만들기 작업을 완료 합니다. 다음 예제에서는 서명 된 X.509 CA 인증을 사용 하 여 새 IoT 장치를 만들고 부모 장치 할당: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

장치 만들기 및 부모/자식 관리에 대 한 Azure CLI 명령에 대 한 자세한 내용은 참조에 대 한 참조 콘텐츠 [az iot hub 장치 id](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 명령입니다.


### <a name="connect-to-iot-hub-through-a-gateway"></a>게이트웨이 통해 IoT Hub에 연결

각 Azure IoT SDK는 X.509 인증을 약간 다르게 처리합니다. 그러나 동일한 프로세스 일반 IoT를 인증에 사용 되는 X.509 인증서를 사용 하 여 IoT Hub에 장치 다운스트림 장치에도 적용 됩니다. 유일한 차이점은 연결을 라우팅하는 게이트웨이 장치 또는 IoT Hub를 대신 하 여 인증을 처리 하려면 오프 라인 시나리오에 대 한 포인터를 추가 해야 합니다. 모든 IoT Hub 장치에 대 한 동일한 X.509 인증 단계에 따라 다음 단순히 값을 바꿀 수는 일반적으로 **Hostname** 게이트웨이 장치의 호스트 되도록 연결 문자열에 있습니다. 

다음 섹션에서는 다른 SDK 언어에 대 한 몇 가지 예를 보여 줍니다. 

>[!IMPORTANT]
>다음 샘플에서는 IoT Hub Sdk 장치 인증에 인증서를 사용 하는 방법을 보여 줍니다. 프로덕션 배포에서는 개인 또는 하드웨어 보안 모듈 (HSM)에서 SAS 키와 같은 모든 비밀을 저장 해야 합니다. 

#### <a name="net"></a>.NET

예는 C# X.509 인증서를 사용 하 여 IoT Hub로 인증 프로그램, 참조 [Azure IoT hub의 X.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)합니다. 샘플의 핵심 줄의 일부 여기에 인증 프로세스를 보여 주기 위해 포함 됩니다.

DeviceClient 인스턴스에 대 한 호스트를 선언할 때 IoT Edge 게이트웨이 장치 호스트 이름을 사용 합니다. 게이트웨이 장치의 config.yaml 파일에서 호스트를 찾을 수 있습니다. 

IoT Edge git 저장소에서 제공 하는 테스트 인증서를 사용 하는 경우 인증서 키는 **1234**합니다.

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

C 프로그램의 예 C IoT SDK의 참조 X.509 인증서를 사용 하 여 IoT Hub로 인증 [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) 샘플입니다. 샘플의 핵심 줄의 일부 여기에 인증 프로세스를 보여 주기 위해 포함 됩니다.

다운스트림 장치에 대 한 연결 문자열을 정의할 때 사용 하 여 IoT Edge 게이트웨이 장치 호스트 이름에 대 한 합니다 **HostName** 매개 변수입니다. 게이트웨이 장치의 config.yaml 파일에서 호스트를 찾을 수 있습니다. 

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

Node.js 프로그램의 예로 Node.js IoT SDK를 참조 하세요 X.509 인증서를 사용 하 여 IoT Hub로 인증 [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) 샘플입니다. 샘플의 핵심 줄의 일부 여기에 인증 프로세스를 보여 주기 위해 포함 됩니다.

다운스트림 장치에 대 한 연결 문자열을 정의할 때 사용 하 여 IoT Edge 게이트웨이 장치 호스트 이름에 대 한 합니다 **HostName** 매개 변수입니다. 게이트웨이 장치의 config.yaml 파일에서 호스트를 찾을 수 있습니다. 

IoT Edge git 저장소에서 제공 하는 테스트 인증서를 사용 하는 경우 인증서 키는 **1234**합니다.

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

Python 프로그램의 예는 Java IoT SDK를 참조 하십시오 X.509 인증서를 사용 하 여 IoT Hub로 인증 [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) 샘플입니다. 샘플의 핵심 줄의 일부 여기에 인증 프로세스를 보여 주기 위해 포함 됩니다.

다운스트림 장치에 대 한 연결 문자열을 정의할 때 사용 하 여 IoT Edge 게이트웨이 장치 호스트 이름에 대 한 합니다 **HostName** 매개 변수입니다. 게이트웨이 장치의 config.yaml 파일에서 호스트를 찾을 수 있습니다. 

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

Java 프로그램의 예는 Java IoT SDK를 참조 하십시오 X.509 인증서를 사용 하 여 IoT Hub로 인증 [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) 샘플입니다. 샘플의 핵심 줄의 일부 여기에 인증 프로세스를 보여 주기 위해 포함 됩니다.

다운스트림 장치에 대 한 연결 문자열을 정의할 때 사용 하 여 IoT Edge 게이트웨이 장치 호스트 이름에 대 한 합니다 **HostName** 매개 변수입니다. 게이트웨이 장치의 config.yaml 파일에서 호스트를 찾을 수 있습니다. 

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

이 문서를 완료 하 여 IoT Edge 장치를 투명 한 게이트웨이로 작동 해야 하 고 다운스트림 장치는 IoT hub를 사용 하 여 등록 키를 누릅니다. 그런 다음 다운스트림 장치에 메시지를 보내고 신뢰 게이트웨이 장치를 구성 해야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.
