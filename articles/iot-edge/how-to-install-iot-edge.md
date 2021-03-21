---
title: Azure IoT Edge | 설치 Microsoft Docs
description: Windows 또는 Linux 장치에 대 한 설치 지침 Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: kgremban
ms.openlocfilehash: 6a64bb2801830440dc49e72786c9c00a6e4796b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201611"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Linux 용 Azure IoT Edge 설치 또는 제거

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. 디바이스가 IoT Edge 런타임을 사용하여 구성되면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 자세히 알아보려면 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조 하세요.

이 문서에는 Linux 장치에 Azure IoT Edge 런타임을 설치 하는 단계가 나와 있습니다.

## <a name="prerequisites"></a>필수 조건

* [등록 된 장치 ID](how-to-register-device.md)

  대칭 키 인증을 사용 하 여 장치를 등록 한 경우 장치 연결 문자열을 준비 합니다.

  X.509 자체 서명 된 인증서 인증을 사용 하 여 장치를 등록 한 경우 장치를 등록 하는 데 사용한 id 인증서와 장치에서 일치 하는 개인 키를 하나 이상 사용 해야 합니다.

* Linux 장치

  X64, ARM32 또는 ARM64 Linux 장치가 있어야 합니다. Microsoft는 Ubuntu Server 18.04 및 Raspberry Pi OS Stretch 운영 체제에 대 한 설치 패키지를 제공 합니다.

  현재 프로덕션 시나리오에 대해 지원 되는 운영 체제에 대 한 최신 정보는 [지원 되는 Azure IoT Edge 시스템](support.md#operating-systems) 을 참조 하세요.

  >[!NOTE]
  >ARM64 장치에 대 한 지원은 [공개 미리 보기로](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)제공 됩니다.

* Microsoft 설치 패키지에 액세스할 수 있도록 장치를 준비 합니다.

  디바이스 운영 체제와 일치하는 리포지토리 구성을 설치합니다.

  * **Ubuntu Server 18.04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS Stretch**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  생성된 목록을 sources.list.d 디렉터리에 복사합니다.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Microsoft GPG 공개 키를 설치합니다.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge 소프트웨어 패키지에는 각 패키지 ( `usr/share/doc/{package-name}` 또는 디렉터리)에 있는 사용 조건이 적용 됩니다 `LICENSE` . 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치하고 사용하면 이러한 사용 조건에 동의하는 것입니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="install-a-container-engine"></a>컨테이너 엔진 설치

Azure IoT Edge는 OCI 호환 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 Moby 엔진을 사용 하는 것이 좋습니다. Moby 엔진은 Azure IoT Edge에서 공식적으로 지원 되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

Moby 컨테이너 엔진을 설치할 때 오류가 발생 하는 경우에는 Linux 커널을 Moby 호환성을 확인 하세요. 일부 임베디드 장치 제조업체는 컨테이너 엔진 호환성에 필요한 기능이 없는 사용자 지정 Linux 커널을 포함 하는 장치 이미지를 제공 합니다. Moby에서 제공한 [체크 인 스크립트](https://github.com/moby/moby/blob/master/contrib/check-config.sh) 를 사용 하 여 커널 구성을 확인 하는 다음 명령을 실행 합니다.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

스크립트의 출력에서 및 아래의 모든 항목을 `Generally Necessary` 사용할 수 있는지 확인 `Network Drivers` 합니다. 누락 된 기능을 사용 하는 경우 소스에서 커널을 다시 작성 하 고 적절 한 커널 .config에 포함 하기 위해 관련 모듈을 선택 하 여 사용 하도록 설정 합니다. 마찬가지로 또는와 같은 커널 구성 생성기를 사용 하는 `defconfig` 경우 `menuconfig` 해당 기능을 찾아서 사용 하도록 설정 하 고 그에 따라 커널을 다시 빌드합니다. 새로 수정 된 커널을 배포한 후에는 확인-구성 스크립트를 다시 실행 하 여 필요한 모든 기능이 성공적으로 설정 되었는지 확인 합니다.

## <a name="install-iot-edge"></a>IoT Edge 설치

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 보안 디먼은 IoT Edge 장치에서 보안 표준을 제공 하 고 유지 관리 합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결 된 장치에 최신 버전을 설치 하는 일반적인 프로세스를 나타냅니다. 시험판 버전과 같은 특정 버전을 설치 해야 하거나 오프 라인 상태에서 설치 해야 하는 경우이 문서 뒷부분의 [오프 라인 또는 특정 버전 설치](#offline-or-specific-version-installation-optional) 단계를 따르세요.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

사용할 수 있는 IoT Edge 버전을 확인 합니다.

   ```bash
   apt list -a iotedge
   ```

최신 버전의 보안 디먼을 설치 하려는 경우 다음 명령을 사용 하 여 최신 버전의 **libiothsm** 패키지를 설치 합니다.

   ```bash
   sudo apt-get install iotedge
   ```

또는 특정 버전의 보안 디먼을 설치 하려는 경우 apt 목록 출력의 버전을 지정 합니다. 또한 **libiothsm** 패키지에 대해 동일한 버전을 지정 합니다. 그렇지 않으면 최신 버전이 설치 됩니다. 예를 들어 다음 명령은 1.0.10 릴리스의 최신 버전을 설치 합니다.

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

설치 하려는 버전이 나열 되지 않은 경우이 문서 뒷부분의 [오프 라인 또는 특정 버전 설치](#offline-or-specific-version-installation-optional) 단계를 따르세요. 이 섹션에서는 이전 버전의 IoT Edge 보안 디먼 또는 릴리스 후보 버전을 대상으로 하는 방법을 보여 줍니다.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 서비스는 IoT Edge 장치에서 보안 표준을 제공 하 고 유지 관리 합니다. 서비스는 모든 부팅에서 시작 하 고 나머지 IoT Edge 런타임의 부트스트랩를 시작 하 여 장치를 다시 시작 합니다.

IoT id 서비스는 IoT Edge 버전 1.2와 함께 도입 되었습니다. 이 서비스는 IoT Edge 및 IoT Hub와 통신 해야 하는 기타 장치 구성 요소에 대 한 id 프로 비전 및 관리를 처리 합니다.

이 섹션의 단계는 인터넷에 연결 된 장치에 최신 버전을 설치 하는 일반적인 프로세스를 나타냅니다. 시험판 버전과 같은 특정 버전을 설치 해야 하거나 오프 라인 상태에서 설치 해야 하는 경우이 문서 뒷부분의 [오프 라인 또는 특정 버전 설치](#offline-or-specific-version-installation-optional) 단계를 따르세요.

>[!NOTE]
>이 섹션의 단계에서는 현재 공개 미리 보기로 제공 되는 IoT Edge 버전 1.2을 설치 하는 방법을 보여 줍니다. 최신 버전의 IoT Edge를 설치 하는 단계를 찾고 있는 경우이 문서의 [1.1 (LTS)](?view=iotedge-2018-06&preserve-view=true) 버전을 확인 하세요.
>
>이전 버전을 실행 하는 IoT Edge 장치가 이미 있고 1.2로 업그레이드 하려는 경우 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)의 단계를 사용 합니다. 버전 1.2은 업그레이드 하는 데 특정 단계가 필요한 이전 버전의 IoT Edge와 충분히 다릅니다.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

사용할 수 있는 IoT Edge 버전을 확인 합니다.

   ```bash
   apt list -a aziot-edge
   ```

최신 버전의 IoT Edge를 설치 하려는 경우 다음 명령을 사용 하 여 최신 버전의 id 서비스 패키지도 설치 합니다.

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- commenting out for public preview. reintroduce at GA

Or, if you want to install a specific version of IoT Edge and the identity service, specify the versions from the apt list output. Specify the same versions for both services.. For example, the following command installs the most recent version of the 1.2 release:

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

-->

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 id를 사용 하 여 장치 프로 비전

이제 컨테이너 엔진과 IoT Edge 런타임이 장치에 설치 되었으므로 다음 단계를 수행할 수 있습니다 .이 단계에서는 클라우드 id 및 인증 정보를 사용 하 여 장치를 설정 합니다.

사용 하려는 인증 유형에 따라 다음 섹션을 선택 합니다.

* [옵션 1: 대칭 키를 사용 하 여 인증](#option-1-authenticate-with-symmetric-keys)
* [옵션 2: x.509 인증서를 사용 하 여 인증](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>옵션 1: 대칭 키를 사용 하 여 인증

이 시점에서 IoT Edge 런타임은 Linux 장치에 설치 되 고, 클라우드 id 및 인증 정보를 사용 하 여 장치를 프로 비전 해야 합니다.

이 섹션에서는 대칭 키 인증을 사용 하 여 장치를 프로 비전 하는 단계를 안내 합니다. IoT Hub에 장치를 등록 하 고 장치 정보에서 연결 문자열을 검색 해야 합니다. 그렇지 않은 경우 [IoT Hub에서 IoT Edge 장치 등록](how-to-register-device.md)의 단계를 따릅니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 장치에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

파일의 프로 비전 구성을 찾고, 아직 주석 처리가 제거 되지 않은 경우 **연결 문자열 섹션을 사용 하 여 수동 프로 비전 구성** 의 주석 처리를 제거 합니다.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**device_connection_string** 의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다. 다른 프로 비전 섹션을 주석으로 처리 해야 합니다. **프로 비전:** 줄에 앞에 공백이 없고 중첩 된 항목이 두 개의 공백으로 들여쓰기 되는지 확인 합니다.

클립보드 내용을 Nano에 붙여 넣으려면 `Shift+Right Click` 또는 `Shift+Insert` 키를 누릅니다.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 설치의 일부로 제공 되는 템플릿 파일을 기반으로 장치에 대 한 구성 파일을 만듭니다.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

IoT Edge 장치에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

파일의 **프로 비전** 섹션을 찾고 연결 문자열 줄로 수동 프로 비전의 주석 처리를 제거 합니다.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**Connection_string** 의 값을 IoT Edge 장치의 연결 문자열로 업데이트 합니다.

클립보드 내용을 Nano에 붙여 넣으려면 `Shift+Right Click` 또는 `Shift+Insert` 키를 누릅니다.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에 프로 비전 정보를 입력 한 후에 변경 내용을 적용 합니다.

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>옵션 2: x.509 인증서를 사용 하 여 인증

이 시점에서 IoT Edge 런타임은 Linux 장치에 설치 되 고, 클라우드 id 및 인증 정보를 사용 하 여 장치를 프로 비전 해야 합니다.

이 섹션에서는 x.509 인증서 인증을 사용 하 여 장치를 프로 비전 하는 단계를 안내 합니다. 장치를 IoT Hub에 등록 하 여 IoT Edge 장치에 있는 인증서 및 개인 키와 일치 하는 지문을 제공 해야 합니다. 그렇지 않은 경우 [IoT Hub에서 IoT Edge 장치 등록](how-to-register-device.md)의 단계를 따릅니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 장치에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

파일의 프로 비전 구성 섹션을 찾고 **x.509 id 인증서 섹션을 사용 하 여 수동 프로 비전 구성** 의 주석 처리를 제거 합니다. 다른 프로 비전 섹션을 주석으로 처리 해야 합니다. **프로 비전:** 줄에 앞에 공백이 없고 중첩 된 항목이 두 개의 공백으로 들여쓰기 되는지 확인 합니다.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

다음 필드를 업데이트 합니다.

* **iothub_hostname**: 장치가 연결 될 IoT hub의 호스트 이름입니다. 예: `{IoT hub name}.azure-devices.net`
* **device_id**: 장치를 등록할 때 제공한 id입니다.
* **identity_cert**: 장치의 id 인증서에 대 한 URI입니다. 예: `file:///path/identity_certificate.pem`
* **identity_pk**: 제공 된 id 인증서의 개인 키 파일에 대 한 URI입니다. 예: `file:///path/identity_key.pem`

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 설치의 일부로 제공 되는 템플릿 파일을 기반으로 장치에 대 한 구성 파일을 만듭니다.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

IoT Edge 장치에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

파일의 **프로 비전** 섹션을 찾고 x.509 id 인증서를 사용 하 여 수동으로 프로 비전 할 줄의 주석 처리를 제거 합니다. 다른 프로 비전 섹션을 주석으로 처리 해야 합니다.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

다음 필드를 업데이트 합니다.

* **iothub_hostname**: 장치가 연결 될 IoT hub의 호스트 이름입니다. 예: `{IoT hub name}.azure-devices.net`
* **device_id**: 장치를 등록할 때 제공한 id입니다.
* **identity_cert**: 장치의 id 인증서에 대 한 URI입니다 (예:). `file:///path/identity_certificate.pem` 또는 EST 또는 로컬 인증 기관을 사용 하 여 동적으로 인증서를 발급 합니다.
* **identity_pk**: 제공 된 id 인증서의 개인 키 파일에 대 한 URI입니다 (예:). `file:///path/identity_key.pem` 또는 PKCS # 11 URI를 제공한 다음 구성 파일의 뒷부분에 있는 **pkcs # 11** 섹션에서 구성 정보를 제공 합니다.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에 프로 비전 정보를 입력 한 후에 변경 내용을 적용 합니다.

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>성공적인 구성 확인

IoT Edge 디바이스에서 런타임이 성공적으로 설치 및 구성되었는지 확인합니다.

>[!TIP]
>`iotedge` 명령을 실행하려면 상승된 권한이 필요합니다. IoT Edge 런타임을 설치한 후 처음으로 머신에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다. 그 전까지는 명령 앞에 `sudo`를 사용합니다.

IoT Edge 시스템 서비스가 실행 되 고 있는지 확인 하십시오.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

::: moniker-end

서비스 문제를 해결해야 할 경우 서비스 로그를 검색합니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

도구를 사용 `check` 하 여 장치의 구성 및 연결 상태를 확인 합니다.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>사용 권한이 업데이트 된 후에도 항상를 사용 `sudo` 하 여 검사 도구를 실행 합니다. 구성 파일에 액세스 하 여 구성 상태를 확인 하려면이 도구에 상승 된 권한이 필요 합니다.

IoT Edge 디바이스에서 실행되는 모든 모듈을 봅니다. 서비스가 처음 시작 될 때 실행 되는 **edgeAgent** 모듈만 표시 되어야 합니다. edgeAgent 모듈은 기본적으로 실행되며, 디바이스에 배포하는 추가 모듈을 설치하고 시작하는 데 도움이 됩니다.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>오프 라인 또는 특정 버전 설치 (선택 사항)

이 섹션의 단계는 표준 설치 단계에서 다루지 않는 시나리오에 대 한 것입니다. 다음을 포함할 수 있습니다.

* 오프 라인 상태에서 IoT Edge 설치
* 릴리스 후보 버전 설치

에서 제공 하지 않는 특정 버전의 Azure IoT Edge 런타임을 설치 하려는 경우이 섹션의 단계를 사용 `apt-get install` 합니다. Microsoft 패키지 목록에는 제한 된 최신 버전과 해당 하위 버전 집합만 포함 되어 있으므로 이러한 단계는 이전 버전 또는 릴리스 후보 버전을 설치 하려는 모든 사용자를 위한 것입니다.

말아 넘기기 명령을 사용 하면 IoT Edge GitHub 리포지토리에서 직접 구성 요소 파일을 대상으로 지정할 수 있습니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. [Azure IoT Edge 릴리스로](https://github.com/Azure/azure-iotedge/releases)이동 하 고 대상으로 지정할 릴리스 버전을 찾습니다.

2. 해당 버전에 대 한 **자산** 섹션을 확장 합니다.

3. 모든 릴리스에는 IoT Edge 보안 디먼 및 hsmlib에 대 한 새 파일이 있어야 합니다. 오프 라인 장치에 IoT Edge를 설치 하려는 경우 미리 이러한 파일을 다운로드 하세요. 그렇지 않으면 다음 명령을 사용 하 여 해당 구성 요소를 업데이트 합니다.

   1. IoT Edge 장치의 아키텍처와 일치 하는 **libiothsm** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭 하 고 링크 주소를 복사 합니다.

   2. 다음 명령의 복사 된 링크를 사용 하 여 해당 버전의 hsmlib를 설치 합니다.

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. IoT Edge 장치의 아키텍처와 일치 하는 **iotedge** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭 하 고 링크 주소를 복사 합니다.

   4. 다음 명령의 복사 된 링크를 사용 하 여 해당 버전의 IoT Edge 보안 디먼을 설치 합니다.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>장치가 현재 1.1 이전 버전 IoT Edge 실행 되 고 있는 경우이 섹션의 단계를 수행 하기 전에 **iotedge** 및 **libiothsm** 패키지를 제거 합니다. 자세한 내용은 [1.0 또는 1.1에서 1.2로 업데이트](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)를 참조 하세요.

1. [Azure IoT Edge 릴리스로](https://github.com/Azure/azure-iotedge/releases)이동 하 고 대상으로 지정할 릴리스 버전을 찾습니다.

2. 해당 버전에 대 한 **자산** 섹션을 확장 합니다.

3. 모든 릴리스에 IoT Edge 및 id 서비스에 대 한 새 파일이 있어야 합니다. 오프 라인 장치에 IoT Edge를 설치 하려는 경우 미리 이러한 파일을 다운로드 하세요. 그렇지 않으면 다음 명령을 사용 하 여 해당 구성 요소를 업데이트 합니다.

   1. IoT Edge 장치의 아키텍처와 일치 하는 **aziot** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭 하 고 링크 주소를 복사 합니다.

   2. 다음 명령의 복사 된 링크를 사용 하 여 해당 버전의 id 서비스를 설치 합니다.

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo dpkg -i ./aziot-identity-service.deb
      ```

   3. IoT Edge 장치의 아키텍처와 일치 하는 **aziot** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭 하 고 링크 주소를 복사 합니다.

   4. 다음 명령의 복사 된 링크를 사용 하 여 해당 버전의 IoT Edge를 설치 합니다.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo dpkg -i ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

컨테이너 엔진과 IoT Edge 런타임이 장치에 설치 되었으므로 다음 단계를 수행할 준비가 되었습니다 .이 단계에서는 [클라우드 id를 사용 하 여 장치를 프로 비전](#provision-the-device-with-its-cloud-identity)합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

장치에서 IoT Edge 설치를 제거 하려면 다음 명령을 사용 합니다.

IoT Edge 런타임을 제거합니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

`--purge`구성 파일을 포함 하 여 IoT Edge와 연결 된 모든 파일을 삭제 하려면 플래그를 사용 합니다. IoT Edge를 다시 설치 하 고 나중에 동일한 구성 정보를 사용 하려면이 플래그를 그대로 둡니다.

IoT Edge 런타임을 제거 하면 만든 컨테이너가 모두 중지 되지만 장치에는 계속 존재 합니다. 모든 컨테이너를 보고 남아 있는 항목을 확인합니다.

```bash
sudo docker ps -a
```

두 개의 런타임 컨테이너를 포함하여 디바이스에서 컨테이너를 삭제합니다.

```bash
sudo docker rm -f <container name>
```

마지막으로 디바이스에서 컨테이너 런타임을 제거합니다.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>다음 단계

장치에 모듈을 배포 하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md) 를 계속 진행 하세요.
