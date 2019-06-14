---
title: 투명한 게이트웨이 디바이스 만들기 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 디바이스를 다운스트림 디바이스의 정보를 처리할 수 있는 투명한 게이트웨이로 사용
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5881adb7e2fc0d52cc2037d3d4a9e986b3e29d74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058363"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성

이 문서에서는 IoT Hub와 통신 하는 다른 장치에 대 한 투명 한 게이트웨이로 작동 하는 IoT Edge 장치를 구성 하기 위한 자세한 지침을 제공 합니다. 이 문서에서 *IoT Edge 게이트웨이*라는 용어는 투명한 게이트웨이로 사용되는 IoT Edge 디바이스를 의미합니다. 자세한 내용은 [어떻게는 IoT Edge 장치를 게이트웨이로 사용할 수](./iot-edge-as-gateway.md)입니다.

>[!NOTE]
>현재 상황:
> * Edge 가능 디바이스는 IoT Edge 게이트웨이에 연결할 수 없습니다. 
> * 다운스트림 디바이스는 파일 업로드를 사용할 수 없습니다.

성공적으로 투명 한 게이트웨이 연결을 설정 하려면 세 가지 일반적인 단계가 있습니다. 이 문서에서는 첫 번째 단계를 다룹니다.

1. **게이트웨이 장치를 안전 하 게 연결 하 여 다운스트림 장치, 커뮤니케이션 다운스트림 장치에서 메시지를 적절 한 대상으로 라우팅할 수 있어야 합니다.**
2. 다운스트림 장치는 장치 id를 IoT Hub를 사용 하 여 인증 하 고 해당 게이트웨이 장치를 통해 통신을 할 수 있어야 합니다. 자세한 내용은 [Azure IoT Hub에 다운스트림 장치를 인증할](how-to-authenticate-downstream-device.md)합니다.
3. 다운스트림 장치는 게이트웨이 장치에 안전 하 게 연결할 수 있어야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.


게이트웨이로 작동 하려면 장치에 대 한 다운스트림 장치를 안전 하 게 연결할 수 있으려면 해야 합니다. Azure IoT Edge를 사용하면 PKI(공개 키 인프라)를 사용하여 이러한 디바이스 간에 안전한 연결을 설정할 수 있습니다. 이 경우에 투명한 게이트웨이로 작동하는 IoT Edge 디바이스에 다운스트림 디바이스를 연결할 수 있습니다. 적절 한 보안 유지를 위해 다운스트림 장치는 게이트웨이 장치의 id를 확인 해야 합니다. 잠재적으로 악의적인 게이트웨이에 연결할에서 장치를 방지 하는이 id를 검사 합니다.

다운스트림 디바이스는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. 모든 실질적인 용도의 경우 다운스트림 디바이스는 IoT Edge 게이트웨이 디바이스 자체에서 실행 중인 애플리케이션일 수 있습니다. 

디바이스-게이트웨이 토폴로지에 필요한 신뢰를 설정하는 어떤 인증서 인프라도 만들 수 있습니다. 이 문서에서는 사용 하도록 설정 하는 데 사용 하는 동일한 인증서 설정을 가정 [X.509 CA 보안](../iot-hub/iot-hub-x509ca-overview.md) 는 특정 IoT hub (IoT hub 루트 CA), 일련의 서명 된 인증서에 연결 된 X.509 CA 인증서를 포함 하는 IoT Hub에서 이 CA와 IoT Edge 장치에 대 한 CA입니다.

![게이트웨이 인증서 설정](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>용어 "root CA"이이 문서 전체에서 사용 되는 PKI 인증서 체인의 맨 위에 있는 기관 공용 인증서를 반드시 신디케이티드 인증 기관 인증서 루트 참조 합니다. 대부분의 경우에서는 실제로 중간 CA 공용 인증서입니다. 

게이트웨이 연결의 시작 하는 동안 해당 IoT Edge 장치 CA 인증서는 다운스트림 장치를 표시합니다. 다운스트림 장치에 IoT Edge 장치 CA 인증서는 루트 CA 인증서로 서명 되어 있는지 확인 합니다. 이 프로세스를 통해 다운스트림 장치가 게이트웨이 신뢰할 수 있는 원본에서 제공 되는지 확인 합니다.

다음 단계에서는 인증서를 만들고 게이트웨이에서 올바른 위치에 설치 하는 과정을 안내 합니다. 머신을 사용하여 인증서를 생성한 다음, IoT Edge 디바이스로 복사할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 디바이스를 게이트웨이로 구성합니다. 다음 운영 체제 중 하나에 대 한 IoT Edge 설치 단계를 사용 합니다.
  * [Windows](./how-to-install-iot-edge-windows.md)
  * [Linux x64](./how-to-install-iot-edge-linux.md)
  * [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

이 문서는 참조를 *게이트웨이 호스트 이름* 여러 지점에서. 게이트웨이 호스트 이름에 선언 된 **호스트 이름** IoT Edge 게이트웨이 장치에 있는 config.yaml 파일의 매개 변수. 이 문서에서는 인증서를 만드는 데 사용 됩니다 하 고 다운스트림 장치 연결 문자열에서 참조 됩니다. 게이트웨이 호스트 이름 IP 주소를 사용 하거나 DNS 호스트 파일 항목을 확인할 수 있어야 합니다.

## <a name="generate-certificates-with-windows"></a>Windows를 사용하여 인증서 생성

Windows에서 테스트 인증서를 생성 하려면이 섹션의 단계를 사용 합니다. 인증서를 생성 하는 Windows 컴퓨터를 사용할 수 있으며 다음을 통해 지원 되는 운영 체제에서 실행 되는 모든 IoT Edge 장치에 복사. 

이 섹션에서 생성된 인증서는 테스트 목적으로만 설계되었습니다. 

### <a name="install-openssl"></a>OpenSSL 설치

인증서를 생성하는 데 사용하는 머신에서 Windows용 OpenSSL을 설치합니다. Windows 장치에 설치 된 OpenSSL에 이미 있는 경우이 단계를 건너뛸 수 있지만 해당 openssl.exe 프로그램이 경로 환경 변수에 사용할 수 있는지 확인 합니다. 

다양한 방법으로 OpenSSL을 설치할 수 있습니다.

* **간편:** 다운로드 및 설치 [타사 OpenSSL 이진 파일](https://wiki.openssl.org/index.php/Binaries), 예를 들어에서 [sourceforge OpenSSL](https://sourceforge.net/projects/openssl/)합니다. PATH 환경 변수에 openssl.exe에 대한 전체 경로를 추가합니다. 
   
* **권장:** 직접 또는 [vcpkg](https://github.com/Microsoft/vcpkg)를 통해 OpenSSL 소스 코드를 다운로드하고 머신에서 이진 파일을 빌드합니다. 아래에 나열된 지침은 vcpkg를 사용하여 간단한 단계를 통해 소스 코드를 다운로드하고, 컴파일하고, Windows 머신에 OpenSSL을 설치합니다.

   1. vcpkg를 설치하려는 디렉터리로 이동합니다. 이 디렉터리를 *\<VCPKGDIR>* 이라고 부르겠습니다. 지침에 따라 [vcpkg](https://github.com/Microsoft/vcpkg)를 다운로드하고 설치합니다.
   
   2. Vcpkg는 설치가 끝나면 Windows x64 용 OpenSSL 패키지를 설치 하려면 powershell 프롬프트에서 다음 명령을 실행 합니다. 설치를 완료하는 데 일반적으로 약 5분이 걸립니다.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. openssl.exe 파일을 호출할 수 있도록 `<VCPKGDIR>\installed\x64-windows\tools\openssl`을 PATH 환경 변수에 추가합니다.

### <a name="prepare-creation-scripts"></a>생성 스크립트 준비

Azure IoT Edge git 리포지토리는 테스트 인증서를 생성 하는 데 사용할 수 있는 스크립트를 포함 합니다. 이 섹션에서는 IoT Edge 리포지토리를 복제 하 고 스크립트를 실행 합니다. 

1. 관리자 모드에서 PowerShell 창을 엽니다. 

2. 비-프로덕션 인증서를 생성하는 스크립트가 포함된 git 리포지토리를 복제합니다. 이 스크립트를 통해 인증서가 투명한 게이트웨이를 설정하도록 만들 수 있습니다. `git clone` 명령을 사용하거나 [ZIP을 다운로드](https://github.com/Azure/iotedge/archive/master.zip)합니다. 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 작업하려는 디렉터리로 이동합니다. 이 기사에서는이 디렉터리를 호출  *\<WRKDIR >* 합니다. 이 작업 디렉터리의 모든 인증서 및 키 생성 됩니다.

4. 작업 디렉터리에 복제 된 리포지토리에서 구성 및 스크립트 파일을 복사 합니다. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   리포지토리를 ZIP으로 다운로드 한 경우 폴더 이름은 `iotedge-master` 및 경로의 나머지 부분은 동일 합니다. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. 스크립트를 실행하도록 PowerShell을 활성화합니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. 스크립트에서 PowerShell의 전역 네임 스페이스를 사용 하는 함수를 제공 합니다.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 창에는이 스크립트로 생성 된 인증서는 테스트 목적에 대해서만 되며 프로덕션 시나리오에서는 사용할 수 없습니다 경고가 표시 됩니다.

8. OpenSSL이 올바르게 설치 되어 있는지 확인 하 고 기존 인증서를 사용 하 여 이름 충돌 없습니다 있는지 확인 합니다. 문제가 있으면 스크립트는 시스템에서 이러한 문제를 해결하는 방법을 설명해야 합니다.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>인증서 만들기

이 섹션에서는 세 가지 인증서를 만든 다음, 체인에서 연결합니다. 체인 파일에 인증서를 배치하면 IoT Edge 게이트웨이 디바이스 및 모든 다운스트림 디바이스에서 손쉽게 설치할 수 있습니다.  

1. 루트 CA 인증서를 만들고 하나 중간 인증서에 서명 하 게 합니다. 인증서는 모두 작업 디렉터리에 배치 됩니다.

   ```powershell
   New-CACertsCertChain rsa
   ```

   이 스크립트 명령이 여러 인증서 및 키 파일을 만들지만이 문서의 뒷부분에서 특히 하나를 참조 하려고 합니다.
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 다음 명령을 사용 하 여 IoT Edge 장치 CA 인증서와 개인 키를 만듭니다. 게이트웨이 장치의 iotedge\config.yaml 파일에 있는 게이트웨이 호스트 이름을 제공 합니다. 게이트웨이 호스트 이름이 파일 이름을 사용 하 고 인증서를 생성 하는 동안. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   이 스크립트 명령에는 여러 인증서 및 키 파일을이 문서의 뒷부분을 참조 하는 두 가지를 포함 하 여 만듭니다.
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

인증서 설정 했으므로 건너뜁니다 [게이트웨이에 인증서를 설치 합니다.](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Linux를 사용하여 인증서 생성

Linux에서 테스트 인증서를 생성 하려면이 섹션의 단계를 사용 합니다. Linux 컴퓨터를 사용 하 여 인증서를 생성할 수 있으며 다음을 통해 지원 되는 운영 체제에서 실행 되는 모든 IoT Edge 장치에 복사. 

이 섹션에서 생성된 인증서는 테스트 목적으로만 설계되었습니다. 

### <a name="prepare-creation-scripts"></a>생성 스크립트 준비

Azure IoT Edge git 리포지토리는 테스트 인증서를 생성 하는 데 사용할 수 있는 스크립트를 포함 합니다. 이 섹션에서는 IoT Edge 리포지토리를 복제 하 고 스크립트를 실행 합니다. 

1. 비-프로덕션 인증서를 생성하는 스크립트가 포함된 git 리포지토리를 복제합니다. 이 스크립트를 통해 인증서가 투명한 게이트웨이를 설정하도록 만들 수 있습니다. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 작업하려는 디렉터리로 이동합니다. 이 디렉터리와 기사를 지칭  *\<WRKDIR >* 합니다. 모든 인증서 및 키 파일을이 디렉터리에 만들어집니다.
  
3. 작업 디렉터리에 복제 된 IoT Edge 리포지토리의에서 구성 및 스크립트 파일을 복사 합니다.

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

### <a name="create-certificates"></a>인증서 만들기

이 섹션에서는 세 가지 인증서를 만든 다음, 체인에서 연결합니다. 체인 파일에 인증서를 배치하면 IoT Edge 게이트웨이 디바이스 및 모든 다운스트림 디바이스에서 손쉽게 설치할 수 있습니다.  

1. 루트 CA 인증서 및 중간 인증서를 하나 만듭니다. 이러한 인증서는 *\<WRKDIR>* 에 배치됩니다.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   스크립트는 여러 인증서 및 키를 만듭니다. 다음 섹션에서를 지칭 하는 하나의 참고를 확인 합니다.
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. 다음 명령을 사용 하 여 IoT Edge 장치 CA 인증서와 개인 키를 만듭니다. 게이트웨이 장치의 iotedge/config.yaml 파일에 있는 게이트웨이 호스트 이름을 제공 합니다. 게이트웨이 호스트 이름이 파일 이름을 사용 하 고 인증서를 생성 하는 동안. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   스크립트는 여러 인증서 및 키를 만듭니다. 다음 섹션에서를 지칭 하는 두 개의 참고를 확인 합니다. 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>게이트웨이에 인증서 설치

이제 인증서 체인을 만들었으므로 IoT Edge 게이트웨이 디바이스에 설치하고 새 인증서를 참조하도록 IoT Edge 런타임을 구성해야 합니다. 

1. *\<WRKDIR>* 에서 다음 파일을 복사합니다. IoT Edge 디바이스 아무데나 저장합니다. IoT Edge 디바이스에 있는 대상 디렉터리를 *\<CERTDIR>* 이라고 하겠습니다. 

   * 디바이스 CA 인증서 - `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * 디바이스 CA 프라이빗 키 - `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * Root CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   와 같은 서비스를 사용할 수 있습니다 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 등의 기능이 나 [보안 복사 프로토콜](https://www.ssh.com/ssh/scp/) 인증서 파일을 이동 합니다.  IoT Edge 장치 자체에서 인증서를 생성 한 경우이 단계를 건너뛸 하 수 작업 디렉터리에 경로 사용 합니다.

2. IoT Edge 보안 디먼 구성 파일을 엽니다. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. 설정 된 **인증서** IoT Edge 장치에서 인증서 및 키 파일에 전체 경로로 config.yaml 파일의 속성입니다. 제거 된 `#` 네 줄을 주석으로 처리 하는 인증서 속성 하기 전에 문자입니다. Yaml에서 들여쓰기는 두 개의 공백을 저장 합니다.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Linux 장치를 확인 하는 사용자 **iotedge** 읽기 인증서를 보유 하는 디렉터리에 대 한 권한이 있어야 합니다. 

## <a name="deploy-edgehub-to-the-gateway"></a>게이트웨이에 Edge Hub 배포

장치에서 IoT Edge를 처음 설치 하면 하나의 시스템 모듈 자동으로 시작: IoT Edge 에이전트입니다. 디바이스를 게이트웨이로 작동시키려면 시스템 모듈이 모두 필요합니다. 모든 모듈 하기 전에 게이트웨이 장치에 배포 하지 않은 경우 두 번째 시스템 모듈을 IoT Edge hub를 시작 하기 위해 장치에 대 한 초기 배포를 만듭니다. 배포는 마법사에서 모든 모듈을 추가 하지 않지만 시스템 모듈을 모두 실행 되 고 있는지 확인 됩니다 때문에 빈 살펴보겠습니다. 

`iotedge list` 명령을 사용하여 디바이스에서 실행되는 모듈을 확인할 수 있습니다. 목록만 모듈을 반환 하는 경우 **edge 에이전트** 없이 **edge 허브**, 다음 단계를 사용 합니다.

1. Azure Portal에서 IoT Hub로 이동합니다.

2. **IoT Edge**로 이동하고 게이트웨이로 사용할 IoT Edge 디바이스를 선택합니다.

3. **모듈 설정**을 선택합니다.

4. **다음**을 선택합니다.

5. **경로 지정** 페이지에서 모든 메시지를 모든 모듈에서 IoT Hub로 보내는 기본 경로가 있어야 합니다. 그렇지 않은 경우 다음 코드를 추가한 다음, **다음**을 선택합니다.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. **템플릿 검토** 페이지에서 **제출**을 선택합니다.

## <a name="open-ports-on-gateway-device"></a>게이트웨이 장치에서 포트 열기

표준 IoT Edge 장치는 IoT Hub를 사용 하 여 모든 통신이 아웃 바운드 연결을 통해 수행 되기 때문에 함수에 대 한 모든 인바운드 연결이 필요 하지 않습니다. 게이트웨이 장치는 다운스트림 장치에서 메시지를 수신 해야 하기 때문에 다릅니다. 방화벽이 다운스트림 장치 및 게이트웨이 장치 간의 통신도 방화벽을 통해 가능 해야 합니다.

작동 하려면 게이트웨이 시나리오의 경우 다운스트림 장치에서 인바운드 트래픽을 위해 열린 IoT Edge 허브의 지원 되는 프로토콜의 하나 이상 이어야 합니다. 지원 되는 프로토콜에는 MQTT, AMQP 및 HTTPS 됩니다. 

| 포트 | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>다운스트림 디바이스에서 메시지 라우팅
IoT Edge 런타임은 모듈에서 전송한 메시지와 같은 다운스트림 디바이스에서 전송된 메시지를 라우팅할 수 있습니다. 이 기능을 사용 하면 클라우드로 모든 데이터를 보내기 전에 게이트웨이에서 실행 중인 모듈의 분석을 수행할 수 있습니다. 

현재 다운스트림 디바이스에서 보낸 메시지를 라우팅하는 방식은 모듈에 의해 전송 된 메시지와 구별됩니다. 모듈에서 보낸 메시지에는 모두 **connectionModuleId**라는 시스템 속성이 포함되지만 다운스트림 디바이스에서 보낸 메시지에는 포함되지 않습니다. 경로의 WHERE 절을 사용하여 해당 시스템 특성을 포함하는 메시지를 제외할 수 있습니다. 

경로 아래은 예 라는 모듈에 모든 다운스트림 장치에서 메시지를 보낼 것입니다 `ai_insights`, 한 다음 `ai_insights` IoT Hub에 있습니다.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

메시지 라우팅에 대한 자세한 내용은 [모듈 배포 및 경로 설정](./module-composition.md#declare-routes)을 참조하세요.


## <a name="enable-extended-offline-operation"></a>확장 된 오프 라인 작업을 사용 하도록 설정

로 시작 합니다 [v1.0.4를 사용 릴리스](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) IoT Edge 런타임의 게이트웨이 장치 및 다운스트림 장치 연결을 구성할 수 있습니다 확장된 오프 라인 작업에 대 한 합니다. 

이 기능을 사용 하 여 로컬 모듈 또는 다운스트림 장치 필요에 따라 IoT Edge 장치를 사용 하 여 다시 인증 하 고 메시지 및 IoT hub에서 연결이 끊어진 경우에 메서드를 사용 하 여 서로 통신할 수 있습니다. 자세한 내용은 [IoT Edge 디바이스, 모듈 및 하위 디바이스용 확장 오프라인 기능 이해](offline-capabilities.md)를 참조하세요.

확장 된 오프 라인 기능을 사용 하려면 IoT Edge 게이트웨이 장치에 연결 하는 다운스트림 장치 사이의 부모-자식 관계를 설정할 수 있습니다. 이러한 단계에 자세히 설명 되어 [Azure IoT Hub에 다운스트림 장치를 인증할](how-to-authenticate-downstream-device.md)합니다.

## <a name="next-steps"></a>다음 단계

투명한 게이트웨이로 작동하는 IoT Edge 디바이스가 생겼습니다. 이제 게이트웨이를 신뢰하고 메시지를 보내도록 다운스트림 디바이스를 구성해야 합니다. 자세한 내용은 참조 하세요. [다운스트림 장치는 Azure IoT Edge 게이트웨이 연결할](how-to-connect-downstream-device.md) 하 고 [다운스트림 장치를 Azure IoT Hub를 인증](how-to-authenticate-downstream-device.md)합니다.
