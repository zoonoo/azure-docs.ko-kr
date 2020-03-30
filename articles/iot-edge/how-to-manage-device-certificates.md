---
title: 장치 인증서 관리 - Azure IoT 에지 | 마이크로 소프트 문서
description: Azure IoT Edge 장치에서 테스트 인증서를 만들고 설치 및 관리하여 프로덕션 배포를 준비합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539209"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>IoT 에지 장치에서 인증서 관리

모든 IoT Edge 장치는 인증서를 사용하여 런타임과 장치에서 실행되는 모든 모듈 간의 보안 연결을 만듭니다. 게이트웨이로 작동하는 IoT Edge 장치는 이러한 동일한 인증서를 사용하여 다운스트림 장치에도 연결합니다.

## <a name="install-production-certificates"></a>프로덕션 인증서 설치

IoT Edge를 처음 설치하고 장치를 프로비전하면 서비스를 테스트할 수 있도록 장치가 임시 인증서로 설정됩니다.
이러한 임시 인증서는 90일 후에 만료되거나 컴퓨터를 다시 시작하여 다시 설정할 수 있습니다.
장치를 프로덕션 시나리오로 이동할 준비가 되거나 게이트웨이 시나리오를 만들려면 고유한 인증서를 제공해야 합니다.
이 문서에서는 IoT Edge 장치에 인증서를 설치하는 단계를 보여 줍니다.

IoT Edge 시나리오에서 다양한 인증서 유형 및 해당 역할에 대해 자세히 알아보려면 [Azure IoT Edge에서 인증서를 사용하는 방법 이해하기를](iot-edge-certs.md)참조하십시오.

>[!NOTE]
>이 문서에서 사용되는 "루트 CA"라는 용어는 IoT 솔루션에 대한 인증서 체인의 최상위 권한 공용 인증서를 나타냅니다. 신디케이트된 인증 기관의 인증서 루트 또는 조직의 인증 기관의 루트를 사용할 필요가 없습니다. 대부분의 경우 실제로 는 중간 CA 공용 인증서입니다.

### <a name="prerequisites"></a>사전 요구 사항

* [윈도우](how-to-install-iot-edge-windows.md) 또는 [리눅스에서](how-to-install-iot-edge-linux.md)실행되는 IoT 에지 장치 .
* 볼티모어, Verisign, DigiCert 또는 GlobalSign과 같은 신뢰할 수 있는 상용 인증 기관에서 자체 서명하거나 구입한 CA(루트 인증 기관) 인증서가 있습니다.

아직 루트 인증 기관이 없지만 프로덕션 인증서(예: 게이트웨이 시나리오)가 필요한 IoT Edge 기능을 사용해 보고 싶은 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다.

### <a name="create-production-certificates"></a>생산 인증서 만들기

사용자 고유의 인증서 기관을 사용하여 다음 파일을 만들어야 합니다.

* 루트 CA
* 디바이스 CA 인증서
* 장치 CA 개인 키

이 문서에서 *루트 CA라고* 부르는 것은 조직의 최상위 인증 기관이 아닙니다. IoT Edge 허브 모듈, 사용자 모듈 및 다운스트림 장치가 서로 신뢰를 구축하는 데 사용하는 IoT Edge 시나리오의 최상위 인증 기관입니다.

이러한 인증서의 예를 보려면 [샘플 및 자습서에 대한 테스트 CA 인증서 관리에서 데모 인증서를 만드는 스크립트를 검토합니다.](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)

### <a name="install-certificates-on-the-device"></a>장치에 인증서 설치

IoT Edge 장치에 인증서 체인을 설치하고 IoT Edge 런타임을 구성하여 새 인증서를 참조합니다.

예를 들어 샘플 스크립트를 사용하여 [데모 인증서를 만드는](how-to-create-test-certificates.md)경우 다음 파일을 IoT-Edge 장치에 복사합니다.

* 장치 CA 인증서:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 장치 CA 개인 키:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 루트 CA:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 세 개의 인증서와 키 파일을 IoT Edge 장치에 복사합니다.

   [Azure Key Vault와](https://docs.microsoft.com/azure/key-vault) 같은 서비스 또는 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용하여 인증서 파일을 이동할 수 있습니다.  IoT Edge 장치 자체에서 인증서를 생성한 경우 이 단계를 건너뛰고 작업 디렉터리에 대한 경로를 사용할 수 있습니다.

1. IoT Edge 보안 디먼 구성 파일을 엽니다.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. config.yaml 파일의 **인증서** 속성을 IoT Edge 장치의 인증서 및 키 파일에 대한 전체 경로로 설정합니다. 인증서 `#` 속성 앞에 있는 문자를 제거하여 네 줄의 주석을 해제합니다. **인증서:** 줄에 이전 공백이 없고 중첩된 항목이 두 공백으로 들여쓰기되는지 확인합니다. 예를 들어:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. Linux 장치에서 는 사용자 **iotedge가** 인증서를 보유한 디렉터리에 대한 읽기 권한을 가지고 있는지 확인합니다.

1. 이전에 장치에서 IoT Edge에 대한 다른 인증서를 사용한 적이 있는 경우 IoT Edge를 시작하거나 다시 시작하기 전에 다음 두 디렉터리의 파일을 삭제합니다.

   * 윈도우: `C:\ProgramData\iotedge\hsm\certs` 및`C:\ProgramData\iotedge\hsm\cert_keys`

   * 리눅스 : `/var/lib/iotedge/hsm/certs` 그리고`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>인증서 수명 사용자 지정

IoT Edge는 다음과 같은 여러 경우에 장치에서 인증서를 자동으로 생성합니다.

* IoT Edge를 설치하고 프로비전할 때 자체 프로덕션 인증서를 제공하지 않으면 IoT Edge 보안 관리자가 **자동으로 장치 CA 인증서를**생성합니다. 이 자체 서명된 인증서는 프로덕션이 아닌 개발 및 테스트 시나리오만을 위한 것입니다. 이 인증서는 90일 후에 만료됩니다.
* 또한 IoT Edge 보안 관리자는 장치 **CA 인증서에** 의해 서명된 워크로드 CA 인증서를 생성합니다.

IoT Edge 장치에서 다른 인증서의 기능에 대한 자세한 내용은 [Azure IoT Edge에서 인증서를 사용하는 방법 이해하기를](iot-edge-certs.md)참조하십시오.

이 두 개의 자동으로 생성된 인증서의 경우 auto_generated_ca_lifetime_days **플래그를** config.yaml에 설정하여 인증서의 수명 동안 일 수를 구성할 수 있습니다.

>[!NOTE]
>IoT Edge 보안 관리자가 만드는 세 번째 자동 생성 인증서인 **IoT Edge 허브 서버 인증서가 있습니다.** 이 인증서는 항상 90일이 지만 만료되기 전에 자동으로 갱신됩니다. **auto_generated_ca_lifetime_days** 값은 이 인증서에 영향을 주지 않습니다.

인증서 만료를 기본 90일이 아닌 다른 항목으로 구성하려면 config.yaml 파일의 **인증서** 섹션에 일 의 값을 추가합니다.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

사용자 고유의 장치 CA 인증서를 제공한 경우 설정한 수명 값이 장치 CA 인증서의 수명보다 짧은 경우 이 값은 워크로드 CA 인증서에 계속 적용됩니다.

config.yaml 파일에 플래그를 지정한 후 다음 단계를 수행합니다.

1. `hsm` 폴더의 내용을 삭제합니다.

   윈도우 `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` : 리눅스 :`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. IoT 에지 서비스를 다시 시작합니다.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. 수명 설정을 확인합니다.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   생산 준비 의 출력을 **확인: 인증서** 검사, 자동으로 생성 된 장치 CA 인증서가 만료 될 때까지 일 수를 나열 합니다.

## <a name="next-steps"></a>다음 단계

IoT Edge 장치에 인증서를 설치하는 것은 프로덕션 환경에서 솔루션을 배포하기 전에 필요한 단계입니다. [IoT Edge 솔루션을 프로덕션 환경에서 배포할 준비를](production-checklist.md)하는 방법에 대해 자세히 알아보십시오.
