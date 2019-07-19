---
title: Linux ARM32에 Azure IoT Edge 설치 | Microsoft Docs
description: Raspberry PI 같은 ARM32 디바이스의 Linux에 Azure IoT Edge를 설치하는 방법에 대한 지침
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.openlocfilehash: 72349597a2831a64cb11ff66b30e6948f34cc921
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224705"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Linux(ARM32v7/armhf)에 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 

IoT Edge 런타임의 작동 방식 및 포함되는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에는 Linux ARM32v7/armhf IoT Edge 장치에 Azure IoT Edge 런타임을 설치 하는 단계가 나와 있습니다. 예를 들어 다음 단계는 Raspberry Pi 디바이스에서 작동합니다. 지원 되는 ARM32 운영 체제 목록은 [Azure IoT Edge 지원 되는 시스템](support.md#operating-systems)을 참조 하세요. 

>[!NOTE]
>Linux 소프트웨어 저장소의 패키지는 각 패키지에 있는 사용 조건에 따릅니다(/usr/share/doc/*package-name*). 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="install-the-latest-version"></a>최신 버전 설치

다음 섹션을 사용 하 여 Linux ARM 장치에 최신 버전의 Azure IoT Edge 서비스를 설치 합니다. 

### <a name="install-the-container-runtime"></a>컨테이너 런타임 설치

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 아래에 제공된 [Moby 기반](https://mobyproject.org/) 엔진을 사용하는 것이 좋습니다. Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 기반 런타임과 호환 가능합니다.

다음 명령은 Moby 기반 엔진과 CLI (명령줄 인터페이스)를 모두 설치 합니다. CLI는 개발에 유용하지만 프로덕션 배포에서는 선택 사항입니다.

```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f
```

### <a name="install-the-iot-edge-security-daemon"></a>IoT Edge 보안 디먼 설치

**IoT Edge 보안 디먼** 은 IoT Edge 장치에서 보안 표준을 제공 하 고 유지 관리 합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

IoT Edge 성공적으로 설치 되 면 구성 파일을 업데이트 하 라는 메시지가 출력에 표시 됩니다. [Azure IoT Edge 보안 디먼 구성](#configure-the-azure-iot-edge-security-daemon) 섹션의 단계에 따라 장치 프로 비전을 완료 합니다. 

## <a name="install-a-specific-version"></a>특정 버전 설치

Azure IoT Edge의 특정 버전을 설치 하려면 IoT Edge GitHub 리포지토리에서 직접 구성 요소 파일을 대상으로 지정할 수 있습니다. 이전 섹션에 `curl` 나열 된 것과 동일한 명령을 사용 하 여 IoT Edge 모든 구성 요소를 장치에 가져옵니다. Moby 엔진과 CLI, libiothsm 및 IoT Edge security daemon. 유일한 차이점은 **Aka.ms** url을 사용 하려는 각 구성 요소의 버전을 직접 가리키는 링크로 바꾸는 것입니다.

[Azure IoT Edge 릴리스로](https://github.com/Azure/azure-iotedge/releases)이동 하 고 대상으로 지정할 릴리스 버전을 찾습니다. 버전에 대 한 **자산** 섹션을 확장 하 고 IoT Edge 장치의 아키텍처와 일치 하는 파일을 선택 합니다. 모든 IoT Edge 릴리스에는 **iotedge** 및 **libiothsm** 파일이 포함 되어 있습니다. 모든 릴리스에는 **moby-엔진** 또는 **moby cli가**포함 되어 있지 않습니다. Moby 컨테이너 엔진이 아직 설치 되어 있지 않은 경우 Moby 구성 요소가 포함 된 릴리스를 찾을 때까지 이전 릴리스를 확인 합니다. 

IoT Edge 성공적으로 설치 되 면 구성 파일을 업데이트 하 라는 메시지가 출력에 표시 됩니다. 다음 섹션의 단계에 따라 장치 프로 비전을 완료 합니다. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 구성

물리적 디바이스를 Azure IoT Hub에 있는 디바이스 ID와 연결하도록 IoT Edge 런타임을 구성합니다. 

디먼은 `/etc/iotedge/config.yaml`에 있는 구성 파일을 사용하여 구성할 수 있습니다. 파일은 기본적으로 쓰기 금지 되어 있으므로이 파일을 편집 하려면 관리자 권한이 필요할 수 있습니다.

IoT Hub에서 제공하는 디바이스 연결 문자열을 사용하여 단일 IoT Edge 디바이스를 수동으로 프로비전할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 디바이스를 프로비전할 수 있습니다. 이는 많은 디바이스를 프로비전할 때 유용합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다. 

### <a name="option-1-manual-provisioning"></a>옵션 1: 수동 프로비전

수동으로 장치를 프로 비전 하려면 IoT hub에 새 IoT Edge 장치를 등록 하 여 만들 수 있는 [장치 연결 문자열](how-to-register-device-portal.md) 을 제공 해야 합니다.

구성 파일을 엽니다. 

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로 비전 구성을 찾고 **수동 프로 비전 구성** 섹션의 주석 처리를 제거 합니다. **device_connection_string**의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다. 다른 프로 비전 섹션이 주석 처리 되었는지 확인 합니다.

```yaml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# DPS TPM provisioning configuration
# provisioning:
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   attestation:
#     method: "tpm"
#     registration_id: "{registration_id}"
```

파일을 저장한 후 닫습니다. 

`CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>옵션 2: 자동 프로비전

디바이스를 자동으로 프로비전하려면 [Device Provisioning Service를 설정하고 디바이스 등록 ID를 검색](how-to-auto-provision-simulated-device-linux.md)합니다. 자동 프로 비전을 사용 하는 경우 IoT Edge에서 지 원하는 다양 한 증명 메커니즘이 있지만 사용자의 하드웨어 요구 사항도 선택에 영향을 줍니다. 예를 들어 Raspberry Pi 장치는 기본적으로 TPM (신뢰할 수 있는 플랫폼 모듈) 칩과 함께 제공 되지 않습니다.

구성 파일을 엽니다. 

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로 비전 구성을 찾고 증명 메커니즘에 적합 한 섹션의 주석 처리를 제거 합니다. 예를 들어 TPM 증명을 사용 하는 경우 **scope_id** 및 **registration_id** 의 값을 IoT Hub Device Provisioning 서비스의 값과 TPM을 사용 하는 IoT Edge 장치의 값으로 업데이트 합니다.

```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
```

파일을 저장한 후 닫습니다. 

`CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

이전 섹션의 **수동 구성** 단계를 사용한 경우 IoT Edge 런타임을 디바이스에 성공적으로 프로비전하고 실행해야 합니다. 또는 **자동 구성** 단계를 사용한 경우 런타임이 사용자를 대신해 IoT hub에 장치를 등록할 수 있도록 몇 가지 추가 단계를 완료 해야 합니다. 다음 단계는 [Linux 가상 머신에서 시뮬레이션 된 TPM IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)을 참조 하세요.

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

리소스가 제한된 디바이스에서는 [문제 해결 가이드](troubleshoot.md#stability-issues-on-resource-constrained-devices)의 지침에 따라 *OptimizeForPerformance* 환경 변수를 *false*로 설정하는 것이 좋습니다.

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

IoT Edge 런타임에 제대로 설치 하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md#stability-issues-on-resource-constrained-devices) 페이지를 참조 하세요.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
