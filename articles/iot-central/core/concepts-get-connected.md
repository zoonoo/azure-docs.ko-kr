---
title: Azure IoT Central에서 디바이스 연결 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 디바이스 연결과 관련된 주요 개념을 소개합니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f37b070c74abd8511fc597f9b159312d91281083
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759121"
---
# <a name="get-connected-to-azure-iot-central"></a>Azure IoT 센트럴에 연결하기

*이 문서는 운영자 및 장치 개발자에게 적용됩니다.*

이 문서에서는 Azure IoT Central 응용 프로그램에 장치를 연결하는 옵션에 대해 설명합니다.

일반적으로 연결하려면 먼저 응용 프로그램에 장치를 등록해야 합니다. 그러나 IoT Central은 [장치가 먼저 등록되지 않고 연결할 수](#connect-without-registering-devices)있는 시나리오를 지원합니다.

IoT Central은 [Azure IoT 허브 장치 프로비저닝 서비스(DPS)를](../../iot-dps/about-iot-dps.md) 사용하여 연결 프로세스를 관리합니다. 장치가 먼저 DPS 끝점에 연결하여 응용 프로그램에 연결하는 데 필요한 정보를 검색합니다. 내부적으로 IoT Central 애플리케이션은 IoT 허브를 사용하여 장치 연결을 처리합니다. DPS를 사용하면 다음과 같은 장점이 있습니다.

- IoT Central이 디바이스를 대규모로 온보딩하고 연결하는 것을 지원합니다.
- IoT Central UI를 통해 디바이스를 등록할 필요 없이 오프라인으로 디바이스 자격 증명을 생성하고 디바이스를 구성할 수 있습니다.
- 사용자의 고유한 디바이스 ID를 사용하여 IoT Central에서 디바이스를 등록할 수 있습니다. 사용자의 고유한 디바이스 ID를 사용하면 기존 백 오피스 시스템과 간편하게 통합할 수 있습니다.
- 디바이스를 IoT Central에 연결하는 한 가지 일관적인 방법이 있습니다.

IoT Central은 장치와 응용 프로그램 간의 통신을 보호하기 위해 공유 액세스 서명(SAS) 및 X.509 인증서를 모두 지원합니다. X.509 인증서는 프로덕션 환경에서 권장됩니다.

이 문서에서는 다음과 같은 사용 사례에 대해 설명합니다.

- [SAS를 사용하여 단일 장치 연결](#connect-a-single-device)
- [SAS를 사용하여 대규모로 장치 연결](#connect-devices-at-scale-using-sas)
- 프로덕션 환경에 권장되는 접근 방식인 [X.509 인증서를 사용하여 장치를 대규모로 연결합니다.](#connect-devices-using-x509-certificates)
- [장치를 먼저 등록하지 않고 연결](#connect-without-registering-devices)
- [DPS 개별 등록을 사용하는 장치 연결](#individual-enrollment-based-device-connectivity)
- [IoT 플러그 앤 플레이(미리 보기) 기능을 사용하여 장치 연결](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>단일 디바이스 연결

이 방법은 IoT Central 또는 테스트 장치를 실험할 때 유용합니다. IoT Central 응용 프로그램의 장치 연결 SAS 키를 사용하여 장치를 IoT Central 애플리케이션에 연결할 수 있습니다. 등록된 장치의 연결 정보에서 _장치 SAS 키를_ 복사합니다.

![개별 장치에 대한 SAS 키](./media/concepts-get-connected/single-device-sas.png)

자세한 내용은 [Node.js 클라이언트 응용 프로그램 만들기 및 Azure IoT 중앙 응용 프로그램 자습서를 참조하세요.](./tutorial-connect-device.md)

## <a name="connect-devices-at-scale-using-sas"></a>SAS를 사용하여 대규모로 장치 연결

SAS 키를 사용하여 대규모로 장치를 IoT Central에 연결하려면 장치를 등록한 다음 설정해야 합니다.

### <a name="register-devices-in-bulk"></a>장치 대량 등록

IoT Central 응용 프로그램에 많은 수의 장치를 등록하려면 CSV 파일을 사용하여 [장치 ID 및 장치 이름을 가져옵니다.](howto-manage-devices.md#import-devices)

가져온 장치에 대한 연결 정보를 검색하려면 [IoT Central 응용 프로그램에서 CSV 파일을 내보냅니다.](howto-manage-devices.md#export-devices) 내보낸 CSV 파일에는 장치 ID와 SAS 키가 포함됩니다.

### <a name="set-up-your-devices"></a>기기 설정

장치 코드의 내보내기 파일의 연결 정보를 사용하여 장치가 IoT에 데이터를 연결하고 IoT로 전송할 수 있도록 합니다. 또한 응용 프로그램에 대한 DPS **ID 범위가** 필요합니다. 관리 > 장치 **연결에서**이 값을 찾을 수 있습니다.

> [!NOTE]
> IoT Central에 먼저 등록하지 않고 장치를 연결하는 방법을 알아보려면 [장치를 먼저 등록하지 않고 연결을](#connect-without-registering-devices)참조하십시오.

## <a name="connect-devices-using-x509-certificates"></a>X.509 인증서를 사용하여 장치 연결

프로덕션 환경에서 X.509 인증서를 사용하는 것은 IoT Central에 권장되는 장치 인증 메커니즘입니다. 자세한 내용은 [X.509 CA 인증서를 사용하여 디바이스 인증](../../iot-hub/iot-hub-x509ca-overview.md)을 참조하세요.

장치를 X.509 인증서로 연결하기 전에 응용 프로그램에서 중간 또는 루트 X.509 인증서를 추가하고 확인합니다. 장치는 루트 또는 중간 인증서에서 생성된 리프 X.509 인증서를 사용해야 합니다.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>루트 또는 중간 인증서 추가 및 확인

관리 **> 장치 연결 > 기본 인증서를 관리하고** 장치 인증서를 생성하는 데 사용하는 X.509 루트 또는 중간 인증서를 추가합니다.

![연결 설정](media/concepts-get-connected/manage-x509-certificate.png)

인증서 소유권을 확인하면 인증서를 업로드하는 사람이 인증서의 개인 키를 갖도록 할 수 있습니다. 인증서를 확인하려면 다음을 수행하십시오.

  1. **확인 코드** 옆에 있는 단추를 선택하여 코드를 생성합니다.
  1. 이전 단계에서 생성한 확인 코드로 X.509 확인 인증서를 만듭니다. 인증서를 .cer 파일로 저장합니다.
  1. 서명된 확인 인증서를 업로드하고 **확인을**선택합니다. 확인이 성공하면 인증서가 **확인됨으로** 표시됩니다.

보안 위반이 있거나 기본 인증서가 만료하도록 설정된 경우 보조 인증서를 사용하여 가동 중지 시간을 줄입니다. 기본 인증서를 업데이트하는 동안 보조 인증서를 사용하여 장치를 계속 프로비전할 수 있습니다.

### <a name="register-and-connect-devices"></a>장치 등록 및 연결

X.509 인증서를 사용하여 장치를 일괄 연결하려면 먼저 CSV 파일을 사용하여 장치 ID 및 장치 이름을 가져오도록 응용 프로그램에서 장치를 [등록합니다.](howto-manage-devices.md#import-devices) 장치 ID는 모두 소문자여야 합니다.

업로드된 루트 또는 중간 인증서를 사용하여 장치에 대한 X.509 리프 인증서를 생성합니다. 장치 **ID를** 리프 `CNAME` 인증서의 값으로 사용합니다. 장치 코드에는 응용 프로그램, **장치 ID**및 해당 장치 인증서에 대한 **ID 범위** 값이 필요합니다.

### <a name="for-testing-purposes-only"></a>테스트 목적으로만

테스트전용으로 다음 유틸리티를 사용하여 루트, 중간 및 장치 인증서를 생성할 수 있습니다.

- [Azure IoT 장치 프로비저닝 장치 SDK용 도구:](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)X.509 인증서 및 키를 생성하고 확인하는 데 사용할 수 있는 Node.js 도구 모음입니다.
- DevKit 장치를 사용하는 경우 이 [명령줄 도구는](https://aka.ms/iotcentral-docs-dicetool) IoT Central 응용 프로그램에 추가하여 인증서를 확인할 수 있는 CA 인증서를 생성합니다.
- [샘플 및 자습서에 대한 테스트 CA 인증서 관리:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)PowerShell 및 Bash 스크립트 컬렉션을 수행합니다.
  - 인증서 체인을 만듭니다.
  - 인증서를 .cer 파일로 저장하여 IoT Central 응용 프로그램에 업로드합니다.
  - IoT Central 응용 프로그램의 확인 코드를 사용하여 확인 인증서를 생성합니다.
  - 장치 ID를 도구의 매개 변수로 사용하여 장치에 대한 리프 인증서를 만듭니다.

### <a name="further-reference"></a>추가 참조

- [라즈베리파이에 대한 샘플 구현](https://aka.ms/iotcentral-docs-Raspi-releases)
- [C의 샘플 장치 클라이언트](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>장치를 등록하지 않고 연결

앞에서 설명한 시나리오에서는 모두 연결하기 전에 응용 프로그램에 장치를 등록해야 합니다. 또한 IoT Central을 사용하면 OEM이 먼저 등록되지 않고 연결할 수 있는 장치를 대량 생산할 수 있습니다. OEM은 적합한 장치 자격 증명을 생성하고 공장에서 장치를 구성합니다. 고객이 장치를 처음 켜면 DPS에 연결한 다음 장치를 올바른 IoT Central 응용 프로그램에 자동으로 연결합니다. IoT Central 운영자는 응용 프로그램에 데이터를 보내기 전에 장치를 승인해야 합니다.

장치가 SAS 토큰 또는 X.509 인증서를 사용하는지 여부에 따라 흐름이 약간 다릅니다.

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>등록하지 않고 SAS 토큰을 사용하는 장치 연결

1. IoT Central 응용 프로그램의 그룹 기본 키 를 복사합니다.

    ![응용 프로그램 그룹 기본 SAS 키](media/concepts-get-connected/group-sas-keys.png)

1. [Dps 키겐](https://www.npmjs.com/package/dps-keygen) 도구를 사용하여 장치 SAS 키를 생성합니다. 이전 단계의 그룹 기본 키를 사용합니다. 장치 ID는 소문자여야 합니다.

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. OEM은 장치 ID, 생성된 장치 SAS 키 및 응용 프로그램 **ID 범위** 값으로 각 장치를 깜박입니다.

1. 장치를 켜면 먼저 DPS에 연결하여 IoT 중앙 등록 정보를 검색합니다.

    디바이스는 처음에 **장치** 페이지에서 **연결되지 않은** 장치 상태를 가지고 있으며 장치 템플릿에 할당되지 않습니다. **장치** 페이지에서 장치를 적절한 장치 템플릿으로 **마이그레이션합니다.** 이제 장치 프로비저닝이 완료되고 장치 상태가 **프로비전되고**장치가 데이터 전송을 시작할 수 있습니다.

    장치 **관리 > 장치 연결** 페이지에서 **자동 승인** 옵션은 데이터 전송을 시작하기 전에 장치를 수동으로 승인해야 하는지 여부를 제어합니다.

    > [!NOTE]
    > 장치를 장치 템플릿과 자동으로 연결하는 방법을 알아보려면 [IoT 플러그 앤 플레이(미리 보기)가 있는 장치 연결을](#connect-devices-with-iot-plug-and-play-preview)참조하세요.

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>등록하지 않고 X.509 인증서를 사용하는 장치 연결

1. IoT Central [애플리케이션에 루트 또는 중간 X.509 인증서를 추가하고 확인합니다.](#connect-devices-using-x509-certificates) (#connect 장치 사용 x509-인증서)

1. IoT Central 응용 프로그램에 추가한 루트 또는 중간 인증서를 사용하여 장치에 대한 리프 인증서를 생성합니다. 소문자 장치 ID를 리프 `CNAME` 인증서의 ID로 사용합니다.

1. OEM은 장치 ID, 생성된 왼쪽 왼쪽 X.509 인증서 및 응용 프로그램 **ID 범위** 값으로 각 장치를 깜박입니다.

1. 장치를 켜면 먼저 DPS에 연결하여 IoT 중앙 등록 정보를 검색합니다.

    디바이스는 처음에 **장치** 페이지에서 **연결되지 않은** 장치 상태를 가지고 있으며 장치 템플릿에 할당되지 않습니다. **장치** 페이지에서 장치를 적절한 장치 템플릿으로 **마이그레이션합니다.** 이제 장치 프로비저닝이 완료되고 장치 상태가 **프로비전되고**장치가 데이터 전송을 시작할 수 있습니다.

    장치 **관리 > 장치 연결** 페이지에서 **자동 승인** 옵션은 데이터 전송을 시작하기 전에 장치를 수동으로 승인해야 하는지 여부를 제어합니다.

    > [!NOTE]
    > 장치를 장치 템플릿과 자동으로 연결하는 방법을 알아보려면 [IoT 플러그 앤 플레이(미리 보기)가 있는 장치 연결을](#connect-devices-with-iot-plug-and-play-preview)참조하세요.

## <a name="individual-enrollment-based-device-connectivity"></a>개별 등록 기반 장치 연결

각각 고유한 인증 자격 증명이 있는 장치를 연결하는 고객의 경우 개별 등록을 사용합니다. 개별 등록은 연결할 수 있는 단일 장치에 대한 항목입니다. 개별 등록은 X.509 리프 인증서 또는 SAS 토큰(물리적 또는 가상 신뢰할 수 있는 플랫폼 모듈)을 증명 메커니즘으로 사용할 수 있습니다. 개별 등록의 장치 ID(등록 ID라고도 함)는 참숫자, 소문자이며 하이픈을 포함할 수 있습니다. 자세한 내용은 [DPS 개별 등록을](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)참조하십시오.

> [!NOTE]
> 장치에 대한 개별 등록을 만들 때 IoT Central 응용 프로그램의 기본 그룹 등록 옵션보다 우선합니다.

### <a name="creating-individual-enrollments"></a>개별 등록 만들기

IoT Central은 개별 등록에 대해 다음과 같은 증명 메커니즘을 지원합니다.

- **대칭 키 증명:** 대칭 키 증명은 DPS 인스턴스를 사용하여 장치를 인증하는 간단한 방법입니다. 대칭 키를 사용하는 개별 등록을 만들려면 **장치 연결** 페이지를 열고 **개별 등록을** 연결 방법으로 선택하고 **SAS(공유 액세스 서명)를** 메커니즘으로 선택합니다. base64 인코딩된 기본 및 보조 키를 입력하고 변경 내용을 저장합니다. ID **범위,** **장치 ID**및 기본 또는 보조 키를 사용하여 장치를 연결합니다.

    > [!TIP]
    > 테스트의 경우 **OpenSSL을** 사용하여 base64 인코딩된 키를 생성할 수 있습니다.`openssl rand -base64 64`

- **X.509 인증서:** X.509 인증서를 사용하여 개별 등록을 만들려면 **장치 연결** 페이지를 열고 **개별 등록을** 연결 방법으로 선택하고 **인증서(X.509)를** 메커니즘으로 선택합니다. 개별 등록 항목과 함께 사용되는 장치 인증서에는 발급자 및 주체 CN이 장치 ID로 설정해야 합니다.

    > [!TIP]
    > 테스트를 위해 [Node.js용 Azure IoT 장치 프로비저닝 장치 SDK에 대한 도구를](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) 사용하여 자체 서명된 인증서를 생성할 수 있습니다.`node create_test_cert.js device "mytestdevice"`

- **신뢰할 수 있는 플랫폼 모듈(TPM) 증명:** [TPM은](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) 하드웨어 보안 모듈의 한 유형입니다. TPM을 사용하는 것은 장치를 연결하는 가장 안전한 방법 중 하나입니다. 이 문서에서는 개별, 펌웨어 또는 통합 TPM을 사용하고 있다고 가정합니다. 소프트웨어 에뮬레이트된 TPM은 프로토타이핑 이나 테스트에 적합하지만 이산, 펌웨어 또는 통합 TPM과 동일한 수준의 보안을 제공하지는 않습니다. 프로덕션 환경에서 소프트웨어 TPM을 사용하지 마십시오. TPM을 사용하는 개별 등록을 만들려면 **장치 연결** 페이지를 열고 개별 **등록을** 연결 방법으로 선택하고 **TPM을** 메커니즘으로 선택합니다. TPM 인증 키를 입력하고 장치 연결 정보를 저장합니다.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>IoT 플러그 앤 플레이로 장치 연결(미리 보기)

IoT Central의 IoT 플러그 앤 플레이(미리 보기)의 주요 기능 중 하나는 장치 연결시 장치 템플릿을 자동으로 연결하는 기능입니다. 장치 자격 증명과 함께 장치는 이제 장치 등록 호출의 일부로 **CapabilityModelId를** 보낼 수 있습니다. 이 기능을 통해 IoT Central은 장치 템플릿을 검색하고 장치와 연결할 수 있습니다. 검색 프로세스는 다음과 같이 작동합니다.

1. IoT Central 응용 프로그램에 이미 게시된 경우 장치 템플릿과 연결합니다.
1. 게시된 기능 및 인증된 기능 모델의 공용 리포지토리에서 가져옵니다.

다음은 장치가 DPS 등록 통화 중에 보내는 추가 페이로드의 형식입니다.

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> 장치가 자동으로 연결하고, 장치 템플릿을 검색하고, 데이터 전송을 시작하려면 **관리 > 장치 연결에** 대한 **자동 승인** 옵션을 사용하도록 설정해야 합니다.

## <a name="device-status-values"></a>장치 상태 값

실제 장치가 IoT Central 응용 프로그램에 연결되면 장치 상태는 다음과 같이 변경됩니다.

1. 장치 상태가 먼저 **등록됩니다.** 이 상태는 장치가 IoT Central에서 만들어지고 장치 ID가 있다는 것을 의미합니다. 다음과 같은 경우 장치가 등록됩니다.
    - **장치** 페이지에 새 실제 장치가 추가됩니다.
    - 장치 가져오기 페이지에서 **가져오기를** 사용하여 **장치** 집합이 추가됩니다.

1. 유효한 자격 증명을 사용하여 IoT Central 응용 프로그램에 연결된 장치가 프로비전 단계를 완료하면 장치 상태가 **프로비저닝으로** 변경됩니다. 이 단계에서 장치는 DPS를 사용하여 IoT Central 응용 프로그램에서 사용하는 IoT Hub에서 연결 문자열을 자동으로 검색합니다. 이제 장치가 IoT Central에 연결하여 데이터 전송을 시작할 수 있습니다.

1. 운영자는 장치를 차단할 수 있습니다. 장치가 차단되면 IoT Central 응용 프로그램으로 데이터를 보낼 수 없습니다. 차단된 장치의 상태가 **차단되었습니다.** 운영자는 데이터 전송을 다시 시작하기 전에 장치를 재설정해야 합니다. 운영자가 장치의 차단을 해제하면 상태가 이전 값인 **등록** 또는 프로비전된 값으로 **반환됩니다.**

1. 장치 상태가 **승인을 기다리는**경우 자동 **승인** 옵션이 비활성화됨을 의미합니다. 운영자는 데이터 전송을 시작하기 전에 장치를 명시적으로 승인해야 합니다. **장치** 페이지에 수동으로 등록되지 않았지만 유효한 자격 증명으로 연결된 장치는 **승인 대기**상태입니다. 운영자는 **승인** 단추를 사용하여 **장치** 페이지에서 이러한 장치를 승인할 수 있습니다.

1. 장치 상태가 **연결되지 않은**경우 IoT Central에 연결하는 장치에 연결된 장치 템플릿이 없는 것을 의미합니다. 이 상황은 일반적으로 다음과 같은 시나리오에서 발생합니다.

    - 장치 템플릿을 지정하지 않고 **장치** 가져오기 페이지에서 **장치** 집합이 추가됩니다.
    - 장치 템플릿을 지정하지 않고 **장치** 페이지에 장치가 수동으로 등록되었습니다. 그런 다음 장치가 유효한 자격 증명으로 연결되었습니다.  

    운영자는 **마이그레이션** 단추를 사용하여 장치 페이지에서 장치 템플릿에 **장치를** 연결할 수 있습니다.

## <a name="best-practices"></a>모범 사례

장치를 처음 연결할 때 DPS가 반환하는 장치 연결 문자열을 유지하거나 캐시하지 마십시오. 장치를 다시 연결하려면 표준 장치 등록 흐름을 통해 올바른 장치 연결 문자열을 가져옵니다. 장치가 연결 문자열을 캐시하는 경우 IoT Central이 사용하는 기본 Azure IoT 허브를 업데이트하면 장치 소프트웨어가 오래된 연결 문자열을 가질 위험이 있습니다.

## <a name="sdk-support"></a>SDK 지원

Azure 장치 SDK는 장치 코드를 구현하는 가장 쉬운 방법을 제공합니다. 현재 다음과 같은 디바이스 SDK를 사용할 수 있습니다.

- [C용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Python용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node.js용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Java용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 기능 및 IoT Hub 연결

IoT Hub와의 모든 디바이스 통신에 다음 IoT Hub 연결 옵션이 사용됩니다.

- [디바이스-클라우드 메시징](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [장치 쌍](../../iot-hub/iot-hub-devguide-device-twins.md)

다음 표에는 Azure IoT Central 디바이스 기능이 IoT Hub 기능에 매핑되는 방식이 요약되어 있습니다.

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| 원격 분석 | 디바이스-클라우드 메시징 |
| 속성 | 디바이스 쌍 reported 속성 |
| 속성(쓰기 가능) | 디바이스 쌍 desired 및 reported 속성 |
| 명령 | 직접 메서드 |

장치 SDK 사용에 대한 자세한 내용은 [DevDiv 키트 장치를 Azure IoT Central 응용 프로그램에 연결(예:](howto-connect-devkit.md) 코드)을 참조하십시오.

### <a name="protocols"></a>프로토콜

디바이스 SDK는 IoT Hub에 연결하기 위한 다음과 같은 네트워크 프로토콜을 지원합니다.

- MQTT
- AMQP
- HTTPS

프로토콜 간 차이점 및 프로토콜 선택 방법에 대한 지침은 [통신 프로토콜 선택](../../iot-hub/iot-hub-devguide-protocols.md)을 참조하세요.

지원되는 프로토콜을 디바이스에서 사용할 수 없는 경우 Azure IoT Edge를 사용하여 프로토콜을 변환하면 됩니다. IoT Edge는 Azure IoT Central 애플리케이션의 에지로 프로세싱을 오프로드하는 에지의 인텔리전스 시나리오를 지원합니다.

## <a name="security"></a>보안

디바이스와 Azure IoT Central 간에 교환되는 모든 데이터는 암호화됩니다. IoT Hub는 디바이스 연결 IoT Hub 엔드포인트 중 하나에 연결하는 디바이스의 모든 요청을 인증합니다. 유선으로 자격 증명을 교환하지 못하도록 방지하기 위해 디바이스에서는 서명된 토큰을 사용하여 인증합니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어를](../../iot-hub/iot-hub-devguide-security.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

장치 개발자인 경우 다음과 같은 다음 단계로 제안되는 몇 가지 단계를 수행합니다.

- [Azure CLI를 사용하여 장치 연결을 모니터링하는](./howto-monitor-devices-azure-cli.md) 방법 알아보기
- [Azure IoT Central 응용 프로그램에서 새 IoT 장치 유형을 정의하는](./howto-set-up-template.md) 방법 알아보기
- Azure [IoT 에지 장치 및 Azure IoT 센트럴에](./concepts-iot-edge.md) 대해 읽어보기
