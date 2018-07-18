---
title: Linux에 Azure IoT Edge를 설치하는 방법 | Microsoft Docs
description: ARM32의 Linux에 Azure IoT Edge 설치 지침
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062601"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Linux(ARM32v7/armhf)에 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 세 가지 구성 요소가 있습니다. **IoT Edge 보안 디먼**은 Edge 장치에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, IoT Edge 에이전트를 시작하여 장치를 부트스트랩합니다. **IoT Edge 에이전트**는 IoT Edge 허브를 포함하여 IoT Edge 장치에서 모듈을 쉽게 배포하고 모니터링할 수 있게 합니다. **IoT Edge 허브**는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다.

이 문서에서는 Linux ARM32v7/armhf Edge 장치(예: Raspberry Pi)에 Azure IoT Edge 런타임을 설치하는 단계가 나열합니다.

>[!NOTE]
>Linux 소프트웨어 저장소의 패키지는 각 패키지에 있는 사용 조건에 따릅니다(/usr/share/doc/*package-name*). 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="install-the-container-runtime"></a>컨테이너 런타임 설치

Azure IoT Edge는 [OCI 호환][lnk-oci] 컨테이너 런타임(예: Docker)을 사용합니다. 에지 장치에 Docker CE/EE를 설치한 경우 Azure IoT Edge에서 개발하고 테스트하는 데 계속 사용할 수 있습니다. 

프로덕션 시나리오의 경우 아래에서 제공된 [Moby-based][lnk-moby] 엔진을 사용하는 것이 좋습니다. Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 완전히 호환 가능합니다.

아래 명령은 Moby 엔진 및 CLI(명령줄 인터페이스)를 모두 설치합니다. CLI는 개발에 유용하지만 프로덕션 배포에서는 선택 사항입니다.

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>IoT Edge 보안 디먼 설치

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 구성

`/etc/iotedge/config.yaml`의 구성 파일을 사용하여 디먼을 구성할 수 있습니다. <!--[automatically via Device Provisioning Service][lnk-dps] or--> [장치 연결 문자열][lnk-dcs]을 사용하여 수동으로 에지 장치를 구성할 수 있습니다.

수동 구성의 경우 **config.yaml**의 **프로비전** 섹션에 장치 연결 문자열을 입력합니다.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*파일은 기본적으로 쓰기 금지입니다. 편집하려면 `sudo`를 사용해야 합니다. 예: `sudo nano /etc/iotedge/config.yaml`*

구성에서 프로비전 정보를 입력한 후에 디먼을 다시 시작합니다.

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

다음을 사용하여 IoT Edge 디먼의 상태를 확인할 수 있습니다.

```cmd/sh
systemctl status iotedge
```

다음을 사용하여 디먼 로그를 검사합니다.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

다음을 사용하여 실행 중인 모듈을 나열합니다.

```cmd/sh
iotedge list
```

## <a name="next-steps"></a>다음 단계

Edge 런타임을 제대로 설치하는 데 문제가 있는 경우, [문제 해결][lnk-trouble] 페이지를 확인하세요.

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md