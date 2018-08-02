---
title: Windows 컨테이너를 사용하여 Windows에 Azure IoT Edge를 설치하는 방법 | Microsoft Docs
description: Windows 컨테이너를 사용하여 Windows에 Azure IoT Edge 설치 지침
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 5fc1163f590b2408fca913e35e57f014424b225c
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308401"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Windows 컨테이너에서 사용하기 위해 Windows에 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 세 가지 구성 요소가 있습니다. **IoT Edge 보안 디먼**은 Edge 장치에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, IoT Edge 에이전트를 시작하여 장치를 부트스트랩합니다. **IoT Edge 에이전트**는 IoT Edge 허브를 포함하여 IoT Edge 장치에서 모듈을 쉽게 배포하고 모니터링할 수 있게 합니다. **IoT Edge 허브**는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다.

이 문서에는 Windows x64(AMD/Intel) 시스템에 Azure IoT Edge 런타임을 설치하는 단계가 나와 있습니다. 

Windows 지원은 현재 미리 보기로 제공되고 있습니다.

## <a name="supported-windows-versions"></a>지원되는 Windows 버전
Azure IoT Edge 및 Windows 컨테이너는 다음에서 사용할 수 있습니다.
  * Windows 10/IoT Enterprise/IoT Core 2018년 4월 업데이트(빌드 17134)
  * Windows Server 1803

## <a name="install-the-container-runtime"></a>컨테이너 런타임 설치 

>[!NOTE]
>Windows IoT Core에서 컨테이너 엔진 설치의 경우, [IoT Core 장치 프로비전 문서][lnk-iot-core]의 단계를 따른 다음, 아래 지침을 계속 진행합니다.

Azure IoT Edge는 [OCI 호환][lnk-oci] 컨테이너 런타임(예: Docker)을 사용합니다. 개발 및 테스트에는 [Windows용 Docker][lnk-docker-for-windows]를 사용할 수 있습니다. 

**Windows용 Docker가 [Windows 컨테이너를 사용하도록 구성][lnk-docker-config]되어 있는지 확인합니다.**

## <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 설치

>[!NOTE]
>Azure IoT Edge 소프트웨어 패키지에는 패키지(LICENSE 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보시기 바랍니다. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

### <a name="download-the-edge-daemon-package-and-install"></a>Edge 디먼 패키지 다운로드 및 설치

관리자 PowerShell 창에서 다음 명령을 실행합니다.

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
$path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
Set-ItemProperty -Path $sysenv -Name Path -Value $path
```

다음을 사용하여 vcruntime을 설치합니다. IoT Core Edge 장치에서는 이 단계를 건너뛰어도 됩니다.

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

**iotedge** 서비스를 만들고 시작합니다.

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

서비스가 사용하는 포트에 대해 방화벽 예외를 추가합니다.

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

다음 콘텐츠를 사용하여 **iotedge.reg** 파일을 만들고, 파일을 두 번 클릭하거나 `reg import iotedge.reg` 명령을 사용하여 Windows 레지스트리로 가져옵니다.

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 구성

디먼은 `C:\ProgramData\iotedge\config.yaml`에 있는 구성 파일을 사용하여 구성할 수 있습니다.

에지 장치는 [장치 연결 문자열을 사용하여 수동으로][lnk-dcs] 또는 [Device Provisioning Service를 통해 자동으로][lnk-dps] 구성할 수 있습니다.

* 수동 구성의 경우, **수동** 프로비전 모드의 주석 처리를 제거합니다. **device_connection_string**의 값을 IoT Edge 장치의 연결 문자열로 업데이트합니다.

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

* 자동 구성의 경우 **dps** 프로비전 모드의 주석 처리를 제거합니다. **scope_id** 및 **registration_id**의 값을 IoT Hub DPS 인스턴스 및 TPM이 있는 IoT Edge 장치의 값으로 업데이트합니다. 

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

PowerShell에서 `hostname` 명령을 사용하여 에지 장치의 이름을 가져온 다음, 구성 yaml에서 **hostname:** 의 값으로 설정합니다. 예: 

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

그런 다음, 구성의 **connect:** 및 **listen:** 섹션에서 **workload_uri** 및 **management_uri**에 대한 IP 주소와 포트를 제공합니다.

IP 주소를 검색하려면 PowerShell 창에서 `ipconfig`를 입력하고, 다음 예제와 같이 **vEthernet(nat)** 인터페이스의 IP 주소를 복사합니다(시스템의 IP 주소가 다를 수 있음).  

![nat][img-nat]

구성 파일의 **connect:** 섹션에서 **workload_uri** 및 **management_uri**를 업데이트합니다. **\<GATEWAY_ADDRESS\>** 를 복사한 vEthernet IP 주소로 바꿉니다.

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

**listen:** 섹션에 동일한 주소를 입력합니다.

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

PowerShell 창에서 **management_uri** 주소를 사용하여 **IOTEDGE_HOST** 환경 변수를 만듭니다.

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

다시 부팅 사이에 환경 변수를 그대로 유지합니다.

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

마지막으로, **moby_runtime:** 아래에서 **network:** 설정의 주석 처리가 제거되고 **nat**로 설정되었는지 확인합니다.

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "nat"
```

구성 파일을 저장하고 서비스를 다시 시작합니다.

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

이전 섹션의 **수동 구성** 단계를 사용한 경우 IoT Edge 런타임을 장치에 성공적으로 프로비전하고 실행해야 합니다. **자동 구성** 단계를 사용한 경우 사용자를 대신하여 런타임에서 장치를 IoT 허브에 등록할 수 있도록 몇 가지 추가 단계를 수행해야 합니다. 다음 단계는 [Windows에서 시뮬레이션된 TPM 에지 장치 만들기 및 프로비전](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable)을 참조하세요.

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

## <a name="next-steps"></a>다음 단계

Edge 런타임을 제대로 설치하는 데 문제가 있는 경우, [문제 해결][lnk-trouble] 페이지를 확인하세요.


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md

