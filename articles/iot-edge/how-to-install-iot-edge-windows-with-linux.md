---
title: Linux 컨테이너를 사용하여 Windows에 Azure IoT Edge를 설치하는 방법 | Microsoft Docs
description: Linux 컨테이너를 사용하여 Windows에 Azure IoT Edge 설치 지침
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 2ff7c3482100545c476040ba556d464b9f44e434
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031121"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Linux 컨테이너에서 사용하기 위해 Windows에 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 장치를 IoT Edge 장치로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 장치 또는 산업용 서버처럼 큰 장치에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 장치를 구성하면 클라우드에서 장치에 비즈니스 논리를 배포할 수 있습니다. 

IoT Edge 런타임의 작동 방식 및 포함되는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에는 Windows x64(AMD/Intel) 시스템에 Linux 컨테이너가 포함된 Azure IoT Edge 런타임을 설치하는 단계가 나와 있습니다. Windows 지원은 현재 미리 보기로 제공되고 있습니다.

>[!NOTE]
Windows 시스템에서 Linux 컨테이너를 사용하는 것은 Azure IoT Edge에 대해 권장되거나 지원되는 프로덕션 구성이 아닙니다. 그러나 개발 및 테스트 용도로만 사용할 수 있습니다.

## <a name="supported-windows-versions"></a>지원되는 Windows 버전
Azure IoT Edge는 Linux 컨테이너를 사용할 때 Windows의 다음 버전에서 개발 및 테스트용으로 사용할 수 있습니다.
  * Windows 10 이상 데스크톱 운영 체제
  * Windows Server 2016 이상 서버 운영 체제

현재 지원되는 운영 체제에 대한 자세한 내용은 [Azure IoT Edge 지원](support.md#operating-systems)을 참조하세요. 

## <a name="install-the-container-runtime"></a>컨테이너 런타임 설치 

Azure IoT Edge는 [OCI 호환][lnk-oci] 컨테이너 런타임(예: Docker)을 사용합니다. 

개발 및 테스트에는 [Windows용 Docker][lnk-docker-for-windows]를 사용할 수 있습니다. Windows용 Docker가 [Linux 컨테이너를 사용하도록][lnk-docker-config] 구성합니다.

## <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 설치

>[!NOTE]
>Azure IoT Edge 소프트웨어 패키지에는 패키지(LICENSE 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보시기 바랍니다. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

IoT Hub에서 제공하는 장치 연결 문자열을 사용하여 단일 IoT Edge 장치를 수동으로 프로비전할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 장치를 프로비전할 수 있습니다. 이는 많은 장치를 프로비전할 때 유용합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다. 

### <a name="option-1-install-and-manually-provision"></a>옵션 1: 설치 및 수동으로 프로비전

1. [새 Azure IoT Edge 장치 등록][lnk-dcs]의 단계를 따라 장치를 등록하고 장치 연결 문자열을 검색합니다. 

2. IoT Edge 장치에서 관리자 권한으로 PowerShell을 실행합니다. 

3. IoT Edge 런타임을 다운로드 및 설치합니다. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. **DeviceConnectionString**을 요청하는 메시지가 표시되면 IoT Hub에서 검색한 연결 문자열을 입력합니다. 연결 문자열 옆에 따옴표를 포함하지 마세요. 

### <a name="option-2-install-and-automatically-provision"></a>옵션 2: 설치 및 자동으로 프로비전

1. [Windows에서 시뮬레이션된 TPM 에지 장치 만들기 및 프로비전][lnk-dps]의 단계를 따라 Device Provisioning Service를 설정하고 해당 **범위 ID**를 검색하고, TPM 장치를 시뮬레이션하고 해당 **등록 ID**를 검색한 다음, 개별 등록을 만듭니다. 장치가 IoT Hub에 등록되면 설치를 계속합니다.  

   >[!TIP]
   >설치 및 테스트를 수행하는 동안 TPM 시뮬레이터를 실행하는 창을 열린 상태로 유지합니다. 

2. IoT Edge 장치에서 관리자 권한으로 PowerShell을 실행합니다. 

3. IoT Edge 런타임을 다운로드 및 설치합니다. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Linux
   ```

4. 메시지가 표시되면 프로비저닝 서비스 및 장치에 대한 **ScopeID** 및 **RegistrationID**를 제공합니다.

## <a name="verify-successful-installation"></a>성공적인 설치 확인

다음과 같은 방법으로 IoT Edge 서비스의 상태를 확인할 수 있습니다. 

```powershell
Get-Service iotedge
```

다음을 사용하여 최근 5분간의 서비스 로그를 검사합니다.

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

다음을 사용하여 실행 중인 모듈을 나열합니다.

```powershell
iotedge list
```

## <a name="tips-and-suggestions"></a>팁 및 제안 사항

네트워크에 프록시 서버가 있는 경우 [프록시 서버를 통해 통신하도록 IoT Edge 장치 구성](how-to-configure-proxy-support.md)의 단계를 수행하여 IoT Edge 런타임을 설치하고 시작합니다.

## <a name="next-steps"></a>다음 단계

설치된 런타임을 사용하여 IoT Edge 장치를 프로비전했으므로 [IoT Edge 모듈을 배포][lnk-modules]할 수 있습니다.

Edge 런타임을 제대로 설치하는 데 문제가 있는 경우, [문제 해결][lnk-trouble] 페이지를 확인하세요.


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-modules]: how-to-deploy-modules-portal.md
