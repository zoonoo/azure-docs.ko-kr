---
title: Windows에 Azure IoT Edge 설치 | Microsoft Docs
description: Windows 10, Windows Server 및 Windows IoT Core에 대한 Azure IoT Edge 설치 지침
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: kgremban
ms.openlocfilehash: 80ce962ac6977fcce2455c8e2ef29af448a44075
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133154"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Windows에 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

IoT Edge 런타임에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에서는 Windows 컨테이너를 사용하여 Windows x64(AMD/인텔) 시스템에 Azure IoT Edge 런타임을 설치하는 단계를 나열합니다.

> [!NOTE]
> 알려진 Windows 운영 체제 문제는 IoT Edge 모듈(프로세스 격리된 Windows Nano Server 컨테이너)이 실행 중인 경우 절전 모드로 전환및 최대 절전 모드 전원 상태로 전환하는 것을 방지합니다. 이 문제는 장치의 배터리 수명에 영향을 미칩니다.
>
> 해결 해결 방법을 사용 `Stop-Service iotedge` 하 여 이러한 전원 상태를 사용 하기 전에 실행 중인 IoT Edge 모듈을 중지 하려면 명령을 사용 합니다.

Windows 시스템에서 Linux 컨테이너를 사용하는 것은 Azure IoT Edge에 추천되거나 지원되는 프로덕션 구성이 아닙니다. 그러나 개발 및 테스트 용도로만 사용할 수 있습니다. 자세한 내용은 [Windows에서 IoT Edge 사용을 참조하여 Linux 컨테이너를 실행합니다.](how-to-install-iot-edge-windows-with-linux.md)

최신 버전의 IoT Edge에 포함된 내용에 대한 자세한 내용은 [Azure IoT Edge 릴리스를](https://github.com/Azure/azure-iotedge/releases)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

이 섹션을 사용하여 Windows 디바이스에서 IoT Edge를 지원할 수 있는지 여부를 검토하고 설치 전에 컨테이너 엔진에 대해 준비합니다.

### <a name="supported-windows-versions"></a>지원되는 Windows 버전

윈도우용 IoT Edge는 최신 [Windows 장기 지원 빌드인](https://docs.microsoft.com/windows/release-information/)Windows 버전 1809/빌드 17763이 필요합니다. Windows SKU 지원의 경우 프로덕션 시나리오 또는 개발 및 테스트 시나리오를 준비하고 있는지 여부에 따라 지원되는 내용을 확인하십시오.

* **프로덕션**: 프로덕션 시나리오에서 현재 지원되는 운영 체제에 대한 최신 정보는 [Azure IoT Edge 지원 시스템을](support.md#operating-systems)참조하십시오.
* **개발 및 테스트**: 개발 및 테스트 시나리오의 경우 Windows 컨테이너가 있는 Azure IoT Edge를 컨테이너 기능을 지원하는 모든 버전의 Windows 10 또는 Windows Server 2019에 설치할 수 있습니다.

IoT 코어 장치에는 IoT 에지 런타임을 지원하기 위한 IoT 코어 Windows 컨테이너 옵션 기능이 포함되어야 합니다. [원격 PowerShell 세션에서](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) 다음 명령을 사용하여 장치에서 Windows 컨테이너가 지원되고 있는지 확인합니다.

```powershell
Get-Service vmcompute
```

서비스가 있는 경우 **실행 중인**것으로 나열된 서비스 상태를 통해 성공적인 응답을 받아야 합니다. `vmcompute` 서비스를 찾을 수 없는 경우 장치가 IoT Edge에 대한 요구 사항을 충족하지 않습니다. 이 기능에 대한 지원에 대해 문의하려면 하드웨어 공급자에게 문의하십시오.

### <a name="prepare-for-a-container-engine"></a>컨테이너 엔진 준비

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 엔진을 사용합니다. 프로덕션 시나리오에서는 설치 스크립트에 포함된 Moby 엔진을 사용하여 Windows 디바이스에서 Windows 컨테이너를 실행합니다.

## <a name="install-iot-edge-on-a-new-device"></a>새 디바이스에 IoT Edge 설치

>[!NOTE]
>Azure IoT Edge 소프트웨어 패키지에는 패키지(LICENSE 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보시기 바랍니다. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

PowerShell 스크립트가 Azure IoT Edge 보안 디먼을 다운로드하여 설치합니다. 그런 다음, 보안 디먼이 다른 모듈의 원격 배포를 가능하게 하는 두 가지 런타임 모듈 중 첫 번째인 IoT Edge 에이전트를 시작합니다.

>[!TIP]
>IoT Core 장치의 경우 RemotePowerShell 세션을 사용하여 설치 명령을 실행하는 것이 좋습니다. 자세한 내용은 [Windows IoT용 PowerShell 사용을](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)참조하십시오.

디바이스에 IoT Edge 런타임을 처음 설치하는 경우 IoT 허브의 ID를 사용하여 디바이스를 프로비전해야 합니다. 단일 IoT Edge 장치는 IoT Hub에서 제공하는 장치 연결 문자열을 사용하여 수동으로 프로비전할 수 있습니다. 또는 DPS(장치 프로비저닝 서비스)를 사용하여 장치를 자동으로 프로비저닝할 수 있으므로 설정할 장치가 많을 때 유용합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다.

다음 섹션에서는 새 디바이스의 IoT Edge 설치 스크립트에 대한 일반적인 사용 사례 및 매개 변수를 설명합니다.

### <a name="option-1-install-and-manually-provision"></a>옵션 1: 설치 및 수동으로 프로비전

이 첫 번째 옵션에서는 IoT Hub에서 생성된 **장치 연결 문자열을** 제공하여 장치를 프로비전합니다.

이 예제에서는 Windows 컨테이너를 사용 하 고 수동 설치를 보여 줍니다.

1. 아직 등록하지 않은 경우 새 IoT Edge 장치를 등록하고 **장치 연결 문자열을**검색합니다. 이 섹션의 후반부에서 사용할 연결 문자열을 복사합니다. 다음 도구를 사용하여 이 단계를 완료할 수 있습니다.

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

3. **Deploy-IoTEdge** 명령은 Windows 컴퓨터가 지원되는 버전에 있는지 확인하고 컨테이너 기능을 켜고 moby 런타임 및 IoT Edge 런타임을 다운로드합니다. 명령은 Windows 컨테이너를 사용하는 데 기본설정됩니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. 이 시점에서 IoT Core 장치가 자동으로 다시 시작될 수 있습니다. 다른 Windows 10 또는 Windows 서버 장치에서 다시 시작하라는 메시지가 표시될 수 있습니다. 그렇다면 지금 기기를 다시 시작하십시오. 장치가 준비되면 PowerShell을 관리자로 다시 실행합니다.

5. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 이 명령은 Windows 컨테이너를 통한 수동 프로비저닝으로 기본 설정됩니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. 메시지가 표시되면 1단계에서 검색한 장치 연결 문자열을 제공합니다. 장치 연결 문자열은 물리적 장치를 IoT Hub의 장치 ID와 연결합니다.

   장치 연결 문자열은 다음과 같은 형식을 취하며 따옴표를 포함해서는 안 됩니다.`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. 성공적인 설치 [확인의](#verify-successful-installation) 단계를 사용하여 장치에서 IoT Edge의 상태를 확인합니다.

디바이스를 수동으로 설치하고 프로비전하는 경우 추가 매개 변수를 사용하여 다음과 같이 설치를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 설치 관리자에서 오프라인 디렉터리를 확인하도록 설정
* 특정 에이전트 컨테이너 이미지를 선언하고, 프라이빗 레지스트리에 있는 경우 자격 증명 제공

이러한 설치 옵션에 대한 자세한 내용은 모든 [설치 매개 변수에](#all-installation-parameters)대해 알아보려면 미리 건너뜁니다.

### <a name="option-2-install-and-automatically-provision"></a>옵션 2: 설치 및 자동으로 프로비전

이 두 번째 옵션에서는 IoT Hub Device Provisioning Service를 사용하여 디바이스를 프로비전합니다. 장치 프로비저닝 서비스 인스턴스에서 기본 [증명 메커니즘과](../iot-dps/concepts-security.md#attestation-mechanism)관련된 기타 정보와 함께 **범위 ID를** 제공합니다.

* [Windows에서 가상 TPM을 사용하여 시뮬레이션된 IoT Edge 장치 생성 및 프로비전](how-to-auto-provision-simulated-device-windows.md)
* [X.509 인증서를 사용하여 시뮬레이션된 IoT Edge 장치 생성 및 프로비전](how-to-auto-provision-x509-certs.md)
* [대칭 키 증명을 사용하여 IoT Edge 장치 생성 및 프로비전](how-to-auto-provision-symmetric-keys.md)

장치를 자동으로 설치하고 프로비전할 때 추가 매개 변수를 사용하여 다음을 포함하여 설치를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 설치 관리자에서 오프라인 디렉터리를 확인하도록 설정
* 특정 에이전트 컨테이너 이미지를 선언하고, 프라이빗 레지스트리에 있는 경우 자격 증명 제공

이러한 설치 옵션에 대한 자세한 내용을 보려면 이 문서를 계속 읽거나 [모든 설치 매개 변수](#all-installation-parameters)에 대한 자세한 정보로 건너뛰세요.

## <a name="offline-or-specific-version-installation"></a>오프라인 또는 특정 버전 설치

설치하는 동안 두 개의 파일이 다운로드됩니다 .

* 마이크로소프트 Azure IoT 엣지 캡은 IoT 엣지 보안 데몬(iotedged), 모비 컨테이너 엔진, 모비 CLI를 포함하고 있다.
* 비주얼 C++ 재배포 가능한 패키지(VC 런타임) MSI

장치를 설치하는 동안 오프라인 상태가 되거나 특정 버전의 IoT Edge를 설치하려는 경우 이러한 파일 중 하나 또는 둘 다를 장치에 미리 다운로드할 수 있습니다. 설치할 시간이 되면 다운로드한 파일이 포함된 디렉터리의 설치 스크립트를 가리킵니다. 설치 관리자는 해당 디렉터리를 먼저 확인한 다음 찾을 수 없는 구성 요소만 다운로드합니다. 모든 파일을 오프라인으로 사용할 수 있는 경우 인터넷에 연결하지 않고 설치할 수 있습니다.

이전 버전과 함께 최신 IoT Edge 설치 파일에 대한 [Azure IoT Edge 릴리스를](https://github.com/Azure/azure-iotedge/releases)참조하십시오.

오프라인 구성 요소로 설치하려면 `-OfflineInstallationPath` 배포-IoTEdge 명령의 일부로 매개 변수를 사용하고 파일 디렉터리에 대한 절대 경로를 제공합니다. 예를 들면 다음과 같습니다.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>매개 `-OfflineInstallationPath` 변수는 제공된 디렉터리에서 **Microsoft-Azure-IoTEdge.cab라는** 파일을 찾습니다. IoT Edge 버전 1.0.9-rc4부터 AMD64 장치용 및 ARM32용 .cab 파일 2개가 있습니다. 장치에 맞는 파일을 다운로드한 다음 파일 이름을 변경하여 아키텍처 접미사를 제거합니다.

명령은 `Deploy-IoTEdge` IoT Edge 구성 요소를 설치한 다음 명령을 `Initialize-IoTEdge` 계속 하여 IoT Hub 장치 ID 및 연결로 장치를 프로비전해야 합니다. 명령을 직접 실행하고 IoT Hub에서 연결 문자열을 제공하거나 이전 섹션의 링크 중 하나를 사용하여 장치 프로비저닝 서비스를 사용하여 장치를 자동으로 프로비전하는 방법을 알아봅니다.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

Update-IoTEdge 명령을 사용하여 오프라인 설치 경로 매개 변수를 사용할 수도 있습니다.

## <a name="verify-successful-installation"></a>성공적인 설치 확인

IoT Edge 서비스의 상태를 확인합니다. 실행 중인 것으로 나열되어야 합니다.  

```powershell
Get-Service iotedge
```

최근 5분 간의 서비스 로그를 검사합니다. IoT Edge 런타임 설치를 막 마친 경우 **Deploy-IoTEdge** 및 **초기화-IoTEdge**를 실행하는 동안의 오류 목록이 표시될 수 있습니다. 서비스가 구성되기 전에 시작하려고 하므로 이러한 오류가 예상됩니다.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

가장 일반적인 구성 및 네트워킹 오류에 대한 자동 검사를 실행합니다.

```powershell
iotedge check
```

실행 중인 모듈을 나열합니다. 새 설치 후 실행 중인 유일한 모듈은 **edgeAgent**. [IoT Edge 모듈을](how-to-deploy-modules-portal.md) 처음으로 배포한 후 다른 시스템 모듈인 **edgeHub도**장치에서 시작됩니다.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>모듈 컨테이너 관리

IoT Edge 서비스에는 장치에서 실행되는 컨테이너 엔진이 필요합니다. 장치에 모듈을 배포할 때 IoT Edge 런타임은 컨테이너 엔진을 사용하여 클라우드의 레지스트리에서 컨테이너 이미지를 가져옵니다. IoT Edge 서비스를 사용하면 모듈과 상호 작용하고 로그를 검색할 수 있지만 컨테이너 엔진을 사용하여 컨테이너 자체와 상호 작용할 수 있는 경우도 있습니다.

모듈 개념에 대한 자세한 내용은 [Azure IoT Edge 모듈 이해를](iot-edge-modules.md)참조하십시오.

Windows IoT Edge 장치에서 Windows 컨테이너를 실행하는 경우 IoT Edge 설치에는 Moby 컨테이너 엔진이 포함되어 있습니다. Moby 엔진은 Docker와 동일한 표준을 기반으로 하며 Docker 데스크톱과 동일한 컴퓨터에서 병렬로 실행되도록 설계되었습니다. 따라서 Moby 엔진에서 관리하는 컨테이너를 대상으로 지정하려면 Docker 대신 해당 엔진을 구체적으로 대상으로 지정해야 합니다.

예를 들어 모든 Docker 이미지를 나열하려면 다음 명령을 사용합니다.

```powershell
docker images
```

모든 Moby 이미지를 나열하려면 Moby 엔진에 대한 포인터를 사용하여 동일한 명령을 수정합니다.

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

엔진 URI는 설치 스크립트의 출력에 나열되어 있거나 config.yaml 파일의 컨테이너 런타임 설정 섹션에서 찾을 수 있습니다.

![moby_runtime 우리 에 config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

장치에서 실행되는 컨테이너 및 이미지와 상호 작용하는 데 사용할 수 있는 명령에 대한 자세한 내용은 [Docker 명령줄 인터페이스를](https://docs.docker.com/engine/reference/commandline/docker/)참조하십시오.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

Windows 디바이스에서 IoT Edge 설치를 제거하려는 경우 관리 PowerShell 창에서 다음 명령을 사용합니다. 이 명령은 기존 구성 및 Moby 엔진 데이터와 함께 IoT Edge 런타임을 제거합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

제거-IoTEdge 명령은 Windows IoT 코어에서 작동하지 않습니다. Windows IoT Core 장치에서 IoT Edge를 제거하려면 Windows IoT Core 이미지를 다시 배포해야 합니다.

제거 옵션에 대한 자세한 내용은 `Get-Help Uninstall-IoTEdge -full`명령을 사용하십시오.

## <a name="verify-installation-script"></a>설치 스크립트 확인

이 문서에서 제공하는 설치 명령은 Invoke-WebRequest cmdlet을 사용하여 에서 `aka.ms/iotedge-win`설치 스크립트를 요청합니다. 이 링크는`IoTEdgeSecurityDaemon.ps1` 최신 [IoT Edge 릴리스의](https://github.com/Azure/azure-iotedge/releases)스크립트를 가리킵니다. 또한 이 스크립트 또는 특정 릴리스의 스크립트 버전을 다운로드하여 IoT Edge 장치에서 설치 명령을 실행할 수도 있습니다.

제공된 스크립트는 보안을 강화하기 위해 서명됩니다. 스크립트를 장치에 다운로드한 다음 다음 PowerShell 명령을 실행하여 서명을 확인할 수 있습니다.

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

서명이 확인된 경우 출력 상태가 **유효합니다.**

## <a name="all-installation-parameters"></a>모든 설치 매개 변수

이전 섹션에서는 매개 변수를 사용하여 설치 스크립트를 수정하는 방법의 예제와 함께 일반적인 설치 시나리오를 소개했습니다. 이 섹션에서는 IoT Edge를 설치, 업데이트 또는 제거하는 데 사용되는 공통 매개 변수에 대한 참조 표를 제공합니다.

### <a name="deploy-iotedge"></a>구축-IoTEdge

Deploy-IoTEdge 명령은 IoT 에지 보안 데몬과 해당 종속성을 다운로드하여 배포합니다. 배포 명령은 이러한 공통 매개 변수를 허용합니다. 전체 목록의 경우 명령을 `Get-Help Deploy-IoTEdge -full`사용합니다.  

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **ContainerOs** | **윈도우** 또는 **리눅스** | 컨테이너 운영 체제를 지정하지 않으면 Windows가 기본값입니다.<br><br>Windows 컨테이너의 경우 IoT Edge는 설치에 포함된 모비 컨테이너 엔진을 사용합니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **프록시** | 프록시 URL | 디바이스가 프록시 서버를 통해 인터넷에 연결해야 하는 경우 이 매개 변수를 포함합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **OfflineInstallationPath** | 디렉터리 경로 | 이 매개 변수가 포함된 경우 설치 관리자는 설치에 필요한 IoT Edge 캡 및 VC 런타임 MSI 파일에 대해 나열된 디렉터리를 확인합니다. 디렉터리에서 찾을 수 없는 파일은 다운로드됩니다. 두 파일이 모두 디렉터리에 있는 경우 인터넷 연결 없이 IoT Edge를 설치할 수 있습니다. 이 매개 변수를 사용하여 특정 버전을 사용할 수도 있습니다. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **다시 시작필요** | none | 이 플래그를 사용하면 필요한 경우 배포 스크립트가 프롬프트 없이 컴퓨터를 다시 시작할 수 있습니다. |

### <a name="initialize-iotedge"></a>초기화-IoTEdge

Initialize-IoTEdge 명령은 장치 연결 문자열 및 운영 세부 정보로 IoT Edge를 구성합니다. 이 명령에 의해 생성된 대부분의 정보는 iotedge\config.yaml 파일에 저장됩니다. 초기화 명령은 이러한 공통 매개 변수를 허용합니다. 전체 목록의 경우 명령을 `Get-Help Initialize-IoTEdge -full`사용합니다.

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **수동** | None | **스위치 매개 변수**. 프로비저닝 형식을 지정하지 않으면 설명서가 기본값입니다.<br><br>디바이스 연결 문자열을 제공하여 디바이스를 수동으로 프로비전할 것을 선언합니다. |
| **Dps** | None | **스위치 매개 변수**. 프로비저닝 형식을 지정하지 않으면 설명서가 기본값입니다.<br><br>DPS(Device Provisioning Service) 범위 ID 및 디바이스의 등록 ID를 제공하여 DPS를 통해 프로비전할 것을 선언합니다.  |
| **장치 연결 문자열** | IoT Hub에 등록된 IoT Edge 디바이스의 연결 문자열을 작은따옴표 안에 표시합니다. | 수동 프로비저닝에 **필요합니다.** 스크립트 매개 변수에 연결 문자열을 제공하지 않으면 하나라는 메시지가 표시됩니다. |
| **ScopeId** | IoT Hub와 연결된 Device Provisioning Service 인스턴스의 범위 ID입니다. | DPS 프로비저닝에 **필요합니다.** 스크립트 매개 변수에 범위 ID를 제공하지 않으면 해당 매개 변수에 대한 메시지가 표시됩니다. |
| **RegistrationId** | 디바이스에서 생성된 등록 ID입니다. | TPM 또는 대칭 키 증명을 사용하는 경우 DPS 프로비저닝에 **필요합니다.** X.509 인증서 증명을 사용하는 경우 **선택 사항입니다.** |
| **X509ID인증서** | 장치의 X.509 장치 ID 인증서에 대한 URI 경로입니다. | X.509 인증서 증명을 사용하는 경우 DPS 프로비저닝에 **필요합니다.** |
| **X509아이덴티티프라이빗키** | 장치의 X.509 장치 ID 인증서 키에 대한 URI 경로입니다. | X.509 인증서 증명을 사용하는 경우 DPS 프로비저닝에 **필요합니다.** |
| **SymmetricKey** | DPS를 사용할 때 IoT Edge 장치 ID를 프로비전하는 데 사용되는 대칭 키 | 대칭 키 증명을 사용하는 경우 DPS 프로비저닝에 **필요합니다.** |
| **ContainerOs** | **윈도우** 또는 **리눅스** | 컨테이너 운영 체제를 지정하지 않으면 Windows가 기본값입니다.<br><br>Windows 컨테이너의 경우 IoT Edge는 설치에 포함된 모비 컨테이너 엔진을 사용합니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **AgentImage** | IoT Edge 에이전트 이미지 URI | 기본적으로 새 IoT Edge 설치는 IoT Edge 에이전트 이미지에 대한 최신 롤링 태그를 사용합니다. 이 매개 변수를 사용하여 이미지 버전에 대한 특정 태그를 설정하거나 사용자 고유의 에이전트 이미지를 제공합니다. 자세한 내용은 [IoT Edge 태그 이해](how-to-update-iot-edge.md#understand-iot-edge-tags)를 참조하세요. |
| **사용자** | 컨테이너 레지스트리 사용자 이름입니다. | -AgentImage 매개 변수를 프라이빗 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 대한 액세스 권한이 있는 사용자 이름을 제공합니다. |
| **암호** | 보안 암호 문자열입니다. | -AgentImage 매개 변수를 프라이빗 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 액세스하기 위한 암호를 제공합니다. |

### <a name="update-iotedge"></a>업데이트-IoTEdge

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **ContainerOs** | **윈도우** 또는 **리눅스** | 컨테이너 OS를 지정하지 않으면 Windows가 기본값입니다. Windows 컨테이너의 경우 컨테이너 엔진이 설치에 포함됩니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **프록시** | 프록시 URL | 디바이스가 프록시 서버를 통해 인터넷에 연결해야 하는 경우 이 매개 변수를 포함합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **OfflineInstallationPath** | 디렉터리 경로 | 이 매개 변수가 포함된 경우 설치 관리자는 설치에 필요한 IoT Edge 캡 및 VC 런타임 MSI 파일에 대해 나열된 디렉터리를 확인합니다. 디렉터리에서 찾을 수 없는 파일은 다운로드됩니다. 두 파일이 모두 디렉터리에 있는 경우 인터넷 연결 없이 IoT Edge를 설치할 수 있습니다. 이 매개 변수를 사용하여 특정 버전을 사용할 수도 있습니다. |
| **다시 시작필요** | none | 이 플래그를 사용하면 필요한 경우 배포 스크립트가 프롬프트 없이 컴퓨터를 다시 시작할 수 있습니다. |

### <a name="uninstall-iotedge"></a>제거-IoTEdge

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **강제로** | none | 이 플래그는 이전 제거 시도가 실패한 경우 제거를 강제로 합니다.
| **다시 시작필요** | none | 이 플래그를 사용하면 제거 스크립트가 필요한 경우 프롬프트 없이 컴퓨터를 다시 시작할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

설치된 런타임을 사용하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge를 제대로 설치하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인합니다.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
