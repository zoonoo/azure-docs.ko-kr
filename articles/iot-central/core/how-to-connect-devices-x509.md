---
title: Azure IoT Central 애플리케이션에서 X.509 인증서를 사용하여 디바이스 연결
description: IoT Central 애플리케이션용 Node.js 디바이스 SDK를 사용하여 X.509 인증서로 디바이스를 연결하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: cf0db71600c9350b4d70e6375f509a6e88709f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378335"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>IoT Central 애플리케이션용 Node.js 디바이스 SDK를 사용하여 X.509 인증서로 디바이스를 연결하는 방법

IoT Central은 SAS(공유 액세스 서명) 및 X.509 인증서를 모두 지원하여 디바이스와 애플리케이션 간의 통신을 보호합니다. [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device.md) 자습서는 SAS를 사용합니다. 이 문서에서는 X.509를 사용하도록 코드 샘플을 수정하는 방법에 대해 알아봅니다.  X.509 인증서는 프로덕션 환경에서 권장됩니다. 자세한 내용은 [Azure IoT Central에 연결](./concepts-get-connected.md)을 참조하세요.

이 문서에서는 X.509 사용의 두 가지 방법 - 일반적으로 프로덕션 환경에서 사용되는 [그룹 등록](how-to-connect-devices-x509.md#use-a-group-enrollment) 및 테스트에 유용한 [개별 등록](how-to-connect-devices-x509.md#use-an-individual-enrollment)을 보여 줍니다.

이 문서의 코드 조각에서는 JavaScript를 사용합니다. 다른 언어의 코드 샘플의 경우 다음을 참조하세요.

- [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_ll_client_x509_sample)
- [C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/X509DeviceCertWithChainSample)
- [Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
- [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)

## <a name="prerequisites"></a>사전 요구 사항

- [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결(JavaScript)](./tutorial-connect-device.md) 자습서의 완성
- [Git](https://git-scm.com/download/)
- [OpenSSL](https://www.openssl.org/)을 다운로드 및 설치합니다. Windows를 사용하는 경우 [SourceForge의 OpenSSL 페이지](https://sourceforge.net/projects/openssl/)에서 이진 파일을 사용할 수 있습니다.

## <a name="use-a-group-enrollment"></a>그룹 등록 사용

프로덕션 환경에서 그룹 등록에 X.509 인증서를 사용합니다. 그룹 등록에서 루트 또는 중간 X.509 인증서를 IoT Central 애플리케이션에 추가합니다. 루트 또는 중간 인증서에서 파생된 리프 인증서가 있는 디바이스는 애플리케이션에 연결할 수 있습니다.

## <a name="generate-root-and-device-cert"></a>루트 및 디바이스 인증서 생성

이 섹션에서는 X.509 인증서를 사용하여 등록 그룹의 인증서에서 파생된 인증서를 사용하여 디바이스를 연결합니다. 이 인증서는 IoT Central 애플리케이션에 연결할 수 있습니다.

> [!WARNING]
> X.509 인증서를 생성하는 이 방법은 테스트용으로만 사용할 수 있습니다. 프로덕션 환경의 경우 인증서를 생성하는 데 공식, 보안 메커니즘을 사용해야 합니다.

1. 명령 프롬프트를 엽니다. 인증서 생성 스크립트에 대한 GitHub 리포지토리를 복제합니다.

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. 인증서 생성기 스크립트로 이동하여 필요한 패키지를 설치합니다.

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. 루트 인증서를 만든 다음, 스크립트를 실행하여 디바이스 인증서를 파생합니다.

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > 디바이스 ID는 문자, 숫자 및 `-` 문자를 포함할 수 있습니다.

이러한 명령은 루트 및 디바이스 인증서 각각에 대해 3개의 파일을 생성합니다.

파일 이름 | 내용
-------- | --------
\<name\>_cert.pem | X509 인증서의 공개 부분
\<name\>_key.pem | X509 인증서에 대한 프라이빗 키
\<name\>_fullchain.pem | X509 인증서에 대한 전체 키 집합

## <a name="create-a-group-enrollment"></a>그룹 등록 만들기

1. IoT Central 애플리케이션을 열고 왼쪽 창에서 **관리** 로 이동하여 **디바이스 연결** 을 선택합니다.

1. **+ 등록 그룹 만들기** 를 선택하고, 증명 형식 **인증서(X.509)** 를 사용하여 _MyX509Group_ 이라는 새 등록 그룹을 만듭니다.

1. 만든 등록 그룹을 열고 **기본 관리** 를 선택합니다.

1. 파일 옵션을 선택하고 이전에 생성한 _mytestrootcert_cert.pem_ 이라는 루트 인증서 파일을 업로드합니다.

    ![인증서 업로드](./media/how-to-connect-devices-x509/certificate-upload.png)

1. 확인을 완료하려면 확인 코드를 생성하고, 복사한 다음, 이를 사용하여 명령 프롬프트에서 X.509 확인 인증서를 만듭니다.

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. 서명된 확인 인증서 _verification_cert.pem_ 을 업로드하여 확인을 완료합니다.

    ![확인된 인증서](./media/how-to-connect-devices-x509/verified.png)

이제 이 기본 루트 인증서에서 파생된 X.509 인증서가 있는 디바이스를 연결할 수 있습니다.

등록 그룹을 저장한 후에는 ID 범위를 기록해 둡니다.

## <a name="run-sample-device-code"></a>샘플 디바이스 코드 실행

1. **simple_thermostat.js** 애플리케이션을 포함하는 **sampleDevice01_key.pem** 및 **sampleDevice01_cert.pem** 을 _azure-iot-sdk-node/device/samples/pnp_ 폴더에 복사합니다. [디바이스 연결(JavaScript) 자습서](./tutorial-connect-device.md)를 완료할 때 이 애플리케이션을 사용했습니다.

1. **simple_thermostat.js** 애플리케이션이 포함된 _azure-iot-sdk-node/device/samples/pnp_ 폴더로 이동하고 다음 명령을 실행하여 X.509 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. 텍스트 편집기에서 **simple_thermostat.js** 파일을 엽니다.

1. 다음을 포함하도록 `require` 명령문을 편집합니다.

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. "DPS 연결 정보" 섹션에 다음 네 줄을 추가하여 `deviceCert` 변수를 초기화합니다.

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. 첫 번째 줄을 다음으로 바꿔 클라이언트를 만드는 `provisionDevice` 함수를 편집합니다.

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. 동일한 함수에서 다음과 같이 `deviceConnectionString` 변수를 설정하는 줄을 수정합니다.

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. `main` 함수에서 `Client.fromConnectionString`을 호출하는 줄 뒤에 다음 줄을 추가합니다.

    ```javascript
    client.setOptions(deviceCert);
    ```

1. 셸 환경에서 다음 두 가지 환경 변수를 설정합니다.

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](./tutorial-connect-device.md) 자습서를 완료할 때 다른 필수 환경 변수를 설정합니다.

1. 스크립트를 실행하고 디바이스가 성공적으로 프로비전되었는지 확인합니다.

    ```cmd/sh
    node simple_thermostat.js
    ```

    원격 분석이 대시보드에 나타나는지 확인할 수도 있습니다.

    ![디바이스 원격 분석 확인](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>개별 등록 사용

개별 등록에 X.509 인증서를 사용하여 디바이스와 솔루션을 테스트합니다. 개별 등록에서는 IoT Central 애플리케이션에 루트 또는 중간 X.509 인증서가 없습니다. 디바이스는 자체 서명된 X.509 인증서를 사용하여 애플리케이션에 연결합니다.

## <a name="generate-self-signed-device-cert"></a>자체 서명된 디바이스 인증서 생성

이 섹션에서는 자체 서명된 X.509 인증서를 사용하여 개별 등록을 위해 디바이스를 연결합니다. 이는 단일 디바이스를 등록하는 데 사용됩니다. 자체 서명된 인증서는 테스트 전용입니다.

스크립트를 실행하여 자체 서명된 X.509 디바이스 인증서를 만듭니다. 인증서 이름에는 소문자 영숫자 및 하이픈만 사용해야 합니다.

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>개별 등록 만들기

1. Azure IoT Central 애플리케이션에서 **디바이스** 를 선택하고, 자동 온도 조절기 디바이스 템플릿에서 _mytestselfcertprimary_ 로 **디바이스 ID** 를 사용하여 새 디바이스를 만듭니다. **ID 범위** 값을 기록해 둡니다. 나중에 이를 사용합니다.

1. 만든 디바이스를 열고 **연결** 을 선택합니다.

1. **연결 방법** 으로 **개별 등록** 을 선택하고 메커니즘으로 **인증서(X.509)** 를 선택합니다.

    ![개별 등록](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. 기본에서 파일 옵션을 선택하고 이전에 생성한 _mytestselfcertprimary_cert.pem_ 이라는 인증서 파일을 업로드합니다.

1. 보조 인증서에 대한 파일 옵션을 선택하고 _mytestselfcertsecondary_cert.pem_ 이라는 인증서 파일을 업로드합니다. 그런 다음, **저장** 을 선택합니다.

    ![개별 등록 인증서 업로드](./media/how-to-connect-devices-x509/individual-enrollment.png)

이제 디바이스가 X.509 인증서로 프로비전됩니다.

## <a name="run-a-sample-individual-enrollment-device"></a>샘플 개별 등록 디바이스 실행

1. **simple_thermostat.js** 애플리케이션을 포함하는 _mytestselfcertprimary_key.pem_ 및 _mytestselfcertprimary_cert.pem_ 파일을 _azure-iot-sdk-node/device/samples/pnp_ 폴더에 복사합니다. [디바이스 연결(JavaScript) 자습서](./tutorial-connect-device.md)를 완료할 때 이 애플리케이션을 사용했습니다.

1. 위의 샘플에서 사용한 환경 변수를 다음과 같이 수정합니다.

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. 스크립트를 실행하고 디바이스가 성공적으로 프로비전되었는지 확인합니다.

    ```cmd/sh
    node environmentalSensor.js
    ```

    원격 분석이 대시보드에 나타나는지 확인할 수도 있습니다.

    ![원격 분석 개별 등록](./media/how-to-connect-devices-x509/telemetry-primary.png)

_mytestselfcertsecondary_ 인증서에 대해서도 위의 단계를 반복할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 X.509 인증서를 사용하여 디바이스를 연결하는 방법을 배웠으므로 제안된 다음 단계는 [Azure CLI를 사용하여 디바이스 연결을 모니터링](howto-monitor-devices-azure-cli.md)하는 방법을 배우는 것입니다.
