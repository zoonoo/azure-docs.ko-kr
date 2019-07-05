---
title: Linux에 Azure IoT Edge 설치 | Microsoft Docs
description: Ubuntu를 실행하는 Linux AMD64 디바이스에서 Azure IoT Edge 설치 지침
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: bbab0d8d0947c18cf8e6c178d12fdbd7b335d2b6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485888"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Linux(x64)에서 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

자세한 내용은 참조 하세요 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)합니다.

이 문서에서는 Ubuntu Linux x64 (Intel/AMD)에 Azure IoT Edge 런타임을 설치 하는 단계를 나열 합니다. IoT Edge 장치입니다. 가리킵니다 [지원 되는 Azure IoT Edge 체제](support.md#operating-systems) AMD64 운영 체제의 목록은 합니다.

> [!NOTE]
> Linux 소프트웨어 저장소의 패키지는 각 패키지에 있는 사용 조건에 따릅니다(/usr/share/doc/*package-name*). 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="install-the-latest-version"></a>최신 버전 설치

장치를 Azure IoT Edge 서비스의 최신 버전을 설치 하려면 다음 섹션을 사용 합니다. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft 키 및 소프트웨어 리포지토리 피드 등록

IoT Edge 런타임 설치 장치를 준비 합니다.


저장소 구성을 설치 합니다. 중 하나를 선택 합니다 **16.04** 하거나 **18.04** Ubuntu의 릴리스에 대 한 적절 하 게 코드 조각:

> [!NOTE]
> Ubuntu의 버전에 대 한 올바른 코드 상자에서 코드 조각을 선택 해야 합니다.

* 에 대 한 **Ubuntu 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* 에 대 한 **Ubuntu 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
생성 된 목록에 복사 합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Microsoft GPG 공개 키를 설치 합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>컨테이너 런타임 설치

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 런타임을 사용합니다. 사용 하는 좋습니다 프로덕션 시나리오에는 [모 비 기반](https://mobyproject.org/) 아래에 제공 된 엔진입니다. Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

Apt 업데이트를 수행 합니다.

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

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>모 비 호환성을 위해 Linux 커널을 확인합니다

대부분의 임베디드 장치 제조업체 컨테이너 런타임 호환성을 위해 필요한 기능 누락 될 수 있는 사용자 지정 Linux 커널을 포함 하는 장치 이미지를 제공 합니다. 권장 설치 하는 경우 문제가 발생 하면 [모 비](https://github.com/moby/moby) 컨테이너 런타임을 수에 커널 구성을 사용 하 여 Linux 문제를 해결할 수는 [검사-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) 에 제공 된 스크립트는 공식 [모 비 Github 리포지토리](https://github.com/moby/moby) 장치에서 다음 명령을 실행 하 여 합니다.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

이 모 비 런타임에서 사용 되는 커널 기능의 상태를 포함 하는 자세한 출력을 제공 합니다. 아래에 있는 모든 항목을 확인 하려고 `Generally Necessary` 및 `Network Drivers` 커널을 모 비 런타임 완벽 하 게 호환 되도록 설정 됩니다.  모든 누락 된 기능을 식별 하는 경우에 원본에서 커널을 다시 작성 하 고 적절 한 커널.config에 포함에 대 한 연결 된 모듈을 선택 하 여 활성화할 수 있습니다.  마찬가지로, defconfig 또는 menuconfig와 같은 커널 구성 생성기를 사용 하는 경우 찾기 및 해당 기능을 사용 하도록 설정 하 고, 커널을 적절 하 게 다시 작성 해야 합니다.  새로 수정 된 커널을 배포한 후에 식별 된 기능을 성공적으로 설정 된가 있는지 확인 하려면 다시 확인 구성 스크립트를 실행 합니다.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 설치

합니다 **IoT Edge 보안 디먼** 제공 하 고 IoT Edge 장치의 보안 표준을 유지 관리 합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

설치 명령에는의 표준 버전도 설치 합니다 **libiothsm** 아직 없는 경우.

Apt 업데이트를 수행 합니다.

   ```bash
   sudo apt-get update
   ```

보안 디먼을 설치합니다. 패키지가 `/etc/iotedge/`에 설치됩니다.

   ```bash
   sudo apt-get install iotedge
   ```

IoT Edge 성공적으로 설치 되 면 출력 하 라는 메시지가 나타납니다 구성 파일을 업데이트 합니다. 단계를 수행 합니다 [Azure IoT Edge 보안 디먼을 구성](#configure-the-azure-iot-edge-security-daemon) 장치를 프로 비전을 완료할 섹션. 

## <a name="install-a-specific-version"></a>특정 버전 설치

Azure IoT Edge의 특정 버전을 설치 하려는 경우에 IoT Edge GitHub 리포지토리에서 직접 구성 요소 파일을 대상 수 있습니다. 모든 IoT Edge 장치에 구성 요소를 가져오려면 다음 단계를 사용 하 여: 모 비 엔진 및 CLI, libiothsm, 및 마지막으로 IoT Edge 보안 디먼입니다.

1. 이동할를 [Azure IoT Edge를 해제](https://github.com/Azure/azure-iotedge/releases)를 대상으로 하려는 릴리스 버전을 찾습니다. 

2. 확장 된 **자산** 해당 버전에 대 한 섹션입니다.

3. 수도 있고 지정 된 모든 릴리스에서 모 비 엔진에는 업데이트 되지 않을 수 있습니다. 로 시작 하는 파일을 표시 하는 경우 **모 비 엔진** 하 고 **모 비-cli**, 다음 명령을 사용 하 여 해당 구성 요소를 업데이트 합니다. 모 비 파일이 표시 되지 않는 모 비 장치에 설치 하지 않은 경우 다시 실행 하 여 이전 릴리스 자산을 찾습니다. 

   1. 찾을 합니다 **모 비 엔진** IoT Edge 장치의 아키텍처와 일치 하는 파일입니다. 파일 링크 단추로 클릭 하 고 링크 주소를 복사 합니다.

   2. 모 비 엔진의 해당 버전을 설치 하려면 다음 명령을 복사한 링크를 사용 합니다. 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. 찾을 합니다 **모 비-cli** IoT Edge 장치의 아키텍처와 일치 하는 파일입니다. 모 비 CLI는 선택적 구성 요소인 이지만 개발 중에 유용할 수 있습니다. 파일 링크 단추로 클릭 하 고 링크 주소를 복사 합니다. 

   4. 모 비 CLI의 버전을 설치 하려면 다음 명령을 복사한 링크를 사용 합니다. 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. 모든 릴리스는 hsmlib IoT Edge 보안 디먼에 새 파일이 있어야 합니다. 다음 명령을 사용 하 여 해당 구성 요소를 업데이트 합니다. 

   1. 찾을 합니다 **libiothsm-std** IoT Edge 장치의 아키텍처와 일치 하는 파일입니다. 파일 링크 단추로 클릭 하 고 링크 주소를 복사 합니다. 

   2. hsmlib의 해당 버전을 설치 하려면 다음 명령을 복사한 링크를 사용 합니다.

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. 찾을 합니다 **iotedge** IoT Edge 장치의 아키텍처와 일치 하는 파일입니다. 파일 링크 단추로 클릭 하 고 링크 주소를 복사 합니다. 

   4. 다음 명령에서 복사한 링크를 사용 하 여 IoT Edge 보안 디먼의 해당 버전을 설치 합니다. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

IoT Edge 성공적으로 설치 되 면 출력 하 라는 메시지가 나타납니다 구성 파일을 업데이트 합니다. 장치를 프로 비전을 완료 하려면 다음 섹션의 단계를 수행 합니다. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 구성

물리적 디바이스를 Azure IoT Hub에 있는 디바이스 ID와 연결하도록 IoT Edge 런타임을 구성합니다.

디먼은 `/etc/iotedge/config.yaml`에 있는 구성 파일을 사용하여 구성할 수 있습니다. 이 파일은 기본적으로 쓰기 금지되어 있습니다 편집하려면 관리자 권한이 필요합니다.

IoT Hub에서 제공하는 디바이스 연결 문자열을 사용하여 단일 IoT Edge 디바이스를 수동으로 프로비전할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 디바이스를 프로비전할 수 있습니다. 이는 많은 디바이스를 프로비전할 때 유용합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다.

### <a name="option-1-manual-provisioning"></a>옵션 1: 수동 프로비전

디바이스를 수동으로 프로비전하려면 IoT Hub에 새 디바이스를 등록하여 만들 수 있는 [디바이스 연결 문자열](how-to-register-device-portal.md)을 디바이스에 프로비전해야 합니다.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로 비전 섹션을 찾습니다. 주석 처리를 제거 합니다 **수동** 모드 프로 비전 및 프로 비전 모드 dps 주석 처리 되어 있는지 확인 합니다. **device_connection_string**의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>옵션 2: 자동 프로비전

디바이스를 자동으로 프로비전하려면 [Device Provisioning Service를 설정하고 디바이스 등록 ID를 검색](how-to-auto-provision-simulated-device-linux.md)합니다. 자동 프로비전은 TPM(신뢰할 수 있는 플랫폼 모듈) 칩이 있는 디바이스에서만 작동합니다. 예를 들어 Raspberry Pi 디바이스는 기본적으로 TPM을 제공하지 않습니다.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로 비전 섹션을 찾습니다. 주석 처리를 제거 합니다 **dps** 모드 및 수동 섹션을 주석 프로 비전 합니다. **scope_id** 및 **registration_id** 값을 IoT Hub Device Provisioning Service 및 TPM이 있는 IoT Edge 디바이스의 값으로 업데이트합니다.

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

이전 섹션의 **수동 구성** 단계를 사용한 경우 IoT Edge 런타임을 디바이스에 성공적으로 프로비전하고 실행해야 합니다. **자동 구성** 단계를 사용한 경우 사용자를 대신하여 런타임에서 디바이스를 IoT 허브에 등록할 수 있도록 몇 가지 추가 단계를 수행해야 합니다. 다음 단계를 참조 하세요. [만들기 및 Linux 가상 컴퓨터에서 시뮬레이션된 된 TPM IoT Edge 장치를 프로 비전](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)합니다.

다음을 사용하여 IoT Edge 디먼의 상태를 확인할 수 있습니다.

```bash
systemctl status iotedge
```

다음을 사용하여 디먼 로그를 검사합니다.

```bash
journalctl -u iotedge --no-pager --no-full
```

다음을 사용하여 실행 중인 모듈을 나열합니다.

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>팁 및 제안 사항

`iotedge` 명령을 실행하려면 상승된 권한이 필요합니다. 런타임을 설치한 후 머신에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다. 그 전까지는 `iotedge` 명령 앞에 **sudo**를 사용합니다.

리소스가 제한된 디바이스에서는 [문제 해결 가이드](troubleshoot.md)의 지침에 따라 *OptimizeForPerformance* 환경 변수를 *false*로 설정하는 것이 좋습니다.

프록시 서버가 있는 네트워크의 경우 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)의 단계를 수행합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

Linux 디바이스에서 IoT Edge 설치를 제거하려면 명령줄에서 다음 명령을 사용합니다.

IoT Edge 런타임을 제거합니다.

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge 런타임을 제거하면 만든 컨테이너는 중지되지만 디바이스에는 계속 남아 있습니다. 모든 컨테이너를 보고 남아 있는 항목을 확인합니다.

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

런타임을 설치하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge 런타임이 제대로 설치를 사용 하 여 문제가 경우 체크 아웃 합니다 [문제 해결](troubleshoot.md) 페이지입니다.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
