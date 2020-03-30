---
title: 대칭 키 증명을 사용하여 장치 프로비저닝 - Azure IoT Edge
description: 대칭 키 증명을 사용하여 장치 프로비저닝 서비스를 사용하여 Azure IoT 가장자리에 대한 자동 장치 프로비저닝을 테스트합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9944308d00c9cfecbd38a6443efb49913148806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535921"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>대칭 키 증명을 사용하여 IoT Edge 장치 생성 및 프로비전

Azure IoT Edge 장치는 에지 가 활성화되지 않은 장치와 마찬가지로 [장치 프로비저닝 서비스를](../iot-dps/index.yml) 사용하여 자동 프로비전할 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](../iot-dps/concepts-auto-provisioning.md)을 검토하세요.

이 문서에서는 다음 단계를 통해 IoT Edge 장치에서 대칭 키 증명을 사용하여 장치 프로비저닝 서비스 개별 등록을 만드는 방법을 보여 주며 다음과 같은 단계를 수행합니다.

* IoT Hub DPS(Device Provisioning Service)의 인스턴스를 만듭니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT 허브에 연결합니다.

대칭 키 증명은 Device Provisioning Service 인스턴스로 디바이스를 인증하는 간단한 방법입니다. 이 증명 방법은 디바이스 프로비저닝을 처음 사용하는 개발자나 엄격한 보안 요구 사항이 없는 개발자를 위한 "Hello World" 환경을 나타냅니다. [TPM](../iot-dps/concepts-tpm-attestation.md) 또는 [X.509 인증서를](../iot-dps/concepts-security.md#x509-certificates) 사용하는 장치 증명은 더 안전하며 보다 엄격한 보안 요구 사항에 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 IoT 허브
* 물리적 또는 가상 장치

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 설정

Azure에서 IoT Hub Device Provisioning Service의 새 인스턴스를 만들어 IoT hub에 연결합니다. [IoT Hub DPS 설정](../iot-dps/quick-setup-auto-provision.md) 지침을 따르면 됩니다.

Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="choose-a-unique-registration-id-for-the-device"></a>디바이스에 대한 고유한 등록 ID를 선택합니다.

각 디바이스를 식별하기 위한 고유한 등록 ID가 정의되어야 합니다. 디바이스에서 MAC 주소, 일련 번호 또는 고유 정보를 사용할 수 있습니다.

이 예제에서는 등록 ID에 대해 다음 문자열을 형성하는 MAC 주소와 `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`일련 번호를 조합하여 사용합니다.

디바이스에 대한 고유한 등록 ID를 만듭니다. 유효한 문자는 소문자 영숫자 및 대시('-')입니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

기기의 등록 ID를 사용하여 DPS에 개별 등록을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태**를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-monitor.md)를 만드는 데 사용됩니다.

> [!TIP]
> 대칭 키 증명을 사용하고 개별 등록과 동일한 결정을 내릴 때 그룹 등록도 가능합니다.

1. Azure [포털에서](https://portal.azure.com)IoT 허브 장치 프로비저닝 서비스의 인스턴스로 이동합니다.

1. **설정**에서 **등록 관리**를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가**를 선택한 다음, 다음 단계를 완료합니다.  

   1. **메커니즘의**경우 **대칭 키를 선택합니다.**

   1. 자동 **생성 키** 확인란을 선택합니다.

   1. 장치에 대해 만든 **등록 ID를** 제공합니다.

   1. 원하는 경우 장치에 **IoT 허브 장치 ID를** 제공합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 장치 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   1. 등록이 IoT Edge 장치에 대한 것이라는 것을 선언하려면 **True를** 선택합니다. 그룹 등록의 경우 모든 장치는 IoT Edge 장치여야 하며 그 중 어느 것도 할 수 없습니다.

   1. **허브에 장치를 할당하거나** 이 등록과 관련된 다른 값을 선택하는 방법에 대한 장치 프로비저닝 서비스의 할당 정책에서 기본값을 수락합니다.

   1. 디바이스를 연결하려는 링크된 **IoT Hub**를 선택합니다. 여러 허브를 선택할 수 있으며 선택한 할당 정책에 따라 장치가 해당 허브 중 하나에 할당됩니다.

   1. 장치가 처음 **프로비저닝을 요청할 때 다시 프로비저닝할** 때 장치 데이터를 처리할 방법을 선택합니다.

   1. 원하는 경우 **초기 디바이스 쌍 상태**에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들어:

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

   1. **사용 항목이** **사용**설정으로 설정되어 있는지 확인합니다.

   1. **저장**을 선택합니다.

이제 이 장치에 대한 등록이 있으므로 IoT Edge 런타임은 설치 하는 동안 장치를 자동으로 프로비전할 수 있습니다. IoT Edge 런타임을 설치할 때 또는 그룹 등록과 함께 사용할 장치 키를 만들 려는 경우 등록의 **기본 키** 값을 복사하여 사용해야 합니다.

## <a name="derive-a-device-key"></a>디바이스 키 파생

> [!NOTE]
> 이 섹션은 그룹 등록을 사용하는 경우에만 필요합니다.

각 장치는 고유 등록 ID와 함께 파생 된 장치 키를 사용하여 프로비저닝 중에 등록과 대칭 키 증명을 수행합니다. 장치 키를 생성하려면 DPS 등록에서 복사한 키를 사용하여 장치에 대한 고유 등록 ID의 [HMAC-SHA256을](https://wikipedia.org/wiki/HMAC) 계산하고 결과를 Base64 형식으로 변환합니다.

장치 코드에 등록의 기본 또는 보조 키를 포함하지 마십시오.

### <a name="linux-workstations"></a>Linux 워크스테이션

Linux 워크스테이션을 사용하는 경우 openssl을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY**의 값을 이전에 적어 둔 **기본 키**로 바꿉니다.

**REG_ID** 값을 기기의 등록 ID로 바꿉습니다.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows 기반 워크스테이션

Windows 기반 워크스테이션을 사용하는 경우 PowerShell을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY**의 값을 이전에 적어 둔 **기본 키**로 바꿉니다.

**REG_ID** 값을 기기의 등록 ID로 바꿉습니다.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 디바이스에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 디바이스에 추가 컨테이너의 배포를 허용합니다.

기기를 프로비전할 때 다음 정보가 필요합니다.

* DPS **ID 범위** 값
* 만든 장치 **등록 ID**
* DPS 등록에서 복사한 **기본 키**

> [!TIP]
> 그룹 등록의 경우 DPS 등록 키가 아닌 각 장치의 [파생](#derive-a-device-key) 키가 필요합니다.

### <a name="linux-device"></a>리눅스 장치

장치의 아키텍처에 대한 지침을 따릅니다. 수동이 아닌 자동 프로비전에 대한 IoT Edge 런타임을 구성해야 합니다.

[리눅스에 Azure IoT 에지 런타임 설치](how-to-install-iot-edge-linux.md)

대칭 키 프로비저닝을 위한 구성 파일의 섹션은 다음과 같습니다.

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

의 자리 표시자 `<SCOPE_ID>`값을 `<SYMMETRIC_KEY>` 이전에 수집한 데이터와 바꿉습니다. `<REGISTRATION_ID>` **프로비저닝:** 줄에 이전 공백이 없고 중첩된 항목이 두 공백으로 들여쓰기되는지 확인합니다.

### <a name="windows-device"></a>Windows 디바이스

파생 된 장치 키를 생성 한 장치에 IoT Edge 런타임을 설치합니다. IoT Edge 런타임을 수동이 아닌 자동 프로비저닝용으로 구성합니다.

컨테이너 관리 및 IoT 에지 업데이트와 같은 작업에 대한 필수 구성 프로그램 및 지침을 포함하여 Windows에 IoT Edge를 설치하는 방법에 대한 자세한 내용은 [Windows에서 Azure IoT Edge 런타임 설치를](how-to-install-iot-edge-windows.md)참조하십시오.

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell(x86)이 아닌 IoT Edge를 설치할 때 는 PowerShell의 AMD64 세션을 사용해야 합니다.

1. **Deploy-IoTEdge** 명령은 Windows 컴퓨터가 지원되는 버전에 있는지 확인하고 컨테이너 기능을 켜고 moby 런타임 및 IoT Edge 런타임을 다운로드합니다. 명령은 Windows 컨테이너를 사용하는 데 기본설정됩니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 이 시점에서 IoT Core 장치가 자동으로 다시 시작될 수 있습니다. 다른 Windows 10 또는 Windows 서버 장치에서 다시 시작하라는 메시지가 표시될 수 있습니다. 그렇다면 지금 기기를 다시 시작하십시오. 장치가 준비되면 PowerShell을 관리자로 다시 실행합니다.

1. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 자동 프로비저닝을 사용 하려면 `-Dps` 플래그를 사용 하지 않는 한 명령은 Windows 컨테이너를 사용 하 여 수동 프로비저닝을 기본값으로 설정 합니다.

   의 자리 표시자 `{scope_id}`값을 `{symmetric_key}` 이전에 수집한 데이터와 바꿉습니다. `{registration_id}`

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다. 디바이스에서 다음 명령을 사용하여 런타임이 성공적으로 설치되고 시작되는지 확인합니다.

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

장치 프로비저닝 서비스에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure 포털에서 장치 프로비저닝 서비스 인스턴스로 이동합니다. 만든 개별 등록에 대한 등록 세부 정보를 엽니다. 등록 상태가 **할당되고** 장치 ID가 나열됩니다.

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. Azure 포털을 사용하거나 [Azure CLI를 사용하여](how-to-deploy-monitor-cli.md)규모에 따라 [IoT Edge 모듈을 배포하고 모니터링하는](how-to-deploy-monitor.md) 방법에 대해 알아봅니다.
