---
title: 윈도우에 리눅스에 대 한 Azure IoT 가장자리를 설치 | 마이크로 소프트 문서
description: 윈도우에 리눅스 컨테이너에 대 한 Azure IoT 가장자리 설치 지침 10, 윈도우 서버, 그리고 윈도우 IoT 코어
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133171"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Windows에서 IoT Edge를 사용하여 Linux 컨테이너를 실행

Windows 컴퓨터를 사용하여 Linux 장치에 대한 IoT Edge 모듈을 테스트합니다.

프로덕션 시나리오에서 Windows 장치는 Windows 컨테이너만 실행해야 합니다. 그러나 일반적인 개발 시나리오는 Windows 컴퓨터를 사용하여 Linux 장치에 대한 IoT Edge 모듈을 빌드하는 것입니다. Windows용 IoT Edge 런타임을 사용하면 **테스트 및 개발** 목적으로 Linux 컨테이너를 실행할 수 있습니다.

이 문서에서는 Windows x64(AMD/인텔) 시스템의 Linux 컨테이너를 사용하여 Azure IoT Edge 런타임을 설치하는 단계를 나열합니다. 모든 설치 매개 변수에 대한 세부 정보를 포함하여 IoT Edge 런타임 설치 관리자에 대해 자세히 알아보려면 [Windows에서 Azure IoT Edge 런타임 설치를](how-to-install-iot-edge-windows.md)참조하십시오.

최신 버전의 IoT Edge에 포함된 내용에 대한 자세한 내용은 [Azure IoT Edge 릴리스를](https://github.com/Azure/azure-iotedge/releases)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

이 섹션을 사용하여 Windows 디바이스에서 IoT Edge를 지원할 수 있는지 여부를 검토하고 설치 전에 컨테이너 엔진에 대해 준비합니다.

### <a name="supported-windows-versions"></a>지원되는 Windows 버전

Linux 컨테이너를 갖춘 Azure IoT Edge는 Docker [데스크톱에 대한 요구 사항을](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) 충족하는 모든 버전의 Windows에서 실행할 수 있습니다.

가상 시스템에 IoT Edge를 설치하려면 중첩가상화를 사용하도록 설정하고 최소 2GB 메모리를 할당합니다. 중첩 가상화를 활성화하는 방법은 사용하는 하이퍼바이저에 따라 다릅니다. Hyper-V의 경우 2세대 가상 머신은 기본적으로 중첩된 가상화를 사용하도록 설정했습니다. VMWare의 경우 가상 컴퓨터에서 기능을 활성화하는 토글이 있습니다.

### <a name="prepare-the-container-engine"></a>컨테이너 엔진 준비

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 엔진을 사용합니다. Windows 컴퓨터에서 Windows 및 Linux 컨테이너를 실행하는 가장 큰 구성 차이점은 IoT Edge 설치에 Windows 컨테이너 런타임이 포함되어 있지만 IoT Edge를 설치하기 전에 Linux 컨테이너에 대한 자체 런타임을 제공해야 한다는 것입니다.

Linux 장치에 대한 컨테이너를 개발하고 테스트하기 위해 Windows 컴퓨터를 설정하려면 [Docker Desktop을](https://www.docker.com/docker-windows) 컨테이너 엔진으로 사용할 수 있습니다. IoT Edge를 설치하기 전에 Docker를 설치하고 [Linux 컨테이너를 사용하도록](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) 구성해야 합니다.  

IoT Edge 디바이스가 Windows 컴퓨터인 경우 Hyper-V에 대한 [시스템 요구 사항](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)을 충족하는지 확인합니다.

## <a name="install-iot-edge-on-a-new-device"></a>새 디바이스에 IoT Edge 설치

>[!NOTE]
>Azure IoT Edge 소프트웨어 패키지에는 패키지(LICENSE 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보시기 바랍니다. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

PowerShell 스크립트가 Azure IoT Edge 보안 디먼을 다운로드하여 설치합니다. 그런 다음, 보안 디먼이 다른 모듈의 원격 배포를 가능하게 하는 두 가지 런타임 모듈 중 첫 번째인 IoT Edge 에이전트를 시작합니다.

디바이스에 IoT Edge 런타임을 처음 설치하는 경우 IoT 허브의 ID를 사용하여 디바이스를 프로비전해야 합니다. 단일 IoT Edge 장치는 IoT 허브에서 제공하는 장치 연결 문자열을 사용하여 수동으로 프로비전할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 디바이스를 프로비전할 수 있습니다. 이 기능은 많은 디바이스를 설정해야 하는 경우에 유용합니다.

Windows에서 [Azure IoT Edge 런타임](how-to-install-iot-edge-windows.md)설치 문서에서 다양한 설치 옵션 및 매개 변수에 대해 자세히 확인할 수 있습니다. Docker Desktop을 설치하고 Linux 컨테이너용으로 구성하면 주요 설치 차이점은 **-ContainerOs** 매개 변수를 사용하여 Linux를 선언하는 것입니다. 예를 들어:

1. 아직 등록하지 않은 경우 새 IoT Edge 장치를 등록하고 장치 연결 문자열을 검색합니다. 이 섹션의 후반부에서 사용할 연결 문자열을 복사합니다. 다음 도구를 사용하여 이 단계를 완료할 수 있습니다.

   * [Azure 포털](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [비주얼 스튜디오 코드](how-to-register-device.md#register-with-visual-studio-code)

2. PowerShell을 관리자 권한으로 실행합니다.

   >[!NOTE]
   >PowerShell(x86)이 아닌 IoT Edge를 설치하려면 PowerShell의 AMD64 세션을 사용합니다. 사용 중인 세션 형식을 잘 모르는 경우 다음 명령을 실행합니다.
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **Deploy-IoTEdge** 명령은 Windows 컴퓨터가 지원되는 버전에 있는지 확인하고 컨테이너 기능을 켜고 모비 런타임(Linux 컨테이너에 사용되지 않음)과 IoT Edge 런타임을 다운로드합니다. 명령은 Windows 컨테이너를 기본값으로 설정하므로 Linux를 원하는 컨테이너 운영 체제로 선언합니다.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. 이 시점에서 IoT Core 장치가 자동으로 다시 시작될 수 있습니다. 다른 Windows 10 또는 Windows 서버 장치에서 다시 시작하라는 메시지가 표시될 수 있습니다. 그렇다면 지금 기기를 다시 시작하십시오. 장치가 준비되면 PowerShell을 관리자로 다시 실행합니다.

5. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 명령은 기본적으로 장치 연결 문자열을 사용하여 수동 프로비저닝을 설정합니다. Linux를 원하는 컨테이너 운영 체제로 다시 선언합니다.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. 메시지가 표시되면 1단계에서 검색한 장치 연결 문자열을 제공합니다. 장치 연결 문자열은 물리적 장치를 IoT Hub의 장치 ID와 연결합니다.

   장치 연결 문자열은 다음과 같은 형식을 취하며 따옴표를 포함해서는 안 됩니다.`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>성공적인 설치 확인

IoT 에지 서비스의 상태를 확인합니다.

```powershell
Get-Service iotedge
```

지난 5분 동안의 서비스 로그를 검사합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

가장 일반적인 구성 및 네트워킹 오류에 대한 자동 검사를 실행합니다.

```powershell
iotedge check
```

실행 중인 모듈을 나열합니다. 새 설치 후 실행 중인 유일한 모듈은 **edgeAgent**. [IoT Edge 모듈을](how-to-deploy-modules-portal.md) 처음으로 배포한 후 다른 시스템 모듈인 **edgeHub도**장치에서 시작됩니다.

```powershell
iotedge list
```

## <a name="next-steps"></a>다음 단계

설치된 런타임을 사용하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge를 제대로 설치하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인합니다.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
