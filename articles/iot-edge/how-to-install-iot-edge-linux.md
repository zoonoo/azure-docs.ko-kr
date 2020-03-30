---
title: Linux에 Azure IoT Edge 설치 | Microsoft Docs
description: 우분투 또는 라스비안을 실행 하는 리눅스 장치에 Azure IoT 가장자리 설치 지침
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535904"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Debian 기반 Linux 시스템에서 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해를](iot-edge-runtime.md)참조하십시오.

이 문서에서는 X64, ARM32 또는 ARM64 Linux 장치에 Azure IoT Edge 런타임을 설치하는 단계를 나열합니다. 우리는 우분투 서버 16.04, 우분투 서버 18.04, 라스비안 스트레치에 대한 설치 패키지를 제공합니다. 지원되는 Linux 운영 체제 및 아키텍처 목록은 [Azure IoT Edge 지원 시스템을](support.md#operating-systems) 참조하십시오.

> [!NOTE]
> Linux 소프트웨어 저장소의 패키지는 각 패키지에 있는 사용 조건에 따릅니다(/usr/share/doc/*package-name*). 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="install-the-latest-runtime-version"></a>최신 런타임 버전 설치

다음 섹션을 사용하여 Azure IoT Edge 런타임의 최신 버전을 장치에 설치합니다.

>[!NOTE]
>ARM64 장치에 대한 지원은 [공개 미리 보기입니다.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

### <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft 키 및 소프트웨어 리포지토리 피드 등록

IoT Edge 런타임 설치를 위해 장치를 준비합니다.

리포지토리 구성을 설치합니다. 장치 운영 체제와 일치하는 **16.04** 또는 **18.04** 명령을 선택합니다.

* **우분투 서버 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **우분투 서버 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **라스비안 스트레치**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

생성된 목록을 복사합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

마이크로 소프트 GPG 공개 키를 설치

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>컨테이너 런타임 설치

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 아래에 제공된 [Moby 기반](https://mobyproject.org/) 엔진을 사용하는 것이 좋습니다. Moby 엔진은 Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

기기의 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

Moby CLI(명령줄 인터페이스)를 설치합니다. CLI는 개발에 유용하지만 프로덕션 배포에서는 선택 사항입니다.

   ```bash
   sudo apt-get install moby-cli
   ```

Moby 컨테이너 런타임을 설치할 때 오류가 발생하면 이 문서의 후반부에서 제공하는 [Moby 호환성을 확인하기 위한 Linux 커널 확인](#verify-your-linux-kernel-for-moby-compatibility)단계를 따르십시오.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 설치

**IoT Edge 보안 데몬은 IoT** Edge 장치에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

설치 명령은 아직 존재하지 않는 경우 **libiothsm의** 표준 버전을 설치합니다.

기기의 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

보안 디먼을 설치합니다. 패키지가 `/etc/iotedge/`에 설치됩니다.

   ```bash
   sudo apt-get install iotedge
   ```

IoT Edge를 성공적으로 설치하면 출력에서 구성 파일을 업데이트하라는 메시지가 표시됩니다. 장치 프로비저닝을 완료하려면 [보안 데몬 구성](#configure-the-security-daemon) 섹션의 단계를 따릅니다.

## <a name="install-a-specific-runtime-version"></a>특정 런타임 버전 설치

최신 버전을 사용하는 대신 특정 버전의 Moby 및 Azure IoT Edge 런타임을 설치하려는 경우 IoT Edge GitHub 리포지토리에서 직접 구성 요소 파일을 대상으로 지정할 수 있습니다. 다음 단계를 사용하여 모든 IoT Edge 구성 요소를 장치에 탑재할 수 있습니다: 모비 엔진및 CLI, 리바이오츠m, 그리고 마지막으로 IoT Edge 보안 데몬. 다음 섹션으로 건너뛰고 [보안 데몬 구성](#configure-the-security-daemon), 특정 런타임 버전으로 변경하지 않으려면

1. [Azure IoT Edge 릴리스로](https://github.com/Azure/azure-iotedge/releases)이동하여 대상으로 할 릴리스 버전을 찾습니다.

2. 해당 버전의 **자산** 섹션을 확장합니다.

3. 지정된 릴리스에서 Moby 엔진에 대한 업데이트가 있을 수도 있고 그렇지 않을 수도 있습니다. **moby-engine** 및 **moby-cli로**시작하는 파일이 표시되면 다음 명령을 사용하여 해당 구성 요소를 업데이트합니다. Moby 파일이 표시되지 않으면 최신 버전을 찾을 때까지 이전 릴리스 에셋을 다시 살펴보겠습니다.

   1. IoT Edge 장치의 아키텍처와 일치하는 **모비 엔진** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   2. 다음 명령에서 복사된 링크를 사용하여 Moby 엔진의 해당 버전을 설치합니다.

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. IoT Edge 장치의 아키텍처와 일치하는 **moby-cli** 파일을 찾습니다. Moby CLI는 선택적 구성 요소이지만 개발 중에 유용할 수 있습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   4. 다음 명령에서 복사된 링크를 사용하여 Moby CLI의 해당 버전을 설치합니다.

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. 모든 릴리스에는 IoT Edge 보안 데몬과 hsmlib용 새 파일이 있어야 합니다. 다음 명령을 사용하여 해당 구성 요소를 업데이트합니다.

   1. IoT Edge 장치의 아키텍처와 일치하는 **libiothsm-std** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   2. 다음 명령에서 복사된 링크를 사용하여 해당 버전의 hsmlib를 설치합니다.

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. IoT Edge 장치의 아키텍처와 일치하는 **iotedge** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   4. 다음 명령에서 복사된 링크를 사용하여 해당 버전의 IoT Edge 보안 데몬을 설치합니다.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

IoT Edge를 성공적으로 설치하면 출력에서 구성 파일을 업데이트하라는 메시지가 표시됩니다. 다음 섹션의 단계를 수행하여 장치 프로비전을 완료합니다.

## <a name="configure-the-security-daemon"></a>보안 데몬 구성

물리적 디바이스를 Azure IoT Hub에 있는 디바이스 ID와 연결하도록 IoT Edge 런타임을 구성합니다.

디먼은 `/etc/iotedge/config.yaml`에 있는 구성 파일을 사용하여 구성할 수 있습니다. 이 파일은 기본적으로 쓰기 금지되어 있습니다 편집하려면 관리자 권한이 필요합니다.

IoT Hub에서 제공하는 디바이스 연결 문자열을 사용하여 단일 IoT Edge 디바이스를 수동으로 프로비전할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 디바이스를 프로비전할 수 있습니다. 이는 많은 디바이스를 프로비전할 때 유용합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다.

### <a name="option-1-manual-provisioning"></a>옵션 1: 수동 프로비전

수동으로 디바이스를 프로비전하려면 IoT Hub에 새 디바이스를 등록하여 만들 수 있는 [디바이스 연결 문자열](how-to-register-device.md#register-in-the-azure-portal)을 디바이스에 프로비전해야 합니다.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로비저닝 구성을 찾아 **수동 프로비저닝 구성** 섹션의 주석 을 제거합니다. **device_connection_string**의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다. 다른 프로비저닝 섹션이 주석이 없는지 확인합니다. **프로비저닝:** 줄에 이전 공백이 없고 중첩된 항목이 두 공백으로 들여쓰기되는지 확인합니다.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

클립보드 내용을 Nano에 `Shift+Right Click` 붙여넣거나 `Shift+Insert`누릅니다.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>옵션 2: 자동 프로비전

IoT Edge 장치는 [Azure IoT 허브 장치 프로비전 서비스(DPS)를](../iot-dps/index.yml)사용하여 자동으로 프로비저닝할 수 있습니다. 현재 IoT Edge는 자동 프로비저닝을 사용할 때 두 가지 증명 메커니즘을 지원하지만 하드웨어 요구 사항이 사용자의 선택에 영향을 미칠 수 있습니다. 예를 들어, 라즈베리 파이 장치는 기본적으로 신뢰할 수 있는 플랫폼 모듈 (TPM) 칩함께 제공 되지 않습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Linux VM에서 가상 TPM을 사용하여 IoT Edge 장치 생성 및 프로비전](how-to-auto-provision-simulated-device-linux.md)
* [X.509 인증서를 사용하여 IoT Edge 장치 생성 및 프로비전](how-to-auto-provision-x509-certs.md)
* [대칭 키 증명을 사용하여 IoT Edge 장치 생성 및 프로비전](how-to-auto-provision-symmetric-keys.md)

이러한 문서에서는 DPS에 등록을 설정하고 증명을 위한 적절한 인증서 또는 키를 생성하는 데 대해 안내합니다. 어떤 증명 메커니즘을 선택하든 프로비저닝 정보가 IoT Edge 장치의 IoT Edge 구성 파일에 추가됩니다.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로비저닝 구성을 찾아 증명 메커니즘에 적합한 섹션의 주석을 주석 해제합니다. 다른 프로비저닝 섹션이 주석이 없는지 확인합니다. **프로비저닝:** 줄에는 이전 공백이 없어야 하며 중첩된 항목은 두 공백으로 들여쓰기되어야 합니다. IoT Hub 장치 프로비저닝 서비스 인스턴스의 값으로 **scope_id** 값을 업데이트하고 증명 필드에 적합한 값을 제공합니다.

TPM 증명:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

X.509 증명:

```yml
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

대칭 키 증명:

```yml
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

클립보드 내용을 Nano에 `Shift+Right Click` 붙여넣거나 `Shift+Insert`누릅니다.

파일을 저장하고 닫습니다. `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

이전 섹션의 **수동 구성** 단계를 사용한 경우 IoT Edge 런타임을 디바이스에 성공적으로 프로비전하고 실행해야 합니다. **자동 구성** 단계를 사용한 경우 사용자를 대신하여 런타임에서 디바이스를 IoT 허브에 등록할 수 있도록 몇 가지 추가 단계를 수행해야 합니다. 다음 단계는 [Linux 가상 컴퓨터에서 시뮬레이션된 TPM IoT Edge 장치 만들기 및 프로비전을](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)참조하십시오.

IoT 에지 데몬의 상태를 확인할 수 있습니다.

```bash
systemctl status iotedge
```

데몬 로그 검사:

```bash
journalctl -u iotedge --no-pager --no-full
```

가장 일반적인 구성 및 네트워킹 오류에 대한 자동 검사를 실행합니다.

```bash
sudo iotedge check
```

장치에 IoT Edge에 첫 번째 모듈을 배포할 때까지 **$edgeHub** 시스템 모듈은 장치에 배포되지 않습니다. 따라서 자동 검사가 `Edge Hub can bind to ports on host` 연결 검사에 대한 오류를 반환합니다. 이 오류는 모듈을 장치에 배포한 후 발생하지 않는 한 무시할 수 있습니다.

마지막으로 실행 중인 모듈을 나열합니다.

```bash
sudo iotedge list
```

장치에 IoT Edge를 설치한 후 실행 중인 유일한 모듈은 **edgeAgent**. 첫 번째 배포를 만들면 **$edgeHub** 다른 시스템 모듈도 장치에서 시작됩니다. 자세한 내용은 [IoT Edge 모듈 배포를](how-to-deploy-modules-portal.md)참조하십시오.

## <a name="tips-and-troubleshooting"></a>팁 및 문제 해결

`iotedge` 명령을 실행하려면 상승된 권한이 필요합니다. 런타임을 설치한 후 머신에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다. 그 전까지는 `iotedge` 명령 앞에 **sudo**를 사용합니다.

리소스 제한이 있는 디바이스에서는 [문제 해결 가이드](troubleshoot.md)의 지침에 따라 *OptimizeForPerformance* 환경 변수를 *false*로 설정하는 것이 좋습니다.

프록시 서버가 있는 네트워크의 경우 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)의 단계를 수행합니다.

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>모비 호환성에 대 한 리눅스 커널 확인

많은 임베디드 장치 제조업체는 컨테이너 런타임 호환성에 필요한 기능 없이 사용자 지정 Linux 커널이 포함된 장치 이미지를 제공합니다. 권장되는 Moby 컨테이너 런타임을 설치하는 동안 문제가 발생하면 공식 [Moby GitHub 리포지토리의](https://github.com/moby/moby) [체크 구성](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) 스크립트를 사용하여 Linux 커널 구성 문제를 해결할 수 있습니다. 장치에서 다음 명령을 실행하여 커널 구성을 확인합니다.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

이 명령은 Moby 런타임에서 사용되는 커널 피처의 상태를 포함하는 자세한 출력을 제공합니다. 커널이 Moby 런타임과 완벽하게 호환되도록 모든 항목이 아래에 `Generally Necessary` 있고 `Network Drivers` 활성화되어 있는지 확인해야 합니다.  누락된 피처를 식별한 경우 소스에서 커널을 다시 빌드하고 적절한 커널 .config에 포함할 관련 모듈을 선택하여 활성화합니다.  마찬가지로, `defconfig` 또는 `menuconfig`같은 커널 구성 생성기를 사용하는 경우 , 찾아 각각의 기능을 활성화하고 그에 따라 커널을 다시 빌드합니다.  새로 수정된 커널을 배포한 후에는 체크-구성 스크립트를 다시 실행하여 필요한 모든 기능이 성공적으로 활성화되었는지 확인합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

Linux 디바이스에서 IoT Edge 설치를 제거하려면 명령줄에서 다음 명령을 사용합니다.

IoT Edge 런타임을 제거합니다.

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge 런타임이 제거되면 생성된 컨테이너가 중지되지만 장치에 계속 존재합니다. 모든 컨테이너를 보고 남아 있는 항목을 확인합니다.

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

설치된 런타임을 사용하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge 런타임 설치에 문제가 있는 경우 문제 [해결](troubleshoot.md) 페이지를 확인하십시오.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
