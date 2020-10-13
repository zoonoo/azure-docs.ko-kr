---
title: X.509 인증서를 사용 하 여 DPS에 장치를 자동으로 프로 비전-Azure IoT Edge | Microsoft Docs
description: X.509 인증서를 사용 하 여 장치 프로 비전 서비스와 Azure IoT Edge에 대 한 자동 장치 프로 비전 테스트
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 13c15eeb98b13d0fe9a5b7797ec942209d403cc6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447751"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>X.509 인증서를 사용 하 여 IoT Edge 장치 만들기 및 프로 비전

[Azure IoT Hub 장치 프로 비전 서비스 (DPS)](../iot-dps/index.yml)를 사용 하면 x.509 인증서를 사용 하 여 IoT Edge 장치를 자동으로 프로 비전 할 수 있습니다. 자동 프로비저닝 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비저닝](../iot-dps/about-iot-dps.md#provisioning-process) 개요를 검토하세요.

이 문서에서는 다음 단계를 수행 하 여 IoT Edge 장치에서 x.509 인증서를 사용 하 여 장치 프로 비전 서비스 등록을 만드는 방법을 보여 줍니다.

* 인증서 및 키를 생성 합니다.
* 장치에 대 한 개별 등록 또는 장치 집합에 대 한 그룹 등록을 만듭니다.
* IoT Edge 런타임을 설치 하 고 IoT Hub에 장치를 등록 합니다.

X.509 인증서를 증명 메커니즘으로 사용하면 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. 일반적으로 x.509 인증서는 신뢰의 인증서 체인에 정렬 됩니다. 자체 서명 되거나 신뢰할 수 있는 루트 인증서로 시작 하는 체인의 각 인증서는 다음으로 낮은 인증서에 서명 합니다. 이 패턴은 루트 인증서에서 각 중간 인증서를 통해 장치에 설치 된 최종 "리프" 인증서로 위임 된 신뢰 체인을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 IoT Hub
* IoT Edge 장치에 대 한 실제 또는 가상 장치입니다.
* 최신 버전의 [Git](https://git-scm.com/download/) 이 설치 되어 있습니다.
* IoT Hub에 연결 된 Azure의 IoT Hub Device Provisioning Service 인스턴스입니다.
  * 장치 프로 비전 서비스 인스턴스가 없는 경우 [IOT HUB DPS 설정](../iot-dps/quick-setup-auto-provision.md)의 지침을 따르세요.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="generate-device-identity-certificates"></a>장치 id 인증서 생성

장치 id 인증서는 신뢰의 인증서 체인을 통해 상위 x.509 CA (인증 기관) 인증서에 연결 하는 리프 인증서입니다. 장치 id 인증서에는 장치를 IoT hub에 포함 하려는 장치 ID로 설정 된 CN (일반 이름)이 있어야 합니다.

장치 id 인증서는 IoT Edge 장치를 프로 비전 하 고 Azure IoT Hub 장치를 인증 하는 데만 사용 됩니다. 이러한 인증서는 IoT Edge 장치가 모듈에 제공 하는 CA 인증서 또는 확인을 위한 리프 장치에 대 한 것과 달리 인증서에 서명 하지 않습니다. 자세한 내용은 [Azure IoT Edge 인증서 사용 세부](iot-edge-certs.md)정보를 참조 하세요.

장치 id 인증서를 만든 후에는 인증서의 공개 부분을 포함 하는 .cer 또는. i d 파일, 인증서의 개인 키가 포함 된 .cer 또는. d i d 파일의 두 파일이 있어야 합니다. DPS에서 그룹 등록을 사용 하려면 동일한 인증서 신뢰 체인에 중간 또는 루트 CA 인증서의 공용 부분도 필요 합니다.

X.509를 사용 하 여 자동 프로비저닝을 설정 하려면 다음 파일이 필요 합니다.

* 장치 id 인증서 및 해당 개인 키 인증서입니다. 개별 등록을 만들면 장치 id 인증서가 DPS에 업로드 됩니다. 개인 키가 IoT Edge 런타임에 전달 됩니다.
* 하나 이상의 장치 id와 중간 인증서가 있어야 하는 전체 체인 인증서. 전체 체인 인증서는 IoT Edge 런타임에 전달 됩니다.
* 인증서 신뢰 체인의 중간 또는 루트 CA 인증서입니다. 이 인증서는 그룹 등록을 만드는 경우 DPS에 업로드 됩니다.

> [!NOTE]
> 현재 libiothsm의 제한으로 인해 2038 년 1 월 1 일 이후에 만료 되는 인증서를 사용할 수 없습니다.

### <a name="use-test-certificates"></a>테스트 인증서 사용

새 id 인증서를 만드는 데 사용할 수 있는 인증 기관이 없고이 시나리오를 사용해 보려는 경우 git 리포지토리에 Azure IoT Edge는 테스트 인증서를 생성 하는 데 사용할 수 있는 스크립트가 포함 되어 있습니다. 이러한 인증서는 개발 테스트용 으로만 설계 되었으며 프로덕션 환경에서는 사용 하지 않아야 합니다.

테스트 인증서를 만들려면 데모 인증서 만들기의 단계에 따라 [IoT Edge 장치 기능을 테스트](how-to-create-test-certificates.md)합니다. 인증서 생성 스크립트를 설정 하 고 루트 CA 인증서를 만들기 위한 두 가지 필수 섹션을 완료 합니다. 그런 다음 장치 id 인증서를 만드는 단계를 수행 합니다. 완료 되 면 다음 인증서 체인 및 키 쌍이 있어야 합니다.

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

IoT Edge 장치에는 이러한 인증서가 모두 필요 합니다. DPS에서 개별 등록을 사용 하려는 경우에는. cert. pem 파일을 업로드 합니다. DPS에서 그룹 등록을 사용 하려는 경우 업로드할 신뢰의 동일한 인증서 체인에 중간 또는 루트 CA 인증서도 필요 합니다. 데모 인증서를 사용 하는 경우 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 그룹 등록에 인증서를 사용 합니다.

## <a name="create-a-dps-individual-enrollment"></a>DPS 개별 등록 만들기

단일 IoT Edge 장치에 대해 DPS에서 개별 등록을 만들려면 생성 된 인증서와 키를 사용 합니다. 개별 등록 장치의 id 인증서의 공개 부분을 가져와서 장치의 인증서와 일치 시킵니다.

여러 IoT Edge 장치를 프로 비전 하려는 경우 다음 섹션인 [DPS 그룹 등록 만들기](#create-a-dps-group-enrollment)의 단계를 수행 합니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태**를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

장치 프로 비전 서비스의 등록에 대 한 자세한 내용은 [How to manage device 등록](../iot-dps/how-to-manage-enrollments.md)항목을 참조 하세요.

   > [!TIP]
   > Azure CLI에서 [등록](/cli/azure/ext/azure-iot/iot/dps/enrollment) 또는 [등록 그룹](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) 을 만들고,에 **지 사용** 플래그를 사용 하 여 장치 또는 장치 그룹이 IoT Edge 장치 임을 지정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동 합니다.

1. **설정**에서 **등록 관리**를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가**를 선택한 다음, 다음 단계를 완료합니다.  

   * **메커니즘**: **x.509**을 선택 합니다.

   * **기본 인증서. pem 또는 .cer 파일**: 장치 id 인증서에서 공용 파일을 업로드 합니다. 스크립트를 사용 하 여 테스트 인증서를 생성 한 경우 다음 파일을 선택 합니다.

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub 장치 id**: 원하는 경우 장치에 대 한 id를 제공 합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 장치 ID를 제공 하지 않으면 x.509 인증서의 CN (일반 이름)이 사용 됩니다.

   * **IoT Edge 장치**: IoT Edge 장치에 대 한 등록이 되도록 선언 하려면 **True** 를 선택 합니다.

   * **이 장치를 할당할 수 있는 iot Hub 선택**: 장치를 연결 하려는 연결 된 iot hub를 선택 합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 장치 중 하나에 장치가 할당 됩니다.

   * **초기 장치 쌍 상태**: 원하는 경우 장치 쌍에 추가할 태그 값을 추가 합니다. 태그를 사용 하 여 자동 배포를 위한 장치 그룹을 대상으로 지정할 수 있습니다. 예를 들면 다음과 같습니다.

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

이제이 장치에 대 한 등록이 있으므로 IoT Edge 런타임은 설치 중에 장치를 자동으로 프로 비전 할 수 있습니다. [IoT Edge 런타임 설치](#install-the-iot-edge-runtime) 섹션으로 이동 하 여 IoT Edge 장치를 설정 합니다.

## <a name="create-a-dps-group-enrollment"></a>DPS 그룹 등록 만들기

생성 된 인증서 및 키를 사용 하 여 DPS에서 여러 IoT Edge 장치에 대 한 그룹 등록을 만듭니다. 그룹 등록 개별 장치 id 인증서를 생성 하는 데 사용 되는 신뢰의 인증서 체인에서 중간 또는 루트 CA 인증서를 사용 합니다.

단일 IoT Edge 장치를 대신 프로 비전 하려는 경우 이전 섹션인 [DPS 개별 등록 만들기](#create-a-dps-individual-enrollment)의 단계를 따르세요.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태**를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

### <a name="verify-your-root-certificate"></a>루트 인증서 확인

등록 그룹을 만들 때 확인 된 인증서를 사용 하는 옵션이 있습니다. 루트 인증서의 소유권이 있음을 증명 하 여 DPS로 인증서를 확인할 수 있습니다. 자세한 내용은 [X.509 CA 인증서의 소유 증명을 수행 하는 방법](../iot-dps/how-to-verify-certificates.md)을 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동 합니다.

1. 왼쪽 메뉴에서 **인증서** 를 선택 합니다.

1. **추가** 를 선택 하 여 새 인증서를 추가 합니다.

1. 인증서의 이름을 입력 하 고 x.509 인증서의 공개 부분을 나타내는 .cer 또는. X X X 파일로 이동 합니다.

   데모 인증서를 사용 하는 경우 인증서를 업로드 `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` 합니다.

1. **저장**을 선택합니다.

1. 이제 **인증서가 인증서 페이지에 나열** 됩니다. 인증서 세부 정보를 열려면 선택 합니다.

1. **확인 코드 생성** 을 선택 하 고 생성 된 코드를 복사 합니다.

1. 사용자 고유의 CA 인증서를 사용 하 든 데모 인증서를 사용 하 든 관계 없이 IoT Edge 리포지토리에 제공 된 확인 도구를 사용 하 여 소유 증명을 확인할 수 있습니다. 확인 도구는 CA 인증서를 사용 하 여 주체 이름으로 제공 된 확인 코드를 포함 하는 새 인증서에 서명 합니다.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Azure Portal의 동일한 인증서 정보 페이지에서 새로 생성 된 확인 인증서를 업로드 합니다.

1. **확인**을 선택합니다.

### <a name="create-enrollment-group"></a>등록 그룹 만들기

장치 프로 비전 서비스의 등록에 대 한 자세한 내용은 [How to manage device 등록](../iot-dps/how-to-manage-enrollments.md)항목을 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동 합니다.

1. **설정**에서 **등록 관리**를 선택합니다.

1. **등록 그룹 추가** 를 선택 하 고 다음 단계를 완료 하 여 등록을 구성 합니다.

   * **그룹 이름**:이 그룹 등록에 대 한 기억 하기 쉬운 이름을 제공 합니다.

   * **증명 형식**: **인증서**를 선택 합니다.

   * **IoT Edge 장치**: **True**를 선택 합니다. 그룹 등록의 경우 모든 장치는 장치를 IoT Edge 해야 합니다. 그렇지 않으면 장치를 모두 사용할 수 없습니다.

   * **인증서 유형**: DPS로 저장 된 확인 된 ca 인증서가 있는 경우 **CA 인증서** 를 선택 하 고,이 등록에 대해서만 새 파일을 업로드 하려는 경우 **중간 인증서** 를 선택 합니다.

   * **기본 인증서**: 마지막 섹션에서 CA 인증서를 선택한 경우 드롭다운 목록에서 인증서를 선택 합니다. 중간 인증서를 선택한 경우 장치 id 인증서를 생성 하는 데 사용 된 인증서 신뢰 체인의 CA 인증서에서 공개 파일을 업로드 합니다.

   * **이 장치를 할당할 수 있는 iot Hub 선택**: 장치를 연결 하려는 연결 된 iot hub를 선택 합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 장치 중 하나에 장치가 할당 됩니다.

   * **초기 장치 쌍 상태**: 원하는 경우 장치 쌍에 추가할 태그 값을 추가 합니다. 태그를 사용 하 여 자동 배포를 위한 장치 그룹을 대상으로 지정할 수 있습니다. 예를 들면 다음과 같습니다.

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

이제이 장치에 대 한 등록이 있으므로 IoT Edge 런타임은 설치 중에 장치를 자동으로 프로 비전 할 수 있습니다. 다음 섹션을 계속 진행 하 여 IoT Edge 장치를 설정 합니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 디바이스에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 디바이스에 추가 컨테이너의 배포를 허용합니다.

X.509를 DPS로 프로 비전 하는 것은 IoT Edge 버전 1.0.9 이상 에서만 지원 됩니다.

장치를 프로 비전 할 때 다음 정보가 필요 합니다.

* DPS **ID 범위** 값입니다. Azure Portal에서 DPS 인스턴스의 개요 페이지에서이 값을 검색할 수 있습니다.
* 장치에서 장치 id 인증서 체인 파일
* 장치에서 장치 id 키 파일입니다.
* 제공 되지 않은 경우 장치 id 인증서의 일반 이름에서 가져온 선택적 등록 ID입니다.

### <a name="linux-device"></a>Linux 장치

다음 링크를 사용 하 여 장치 아키텍처에 적절 한 명령을 사용 하 여 장치에 Azure IoT Edge 런타임을 설치 합니다. 보안 디먼 구성 섹션에 표시 되는 경우 x.509 자동, 수동, 프로 비전에 대 한 IoT Edge 런타임을 구성 합니다. 이 문서의 이전 섹션을 완료 한 후에 필요한 모든 정보 및 인증서 파일을 만들어야 합니다.

[Linux에 Azure IoT Edge 런타임 설치](how-to-install-iot-edge-linux.md)

X.509 인증서 및 키 정보를 config.xml 파일에 추가 하는 경우 경로를 파일 Uri로 제공 해야 합니다. 예를 들면 다음과 같습니다.

* `file:///<path>/identity_certificate_chain.pem`
* `file:///<path>/identity_key.pem`

X.509 자동 프로 비전에 대 한 구성 파일의 섹션은 다음과 같습니다.

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

,,에 대 한 자리 표시자 값을 `scope_id` `identity_cert` `identity_pk` DPS 인스턴스의 범위 ID로, uri를 장치의 인증서 체인 및 키 파일 위치로 바꿉니다. `registration_id`원하는 경우 장치에 대 한를 제공 하거나,이 줄을 주석으로 처리 하 여 id 인증서의 CN 이름으로 장치를 등록 합니다.

Config.xml 파일을 업데이트 한 후 항상 보안 디먼을 다시 시작 합니다.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Windows 디바이스

Id 인증서 체인 및 id 키를 생성 한 장치에 IoT Edge 런타임을 설치 합니다. 수동 프로 비전이 아니라 자동으로 IoT Edge 런타임을 구성 합니다.

Windows에 IoT Edge를 설치 하는 방법에 대 한 자세한 내용은 컨테이너 관리 및 IoT Edge 업데이트와 같은 작업에 대 한 지침 및 지침을 포함 하 여 [windows에 Azure IoT Edge 런타임 설치](how-to-install-iot-edge-windows.md)를 참조 하세요.

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell이 아닌 IoT Edge 설치 하는 경우 (x86) PowerShell의 AMD64 세션을 사용 해야 합니다.

1. **배포-IoTEdge** 명령은 Windows 컴퓨터가 지원 되는 버전에 있는지 확인 하 고 컨테이너 기능을 설정한 다음 moby 런타임 및 IoT Edge 런타임을 다운로드 합니다. 이 명령은 기본적으로 Windows 컨테이너를 사용 합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 이 시점에서 IoT Core 장치가 자동으로 다시 시작 될 수 있습니다. 다른 Windows 10 또는 Windows Server 장치를 다시 시작 하 라는 메시지가 표시 될 수 있습니다. 그렇다면 장치를 지금 다시 시작 하세요. 장치가 준비 되 면 관리자 권한으로 PowerShell을 다시 실행 합니다.

1. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 플래그를 사용 하 여 자동 프로 비전을 사용 하지 않는 한이 명령은 기본적으로 수동 프로 비전을 사용 `-Dps` 합니다.

   , 및에 대 한 자리 표시자 값을 `{scope_id}` `{identity cert chain path}` `{identity key path}` DPS 인스턴스의 적절 한 값 및 장치의 파일 경로로 바꿉니다. 등록 ID를 지정 하려는 경우에도를 포함 하 여 `-RegistrationId {registration_id}` 자리 표시자를 적절히 바꿉니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Config.xml 파일은 인증서 및 키 정보를 파일 Uri로 저장 합니다. 그러나 Initialize-IoTEdge 명령은이 형식 지정 단계를 처리 하므로 장치의 인증서 및 키 파일에 대 한 절대 경로를 제공할 수 있습니다.

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

장치 프로 비전 서비스에서 만든 개별 등록이 사용 되었는지 확인할 수 있습니다. Azure Portal에서 장치 프로 비전 서비스 인스턴스로 이동 합니다. 만든 개별 등록에 대 한 등록 정보를 엽니다. 등록 상태가 **할당** 되 고 장치 ID가 나열 됩니다.

디바이스에서 다음 명령을 사용하여 런타임이 성공적으로 설치되고 시작되는지 확인합니다.

### <a name="linux-device"></a>Linux 장치

IoT Edge 서비스의 상태를 확인합니다.

```cmd/sh
systemctl status iotedge
```

서비스 로그를 검사 합니다.

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

서비스 로그를 검사 합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

실행 중인 모듈을 나열합니다.

```powershell
iotedge list
```

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. Azure Portal 또는 [Azure CLI](how-to-deploy-cli-at-scale.md)를 사용 [하 여 대규모로 IoT Edge 모듈을 배포 하 고 모니터링](how-to-deploy-at-scale.md) 하는 방법을 알아봅니다.
