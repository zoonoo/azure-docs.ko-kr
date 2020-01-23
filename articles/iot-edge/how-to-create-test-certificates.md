---
title: 테스트 인증서 만들기-Azure IoT Edge | Microsoft Docs
description: 테스트 인증서를 만들어 프로덕션 배포를 준비 하는 Azure IoT Edge 장치에 설치 하는 방법을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6f64a6714b9d795a1e809c555394be6f7671c63
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510671"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>데모 인증서를 만들어 IoT Edge 장치 기능 테스트

IoT Edge 장치에는 런타임, 모듈 및 다운스트림 장치 간의 보안 통신용 인증서가 필요 합니다.
필요한 인증서를 만들 수 있는 인증 기관이 없는 경우 데모 인증서를 사용 하 여 테스트 환경에서 IoT Edge 기능을 사용해 볼 수 있습니다.
이 문서에서는 테스트를 위해 IoT Edge에서 제공 하는 인증서 생성 스크립트의 기능을 설명 합니다.

이러한 인증서는 30 일 후에 만료 되며 프로덕션 시나리오에서 사용 하면 안 됩니다.

모든 컴퓨터에서 인증서를 만든 다음 IoT Edge 장치에 복사할 수 있습니다.
IoT Edge 장치 자체에서 인증서를 생성 하는 대신 기본 컴퓨터를 사용 하 여 인증서를 만드는 것이 더 쉽습니다.
기본 컴퓨터를 사용 하 여 스크립트를 한 번 설정 하 고 프로세스를 반복 하 여 여러 장치에 대 한 인증서를 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Git가 설치 된 개발 컴퓨터.

## <a name="set-up-scripts"></a>스크립트 설정

GitHub의 IoT Edge 리포지토리에는 데모 인증서를 만드는 데 사용할 수 있는 인증서 생성 스크립트가 포함 되어 있습니다.
이 섹션에서는 Windows 또는 Linux에서 컴퓨터에서 실행할 스크립트를 준비 하는 방법에 대 한 지침을 제공 합니다.
Linux 컴퓨터의 경우 [linux에서 설정](#set-up-on-linux)으로 건너뜁니다.

### <a name="set-up-on-windows"></a>Windows에서 설정

Windows 장치에서 데모 인증서를 만들려면 OpenSSL을 설치한 다음 생성 스크립트를 복제 하 고 PowerShell에서 로컬로 실행 되도록 설정 해야 합니다.

#### <a name="install-openssl"></a>OpenSSL 설치

인증서를 생성하는 데 사용하는 머신에서 Windows용 OpenSSL을 설치합니다.
Windows 장치에 OpenSSL가 이미 설치 되어 있는 경우이 단계를 건너뛸 수 있지만 PATH 환경 변수에서 OpenSSL을 사용할 수 있는지 확인 합니다.

다음 옵션을 포함 하 여 OpenSSL를 설치 하는 몇 가지 방법이 있습니다.

* **쉬워진:** [타사 OpenSSL 이진 파일](https://wiki.openssl.org/index.php/Binaries)을 다운로드 하 여 설치 합니다 (예: [Sourceforge의 OpenSSL](https://sourceforge.net/projects/openssl/)). PATH 환경 변수에 openssl.exe에 대한 전체 경로를 추가합니다.

* **권장 사항:** 직접 또는 [vcpkg](https://github.com/Microsoft/vcpkg)를 통해 OpenSSL 소스 코드를 다운로드하고 머신에서 이진 파일을 빌드합니다. 아래에 나열된 지침은 vcpkg를 사용하여 간단한 단계를 통해 소스 코드를 다운로드하고, 컴파일하고, Windows 머신에 OpenSSL을 설치합니다.

   1. vcpkg를 설치하려는 디렉터리로 이동합니다. 지침에 따라 [vcpkg](https://github.com/Microsoft/vcpkg)를 다운로드하고 설치합니다.

   2. Vcpkg가 설치 되 면 powershell 프롬프트에서 다음 명령을 실행 하 여 Windows x 64 용 OpenSSL 패키지를 설치 합니다. 설치는 일반적으로 완료 하는 데 약 5 분이 걸립니다.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. openssl.exe 파일을 호출할 수 있도록 `<vcpkg path>\installed\x64-windows\tools\openssl`을 PATH 환경 변수에 추가합니다.

#### <a name="prepare-scripts-in-powershell"></a>PowerShell에서 스크립트 준비

Azure IoT Edge git 리포지토리에는 테스트 인증서를 생성 하는 데 사용할 수 있는 스크립트가 포함 되어 있습니다.
이 섹션에서는 IoT Edge 리포지토리를 복제 하 고 스크립트를 실행 합니다.

1. 관리자 모드에서 PowerShell 창을 엽니다.

2. 데모 인증서를 생성 하는 스크립트를 포함 하는 IoT Edge git 리포지토리를 복제 합니다. `git clone` 명령을 사용하거나 [ZIP을 다운로드](https://github.com/Azure/iotedge/archive/master.zip)합니다.

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 작업하려는 디렉터리로 이동합니다. 이 문서 전체에서이 디렉터리 *\<WRKDIR >* 를 호출 합니다. 모든 인증서 및 키가이 작업 디렉터리에 만들어집니다.

4. 복제 된 리포지토리의 구성 및 스크립트 파일을 작업 디렉터리로 복사 합니다.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   리포지토리를 ZIP으로 다운로드 한 경우 폴더 이름이 `iotedge-master` 되며 나머지 경로는 동일 합니다.

5. 스크립트를 실행하도록 PowerShell을 활성화합니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. 스크립트에서 사용 하는 함수를 PowerShell의 전역 네임 스페이스에 가져옵니다.

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 창에는이 스크립트에 의해 생성 된 인증서가 테스트 목적 으로만 사용 되 고 프로덕션 시나리오에서는 사용 되지 않는 경고가 표시 됩니다.

7. OpenSSL가 올바르게 설치 되었는지 확인 하 고 기존 인증서와 이름이 충돌 하지 않는지 확인 합니다. 문제가 있는 경우 스크립트 출력은 시스템에서 문제를 해결 하는 방법을 설명 해야 합니다.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Linux에서 설정

Windows 장치에서 데모 인증서를 만들려면 생성 스크립트를 복제 하 고 bash에서 로컬로 실행 되도록 설정 해야 합니다.

1. 데모 인증서를 생성 하는 스크립트를 포함 하는 IoT Edge git 리포지토리를 복제 합니다.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 작업하려는 디렉터리로 이동합니다. *\<WRKDIR >* 문서 전체에서이 디렉터리를 참조 합니다. 모든 인증서 및 키 파일이이 디렉터리에 생성 됩니다.
  
3. 복제 된 IoT Edge 리포지토리의 구성 및 스크립트 파일을 작업 디렉터리로 복사 합니다.

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

루트 CA 인증서는 IoT Edge 시나리오를 테스트 하기 위한 다른 모든 데모 인증서를 만드는 데 사용 됩니다.
동일한 루트 CA 인증서를 계속 사용 하 여 여러 IoT Edge 또는 다운스트림 장치에 대 한 데모 인증서를 만들 수 있습니다.

작업 폴더에 루트 CA 인증서가 이미 있는 경우 새 인증서를 만들지 마세요.
새 루트 CA 인증서는 이전 버전을 덮어쓰고 이전 인증서에서 만든 다운스트림 인증서의 작동이 중단 됩니다.
여러 루트 CA 인증서를 원하는 경우 별도의 폴더에서 관리 해야 합니다.

이 섹션의 단계를 진행 하기 전에 데모 인증서 생성 스크립트를 사용 하 여 작업 디렉터리를 준비 하려면 [스크립트 설정](#set-up-scripts) 섹션의 단계를 따르세요.

### <a name="windows"></a>Windows

1. 인증서 생성 스크립트를 배치한 작업 디렉터리로 이동 합니다.

1. 루트 CA 인증서를 만들어 하나의 중간 인증서에 서명 하도록 합니다. 인증서는 모두 작업 디렉터리에 저장 됩니다.

   ```powershell
   New-CACertsCertChain rsa
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만들지만, 아티클이 **루트 CA 인증서**를 요청 하는 경우 다음 파일을 사용 합니다.

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. 인증서 생성 스크립트를 배치한 작업 디렉터리로 이동 합니다.

1. 루트 CA 인증서와 하나의 중간 인증서를 만듭니다.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만들지만, 아티클이 **루트 CA 인증서**를 요청 하는 경우 다음 파일을 사용 합니다.

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>IoT Edge 장치 CA 인증서 만들기

프로덕션으로 이동 하는 모든 IoT Edge 장치에는 config.xml 파일에서 참조 되는 장치 CA 인증서가 필요 합니다. 장치 CA 인증서는 장치에서 실행 되는 모듈에 대 한 인증서를 만듭니다. 또한 다운스트림 장치에 연결할 때 IoT Edge 장치에서 해당 id를 확인 하는 방법도 있습니다.

이 섹션의 단계를 진행 하기 전에 [스크립트 설정](#set-up-scripts) 및 [루트 CA 인증서 만들기](#create-root-ca-certificate) 섹션의 단계를 따르세요.

### <a name="windows"></a>Windows

1. 인증서 생성 스크립트 및 루트 CA 인증서가 있는 작업 디렉터리로 이동 합니다.

2. 다음 명령을 사용 하 여 IoT Edge 장치 CA 인증서와 개인 키를 만듭니다. 출력 파일의 이름을 지정 하는 데 사용 되는 CA 인증서의 이름 (예: **MyEdgeDeviceCA**)을 제공 합니다.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 IoT Edge 장치에 복사 하 여 config.xml 파일에서 참조 해야 합니다.

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

해당 스크립트로 전달 되는 게이트웨이 장치 이름은 구성의 "hostname" 매개 변수와 달라 야 합니다. 이 스크립트는 사용자가 두 위치에서 같은 이름을 사용 하 여 IoT Edge를 설정 하는 경우 이름 충돌을 방지 하기 위해 게이트웨이 장치 이름에 ". ca" 문자열을 추가 하 여 문제를 방지 하는 데 도움이 됩니다. 하지만 같은 이름은 사용하지 않는 것이 좋습니다.

### <a name="linux"></a>Linux

1. 인증서 생성 스크립트 및 루트 CA 인증서가 있는 작업 디렉터리로 이동 합니다.

2. 다음 명령을 사용 하 여 IoT Edge 장치 CA 인증서와 개인 키를 만듭니다. 출력 파일의 이름을 지정 하는 데 사용 되는 CA 인증서의 이름 (예: **MyEdgeDeviceCA**)을 제공 합니다.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 IoT Edge 장치에 복사 하 여 config.xml 파일에서 참조 해야 합니다.

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

해당 스크립트로 전달 되는 게이트웨이 장치 이름은 구성의 "hostname" 매개 변수와 달라 야 합니다. 이 스크립트는 사용자가 두 위치에서 같은 이름을 사용 하 여 IoT Edge를 설정 하는 경우 이름 충돌을 방지 하기 위해 게이트웨이 장치 이름에 ". ca" 문자열을 추가 하 여 문제를 방지 하는 데 도움이 됩니다. 하지만 같은 이름은 사용하지 않는 것이 좋습니다.

## <a name="create-x509-certs-for-downstream-devices"></a>다운스트림 장치에 대 한 x.509 인증서 만들기

게이트웨이 시나리오에 대 한 다운스트림 IoT 장치를 설정 하는 경우 x.509 인증에 대 한 데모 인증서를 생성할 수 있습니다.
X.509 인증서를 사용 하거나 자체 서명 된 인증서를 사용 하거나 CA (인증 기관) 서명 된 인증서를 사용 하 여 IoT 장치를 인증 하는 방법에는 두 가지가 있습니다.
X.509 자체 서명 된 인증의 경우 (지문 인증이 라고도 함) IoT 장치에 새 인증서를 만들어야 합니다.
이러한 인증서에는 인증을 위해 IoT Hub와 공유 하는 지문이 있습니다.
X.509 CA (인증 기관) 서명 된 인증의 경우 IoT 장치에 대 한 인증서에 서명 하는 데 사용 하는 IoT Hub에 등록 된 루트 CA 인증서가 필요 합니다.
루트 CA 인증서 또는 중간 인증서 중 하나에서 발급 한 인증서를 사용 하는 모든 장치는 인증을 허용 합니다.

인증서 생성 스크립트를 통해 이러한 인증 시나리오 중 하나를 테스트 하는 데모 인증서를 만들 수 있습니다.

이 섹션의 단계를 진행 하기 전에 [스크립트 설정](#set-up-scripts) 및 [루트 CA 인증서 만들기](#create-root-ca-certificate) 섹션의 단계를 따르세요.

### <a name="self-signed-certificates"></a>자체 서명된 인증서

자체 서명 된 인증서를 사용 하 여 IoT 장치를 인증 하는 경우 솔루션에 대 한 루트 CA 인증서를 기반으로 장치 인증서를 만들어야 합니다.
그런 다음 IoT Hub 제공할 인증서에서 16 진수 "지문"을 검색 합니다.
IoT 장치에는 IoT Hub를 사용 하 여 인증할 수 있도록 장치 인증서의 복사본도 필요 합니다.

#### <a name="windows"></a>Windows

1. 인증서 생성 스크립트 및 루트 CA 인증서가 있는 작업 디렉터리로 이동 합니다.

2. 다운스트림 장치에 대해 두 개의 인증서 (기본 및 보조)를 만듭니다. 사용 하기 쉬운 명명 규칙은 IoT 장치의 이름과 기본 또는 보조 레이블을 사용 하 여 인증서를 만드는 것입니다. 예:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 장치로 복사 하 고 IoT Hub에 연결 하는 응용 프로그램에서 참조 해야 합니다.

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. 각 인증서에서 SHA1 지문 (IoT Hub 컨텍스트에서 지문 이라고 함)을 검색 합니다. 지 문은 40 16 진수 문자열입니다. 다음 openssl 명령을 사용 하 여 인증서를 확인 하 고 지문을 찾습니다.

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   기본 인증서에 대해 한 번, 보조 인증서의 경우이 명령을 두 번 실행 합니다. 자체 서명 된 x.509 인증서를 사용 하 여 새 IoT 장치를 등록할 때 두 인증서에 대해 지문을 제공 합니다.

#### <a name="linux"></a>Linux

1. 인증서 생성 스크립트 및 루트 CA 인증서가 있는 작업 디렉터리로 이동 합니다.

2. 다운스트림 장치에 대해 두 개의 인증서 (기본 및 보조)를 만듭니다. 사용 하기 쉬운 명명 규칙은 IoT 장치의 이름과 기본 또는 보조 레이블을 사용 하 여 인증서를 만드는 것입니다. 예:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 장치로 복사 하 고 IoT Hub에 연결 하는 응용 프로그램에서 참조 해야 합니다.

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. 각 인증서에서 SHA1 지문 (IoT Hub 컨텍스트에서 지문 이라고 함)을 검색 합니다. 지 문은 40 16 진수 문자열입니다. 다음 openssl 명령을 사용 하 여 인증서를 확인 하 고 지문을 찾습니다.

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   자체 서명 된 x.509 인증서를 사용 하 여 새 IoT 장치를 등록 하면 기본 및 보조 지문을 모두 제공 합니다.

### <a name="ca-signed-certificates"></a>CA 서명 인증서

자체 서명 된 인증서를 사용 하 여 IoT 장치를 인증 하는 경우 솔루션에 대 한 루트 CA 인증서를 IoT Hub 업로드 해야 합니다.
그런 다음 확인을 수행 하 여 루트 CA 인증서를 소유 하 고 있음을 IoT Hub 증명 합니다.
마지막으로, 동일한 루트 CA 인증서를 사용 하 여 IoT 장치에 배치할 장치 인증서를 만들어 IoT Hub 인증할 수 있습니다.

이 섹션의 인증서는 [Azure IoT hub에서 x.509 보안 설정](../iot-hub/iot-hub-security-x509-get-started.md)의 단계에 대 한 것입니다.

#### <a name="windows"></a>Windows

1. 작업 디렉터리에서 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`하 여 루트 CA 인증서 파일을 IoT hub에 업로드 합니다.

2. Azure Portal에 제공 된 코드를 사용 하 여 루트 CA 인증서를 소유 하 고 있는지 확인 합니다.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. 다운스트림 장치에 대 한 인증서 체인을 만듭니다. IoT Hub에서 장치가 등록 된 것과 동일한 장치 ID를 사용 합니다.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 장치로 복사 하 고 IoT Hub에 연결 하는 응용 프로그램에서 참조 해야 합니다.

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. 작업 디렉터리에서 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`하 여 루트 CA 인증서 파일을 IoT hub에 업로드 합니다.

2. Azure Portal에 제공 된 코드를 사용 하 여 루트 CA 인증서를 소유 하 고 있는지 확인 합니다.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. 다운스트림 장치에 대 한 인증서 체인을 만듭니다. IoT Hub에서 장치가 등록 된 것과 동일한 장치 ID를 사용 합니다.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 장치로 복사 하 고 IoT Hub에 연결 하는 응용 프로그램에서 참조 해야 합니다.

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
