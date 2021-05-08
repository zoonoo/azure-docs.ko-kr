---
title: 대칭 키 증명을 사용하여 디바이스 프로비저닝 - Azure IoT Edge
description: 대칭 키 증명을 사용하여 Device Provisioning Service로 Azure IoT Edge에 대한 자동 디바이스 프로비저닝 테스트
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66e1e561c14b169d41028e151ac054888830b881
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481976"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>대칭 키 증명을 사용하여 IoT Edge 디바이스 만들기 및 프로비저닝

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Edge를 사용하지 않는 디바이스와 마찬가지로 [Device Provisioning Service](../iot-dps/index.yml)를 사용하여 Azure IoT Edge 디바이스를 자동 프로비전할 수 있습니다. 자동 프로비저닝 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비저닝](../iot-dps/about-iot-dps.md#provisioning-process) 개요를 검토하세요.

이 문서에서는 다음 단계를 수행하여 IoT Edge 디바이스에서 대칭 키 증명을 사용해 Device Provisioning Service 개별 등록 또는 그룹 등록을 만드는 방법을 보여 줍니다.

* IoT Hub DPS(Device Provisioning Service)의 인스턴스를 만듭니다.
* 디바이스에 대한 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT Hub에 연결합니다.

대칭 키 증명은 Device Provisioning Service 인스턴스로 디바이스를 인증하는 간단한 방법입니다. 이 증명 방법은 디바이스 프로비저닝을 처음 사용하는 개발자나 엄격한 보안 요구 사항이 없는 개발자를 위한 "Hello World" 환경을 나타냅니다. [TPM](../iot-dps/concepts-tpm-attestation.md) 또는 [X.509 인증서](../iot-dps/concepts-x509-attestation.md)를 사용하는 디바이스 증명은 더욱 안전하며, 보다 엄격한 보안 요구 사항에 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 IoT Hub
* 실제 또는 가상 디바이스

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 설정

Azure에서 IoT Hub Device Provisioning Service의 새 인스턴스를 만들어 IoT hub에 연결합니다. [IoT Hub DPS 설정](../iot-dps/quick-setup-auto-provision.md) 지침을 따르면 됩니다.

Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="choose-a-unique-registration-id-for-the-device"></a>디바이스에 대한 고유한 등록 ID를 선택합니다.

각 디바이스를 식별하기 위한 고유한 등록 ID가 정의되어야 합니다. 디바이스에서 MAC 주소, 일련 번호 또는 고유 정보를 사용할 수 있습니다.

이 예제에서는 등록 ID에 대한 `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` 문자열을 형성하는 MAC 주소와 일련 번호의 조합을 사용합니다.

디바이스에 대한 고유한 등록 ID를 만듭니다. 유효한 문자는 소문자 영숫자 및 대시('-')입니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

디바이스의 등록 ID를 사용하여 DPS에서 개별 등록을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

> [!TIP]
> 대칭 키 증명을 사용하고 개별 등록과 동일한 결정 사항을 포함하는 경우에도 그룹 등록이 가능합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가** 를 선택한 다음, 다음 단계를 완료합니다.  

   1. **메커니즘** 에서 **대칭 키** 를 선택합니다.

   1. **키 자동 생성** 확인란을 선택합니다.

   1. 디바이스용으로 만든 **등록 ID** 를 제공합니다.

   1. 원하는 경우 디바이스에 대한 **IoT Hub 디바이스 ID** 를 제공합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 디바이스 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   1. 등록이 IoT Edge 디바이스에 대한 것이라고 선언하려면 **True** 를 선택합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

      > [!TIP]
      > Azure CLI에서 [등록](/cli/azure/iot/dps/enrollment) 또는 [등록 그룹](/cli/azure/iot/dps/enrollment-group)을 만들고, **edge-enabled** 플래그를 사용하여 디바이스 또는 디바이스 그룹이 IoT Edge 디바이스임을 지정할 수 있습니다.

   1. Device Provisioning Service의 할당 정책에서 **허브에 디바이스를 할당하는 방법** 의 기본값을 사용하거나 이 등록과 관련된 다른 값을 선택합니다.

   1. 디바이스를 연결하려는 링크된 **IoT Hub** 를 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   1. 첫 프로비전 이후 디바이스에서 프로비저닝을 요청할 때에는 **다시 프로비저닝할 때 디바이스 데이터를 처리하는 방법** 을 선택합니다.

   1. 원하는 경우 **초기 디바이스 쌍 상태** 에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들면 다음과 같습니다.

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

   1. **항목 사용** 이 **사용** 으로 설정되어 있는지 확인합니다.

   1. **저장** 을 선택합니다.

이제 이 디바이스에 대한 등록이 존재하므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다. IoT Edge 런타임을 설치하거나 그룹 등록에 사용할 디바이스 키를 만들려는 경우에는 사용할 등록의 **기본 키** 값을 복사해야 합니다.

## <a name="derive-a-device-key"></a>디바이스 키 파생

> [!NOTE]
> 이 섹션은 그룹 등록을 사용하는 경우에만 필요합니다.

각 디바이스는 고유한 등록 ID로 파생된 디바이스 키를 사용하여 프로비저닝하는 동안 등록과의 대칭 키 증명을 수행합니다. 디바이스 키를 생성하려면 DPS 등록으로부터 복사한 키를 사용하여 디바이스에 대한 고유한 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)을 계산하고 결과를 Base64 형식으로 변환합니다.

디바이스 코드에 등록의 기본 또는 보조 키를 포함하지 마세요.

### <a name="linux-workstations"></a>Linux 워크스테이션

Linux 워크스테이션을 사용하는 경우 openssl을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY** 의 값을 이전에 적어 둔 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 디바이스의 등록 ID로 바꿉니다.

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

**KEY** 의 값을 이전에 적어 둔 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 디바이스의 등록 ID로 바꿉니다.

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

[Azure IoT Edge 런타임 설치](how-to-install-iot-edge.md)의 단계를 수행한 다음 이 문서로 돌아와서 디바이스를 프로비저닝합니다.

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

런타임이 디바이스에 설치되면 디바이스에서 Device Provisioning Service 및 IoT Hub에 연결하는 데 사용하는 정보로 디바이스를 구성합니다.

다음 정보를 준비합니다.

* DPS **ID 범위** 값
* 만든 디바이스 **등록 ID**
* DPS 등록에서 복사한 **기본 키**

> [!TIP]
> 그룹 등록의 경우 DPS 등록 기본 키가 아니라 각 디바이스의 [파생 키](#derive-a-device-key)가 필요합니다.

### <a name="linux-device"></a>Linux 디바이스

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 파일의 프로비저닝 구성 섹션을 찾습니다. DPS 대칭 키 프로비저닝에 대한 줄을 주석 처리 제거하고 다른 프로비저닝 줄이 주석으로 처리되어 있는지 확인합니다.

   `provisioning:` 줄은 앞에 공백이 없어야 하며 중첩 항목은 두 개의 공백으로 들여써야 합니다.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "symmetric_key"
       registration_id: "<REGISTRATION_ID>"
       symmetric_key: "<SYMMETRIC_KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. `scope_id`, `registration_id` 및 `symmetric_key`의 값을 DPS 및 디바이스 정보로 업데이트합니다.

1. 필요에 따라 `always_reprovision_on_startup` 또는 `dynamic_reprovisioning` 줄을 사용하여 디바이스의 다시 프로비저닝 동작을 구성합니다. 시작 시 디바이스가 다시 프로비저닝되도록 설정되어 있는 경우 항상 먼저 DPS를 사용하여 프로비저닝을 시도한 다음, 실패하는 경우 프로비저닝 백업으로 대체합니다. 디바이스가 동적으로 다시 프로비저닝되도록 설정된 경우 다시 프로비저닝 이벤트가 감지되면 IoT Edge가 다시 시작되고 다시 프로비저닝됩니다. 자세한 내용은 [IoT Hub 디바이스 다시 프로비저닝 개념](../iot-dps/concepts-device-reprovision.md)을 참조하세요.

1. 디바이스에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge 설치의 일부로 제공되는 템플릿 파일을 기반으로 디바이스에 대한 구성 파일을 만듭니다.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. 파일의 **프로비전** 섹션을 찾습니다. 대칭 키를 통한 DPS 프로비저닝에 대한 줄을 주석 처리 제거하고 다른 프로비저닝 줄이 주석으로 처리되어 있는지 확인합니다.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "<REGISTRATION_ID>"

   symmetric_key = "<PRIMARY_KEY OR DERIVED_KEY>"
   ```

1. `id_scope`, `registration_id` 및 `symmetric_key`의 값을 DPS 및 디바이스 정보로 업데이트합니다.

   대칭 키 매개 변수로 인라인 키, 파일 URI 또는 PKCS#11 URI 값을 사용할 수 있습니다. 사용 중인 형식을 기준으로 대칭 키 한 줄을 주석 처리 제거합니다.

   PKCS#11 URI를 사용하는 경우 구성 파일에서 **PKCS#11** 섹션을 찾고 PKCS#11 구성에 대한 정보를 제공합니다.

1. config.toml 파일을 저장하고 닫습니다.

1. IoT Edge에 대한 구성 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows 디바이스

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell(x86)이 아닌 IoT Edge를 설치하는 경우 PowerShell의 AMD64 세션을 사용해야 합니다.

1. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 이 명령은 기본적으로 Windows 컨테이너를 사용하여 수동으로 프로비저닝하므로 대칭 키 인증을 사용하여 자동 프로비저닝하려면 `-DpsSymmetricKey` 플래그를 사용합니다.

   `{scope_id}`, `{registration_id}` 및`{symmetric_key}`의 자리 표시자 값을 앞에서 수집한 데이터로 바꿉니다.

   Windows에서 Linux 컨테이너를 사용하는 경우 `-ContainerOs Linux` 매개 변수를 추가합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다. 디바이스에서 다음 명령을 사용하여 런타임이 성공적으로 설치되고 시작되는지 확인합니다.

### <a name="linux-device"></a>Linux 디바이스

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

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

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 서비스의 상태를 확인합니다.

```cmd/sh
sudo iotedge system status
```

서비스 로그를 검사합니다.

```cmd/sh
sudo iotedge system logs
```

실행 중인 모듈을 나열합니다.

```cmd/sh
sudo iotedge list
```

:::moniker-end

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

Device Provisioning Service에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이고 디바이스 ID가 나열된 것을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.
