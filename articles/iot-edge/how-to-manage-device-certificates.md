---
title: 장치 인증서 관리-Azure IoT Edge | Microsoft Docs
description: 프로덕션 배포를 준비 하려면 Azure IoT Edge 장치에서 테스트 인증서를 만들어 설치 하 고 관리 합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4159b464493a34e17a04f17540b3f9c7a20f4740
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971798"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>IoT Edge 장치에서 인증서 관리

모든 IoT Edge 디바이스는 인증서를 사용하여 런타임과 디바이스에서 실행되는 모듈 간에 보안 연결을 만듭니다. 게이트웨이로 작동 하는 IoT Edge 장치는 이러한 동일한 인증서를 사용 하 여 다운스트림 장치에도 연결 합니다.

## <a name="install-production-certificates"></a>프로덕션 인증서 설치

IoT Edge를 처음 설치 하 고 장치를 프로 비전 할 때 서비스를 테스트할 수 있도록 장치가 임시 인증서로 설정 됩니다.
이러한 임시 인증서는 90 일 후에 만료 되거나 컴퓨터를 다시 시작 하 여 다시 설정할 수 있습니다.
프로덕션 시나리오로 이동 하거나 게이트웨이 장치를 만들려는 경우 사용자 고유의 인증서를 제공 해야 합니다.
이 문서에서는 IoT Edge 장치에 인증서를 설치 하는 단계를 보여 줍니다.

여러 종류의 인증서 및 해당 역할에 대 한 자세한 내용은 [Azure IoT Edge에서 인증서를 사용 하는 방법 이해](iot-edge-certs.md)를 참조 하세요.

>[!NOTE]
>이 문서 전체에서 사용 되는 "루트 CA" 라는 용어는 IoT 솔루션에 대 한 인증서 체인의 최상위 권한 공용 인증서를 나타냅니다. 게시 된 인증 기관의 인증서 루트나 조직의 인증 기관 루트를 사용할 필요는 없습니다. 대부분의 경우에는 실제로 중간 CA 공용 인증서입니다.

### <a name="prerequisites"></a>필수 구성 요소

* IoT Edge 장치입니다.

  IoT Edge 장치를 설정 하지 않은 경우 Azure 가상 머신에서 만들 수 있습니다. 빠른 시작 문서 중 하나에 있는 단계에 따라 [가상 Linux 장치를 만들거나](quickstart-linux.md) [가상 Windows 장치를 만듭니다](quickstart.md).

* 루트 CA (인증 기관) 인증서가 Baltimore, Verisign, DigiCert 또는 GlobalSign과 같은 신뢰할 수 있는 상용 인증 기관에서 구매한 CA (인증 기관) 인증서가 있어야 합니다.

  루트 인증 기관이 아직 없지만 프로덕션 인증서 (예: 게이트웨이 시나리오)가 필요한 IoT Edge 기능을 사용해 보려는 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다.

### <a name="create-production-certificates"></a>프로덕션 인증서 만들기

사용자 고유의 인증 기관을 사용 하 여 다음 파일을 만들어야 합니다.

* 루트 CA
* 디바이스 CA 인증서
* 장치 CA 개인 키

이 문서에서 *루트 CA* 를 참조 하는 것은 조직에 대 한 최상위 인증 기관이 아닙니다. IoT Edge 시나리오에 대 한 가장 높은 인증 기관으로, IoT Edge 허브 모듈, 사용자 모듈 및 모든 다운스트림 장치에서 서로 트러스트를 설정 하는 데 사용 합니다.

> [!NOTE]
> 현재 libiothsm의 제한으로 인해 2038 년 1 월 1 일 이후에 만료 되는 인증서를 사용할 수 없습니다.

이러한 인증서의 예를 보려면 예제 [및 자습서에 대 한 테스트 CA 인증서 관리](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)에서 데모 인증서를 만드는 스크립트를 검토 합니다.

### <a name="install-certificates-on-the-device"></a>장치에 인증서 설치

IoT Edge 장치에 인증서 체인을 설치 하 고 새 인증서를 참조 하도록 IoT Edge 런타임을 구성 합니다.

예를 들어 샘플 스크립트를 사용 하 여 [데모 인증서를 만든](how-to-create-test-certificates.md)경우 다음 파일을 IoT-Edge 장치에 복사 합니다.

* 장치 CA 인증서: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 장치 CA 개인 키: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 루트 CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 3 개의 인증서 및 키 파일을 IoT Edge 장치에 복사 합니다.

   [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 또는 [보안 복사 프로토콜](https://www.ssh.com/ssh/scp/) 같은 기능을 사용하여 인증서 파일을 이동할 수 있습니다.  IoT Edge 장치 자체에서 인증서를 생성 한 경우이 단계를 건너뛰고 작업 디렉터리에 대 한 경로를 사용할 수 있습니다.

1. IoT Edge 보안 디먼 구성 파일을 엽니다.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. 구성. yaml의 **인증서** 속성을 IoT Edge 장치의 인증서 및 키 파일에 대 한 파일 URI 경로로 설정 합니다. `#`네 줄의 주석 처리를 제거 하려면 인증서 속성 앞의 문자를 제거 합니다. **인증서:** 줄에 앞에 공백이 없고 중첩 된 항목이 두 개의 공백으로 들여쓰기 되는지 확인 합니다. 예를 들면 다음과 같습니다.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. Linux 장치에서 사용자 **iotedge** 에 인증서가 포함 된 디렉터리에 대 한 읽기 권한이 있는지 확인 합니다.

1. 이전에 장치에서 IoT Edge 하는 데 다른 인증서를 사용한 경우 IoT Edge를 시작 하거나 다시 시작 하기 전에 다음 두 디렉터리에서 파일을 삭제 합니다.

   * Windows: `C:\ProgramData\iotedge\hsm\certs` 및 `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` 및 `/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>인증서 수명 사용자 지정

IoT Edge은 다음을 비롯 한 여러 가지 경우에 장치에서 인증서를 자동으로 생성 합니다.

* IoT Edge를 설치 하 고 프로 비전 할 때 사용자 고유의 프로덕션 인증서를 제공 하지 않으면 IoT Edge security manager에서 자동으로 **장치 CA 인증서**를 생성 합니다. 이 자체 서명 된 인증서는 프로덕션이 아닌 개발 및 테스트 시나리오에만 사용할 수 있습니다. 이 인증서는 90 일 후에 만료 됩니다.
* 또한 IoT Edge security manager는 장치 CA 인증서로 서명 된 **작업 ca 인증서** 를 생성 합니다.

IoT Edge 장치에서 다양 한 인증서의 기능에 대 한 자세한 내용은 [Azure IoT Edge 인증서를 사용 하는 방법 이해](iot-edge-certs.md)를 참조 하세요.

이러한 두 개의 자동으로 생성 된 인증서의 경우 구성에서 **auto_generated_ca_lifetime_days** 플래그를 설정 하 여 인증서의 수명 기간 (일)을 구성할 수 있습니다.

>[!NOTE]
>IoT Edge security manager에서 만든 세 번째 자동 생성 인증서 인 **IoT Edge hub 서버 인증서**가 있습니다. 이 인증서는 항상 90 일 수명을 갖지만 만료 되기 전에 자동으로 갱신 됩니다. **Auto_generated_ca_lifetime_days** 값은이 인증서에 영향을 주지 않습니다.

인증서 만료를 기본 90 일 이외의 값으로 구성 하려면 해당 값을 **구성. yaml** 파일의 **certificate** 섹션에 추가 합니다.

지정 된 일 수 후에 만료 되 면 장치 CA 인증서를 다시 생성 하기 위해 IoT Edge 보안 디먼을 다시 시작 해야 하며, 자동으로 갱신 되지 않습니다.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

> [!NOTE]
> 현재 libiothsm의 제한으로 인해 2038 년 1 월 1 일 이후에 만료 되는 인증서를 사용할 수 없습니다.

Config.xml 파일에서 값을 지정한 후에는 다음 단계를 수행 합니다.

1. 폴더의 내용을 삭제 `hsm` 합니다.

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux: `/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. IoT Edge 서비스를 다시 시작 합니다.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. 수명 설정을 확인 합니다.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   **프로덕션 준비 상태: 인증서** 확인의 출력을 확인 하 여 자동으로 생성 된 장치 CA 인증서가 만료 될 때까지 남은 일 수를 나열 합니다.

## <a name="next-steps"></a>다음 단계

프로덕션 환경에 솔루션을 배포 하기 전에 IoT Edge 장치에 인증서를 설치 해야 합니다. [프로덕션 환경에서 IoT Edge 솔루션 배포를 준비](production-checklist.md)하는 방법에 대해 자세히 알아보세요.
