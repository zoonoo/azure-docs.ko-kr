---
title: X.509 인증서를 사용하여 DPS로 장치를 자동으로 프로비저닝 - Azure IoT Edge | 마이크로 소프트 문서
description: X.509 인증서를 사용하여 장치 프로비저닝 서비스를 사용하여 Azure IoT 가장자리에 대한 자동 장치 프로비저닝테스트
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 48c8179af4a4b69924fb943ac98918b48d3a2008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537363"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>X.509 인증서를 사용하여 IoT Edge 장치 생성 및 프로비전

Azure [IoT Hub 장치 프로비저닝 서비스(DPS)를](../iot-dps/index.yml)사용하면 X.509 인증서를 사용하여 IoT Edge 장치를 자동으로 프로비전할 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](../iot-dps/concepts-auto-provisioning.md)을 검토하세요.

이 문서에서는 다음 단계를 통해 IoT Edge 장치에서 X.509 인증서를 사용하여 장치 프로비저닝 서비스 등록을 만드는 방법을 보여 주며 다음과 같은 단계를 수행합니다.

* 인증서와 키를 생성합니다.
* 장치에 대한 개별 등록 또는 장치 집합에 대한 그룹 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT Hub에 장치를 등록합니다.

X.509 인증서를 증명 메커니즘으로 사용하면 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. 일반적으로 X.509 인증서는 인증서 신뢰 체인에 정렬됩니다. 자체 서명또는 신뢰할 수 있는 루트 인증서로 시작하여 체인의 각 인증서는 다음 하위 인증서에 서명합니다. 이 패턴은 루트 인증서에서 각 중간 인증서를 거쳐 장치에 설치된 최종 "리프" 인증서에 이르기까지 위임된 신뢰 체인을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 IoT Hub
* IoT Edge 장치일 물리적 또는 가상 장치입니다.
* [Git의](https://git-scm.com/download/) 최신 버전이 설치되었습니다.
* IoT 허브에 연결된 Azure의 IoT 허브 장치 프로비저닝 서비스의 인스턴스입니다.
  * 장치 프로비저닝 서비스 인스턴스가 없는 경우 [IoT Hub DPS 설정의](../iot-dps/quick-setup-auto-provision.md)지침을 따릅니다.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="generate-device-identity-certificates"></a>장치 ID 인증서 생성

장치 ID 인증서는 신뢰 인증서 체인을 통해 상위 X.509 인증 기관(CA) 인증서에 연결하는 리프 인증서입니다. 장치 ID 인증서에는 장치가 IoT 허브에 갖도록 하려는 장치 ID로 공통 이름(CN)이 설정되어 있어야 합니다.

장치 ID 인증서는 IoT Edge 장치를 프로비전하고 Azure IoT Hub를 사용하여 장치를 인증하는 데만 사용됩니다. IoT Edge 장치가 확인을 위해 모듈 또는 리프 장치에 제공하는 CA 인증서와 달리 인증서에 서명하지 않습니다. 자세한 내용은 [Azure IoT Edge 인증서 사용 세부 정보를](iot-edge-certs.md)참조하십시오.

장치 ID 인증서를 만든 후에는 인증서의 공용 부분이 포함된 .cer 또는 .pem 파일과 인증서의 개인 키가 있는 .cer 또는 .pem 파일이라는 두 개의 파일이 있어야 합니다. DPS에서 그룹 등록을 사용하려는 경우 동일한 트러스트 인증서 체인에 중간 또는 루트 CA 인증서의 공용 부분도 필요합니다.

### <a name="use-test-certificates"></a>테스트 인증서 사용

새 ID 인증서를 만들 수 있는 인증서 권한이 없고 이 시나리오를 사용해 보고 하려는 경우 Azure IoT Edge git 리포지토리에는 테스트 인증서를 생성하는 데 사용할 수 있는 스크립트가 포함되어 있습니다. 이러한 인증서는 개발 테스트용으로만 설계되었으며 프로덕션 환경에서 사용해서는 안 됩니다.

테스트 인증서를 만들려면 데모 [인증서 만들기의 단계를 수행하여 IoT Edge 장치 기능을 테스트합니다.](how-to-create-test-certificates.md) 인증서 생성 스크립트를 설정하고 루트 CA 인증서를 만드는 데 필요한 두 섹션을 완료합니다. 그런 다음 단계에 따라 장치 ID 인증서를 만듭니다. 작업이 완료되면 다음 인증서 체인과 키 쌍이 있어야 합니다.

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

IoT Edge 장치에서 이러한 인증서가 모두 필요합니다. DPS에서 개별 등록을 사용하려는 경우 .cert.pem 파일을 업로드합니다. DPS에서 그룹 등록을 사용하려는 경우 업로드할 동일한 인증서 체인에 있는 중간 또는 루트 CA 인증서도 필요합니다. 데모 인증서를 사용하는 경우 그룹 등록에 인증서를 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 사용합니다.

## <a name="create-a-dps-individual-enrollment"></a>DPS 개별 등록 만들기

생성된 인증서와 키를 사용하여 단일 IoT Edge 장치에 대한 DPS에 개별 등록을 생성합니다. 개별 등록은 장치의 ID 인증서의 공용 부분을 차지하고 장치의 인증서와 일치합니다.

여러 IoT Edge 장치를 프로비전하려는 경우 다음 [섹션의](#create-a-dps-group-enrollment)단계를 따르십시오.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태**를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-monitor.md)를 만드는 데 사용됩니다.

장치 프로비저닝 서비스의 등록에 대한 자세한 내용은 [장치 등록을 관리하는 방법을 참조하세요.](../iot-dps/how-to-manage-enrollments.md)

1. Azure [포털에서](https://portal.azure.com)IoT 허브 장치 프로비저닝 서비스의 인스턴스로 이동합니다.

1. **설정**에서 **등록 관리**를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가**를 선택한 다음, 다음 단계를 완료합니다.  

   * **메커니즘**: **X.509를**선택합니다.

   * **기본 인증서 .pem 또는 .cer 파일**: 장치 ID 인증서에서 공용 파일을 업로드합니다. 스크립트를 사용하여 테스트 인증서를 생성하는 경우 다음 파일을 선택합니다.

      `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`

   * **IoT 허브 장치 ID**: 원하는 경우 장치에 ID를 제공합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 장치 ID를 제공하지 않으면 X.509 인증서의 CN(일반 이름)이 사용됩니다.

   * **IoT Edge 장치**: **True를** 선택하여 등록이 IoT Edge 장치에 대한 것이라고 선언합니다.

   * **이 장치에 할당할 수 있는 IoT 허브선택:** 장치를 연결할 연결된 IoT 허브를 선택합니다. 여러 허브를 선택할 수 있으며 선택한 할당 정책에 따라 장치가 해당 허브 중 하나에 할당됩니다.

   * **초기 장치 트윈 상태**: 원하는 경우 장치 쌍에 추가할 태그 값을 추가합니다. 태그를 사용하여 자동 배포를 위해 장치 그룹을 대상으로 지정할 수 있습니다. 예를 들어:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **저장**을 선택합니다.

이제 이 장치에 대한 등록이 있으므로 IoT Edge 런타임은 설치 하는 동안 장치를 자동으로 프로비전할 수 있습니다. [IoT Edge 런타임 설치](#install-the-iot-edge-runtime) 섹션을 계속 하여 IoT Edge 장치를 설정합니다.

## <a name="create-a-dps-group-enrollment"></a>DPS 그룹 등록 만들기

생성된 인증서 및 키를 사용하여 여러 IoT Edge 장치에 대한 DPS에 그룹 등록을 만듭니다. 그룹 등록은 개별 장치 ID 인증서를 생성하는 데 사용되는 트러스트 인증서 체인의 중간 또는 루트 CA 인증서를 사용합니다.

대신 단일 IoT Edge 장치를 프로비전하려는 경우 이전 섹션의 단계인 [DPS 개별 등록 만들기의](#create-a-dps-individual-enrollment)단계를 따릅니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태**를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-monitor.md)를 만드는 데 사용됩니다.

### <a name="verify-your-root-certificate"></a>루트 인증서 확인

등록 그룹을 만들 때 확인된 인증서를 사용할 수 있습니다. 루트 인증서의 소유권이 있음을 증명하여 DPS를 통해 인증서를 확인할 수 있습니다. 자세한 내용은 [X.509 CA 인증서에 대한 소유 증명을 수행하는 방법을](../iot-dps/how-to-verify-certificates.md)참조하십시오.

1. Azure [포털에서](https://portal.azure.com)IoT 허브 장치 프로비저닝 서비스의 인스턴스로 이동합니다.

1. 왼쪽 메뉴에서 **인증서를** 선택합니다.

1. 새 인증서를 추가하려면 **추가를** 선택합니다.

1. 인증서에 대한 친숙한 이름을 입력한 다음 X.509 인증서의 공용 부분을 나타내는 .cer 또는 .pem 파일을 찾아봅니다.

   데모 인증서를 사용하는 경우 인증서를 `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` 업로드합니다.

1. **저장**을 선택합니다.

1. 이제 인증서가 **인증서** 페이지에 나열됩니다. 인증서 세부 정보를 열려면 선택합니다.

1. **확인 코드 생성을** 선택한 다음 생성된 코드를 복사합니다.

1. 자체 CA 인증서를 가져오든 데모 인증서를 사용하든 IoT Edge 리포지토리에 제공된 확인 도구를 사용하여 소유 증명을 확인할 수 있습니다. 확인 도구는 CA 인증서를 사용하여 제공된 확인 코드가 주체 이름으로 있는 새 인증서에 서명합니다.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Azure 포털의 동일한 인증서 세부 정보 페이지에서 새로 생성된 확인 인증서를 업로드합니다.

1. **확인**을 선택합니다.

### <a name="create-enrollment-group"></a>등록 그룹 만들기

장치 프로비저닝 서비스의 등록에 대한 자세한 내용은 [장치 등록을 관리하는 방법을 참조하세요.](../iot-dps/how-to-manage-enrollments.md)

1. Azure [포털에서](https://portal.azure.com)IoT 허브 장치 프로비저닝 서비스의 인스턴스로 이동합니다.

1. **설정**에서 **등록 관리**를 선택합니다.

1. **등록 그룹 추가를** 선택한 다음 다음 단계를 완료하여 등록을 구성합니다.

   * **그룹 이름**: 이 그룹 등록에 대해 기억하기 좋은 이름을 제공합니다.

   * **증명 유형**: **인증서**선택 .

   * **IoT 에지 장치**: **True를 선택합니다.** 그룹 등록의 경우 모든 장치는 IoT Edge 장치여야 하며 그 중 어느 것도 할 수 없습니다.

   * **인증서 유형**: DPS와 함께 저장된 검증된 CA 인증서가 있는 경우 **CA** 인증서를 선택하거나 이 등록을 위해 새 파일을 업로드하려는 경우 **중간 인증서를** 선택합니다.

   * **기본 인증서**: 마지막 섹션에서 CA 인증서를 선택한 경우 드롭다운 목록에서 인증서를 선택합니다. 중간 인증서를 선택한 경우 장치 ID 인증서를 생성하는 데 사용된 신뢰 인증서 체인의 CA 인증서에서 공용 파일을 업로드합니다.

   * **이 장치에 할당할 수 있는 IoT 허브선택:** 장치를 연결할 연결된 IoT 허브를 선택합니다. 여러 허브를 선택할 수 있으며 선택한 할당 정책에 따라 장치가 해당 허브 중 하나에 할당됩니다.

   * **초기 장치 트윈 상태**: 원하는 경우 장치 쌍에 추가할 태그 값을 추가합니다. 태그를 사용하여 자동 배포를 위해 장치 그룹을 대상으로 지정할 수 있습니다. 예를 들어:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **저장**을 선택합니다.

이제 이 장치에 대한 등록이 있으므로 IoT Edge 런타임은 설치 하는 동안 장치를 자동으로 프로비전할 수 있습니다. 다음 섹션으로 계속 이동하여 IoT Edge 장치를 설정합니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 디바이스에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 디바이스에 추가 컨테이너의 배포를 허용합니다.

DPS를 갖춘 X.509 프로비전은 IoT Edge 버전 1.0.9 이상에서만 지원됩니다.

기기를 프로비전할 때 다음 정보가 필요합니다.

* DPS **ID 범위** 값입니다. Azure 포털에서 DPS 인스턴스의 개요 페이지에서 이 값을 검색할 수 있습니다.
* 장치의 장치 ID 인증서 파일입니다.
* 장치의 장치 ID 키 파일입니다.
* 선택적 등록 ID(제공되지 않은 경우 장치 ID 인증서의 일반 이름에서 가져온 값).

### <a name="linux-device"></a>리눅스 장치

다음 링크를 사용하여 장치의 아키텍처에 적합한 명령을 사용하여 장치에 Azure IoT Edge 런타임을 설치합니다. 보안 데몬 구성 섹션에 도달하면 수동이 아닌 X.509 자동 프로비저닝에 대한 IoT Edge 런타임을 구성합니다. 이 문서의 이전 섹션을 완료한 후 필요한 모든 정보와 인증서 파일이 있어야 합니다.

[리눅스에 Azure IoT 에지 런타임 설치](how-to-install-iot-edge-linux.md)

config.yaml 파일에 X.509 인증서 및 키 정보를 추가할 때 경로는 파일 URI로 제공되어야 합니다. 예를 들어:

* `file:///<path>/identity_certificate.pem`
* `file:///<path>/identity_key.pem`

X.509 자동 프로비저닝에 대한 구성 파일의 섹션은 다음과 같습니다.

```yaml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

에 대한 `scope_id` `identity_cert` `identity_pk` 자리 표시자 값을 DPS 인스턴스의 범위 ID로 바꾸고 URI를 장치의 인증서 및 키 파일 위치로 바꿉습니다. 원하는 `registration_id` 경우 장치에 대한 장치를 제공하거나 ID 인증서의 CN 이름으로 장치를 등록하기 위해 주석처리 된이 줄을 둡니다.

config.yaml 파일을 업데이트한 후 항상 보안 데몬을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Windows 디바이스

ID 인증서 및 ID 키를 생성한 장치에 IoT Edge 런타임을 설치합니다. IoT Edge 런타임을 수동이 아닌 자동 프로비저닝용으로 구성합니다.

컨테이너 관리 및 IoT 에지 업데이트와 같은 작업에 대한 필수 구성 프로그램 및 지침을 포함하여 Windows에 IoT Edge를 설치하는 방법에 대한 자세한 내용은 [Windows에서 Azure IoT Edge 런타임 설치를](how-to-install-iot-edge-windows.md)참조하십시오.

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell(x86)이 아닌 IoT Edge를 설치할 때 는 PowerShell의 AMD64 세션을 사용해야 합니다.

1. **Deploy-IoTEdge** 명령은 Windows 컴퓨터가 지원되는 버전에 있는지 확인하고 컨테이너 기능을 켜고 moby 런타임 및 IoT Edge 런타임을 다운로드합니다. 명령은 Windows 컨테이너를 사용하는 데 기본설정됩니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 이 시점에서 IoT Core 장치가 자동으로 다시 시작될 수 있습니다. 다른 Windows 10 또는 Windows 서버 장치에서 다시 시작하라는 메시지가 표시될 수 있습니다. 그렇다면 지금 기기를 다시 시작하십시오. 장치가 준비되면 PowerShell을 관리자로 다시 실행합니다.

1. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 자동 프로비저닝을 사용 하려면 `-Dps` 플래그를 사용 하지 않는 한 명령은 수동 프로비저닝으로 기본설정 됩니다.

   의 자리 표시자 `{scope_id}` `{identity cert path}`값을 `{identity key path}` 및 DPS 인스턴스의 적절한 값과 장치의 파일 경로로 바꿉습니다. 등록 ID를 지정하려면 자리 `-RegistrationId {registration_id}` 표시자를 적절하게 교체하는 것도 포함합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >config.yaml 파일은 인증서와 주요 정보를 파일 URI로 저장합니다. 그러나 Initialize-IoTEdge 명령은 이 서식 단계를 처리하므로 장치의 인증서 및 키 파일에 대한 절대 경로를 제공할 수 있습니다.

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

장치 프로비저닝 서비스에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure 포털에서 장치 프로비저닝 서비스 인스턴스로 이동합니다. 만든 개별 등록에 대한 등록 세부 정보를 엽니다. 등록 상태가 **할당되고** 장치 ID가 나열됩니다.

디바이스에서 다음 명령을 사용하여 런타임이 성공적으로 설치되고 시작되는지 확인합니다.

### <a name="linux-device"></a>리눅스 장치

IoT Edge 서비스의 상태를 확인합니다.

```cmd/sh
systemctl status iotedge
```

서비스 로그를 검사합니다.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

실행 중인 모듈을 나열합니다.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows 디바이스

IoT Edge 서비스의 상태를 확인합니다.

```powershell
Get-Service iotedge
```

서비스 로그를 검사합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

실행 중인 모듈을 나열합니다.

```powershell
iotedge list
```

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. Azure 포털을 사용하거나 [Azure CLI를 사용하여](how-to-deploy-monitor-cli.md)규모에 따라 [IoT Edge 모듈을 배포하고 모니터링하는](how-to-deploy-monitor.md) 방법에 대해 알아봅니다.
