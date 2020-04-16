---
title: 테스트 인증서 만들기 - Azure IoT 에지 | 마이크로 소프트 문서
description: 테스트 인증서를 만들고 Azure IoT Edge 장치에 인증서를 설치하는 방법을 알아보고 프로덕션 배포를 준비합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 364846f6cef196f6cefa7872af48f262b387db4f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393812"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>데모 인증서를 만들어 IoT Edge 장치 기능을 테스트합니다.

IoT Edge 장치에는 런타임, 모듈 및 다운스트림 장치 간의 보안 통신을 위한 인증서가 필요합니다.
필요한 인증서를 만들 수 있는 인증서 권한이 없는 경우 데모 인증서를 사용하여 테스트 환경에서 IoT Edge 기능을 사용해 볼 수 있습니다.
이 문서에서는 IoT Edge가 테스트를 위해 제공하는 인증서 생성 스크립트의 기능에 대해 설명합니다.

이러한 인증서는 30일 후에 만료되며 프로덕션 시나리오에서 사용해서는 안 됩니다.

모든 컴퓨터에서 인증서를 만든 다음 IoT Edge 장치에 인증서를 복사할 수 있습니다.
IoT Edge 장치 자체에서 인증서를 생성하는 대신 기본 컴퓨터를 사용하여 인증서를 만드는 것이 더 쉽습니다.
기본 컴퓨터를 사용하여 스크립트를 한 번 설정한 다음 프로세스를 반복하여 여러 장치에 대한 인증서를 만들 수 있습니다.

다음 단계에 따라 IoT Edge 시나리오를 테스트하기 위한 데모 인증서를 만듭니다.

1. 장치에서 인증서 생성을 위한 [스크립트를 설정합니다.](#set-up-scripts)
2. 시나리오에 대한 다른 모든 인증서에 서명하는 데 사용하는 루트 CA 인증서를 [만듭니다.](#create-root-ca-certificate)
3. 테스트할 시나리오에 필요한 인증서를 생성합니다.
   * [IoT Edge 장치 ID 인증서를 만들어 IoT](#create-iot-edge-device-identity-certificates) Hub 장치 프로비저닝 서비스를 사용하여 자동 프로비저닝을 테스트합니다.
   * [IoT Edge 장치 CA 인증서를 만들어](#create-iot-edge-device-ca-certificates) 프로덕션 시나리오 또는 게이트웨이 시나리오를 테스트합니다.
   * [다운스트림 장치 인증서를 만들어](#create-downstream-device-certificates) 게이트웨이 시나리오에서 IoT Hub에 다운스트림 장치를 인증하는 테스트를 테스트합니다.

## <a name="prerequisites"></a>사전 요구 사항

Git이 설치된 개발 기계입니다.

## <a name="set-up-scripts"></a>스크립트 설정

GitHub의 IoT Edge 리포지토리에는 데모 인증서를 만드는 데 사용할 수 있는 인증서 생성 스크립트가 포함되어 있습니다.
이 섹션에서는 Windows 또는 Linux에서 컴퓨터에서 실행되도록 스크립트를 준비하는 방법에 대한 지침을 제공합니다.
리눅스 컴퓨터에 있다면, [리눅스에서 설정](#set-up-on-linux)하기 위해 앞으로 건너뜁니다.

### <a name="set-up-on-windows"></a>윈도우에서 설정

Windows 장치에서 데모 인증서를 만들려면 OpenSSL을 설치한 다음 생성 스크립트를 복제하고 PowerShell에서 로컬로 실행되도록 설정해야 합니다.

#### <a name="install-openssl"></a>OpenSSL 설치

인증서를 생성하는 데 사용하는 머신에서 Windows용 OpenSSL을 설치합니다.
Windows 장치에 OpenSSL이 이미 설치되어 있는 경우 이 단계를 건너뛸 수 있지만 PATH 환경 변수에서 openssl.exe를 사용할 수 있는지 확인합니다.

OpenSSL을 설치하는 방법에는 다음과 같은 여러 가지 옵션이 있습니다.

* **더 쉬운:** 다운로드 및 [소스 포지의 OpenSSL에서,](https://sourceforge.net/projects/openssl/)예를 들어, [타사 OpenSSL 바이너리를](https://wiki.openssl.org/index.php/Binaries)설치합니다. PATH 환경 변수에 openssl.exe에 대한 전체 경로를 추가합니다.

* **권장 사항:** 직접 또는 [vcpkg](https://github.com/Microsoft/vcpkg)를 통해 OpenSSL 소스 코드를 다운로드하고 머신에서 이진 파일을 빌드합니다. 아래에 나열된 지침은 vcpkg를 사용하여 간단한 단계를 통해 소스 코드를 다운로드하고, 컴파일하고, Windows 머신에 OpenSSL을 설치합니다.

   1. vcpkg를 설치하려는 디렉터리로 이동합니다. 지침에 따라 [vcpkg](https://github.com/Microsoft/vcpkg)를 다운로드하고 설치합니다.

   2. vcpkg이 설치되면 PowerShell 프롬프트에서 다음 명령을 실행하여 Windows x64용 OpenSSL 패키지를 설치합니다. 설치를 완료하는 데 일반적으로 약 5분이 걸립니다.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. openssl.exe 파일을 호출할 수 있도록 `<vcpkg path>\installed\x64-windows\tools\openssl`을 PATH 환경 변수에 추가합니다.

#### <a name="prepare-scripts-in-powershell"></a>PowerShell에서 스크립트 준비

Azure IoT Edge git 리포지토리에는 테스트 인증서를 생성하는 데 사용할 수 있는 스크립트가 포함되어 있습니다.
이 섹션에서는 IoT Edge 리포지토리를 복제하고 스크립트를 실행합니다.

1. 관리자 모드에서 PowerShell 창을 엽니다.

2. 데모 인증서를 생성하는 스크립트가 포함된 IoT Edge git 리포지토리를 복제합니다. `git clone` 명령을 사용하거나 [ZIP을 다운로드](https://github.com/Azure/iotedge/archive/master.zip)합니다.

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 작업하려는 디렉터리로 이동합니다. 이 문서 전체에서 이 디렉터리 * \<WRKDIR>* 호출합니다. 이 작업 디렉터리에서 모든 인증서와 키가 만들어집니다.

4. 복제된 리포지토리의 구성 및 스크립트 파일을 작업 디렉토리에 복사합니다.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   리포지토리를 ZIP으로 다운로드한 경우 폴더 `iotedge-master` 이름이 같고 나머지 경로는 동일합니다.

5. 스크립트를 실행하도록 PowerShell을 활성화합니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. 스크립트에서 사용하는 함수를 PowerShell의 전역 네임스페이스로 가져옵니다.

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 창에는 이 스크립트에서 생성된 인증서가 테스트 용일 뿐이며 프로덕션 시나리오에서 사용해서는 안 된다는 경고가 표시됩니다.

7. OpenSSL이 올바르게 설치되었는지 확인하고 기존 인증서와 이름이 충돌하지 않는지 확인합니다. 문제가 있는 경우 스크립트 출력에서 시스템에서 문제를 해결하는 방법을 설명해야 합니다.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>리눅스에 설정

Windows 장치에서 데모 인증서를 만들려면 생성 스크립트를 복제하고 bash에서 로컬로 실행하도록 설정해야 합니다.

1. 데모 인증서를 생성하는 스크립트가 포함된 IoT Edge git 리포지토리를 복제합니다.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 작업하려는 디렉터리로 이동합니다. 이 디렉토리를 * \<WRKDIR>* 참조하겠습니다. 모든 인증서와 키 파일이 이 디렉터리에서 만들어집니다.
  
3. 복제된 IoT Edge 리포지토리의 구성 및 스크립트 파일을 작업 디렉토리에 복사합니다.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>루트 CA 인증서 만들기

루트 CA 인증서는 IoT Edge 시나리오를 테스트하기 위한 다른 모든 데모 인증서를 만드는 데 사용됩니다.
동일한 루트 CA 인증서를 계속 사용하여 여러 IoT Edge 또는 다운스트림 장치에 대한 데모 인증서를 만들 수 있습니다.

작업 폴더에 루트 CA 인증서가 이미 있는 경우 새 인증서를 만들지 마십시오.
새 루트 CA 인증서는 이전 인증서를 덮어쓰고 이전 인증서에서 만든 다운스트림 인증서는 작동이 중지됩니다.
여러 루트 CA 인증서를 원하는 경우 별도의 폴더에서 관리해야 합니다.

이 섹션의 단계를 진행하기 전에 스크립트 [설정](#set-up-scripts) 섹션의 단계를 수행하여 데모 인증서 생성 스크립트로 작업 디렉토리를 준비합니다.

### <a name="windows"></a>Windows

1. 인증서 생성 스크립트를 배치한 작업 디렉터리로 이동합니다.

1. 루트 CA 인증서를 만들고 하나의 중간 인증서에 서명하도록 합니다. 인증서는 모두 작업 디렉토리에 배치됩니다.

   ```powershell
   New-CACertsCertChain rsa
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 생성하지만 문서에서 **루트 CA 인증서를**요청하면 다음 파일을 사용합니다.

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. 인증서 생성 스크립트를 배치한 작업 디렉터리로 이동합니다.

1. 루트 CA 인증서와 하나의 중간 인증서를 만듭니다.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 생성하지만 문서에서 **루트 CA 인증서를**요청하면 다음 파일을 사용합니다.

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>IoT 에지 장치 CA 인증서 만들기

프로덕션으로 가는 모든 IoT Edge 장치에는 config.yaml 파일에서 참조되는 장치 CA 인증서가 필요합니다.
장치 CA 인증서는 장치에서 실행 중인 모듈에 대한 인증서를 만드는 책임을 집니다.
또한 IoT Edge 장치가 다운스트림 장치에 연결할 때 ID를 확인하는 방법도 있습니다.

장치 CA 인증서는 IoT Edge 장치의 config.yaml 파일의 **인증서** 섹션으로 이동합니다.

이 섹션의 단계를 진행하기 전에 스크립트 [설정](#set-up-scripts) 및 [루트 CA 인증서 만들기](#create-root-ca-certificate) 섹션의 단계를 따릅니다.

### <a name="windows"></a>Windows

1. 인증서 생성 스크립트및 루트 CA 인증서가 있는 작업 디렉터리로 이동합니다.

2. 다음 명령을 사용하여 IoT Edge 장치 CA 인증서 및 개인 키를 만듭니다. 출력 파일의 이름을 지정하는 데 사용되는 **MyEdgeDeviceCA와**같은 CA 인증서의 이름을 제공합니다.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서와 키 쌍을 IoT Edge 장치에 복사하여 config.yaml 파일에서 참조해야 합니다.

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

이러한 스크립트에 전달된 게이트웨이 장치 이름은 config.yaml의 "hostname" 매개 변수 또는 IoT Hub의 장치 ID와 같아서는 안 됩니다.
이러한 스크립트를 사용하면 사용자가 두 위치에 같은 이름을 사용하여 IoT Edge를 설정하는 경우 이름이 충돌하지 않도록 게이트웨이 디바이스 이름에 ".ca" 문자열이 추가됨으로써 발생하는 문제를 방지할 수 있습니다.
하지만 같은 이름은 사용하지 않는 것이 좋습니다.

### <a name="linux"></a>Linux

1. 인증서 생성 스크립트및 루트 CA 인증서가 있는 작업 디렉터리로 이동합니다.

2. 다음 명령을 사용하여 IoT Edge 장치 CA 인증서 및 개인 키를 만듭니다. 출력 파일의 이름을 지정하는 데 사용되는 **MyEdgeDeviceCA와**같은 CA 인증서의 이름을 제공합니다.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서와 키 쌍을 IoT Edge 장치에 복사하여 config.yaml 파일에서 참조해야 합니다.

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

이러한 스크립트에 전달된 게이트웨이 장치 이름은 config.yaml의 "hostname" 매개 변수 또는 IoT Hub의 장치 ID와 같아서는 안 됩니다.
이러한 스크립트를 사용하면 사용자가 두 위치에 같은 이름을 사용하여 IoT Edge를 설정하는 경우 이름이 충돌하지 않도록 게이트웨이 디바이스 이름에 ".ca" 문자열이 추가됨으로써 발생하는 문제를 방지할 수 있습니다.
하지만 같은 이름은 사용하지 않는 것이 좋습니다.

## <a name="create-iot-edge-device-identity-certificates"></a>IoT Edge 장치 ID 인증서 만들기

장치 ID 인증서는 Azure IoT 허브 [장치 프로비전 서비스(DPS)를](../iot-dps/index.yml)통해 IoT Edge 장치를 프로비전하는 데 사용됩니다.

장치 ID 인증서는 IoT Edge 장치의 config.yaml 파일의 **프로비저닝** 섹션으로 이동합니다.

이 섹션의 단계를 진행하기 전에 스크립트 [설정](#set-up-scripts) 및 [루트 CA 인증서 만들기](#create-root-ca-certificate) 섹션의 단계를 따릅니다.

### <a name="windows"></a>Windows

다음 명령을 사용하여 IoT Edge 장치 ID 인증서 및 개인 키를 만듭니다.

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

이 명령에 전달하는 이름은 IoT Hub의 IoT Edge 장치의 장치 ID입니다.

새 장치 ID 명령은 DPS에 개별 등록을 만들고 IoT Edge 런타임을 설치할 때 사용할 세 가지 를 포함하여 여러 인증서 및 키 파일을 만듭니다.

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

다음 명령을 사용하여 IoT Edge 장치 ID 인증서 및 개인 키를 만듭니다.

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

이 명령에 전달하는 이름은 IoT Hub의 IoT Edge 장치의 장치 ID입니다.

스크립트는 DPS에 개별 등록을 만들고 IoT Edge 런타임을 설치할 때 사용할 세 가지 를 포함하여 여러 인증서 및 키 파일을 만듭니다.

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>다운스트림 장치 인증서 만들기

게이트웨이 시나리오에 대해 다운스트림 IoT 장치를 설정하는 경우 X.509 인증에 대한 데모 인증서를 생성할 수 있습니다.
X.509 인증서를 사용하여 IoT 장치를 인증하는 방법에는 자체 서명된 인증서를 사용하거나 CA(인증 인증서)를 사용하는 두 가지 방법이 있습니다.
지문 인증이라고도 하는 X.509 자체 서명 인증의 경우 IoT 장치에 배치할 새 인증서를 만들어야 합니다.
이러한 인증서에는 인증을 위해 IoT Hub와 공유하는 지문이 있습니다.
Ca(인증)에 서명한 X.509 인증 기관의 경우 IoT 장치에 대한 인증서에 서명하는 데 사용하는 IoT Hub에 등록된 루트 CA 인증서가 필요합니다.
루트 CA 인증서 또는 중간 인증서에서 발급한 인증서를 사용하는 모든 장치는 인증할 수 있습니다.

인증서 생성 스크립트를 사용하면 데모 인증서를 만들어 이러한 인증 시나리오 중 하나를 테스트할 수 있습니다.

이 섹션의 단계를 진행하기 전에 스크립트 [설정](#set-up-scripts) 및 [루트 CA 인증서 만들기](#create-root-ca-certificate) 섹션의 단계를 따릅니다.

### <a name="self-signed-certificates"></a>자체 서명된 인증서

자체 서명된 인증서를 사용하여 IoT 장치를 인증하는 경우 솔루션의 루트 CA 인증서를 기반으로 장치 인증서를 만들어야 합니다.
그런 다음 인증서에서 육각형 "지문"을 검색하여 IoT Hub에 제공합니다.
또한 IoT 장치에는 IoT Hub로 인증할 수 있도록 장치 인증서 사본이 필요합니다.

#### <a name="windows"></a>Windows

1. 인증서 생성 스크립트및 루트 CA 인증서가 있는 작업 디렉터리로 이동합니다.

2. 다운스트림 장치에 대해 두 개의 인증서(기본 및 보조 인증서)를 만듭니다. 사용하기 쉬운 명명 규칙은 IoT 장치의 이름으로 인증서를 만든 다음 기본 또는 보조 레이블을 만드는 것입니다. 다음은 그 예입니다.

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 장치로 복사하여 IoT Hub에 연결하는 응용 프로그램에서 참조해야 합니다.

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. 각 인증서에서 SHA1 지문(IoT Hub 컨텍스트의 지문이라고 함)을 검색합니다. 지문은 40 헥사데피좀 문자 문자열입니다. 다음 openssa 명령을 사용하여 인증서를 보고 지문을 찾습니다.

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   이 명령을 두 번, 기본 인증서에 대해 한 번, 보조 인증서에 대해 한 번 실행합니다. 자체 서명된 X.509 인증서를 사용하여 새 IoT 장치를 등록할 때 두 인증서 모두에 대한 지문을 제공합니다.

#### <a name="linux"></a>Linux

1. 인증서 생성 스크립트및 루트 CA 인증서가 있는 작업 디렉터리로 이동합니다.

2. 다운스트림 장치에 대해 두 개의 인증서(기본 및 보조 인증서)를 만듭니다. 사용하기 쉬운 명명 규칙은 IoT 장치의 이름으로 인증서를 만든 다음 기본 또는 보조 레이블을 만드는 것입니다. 다음은 그 예입니다.

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 장치로 복사하여 IoT Hub에 연결하는 응용 프로그램에서 참조해야 합니다.

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. 각 인증서에서 SHA1 지문(IoT Hub 컨텍스트의 지문이라고 함)을 검색합니다. 지문은 40 헥사데피좀 문자 문자열입니다. 다음 openssa 명령을 사용하여 인증서를 보고 지문을 찾습니다.

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   자체 서명된 X.509 인증서를 사용하여 새 IoT 장치를 등록할 때 기본 지문과 보조 지문을 모두 제공합니다.

### <a name="ca-signed-certificates"></a>CA 서명 인증서

자체 서명된 인증서를 사용하여 IoT 장치를 인증하는 경우 솔루션의 루트 CA 인증서를 IoT Hub에 업로드해야 합니다.
그런 다음 확인을 수행하여 루트 CA 인증서를 소유하고 있음을 IoT Hub에 증명합니다.
마지막으로 동일한 루트 CA 인증서를 사용하여 IoT Hub로 인증할 수 있도록 IoT 장치에 넣을 장치 인증서를 만듭니다.

이 섹션의 인증서는 [Azure IoT 허브에서 X.509 보안 설정의](../iot-hub/iot-hub-security-x509-get-started.md)단계에 대한 것입니다.

#### <a name="windows"></a>Windows

1. 작업 디렉터리, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`의 IoT 허브에 루트 CA 인증서 파일을 업로드합니다.

2. Azure 포털에 제공된 코드를 사용하여 해당 루트 CA 인증서를 소유하고 있는지 확인합니다.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. 다운스트림 장치에 대한 인증서 체인을 만듭니다. 장치가 IoT Hub에 등록된 것과 동일한 장치 ID를 사용합니다.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 장치로 복사하여 IoT Hub에 연결하는 응용 프로그램에서 참조해야 합니다.

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. 작업 디렉터리, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`의 IoT 허브에 루트 CA 인증서 파일을 업로드합니다.

2. Azure 포털에 제공된 코드를 사용하여 해당 루트 CA 인증서를 소유하고 있는지 확인합니다.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. 다운스트림 장치에 대한 인증서 체인을 만듭니다. 장치가 IoT Hub에 등록된 것과 동일한 장치 ID를 사용합니다.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 장치로 복사하여 IoT Hub에 연결하는 응용 프로그램에서 참조해야 합니다.

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
