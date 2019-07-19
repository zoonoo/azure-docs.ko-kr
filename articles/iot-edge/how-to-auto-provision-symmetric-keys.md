---
title: 대칭 키 증명을 사용 하 여 DPS로 장치 자동 프로 비전-Azure IoT Edge | Microsoft Docs
description: 대칭 키 증명을 사용 하 여 장치 프로 비전 서비스와 Azure IoT Edge에 대 한 자동 장치 프로 비전 테스트
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a91162271bf36e1d68e087a60723497e7fc261ec
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275735"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>대칭 키 증명을 사용 하 여 IoT Edge 장치 만들기 및 프로 비전

Edge를 사용하지 않는 디바이스와 마찬가지로 [Device Provisioning Service](../iot-dps/index.yml)를 사용하여 Azure IoT Edge 디바이스를 자동 프로비전할 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](../iot-dps/concepts-auto-provisioning.md)을 검토하세요.

이 문서에서는 다음 단계를 수행 하 여 IoT Edge 장치에서 대칭 키 증명을 사용 하 여 장치 프로 비전 서비스 개별 등록을 만드는 방법을 보여 줍니다.

* IoT Hub DPS(Device Provisioning Service)의 인스턴스를 만듭니다.
* 디바이스에 대한 개별 등록을 만듭니다.
* IoT Edge 런타임을 설치 하 고 IoT Hub에 연결 합니다.

대칭 키 증명은 Device Provisioning Service 인스턴스로 디바이스를 인증하는 간단한 방법입니다. 이 증명 방법은 디바이스 프로비저닝을 처음 사용하는 개발자나 엄격한 보안 요구 사항이 없는 개발자를 위한 "Hello World" 환경을 나타냅니다. [TPM](../iot-dps/concepts-tpm-attestation.md) 을 사용 하는 장치 증명은 더 안전 하며 보다 엄격한 보안 요구 사항에 사용 해야 합니다.

## <a name="prerequisites"></a>전제 조건

* 활성 IoT Hub
* 실제 또는 가상 장치

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 설정

Azure에서 IoT Hub Device Provisioning Service의 새 인스턴스를 만들어 IoT hub에 연결합니다. [IoT Hub DPS 설정](../iot-dps/quick-setup-auto-provision.md) 지침을 따르면 됩니다.

Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="choose-a-unique-registration-id-for-the-device"></a>디바이스에 대한 고유한 등록 ID를 선택합니다.

각 디바이스를 식별하기 위한 고유한 등록 ID가 정의되어야 합니다. 디바이스에서 MAC 주소, 일련 번호 또는 고유 정보를 사용할 수 있습니다.

이 예제에서는 등록 ID에 대한 다음 문자열을 형성하는 MAC 주소와 일련 번호의 조합을 사용합니다.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

디바이스에 대한 고유한 등록 ID를 만듭니다. 유효한 문자는 소문자 영숫자 및 대시('-')입니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

장치의 등록 ID를 사용 하 여 DPS에서 개별 등록을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태**를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-monitor.md)를 만드는 데 사용됩니다.

> [!TIP]
> 대칭 키 증명을 사용 하 고 개별 등록와 동일한 결정을 포함 하는 경우에도 그룹 등록 가능 합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동 합니다.

1. **설정**에서 **등록 관리**를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가**를 선택한 다음, 다음 단계를 완료합니다.  

   1. **메커니즘**에서 **대칭 키**를 선택 합니다.

   1. **키 자동 생성** 확인란을 선택 합니다.

   1. 장치에 대해 만든 **등록 ID** 를 제공 합니다.

   1. 원하는 경우 장치에 대 한 **IoT Hub 장치 ID** 를 제공 합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 장치 ID를 제공 하지 않으면 등록 ID가 사용 됩니다.

   1. IoT Edge 장치에 대 한 등록이 되도록 선언 하려면 **True** 를 선택 합니다. 그룹 등록의 경우 모든 장치는 장치를 IoT Edge 해야 합니다. 그렇지 않으면 장치를 모두 사용할 수 없습니다.

   1. 장치 프로 비전 서비스의 할당 정책에서 **허브에 장치를 할당** 하거나이 등록과 관련 된 다른 값을 선택 하는 방법에 대 한 기본값을 적용 합니다.

   1. 디바이스를 연결하려는 링크된 **IoT Hub**를 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 장치 중 하나에 장치가 할당 됩니다.

   1. 장치를 처음으로 프로 비전을 요청할 때 **다시 프로 비전 할 때 장치 데이터를 처리 하는 방법을** 선택 합니다.

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

   1. **항목 사용** 이 **사용**으로 설정 되어 있는지 확인 합니다.

   1.           **저장**을 선택합니다.

이제이 장치에 대 한 등록이 있으므로 IoT Edge 런타임은 설치 중에 장치를 자동으로 프로 비전 할 수 있습니다. 장치 키를 만들 때 사용할 등록의 **기본 키** 값을 복사 해야 합니다.

## <a name="derive-a-device-key"></a>디바이스 키 파생

장치는 프로 비전 중에 등록으로 대칭 키 증명을 수행 하기 위해 고유한 등록 ID와 함께 파생 된 장치 키를 사용 합니다. 장치 키를 생성 하려면 DPS 등록에서 복사한 키를 사용 하 여 장치에 대 한 고유 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) 을 계산 하 고 그 결과를 Base64 형식으로 변환 합니다.

장치 코드에 등록의 기본 또는 보조 키를 포함 하지 마세요.

### <a name="linux-workstations"></a>Linux 워크스테이션

Linux 워크스테이션을 사용하는 경우 openssl을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY**의 값을 이전에 적어 둔 **기본 키**로 바꿉니다.

**REG_ID** 의 값을 장치의 등록 ID로 바꿉니다.

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

**REG_ID** 의 값을 장치의 등록 ID로 바꿉니다.

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

장치를 프로 비전 할 때 다음 정보가 필요 합니다.

* DPS **ID 범위** 값
* 만든 장치 **등록 ID**
* 대칭 키 증명에 대 한 장치의 파생 장치 키

### <a name="linux-device"></a>Linux 장치

장치의 아키텍처에 대 한 지침을 따르세요. 수동이 아닌 자동 프로비전에 대한 IoT Edge 런타임을 구성해야 합니다.

* [Linux(x64)에서 Azure IoT Edge 런타임 설치](how-to-install-iot-edge-linux.md)
* [Linux에 Azure IoT Edge 런타임 설치 (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

대칭 키 프로 비전에 대 한 구성 파일의 섹션은 다음과 같습니다.

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

, `{scope_id}` `{registration_id}`및 에대한자리표시자값을앞에서수집한데이터로바꿉니다.`{symmetric_key}`

### <a name="windows-device"></a>Windows 디바이스

지침에 따라 파생 된 장치 키를 생성 한 장치에 IoT Edge 런타임을 설치 합니다. 수동이 아닌 자동 프로비전에 대한 IoT Edge 런타임을 구성해야 합니다.

[IoT Edge 설치 및 자동으로 프로비전](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다. 디바이스에서 다음 명령을 사용하여 런타임이 성공적으로 설치되고 시작되는지 확인합니다.

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

장치 프로 비전 서비스에서 만든 개별 등록이 사용 되었는지 확인할 수 있습니다. Azure Portal에서 장치 프로 비전 서비스 인스턴스로 이동 합니다. 만든 개별 등록에 대 한 등록 정보를 엽니다. 등록 상태가 **할당** 되 고 장치 ID가 나열 됩니다.

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-monitor-cli.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-monitor.md)하는 방법을 알아봅니다.
