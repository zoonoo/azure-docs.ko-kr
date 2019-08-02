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
ms.openlocfilehash: a91860e9ec8d503a01d079925466093d19bbbccf
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698616"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성

이 문서에서는 다른 장치에서 IoT Hub와 통신할 수 있도록 투명 한 게이트웨이로 작동 하도록 IoT Edge 장치를 구성 하는 방법에 대 한 자세한 지침을 제공 합니다. 이 문서에서 *IoT Edge 게이트웨이*라는 용어는 투명한 게이트웨이로 사용되는 IoT Edge 디바이스를 의미합니다. 자세한 내용은 [IoT Edge 장치를 게이트웨이로 사용 하는 방법](./iot-edge-as-gateway.md)을 참조 하세요.

>[!NOTE]
>현재 상황:
> * Edge 가능 디바이스는 IoT Edge 게이트웨이에 연결할 수 없습니다. 
> * 다운스트림 디바이스는 파일 업로드를 사용할 수 없습니다.

성공적인 투명 게이트웨이 연결을 설정 하는 세 가지 일반적인 단계가 있습니다. 이 문서에서는 첫 번째 단계에 대해 설명 합니다.

1. **게이트웨이 장치는 다운스트림 장치에 안전 하 게 연결 하 고, 다운스트림 장치에서 통신을 수신 하 고, 메시지를 적절 한 대상으로 라우팅할 수 있어야 합니다.**
2. 다운스트림 장치는 IoT Hub을 사용 하 여 인증 하 고 게이트웨이 장치를 통해 통신 하는 것을 알 수 있도록 장치 id가 있어야 합니다. 자세한 내용은 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)을 참조 하세요.
3. 다운스트림 장치는 게이트웨이 장치에 안전 하 게 연결할 수 있어야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.


장치가 게이트웨이로 작동 하려면 다운스트림 장치에 안전 하 게 연결할 수 있어야 합니다. Azure IoT Edge를 사용하면 PKI(공개 키 인프라)를 사용하여 이러한 디바이스 간에 안전한 연결을 설정할 수 있습니다. 이 경우에 투명한 게이트웨이로 작동하는 IoT Edge 디바이스에 다운스트림 디바이스를 연결할 수 있습니다. 적절 한 보안을 유지 하기 위해 다운스트림 장치는 게이트웨이 장치의 id를 확인 해야 합니다. 이 id 검사는 장치에서 잠재적으로 악성 게이트웨이에 연결 하는 것을 방지 합니다.

다운스트림 디바이스는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. 모든 실질적인 용도의 경우 다운스트림 디바이스는 IoT Edge 게이트웨이 디바이스 자체에서 실행 중인 애플리케이션일 수 있습니다. 

디바이스-게이트웨이 토폴로지에 필요한 신뢰를 설정하는 어떤 인증서 인프라도 만들 수 있습니다. 이 문서에서는 IoT Hub에서 [X.509 ca 보안](../iot-hub/iot-hub-x509ca-overview.md) 을 사용 하도록 설정 하는 데 사용 하는 것과 동일한 인증서 설정을 가정 합니다. 여기에는 특정 iot Hub (iot HUB 루트 CA)와 연결 된 x.509 ca 인증서와이 ca로 서명 된 일련의 인증서가 포함 됩니다. 및 IoT Edge 장치의 CA

![게이트웨이 인증서 설정](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>이 문서 전체에서 사용 되는 "루트 CA" 라는 용어는 게시 된 인증 기관의 인증서 루트가 아닌 PKI 인증서 체인의 최상위 인증 기관 공용 인증서를 나타냅니다. 대부분의 경우에는 실제로 중간 CA 공용 인증서입니다. 

게이트웨이는 연결을 시작 하는 동안 다운스트림 장치에 IoT Edge 장치 CA 인증서를 제공 합니다. 다운스트림 장치는 IoT Edge 장치 CA 인증서가 루트 CA 인증서로 서명 되었는지 확인 합니다. 이 프로세스를 통해 다운스트림 장치는 게이트웨이가 신뢰할 수 있는 소스에서 온 것임을 확인할 수 있습니다.

다음 단계는 인증서를 만들어 게이트웨이의 올바른 위치에 설치 하는 과정을 안내 합니다. 머신을 사용하여 인증서를 생성한 다음, IoT Edge 디바이스로 복사할 수 있습니다. 

## <a name="prerequisites"></a>전제 조건

Azure IoT Edge 디바이스를 게이트웨이로 구성합니다. 다음 운영 체제 중 하나에 IoT Edge 설치 단계를 사용 합니다.
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

이 문서는 여러 지점에서 *게이트웨이 호스트 이름을* 참조 합니다. 게이트웨이 호스트 이름은 IoT Edge 게이트웨이 장치에서 config.xml 파일의 **hostname** 매개 변수에 선언 됩니다. 이 문서에서 인증서를 만드는 데 사용 되며 다운스트림 장치의 연결 문자열에서 참조 됩니다. IP 주소는 DNS 또는 호스트 파일 항목을 사용 하 여 게이트웨이 호스트 이름을 확인할 수 있어야 합니다.

## <a name="generate-certificates-with-windows"></a>Windows를 사용하여 인증서 생성

이 섹션의 단계를 사용 하 여 Windows에서 테스트 인증서를 생성 합니다. Windows 컴퓨터를 사용 하 여 인증서를 생성 한 다음 지원 되는 운영 체제에서 실행 되는 모든 IoT Edge 장치로 복사할 수 있습니다. 

이 섹션에서 생성된 인증서는 테스트 목적으로만 설계되었습니다. 

### <a name="install-openssl"></a>OpenSSL 설치

인증서를 생성하는 데 사용하는 머신에서 Windows용 OpenSSL을 설치합니다. Windows 장치에 OpenSSL가 이미 설치 되어 있는 경우이 단계를 건너뛸 수 있지만 PATH 환경 변수에서 OpenSSL을 사용할 수 있는지 확인 합니다. 

다양한 방법으로 OpenSSL을 설치할 수 있습니다.

* **간편:** [타사 OpenSSL 이진 파일](https://wiki.openssl.org/index.php/Binaries)을 다운로드 하 여 설치 합니다 (예: [Sourceforge의 OpenSSL](https://sourceforge.net/projects/openssl/)). PATH 환경 변수에 openssl.exe에 대한 전체 경로를 추가합니다. 
   
* **권장:** 직접 또는 [vcpkg](https://github.com/Microsoft/vcpkg)를 통해 OpenSSL 소스 코드를 다운로드하고 머신에서 이진 파일을 빌드합니다. 아래에 나열된 지침은 vcpkg를 사용하여 간단한 단계를 통해 소스 코드를 다운로드하고, 컴파일하고, Windows 머신에 OpenSSL을 설치합니다.

   1. vcpkg를 설치하려는 디렉터리로 이동합니다. 이 디렉터리를 *\<VCPKGDIR>* 이라고 부르겠습니다. 지침에 따라 [vcpkg](https://github.com/Microsoft/vcpkg)를 다운로드하고 설치합니다.
   
   2. Vcpkg가 설치 되 면 powershell 프롬프트에서 다음 명령을 실행 하 여 Windows x 64 용 OpenSSL 패키지를 설치 합니다. 설치를 완료하는 데 일반적으로 약 5분이 걸립니다.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. openssl.exe 파일을 호출할 수 있도록 `<VCPKGDIR>\installed\x64-windows\tools\openssl`을 PATH 환경 변수에 추가합니다.

### <a name="prepare-creation-scripts"></a>생성 스크립트 준비

Azure IoT Edge git 리포지토리에는 테스트 인증서를 생성 하는 데 사용할 수 있는 스크립트가 포함 되어 있습니다. 이 섹션에서는 IoT Edge 리포지토리를 복제 하 고 스크립트를 실행 합니다. 

1. 관리자 모드에서 PowerShell 창을 엽니다. 

2. 비-프로덕션 인증서를 생성하는 스크립트가 포함된 git 리포지토리를 복제합니다. 이 스크립트를 통해 인증서가 투명한 게이트웨이를 설정하도록 만들 수 있습니다. `git clone` 명령을 사용하거나 [ZIP을 다운로드](https://github.com/Azure/iotedge/archive/master.zip)합니다. 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 작업하려는 디렉터리로 이동합니다. 이 문서 전체에서  *\<WRKDIR >* 디렉터리를 호출 합니다. 모든 인증서 및 키가이 작업 디렉터리에 만들어집니다.

4. 복제 된 리포지토리의 구성 및 스크립트 파일을 작업 디렉터리로 복사 합니다. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   리포지토리를 ZIP으로 다운로드 한 경우 폴더 이름은이 `iotedge-master` 고 나머지 경로는 동일 합니다. 
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

7. 스크립트에서 사용 하는 함수를 PowerShell의 전역 네임 스페이스에 가져옵니다.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 창에는이 스크립트에 의해 생성 된 인증서가 테스트 목적 으로만 사용 되 고 프로덕션 시나리오에서는 사용 되지 않는 경고가 표시 됩니다.

8. OpenSSL가 올바르게 설치 되었는지 확인 하 고 기존 인증서와 이름이 충돌 하지 않는지 확인 합니다. 문제가 있으면 스크립트는 시스템에서 이러한 문제를 해결하는 방법을 설명해야 합니다.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>인증서 만들기

이 섹션에서는 세 가지 인증서를 만든 다음, 체인에서 연결합니다. 체인 파일에 인증서를 배치하면 IoT Edge 게이트웨이 디바이스 및 모든 다운스트림 디바이스에서 손쉽게 설치할 수 있습니다.  

1. 루트 CA 인증서를 만들어 하나의 중간 인증서에 서명 하도록 합니다. 인증서는 모두 작업 디렉터리에 저장 됩니다.

   ```powershell
   New-CACertsCertChain rsa
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만들지만,이 문서의 뒷부분에서 설명 하 고 있습니다.
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 다음 명령을 사용 하 여 IoT Edge 장치 CA 인증서와 개인 키를 만듭니다. 게이트웨이 장치에 있는 iotedge\config.yaml 파일에서 찾을 수 있는 게이트웨이 호스트 이름을 제공 합니다. 게이트웨이 호스트 이름은 파일의 이름을 및 인증서를 생성 하는 동안 사용 됩니다. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   이 스크립트 명령은이 문서의 뒷부분에서 참조할 두 가지 인증서 및 키 파일을 만듭니다.
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

이제 인증서가 있으므로 [게이트웨이에서 인증서 설치](#install-certificates-on-the-gateway) 로 건너뜁니다.

## <a name="generate-certificates-with-linux"></a>Linux를 사용하여 인증서 생성

이 섹션의 단계를 사용 하 여 Linux에서 테스트 인증서를 생성 합니다. Linux 컴퓨터를 사용 하 여 인증서를 생성 한 후 지원 되는 운영 체제에서 실행 되는 모든 IoT Edge 장치로 복사할 수 있습니다. 

이 섹션에서 생성된 인증서는 테스트 목적으로만 설계되었습니다. 

### <a name="prepare-creation-scripts"></a>생성 스크립트 준비

Azure IoT Edge git 리포지토리에는 테스트 인증서를 생성 하는 데 사용할 수 있는 스크립트가 포함 되어 있습니다. 이 섹션에서는 IoT Edge 리포지토리를 복제 하 고 스크립트를 실행 합니다. 

1. 비-프로덕션 인증서를 생성하는 스크립트가 포함된 git 리포지토리를 복제합니다. 이 스크립트를 통해 인증서가 투명한 게이트웨이를 설정하도록 만들 수 있습니다. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 작업하려는 디렉터리로 이동합니다. 이 디렉터리는  *\<WRKDIR >* 으로 문서 전체에서 참조할 것입니다. 모든 인증서 및 키 파일이이 디렉터리에 생성 됩니다.
  
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

### <a name="create-certificates"></a>인증서 만들기

이 섹션에서는 세 가지 인증서를 만든 다음, 체인에서 연결합니다. 체인 파일에 인증서를 배치하면 IoT Edge 게이트웨이 디바이스 및 모든 다운스트림 디바이스에서 손쉽게 설치할 수 있습니다.  

1. 루트 CA 인증서와 하나의 중간 인증서를 만듭니다. 이러한 인증서는 *\<WRKDIR>* 에 배치됩니다.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   이 스크립트는 여러 인증서와 키를 만듭니다. 다음 섹션에서 참조할 정보를 적어 둡니다.
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. 다음 명령을 사용 하 여 IoT Edge 장치 CA 인증서와 개인 키를 만듭니다. 게이트웨이 장치의 iotedge/config.xml 파일에서 찾을 수 있는 게이트웨이 호스트 이름을 제공 합니다. 게이트웨이 호스트 이름은 파일의 이름을 및 인증서를 생성 하는 동안 사용 됩니다. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   이 스크립트는 여러 인증서와 키를 만듭니다. 다음 섹션에서 참조할 2를 기록해 둡니다. 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>게이트웨이에 인증서 설치

이제 인증서 체인을 만들었으므로 IoT Edge 게이트웨이 디바이스에 설치하고 새 인증서를 참조하도록 IoT Edge 런타임을 구성해야 합니다. 

1. *\<WRKDIR>* 에서 다음 파일을 복사합니다. IoT Edge 디바이스 아무데나 저장합니다. IoT Edge 디바이스에 있는 대상 디렉터리를 *\<CERTDIR>* 이라고 하겠습니다. 

   * 디바이스 CA 인증서 - `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * 디바이스 CA 프라이빗 키 - `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * 루트 CA-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 와 같은 서비스를 사용 하거나 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용 하 여 인증서 파일을 이동할 수 있습니다.  IoT Edge 장치 자체에서 인증서를 생성 한 경우이 단계를 건너뛰고 작업 디렉터리에 대 한 경로를 사용할 수 있습니다.

2. IoT Edge 보안 디먼 구성 파일을 엽니다. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Config.xml 파일의 **인증서** 속성을 IoT Edge 장치의 인증서 및 키 파일에 대 한 전체 경로로 설정 합니다. 네 줄의 주석 처리를 제거 하려면 인증서 속성 앞의 문자를제거합니다.`#` Yaml의 들여쓰기는 두 개의 공백으로 구분 됩니다.

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

4. Linux 장치에서 사용자 **iotedge** 에 인증서가 포함 된 디렉터리에 대 한 읽기 권한이 있는지 확인 합니다. 

## <a name="deploy-edgehub-to-the-gateway"></a>게이트웨이에 Edge Hub 배포

장치에 IoT Edge를 처음 설치 하는 경우에는 IoT Edge 에이전트와 같은 한 시스템 모듈만 자동으로 시작 됩니다. 디바이스를 게이트웨이로 작동시키려면 시스템 모듈이 모두 필요합니다. 이전에 게이트웨이 장치에 모듈을 배포 하지 않은 경우 장치에 대 한 초기 배포를 만들어 두 번째 시스템 모듈인 IoT Edge 허브를 시작 합니다. 마법사에서 모듈을 추가 하지 않으므로 배포는 비어 있게 되지만 두 시스템 모듈이 모두 실행 되 고 있는지 확인 합니다. 

`iotedge list` 명령을 사용하여 디바이스에서 실행되는 모듈을 확인할 수 있습니다. 목록이 **edgeHub**없이 모듈 **edgeAgent** 반환 하는 경우 다음 단계를 사용 합니다.

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

IoT Hub와의 모든 통신은 아웃 바운드 연결을 통해 수행 되므로 표준 IoT Edge 장치는 작동 하기 위해 인바운드 연결이 필요 하지 않습니다. 게이트웨이 장치는 다운스트림 장치에서 메시지를 수신 해야 하기 때문에 다릅니다. 다운스트림 장치와 게이트웨이 장치 사이에 방화벽이 있는 경우 방화벽을 통해 통신도 가능 해야 합니다.

게이트웨이 시나리오가 작동 하려면 다운스트림 장치에서 인바운드 트래픽에 대해 IoT Edge 허브의 지원 되는 프로토콜 중 하나 이상을 열어야 합니다. 지원 되는 프로토콜은 MQTT, AMQP 및 HTTPS입니다. 

| 포트 | 프로토콜 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>다운스트림 디바이스에서 메시지 라우팅
IoT Edge 런타임은 모듈에서 전송한 메시지와 같은 다운스트림 디바이스에서 전송된 메시지를 라우팅할 수 있습니다. 이 기능을 사용 하면 클라우드에서 데이터를 전송 하기 전에 게이트웨이에서 실행 되는 모듈에서 분석을 수행할 수 있습니다. 

현재 다운스트림 디바이스에서 보낸 메시지를 라우팅하는 방식은 모듈에 의해 전송 된 메시지와 구별됩니다. 모듈에서 보낸 메시지에는 모두 **connectionModuleId**라는 시스템 속성이 포함되지만 다운스트림 디바이스에서 보낸 메시지에는 포함되지 않습니다. 경로의 WHERE 절을 사용하여 해당 시스템 특성을 포함하는 메시지를 제외할 수 있습니다. 

아래 경로는 다운스트림 장치에서 이라는 `ai_insights`모듈에 메시지를 보낸 다음에서 `ai_insights` IoT Hub 하는 예제입니다.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

메시지 라우팅에 대한 자세한 내용은 [모듈 배포 및 경로 설정](./module-composition.md#declare-routes)을 참조하세요.


## <a name="enable-extended-offline-operation"></a>확장 된 오프 라인 작업 사용

IoT Edge 런타임의 [v 1.0.4 릴리스부터](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) 는 장치에 연결 하는 게이트웨이 장치와 다운스트림 장치를 확장 된 오프 라인 작업에 대해 구성할 수 있습니다. 

이 기능을 사용 하는 경우 로컬 모듈 또는 다운스트림 장치는 필요에 따라 IoT Edge 장치를 사용 하 여 다시 인증 하 고 IoT hub와의 연결을 끊은 경우에도 메시지 및 메서드를 사용 하 여 서로 통신할 수 있습니다. 자세한 내용은 [IoT Edge 디바이스, 모듈 및 하위 디바이스용 확장 오프라인 기능 이해](offline-capabilities.md)를 참조하세요.

확장 된 오프 라인 기능을 사용 하도록 설정 하려면 연결 하는 IoT Edge 게이트웨이 장치와 다운스트림 장치 간에 부모-자식 관계를 설정 합니다. 이러한 단계는 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)에 자세히 설명 되어 있습니다.

## <a name="next-steps"></a>다음 단계

투명한 게이트웨이로 작동하는 IoT Edge 디바이스가 생겼습니다. 이제 게이트웨이를 신뢰하고 메시지를 보내도록 다운스트림 디바이스를 구성해야 합니다. 자세한 내용은 [Azure IoT Edge 게이트웨이에 다운스트림 장치 연결](how-to-connect-downstream-device.md) 및 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)을 참조 하세요.
