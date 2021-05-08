---
title: Windows용 Azure IoT Edge 설치 | Microsoft Docs
description: Windows 디바이스에 Windows 컨테이너용 Azure IoT Edge 설치
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: bb87d09b67658f9a3d7c68f635bfcd9a29de675c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201642"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Windows 컨테이너를 사용하여 Azure IoT Edge를 설치하고 관리합니다.

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 디바이스가 IoT Edge 런타임을 사용하여 구성되면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

IoT Edge 디바이스를 설정하는 두 가지 단계는 다음과 같습니다. 첫 번째 단계는 런타임 및 해당 종속성을 설치하는 것입니다. 두 번째 단계는 디바이스를 클라우드 ID에 연결하고 IoT Hub를 사용하여 인증을 설정하는 것입니다.

이 문서에는 Windows 컨테이너가 포함된 Azure IoT Edge 런타임을 설치하는 단계가 나와 있습니다. Windows 디바이스에서 Linux 컨테이너를 사용하려는 경우 [Azure IoT Edge for Linux on Windows](how-to-install-iot-edge-on-windows.md) 문서를 참조하세요.

>[!NOTE]
>Windows 컨테이너를 사용하는 Azure IoT Edge는 Azure IoT Edge 버전 1.2부터 지원되지 않습니다.
>
>Window 디바이스에서 IoT Edge를 실행하는 새로운 방법을 사용하려면 [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Windows 디바이스

  Windows 컨테이너를 사용하는 IoT Edge에는 최신 [Windows 장기 지원 빌드](/windows/release-information/)인 Windows 버전 1809/빌드 17763이 필요합니다. 지원되는 SKU 목록은 [지원되는 시스템 목록](support.md#operating-systems)을 검토해야 합니다.

* [등록된 디바이스 ID](how-to-register-device.md)

  대칭 키 인증을 사용하여 디바이스를 등록한 경우 디바이스 연결 문자열을 준비합니다.

  X.509 자체 서명된 인증서 인증을 사용하여 디바이스를 등록한 경우 디바이스 등록에 사용한 ID 인증서와 해당 디바이스에서 사용할 수 있는 프라이빗 키가 하나 이상 있어야 합니다.

## <a name="install-a-container-engine"></a>컨테이너 엔진 설치

Azure IoT Edge는 [Moby](https://github.com/moby/moby)와 같은 OCI 호환 컨테이너 런타임을 사용합니다. 설치 스크립트에 포함된 Moby 기반 엔진으로, 엔진 설치를 위한 추가 단계는 없습니다.

## <a name="install-the-iot-edge-security-daemon"></a>IoT Edge 보안 디먼 설치

1. PowerShell을 관리자 권한으로 실행합니다.

   PowerShell(x86)이 아닌 AMD64 PowerShell 세션을 사용합니다. 사용 중인 세션 형식을 잘 모르는 경우 다음 명령을 실행합니다.

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. 다음 작업을 수행하는 [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 명령을 실행합니다.

   * Windows 머신이 지원되는 버전에 해당하는지 확인합니다.
   * 컨테이너 기능을 사용하도록 설정합니다.
   * Moby 엔진과 IoT Edge 런타임을 다운로드합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. 메시지가 표시되면 디바이스를 다시 시작합니다.

디바이스에 IoT Edge를 설치하는 경우 추가 매개 변수를 사용하여 다음과 같이 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 오프라인 설치 시 설치 프로그램이 로컬 디렉터리를 가리키도록 합니다.

추가 매개 변수에 대한 자세한 내용은 [Windows 컨테이너를 포함한 IoT Edge용 PowerShell 스크립트](reference-windows-scripts.md)를 참조하세요.

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비저닝

이제 컨테이너 엔진과 IoT Edge 런타임이 디바이스에 설치되었으므로 다음 단계를 수행할 수 있습니다. 이 단계에서는 클라우드 ID 및 인증 정보를 사용하여 디바이스를 설정합니다.

사용하려는 인증 유형에 따라 다음 섹션을 선택합니다.

* [옵션 1: 대칭 키를 사용하여 인증](#option-1-authenticate-with-symmetric-keys)
* [옵션 2: X.509 인증서를 사용하여 인증](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>옵션 1: 대칭 키를 사용하여 인증

이 시점에서 IoT Edge 런타임은 Windows 디바이스에 설치되며, 클라우드 ID 및 인증 정보를 사용하여 디바이스를 프로비저닝해야 합니다.

이 섹션에서는 대칭 키 인증을 사용하여 디바이스를 프로비저닝하는 단계를 안내합니다. IoT Hub에 디바이스를 등록하고 디바이스 정보에서 연결 문자열을 검색해야 합니다. 그러지 않는 경우 [IoT Hub에서 IoT Edge 디바이스 등록](how-to-register-device.md) 단계를 따릅니다.

1. IoT Edge 디바이스에서 관리자 권한으로 PowerShell을 실행합니다.

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 명령을 사용하여 머신에 IoT Edge 런타임을 구성합니다. 이 명령은 Windows 컨테이너를 통한 수동 프로비저닝으로 기본 설정됩니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * 오프라인 설치 또는 특정 버전 설치 시에는 IoTEdgeSecurityDaemon.ps1 스크립트를 디바이스에 다운로드한 경우 스크립트의 로컬 복사본을 참조해야 합니다.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 메시지가 표시되면 이전 섹션에서 검색한 디바이스 연결 문자열을 제공합니다. 디바이스 연결 문자열은 물리적 디바이스를 IoT Hub의 디바이스 ID와 연결하고 인증 정보를 제공합니다.

   디바이스 연결 문자열은 다음 형식을 사용하며 따옴표를 포함하지 않아야 합니다. `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

디바이스를 수동으로 프로비저닝하는 경우 추가 매개 변수를 사용하여 다음과 같이 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 특정 edgeAgent 컨테이너 이미지를 선언하고, 프라이빗 레지스트리에 있는 경우 자격 증명 제공

추가 매개 변수에 대한 자세한 내용은 [Windows 컨테이너를 포함한 IoT Edge용 PowerShell 스크립트](reference-windows-scripts.md)를 참조하세요.

### <a name="option-2-authenticate-with-x509-certificates"></a>옵션 2: X.509 인증서를 사용하여 인증

이 시점에서 IoT Edge 런타임은 Windows 디바이스에 설치되며, 클라우드 ID 및 인증 정보를 사용하여 디바이스를 프로비저닝해야 합니다.

이 섹션에서는 X.509 인증서 인증을 사용하여 디바이스를 프로비저닝하는 단계를 안내합니다. 디바이스를 IoT Hub에 등록하여 IoT Edge 디바이스에 있는 인증서 및 프라이빗 키와 일치하는 지문을 제공했습니다. 그러지 않는 경우 [IoT Hub에서 IoT Edge 디바이스 등록](how-to-register-device.md) 단계를 따릅니다.

1. IoT Edge 디바이스에서 관리자 권한으로 PowerShell을 실행합니다.

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 명령을 사용하여 머신에 IoT Edge 런타임을 구성합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * 오프라인 설치 또는 특정 버전 설치 시에는 IoTEdgeSecurityDaemon.ps1 스크립트를 디바이스에 다운로드한 경우 스크립트의 로컬 복사본을 참조해야 합니다.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 메시지가 표시되면 다음 정보를 제공합니다.

   * **IotHubHostName**: 디바이스가 연결될 IoT 허브의 호스트 이름입니다. 예: `{IoT hub name}.azure-devices.net`.
   * **DeviceId**: 디바이스를 등록할 때 제공한 ID입니다.
   * **X509IdentityCertificate**: 디바이스에 있는 ID 인증서의 절대 경로입니다. 예: `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: 제공된 ID 인증서의 프라이빗 키 파일에 대한 절대 경로입니다. 예: `C:\path\identity_key.pem`.

디바이스를 수동으로 프로비저닝하는 경우 추가 매개 변수를 사용하여 다음과 같이 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 특정 edgeAgent 컨테이너 이미지를 선언하고, 프라이빗 레지스트리에 있는 경우 자격 증명 제공

추가 매개 변수에 대한 자세한 내용은 [Windows 컨테이너를 포함한 IoT Edge용 PowerShell 스크립트](reference-windows-scripts.md)를 참조하세요.

## <a name="offline-or-specific-version-installation-optional"></a>오프라인 설치 또는 특정 버전 설치(선택 사항)

이 섹션의 단계는 표준 설치 단계에서 다루지 않는 시나리오에 대한 것입니다. 다음을 포함할 수 있습니다.

* 오프라인 상태에서 IoT Edge 설치
* 릴리스 후보 버전 설치
* 최신 버전이 아닌 버전 설치

설치 중에 다음 3개의 파일이 다운로드됩니다.

* 설치 지침을 포함하는 PowerShell 스크립트
* IoT Edge 보안 디먼(iotedged), Moby 컨테이너 엔진, Moby CLI를 포함하는 Microsoft Azure IoT Edge cab
* Visual C++ 재배포 가능 패키지(VC 런타임) 설치 프로그램

설치하는 동안 디바이스가 오프라인 상태가 되거나 특정 버전의 IoT Edge를 설치하려는 경우 디바이스에 미리 해당 파일을 다운로드할 수 있습니다. 설치할 때 다운로드한 파일이 포함된 디렉터리의 설치 스크립트를 가리킵니다. 설치 관리자는 먼저 디렉터리를 확인한 다음, 디렉터리에 없는 구성 요소만 다운로드합니다. 파일을 오프라인에서 모두 사용할 수 있는 경우 인터넷 연결 없이 설치할 수 있습니다.

1. 이전 버전 및 최신 IoT Edge 설치 파일은 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조하세요.

2. 설치하려는 버전을 찾고 릴리스 정보의 **자산** 섹션에서 다음 파일을 IoT 디바이스로 다운로드합니다.

   * IoTEdgeSecurityDaemon.ps1
   * 1\.1 릴리스 채널의 Microsoft-Azure-IoTEdge-amd64.cab.

   각 릴리스의 기능을 지원하기 위해 기능이 변경되기 때문에 사용하는 .cab 파일과 동일한 릴리스의 PowerShell 스크립트를 사용하는 것이 중요합니다.

3. 다운로드한 .cab 파일에 아키텍처 접미사가 있는 경우 파일 이름을 **Microsoft-Azure-IoTEdge.cab** 로 바꿉니다.

4. 필요에 따라 Visual C++ 재배포 가능 패키지의 설치 프로그램을 다운로드합니다. 예를 들어 PowerShell 스크립트는 [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe) 버전을 사용합니다. 설치 프로그램을 IoT 디바이스에서 IoT Edge 파일이 있는 곳과 동일한 폴더에 저장합니다.

5. 오프라인 구성 요소를 사용하여 설치하려면 PowerShell 스크립트의 로컬 복사본을 [도트 소싱](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)합니다.

6. `-OfflineInstallationPath` 매개 변수를 사용하여 [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 명령을 실행합니다. 파일 디렉터리에 대한 절대 경로를 제공합니다. 예제:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   배포 명령은 제공된 로컬 파일 디렉터리에 있는 모든 구성 요소를 사용합니다. .cab 파일 또는 Visual C++ 설치 프로그램이 없으면 배포 명령이 다운로드를 시도합니다.

## <a name="update-iot-edge"></a>IoT Edge 업데이트

`Update-IoTEdge` 명령을 사용하여 보안 디먼을 업데이트합니다. 이 스크립트는 보안 디먼의 최신 버전을 자동으로 가져옵니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Update-IoTEdge 명령을 실행하면 두 개의 런타임 컨테이너 이미지와 함께 디바이스에서 보안 디먼이 제거되고 업데이트됩니다. config.yaml 파일뿐 아니라 Moby 컨테이너 엔진의 데이터도 디바이스에서 유지됩니다. 구성 정보를 보존하면 업데이트 프로세스 중에 디바이스의 연결 문자열 또는 디바이스 프로비저닝 서비스 정보를 다시 제공할 필요가 없습니다.

특정 버전의 보안 디먼으로 업데이트하려면 [IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)의 대상으로 지정할 1.1 릴리스 채널의 버전을 찾습니다. 해당 버전에서 **Microsoft-Azure-IoTEdge.cab** 파일을 다운로드합니다. 그런 다음 `-OfflineInstallationPath` 매개 변수를 사용하여 로컬 파일 위치를 가리킵니다. 예를 들면 다음과 같습니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath` 매개 변수는 제공된 디렉터리에서 **Microsoft-Azure-IoTEdge.cab** 이라는 파일을 찾습니다. 파일의 이름을 바꾸면 아키텍처 접미사가 있는 경우 해당 접미사는 제거됩니다.

디바이스를 오프라인으로 업데이트하려면 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)에서 대상으로 지정할 버전을 찾습니다. 해당 버전에서 *IoTEdgeSecurityDaemon.ps1* 및 *Microsoft-Azure-IoTEdge.cab* 파일을 다운로드합니다. 각 릴리스의 기능을 지원하기 위해 기능이 변경되기 때문에 사용하는 .cab 파일과 동일한 릴리스의 PowerShell 스크립트를 사용하는 것이 중요합니다.

다운로드한 .cab 파일에 아키텍처 접미사가 있는 경우 파일 이름을 **Microsoft-Azure-IoTEdge.cab** 로 바꿉니다.

오프라인 구성 요소를 사용하여 업데이트하려면 PowerShell 스크립트의 로컬 복사본을 [도트 소싱](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)합니다. 그런 다음 `-OfflineInstallationPath` 매개 변수를 `Update-IoTEdge` 명령의 일부로 사용하고 파일 디렉터리에 대한 절대 경로를 제공합니다. 예제:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

업데이트 옵션에 대한 자세한 내용은 `Get-Help Update-IoTEdge -full` 명령을 사용하거나 [Windows 컨테이너를 포함하는 IoT Edge용 PowerShell 스크립트](reference-windows-scripts.md)를 참조하세요.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

디바이스에서 IoT Edge 설치를 제거하려면 다음 명령을 사용합니다.

Windows 디바이스에서 IoT Edge 설치를 제거하려는 경우 관리 PowerShell 창에서 [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) 명령을 사용합니다. 이 명령은 기존 구성 및 Moby 엔진 데이터와 함께 IoT Edge 런타임을 제거합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

제거 옵션에 대한 자세한 내용은 `Get-Help Uninstall-IoTEdge -full` 명령을 사용하세요.

## <a name="next-steps"></a>다음 단계

디바이스에 모듈을 배포하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)를 계속 진행하세요.
