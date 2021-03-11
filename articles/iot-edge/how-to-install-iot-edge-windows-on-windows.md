---
title: Windows 용 Azure IoT Edge 설치 | Microsoft Docs
description: Windows 장치에 Windows 컨테이너에 대 한 Azure IoT Edge 설치
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
ms.openlocfilehash: c24389a1957f9e0cfb23e3bb5b8604c34e57a915
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609518"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Windows 컨테이너를 사용 하 여 Azure IoT Edge 설치 및 관리

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 디바이스가 IoT Edge 런타임을 사용하여 구성되면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 자세히 알아보려면 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조 하세요.

IoT Edge 장치를 설정 하는 두 단계는 다음과 같습니다. 첫 번째 단계는 런타임 및 해당 종속성을 설치 하는 것입니다. 두 번째 단계는 장치를 클라우드의 id에 연결 하 고 IoT Hub를 사용 하 여 인증을 설정 하는 것입니다.

이 문서에는 Windows 컨테이너를 사용 하 여 Azure IoT Edge 런타임을 설치 하는 단계가 나와 있습니다. Windows 장치에서 Linux 컨테이너를 사용 하려는 경우 [windows의 linux에 대 한 Azure IoT Edge](how-to-install-iot-edge-on-windows.md) 문서를 참조 하세요.

>[!NOTE]
>Windows 컨테이너와 Azure IoT Edge Azure IoT Edge 버전 1.2부터 지원 되지 않습니다.
>
>Windows [에서 Linux 용 Azure IoT Edge](iot-edge-for-linux-on-windows.md)windows 장치에서 IoT Edge를 실행 하는 데 새 방법을 사용 하는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Windows 장치

  Windows 컨테이너에 IoT Edge Windows 버전 1809/build 17763이 필요 하며,이는 최신 [windows 장기 지원 빌드](/windows/release-information/)입니다. 지원 되는 Sku 목록에 대해 [지원 되는 시스템 목록을](support.md#operating-systems) 검토 해야 합니다.

* [등록 된 장치 ID](how-to-register-device.md)

  대칭 키 인증을 사용 하 여 장치를 등록 한 경우 장치 연결 문자열을 준비 합니다.

  X.509 자체 서명 된 인증서 인증을 사용 하 여 장치를 등록 한 경우 장치를 등록 하는 데 사용한 id 인증서와 해당 장치에서 사용할 수 있는 개인 개인 키가 하나 이상 있어야 합니다.

## <a name="install-a-container-engine"></a>컨테이너 엔진 설치

Azure IoT Edge는 [Moby](https://github.com/moby/moby)와 같은 OCI 호환 컨테이너 런타임에 의존 합니다. 설치 스크립트에 포함 된 Moby 기반 엔진입니다. 엔진을 설치 하는 추가 단계는 없습니다.

## <a name="install-the-iot-edge-security-daemon"></a>IoT Edge 보안 데몬을 설치 합니다.

1. PowerShell을 관리자 권한으로 실행합니다.

   Powershell (x86)이 아닌 PowerShell의 AMD64 세션을 사용 합니다. 사용 중인 세션 유형을 잘 모르는 경우 다음 명령을 실행 합니다.

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. 다음 작업을 수행 하는 [배포-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 명령을 실행 합니다.

   * Windows 컴퓨터가 지원 되는 버전에 있는지 검사 합니다.
   * 컨테이너 기능을 설정 합니다.
   * Moby 엔진과 IoT Edge 런타임을 다운로드 합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. 메시지가 표시 되 면 장치를 다시 시작 합니다.

장치에 IoT Edge를 설치 하는 경우 추가 매개 변수를 사용 하 여 다음을 포함 하 여 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 설치 관리자가 오프 라인 설치를 위해 로컬 디렉터리를 가리키도록 합니다.

이러한 추가 매개 변수에 대 한 자세한 내용은 [Windows 컨테이너를 사용 하 여 IoT Edge에 대 한 PowerShell 스크립트](reference-windows-scripts.md)를 참조 하세요.

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 id를 사용 하 여 장치 프로 비전

이제 컨테이너 엔진과 IoT Edge 런타임이 장치에 설치 되었으므로 다음 단계를 수행할 수 있습니다 .이 단계에서는 클라우드 id 및 인증 정보를 사용 하 여 장치를 설정 합니다.

사용 하려는 인증 유형에 따라 다음 섹션을 선택 합니다.

* [옵션 1: 대칭 키를 사용 하 여 인증](#option-1-authenticate-with-symmetric-keys)
* [옵션 2: x.509 인증서를 사용 하 여 인증](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>옵션 1: 대칭 키를 사용 하 여 인증

이 시점에서 IoT Edge 런타임은 Windows 장치에 설치 되며, 클라우드 id 및 인증 정보를 사용 하 여 장치를 프로 비전 해야 합니다.

이 섹션에서는 대칭 키 인증을 사용 하 여 장치를 프로 비전 하는 단계를 안내 합니다. IoT Hub에 장치를 등록 하 고 장치 정보에서 연결 문자열을 검색 해야 합니다. 그렇지 않은 경우 [IoT Hub에서 IoT Edge 장치 등록](how-to-register-device.md)의 단계를 따릅니다.

1. IoT Edge 장치에서 관리자 권한으로 PowerShell을 실행 합니다.

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 명령을 사용 하 여 컴퓨터에서 IoT Edge 런타임을 구성 합니다. 이 명령은 Windows 컨테이너를 통한 수동 프로비저닝으로 기본 설정됩니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * 오프 라인 또는 특정 버전 설치를 위해 IoTEdgeSecurityDaemon.ps1 스크립트를 장치에 다운로드 한 경우 스크립트의 로컬 복사본을 참조 해야 합니다.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 메시지가 표시 되 면 이전 섹션에서 검색 한 장치 연결 문자열을 제공 합니다. 장치 연결 문자열은 물리적 장치를 IoT Hub의 장치 ID와 연결 하 고 인증 정보를 제공 합니다.

   장치 연결 문자열은 다음 형식을 사용 하며 따옴표를 포함 하지 않아야 합니다. `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

수동으로 장치를 프로 비전 할 때 추가 매개 변수를 사용 하 여 다음을 포함 하 여 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 특정 edgeAgent 컨테이너 이미지를 선언 하 고 개인 레지스트리에 있는 경우 자격 증명을 제공 합니다.

이러한 추가 매개 변수에 대 한 자세한 내용은 [Windows 컨테이너를 사용 하 여 IoT Edge에 대 한 PowerShell 스크립트](reference-windows-scripts.md)를 참조 하세요.

### <a name="option-2-authenticate-with-x509-certificates"></a>옵션 2: x.509 인증서를 사용 하 여 인증

이 시점에서 IoT Edge 런타임은 Windows 장치에 설치 되며, 클라우드 id 및 인증 정보를 사용 하 여 장치를 프로 비전 해야 합니다.

이 섹션에서는 x.509 인증서 인증을 사용 하 여 장치를 프로 비전 하는 단계를 안내 합니다. 장치를 IoT Hub에 등록 하 여 IoT Edge 장치에 있는 인증서 및 개인 키와 일치 하는 지문을 제공 해야 합니다. 그렇지 않은 경우 [IoT Hub에서 IoT Edge 장치 등록](how-to-register-device.md)의 단계를 따릅니다.

1. IoT Edge 장치에서 관리자 권한으로 PowerShell을 실행 합니다.

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 명령을 사용 하 여 컴퓨터에서 IoT Edge 런타임을 구성 합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * 오프 라인 또는 특정 버전 설치를 위해 IoTEdgeSecurityDaemon.ps1 스크립트를 장치에 다운로드 한 경우 스크립트의 로컬 복사본을 참조 해야 합니다.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 메시지가 표시되면 다음 정보를 제공합니다.

   * **IotHubHostName**: 장치가 연결 될 IoT hub의 호스트 이름입니다. 예들 들어 `{IoT hub name}.azure-devices.net`입니다.
   * **DeviceId**: 장치를 등록할 때 제공한 ID입니다.
   * **X509IdentityCertificate**: 장치에서 id 인증서의 절대 경로입니다. 예들 들어 `C:\path\identity_certificate.pem`입니다.
   * **X509IdentityPrivateKey**: 제공 된 id 인증서의 개인 키 파일에 대 한 절대 경로입니다. 예들 들어 `C:\path\identity_key.pem`입니다.

수동으로 장치를 프로 비전 할 때 추가 매개 변수를 사용 하 여 다음을 포함 하 여 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 특정 edgeAgent 컨테이너 이미지를 선언 하 고 개인 레지스트리에 있는 경우 자격 증명을 제공 합니다.

이러한 추가 매개 변수에 대 한 자세한 내용은 [Windows 컨테이너를 사용 하 여 IoT Edge에 대 한 PowerShell 스크립트](reference-windows-scripts.md)를 참조 하세요.

## <a name="offline-or-specific-version-installation-optional"></a>오프 라인 또는 특정 버전 설치 (선택 사항)

이 섹션의 단계는 표준 설치 단계에서 다루지 않는 시나리오에 대 한 것입니다. 다음을 포함할 수 있습니다.

* 오프 라인 상태에서 IoT Edge 설치
* 릴리스 후보 버전 설치
* 최신 버전이 아닌 버전을 설치 합니다.

설치 하는 동안 세 개의 파일이 다운로드 됩니다.

* 설치 지침을 포함 하는 PowerShell 스크립트
* Microsoft Azure IoT Edge cab (IoT Edge 보안 디먼), Moby 컨테이너 엔진 및 Moby CLI를 포함 합니다.
* Visual C++ 재배포 가능 패키지 (VC 런타임) 설치 관리자

설치 하는 동안 장치가 오프 라인 상태가 되거나 특정 버전의 IoT Edge를 설치 하려는 경우 장치에 미리 이러한 파일을 다운로드할 수 있습니다. 를 설치할 때 다운로드 한 파일이 포함 된 디렉터리의 설치 스크립트를 가리킵니다. 설치 관리자는 해당 디렉터리를 먼저 확인 한 다음 찾을 수 없는 구성 요소만 다운로드 합니다. 모든 파일을 오프 라인으로 사용할 수 있는 경우 인터넷에 연결 되지 않은를 설치할 수 있습니다.

1. 이전 버전과 함께 최신 IoT Edge 설치 파일은 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조 하세요.

2. 설치 하려는 버전을 찾고 릴리스 정보의 **자산** 섹션에서 다음 파일을 IoT 장치로 다운로드 합니다.

   * IoTEdgeSecurityDaemon.ps1
   * 1.1 릴리스 채널에서 Microsoft-Azure-IoTEdge-amd64.cab 합니다.

   각 릴리스의 기능을 지원 하기 위해 기능이 변경 되기 때문에 사용 하는 .cab 파일과 동일한 릴리스의 PowerShell 스크립트를 사용 하는 것이 중요 합니다.

3. 다운로드 한 .cab 파일의 아키텍처 접미사가 있는 경우 파일 이름을 **Microsoft-Azure-IoTEdge.cab** 으로 바꿉니다.

4. 필요에 따라 Visual C++ 재배포 가능 패키지에 대 한 설치 관리자를 다운로드 합니다. 예를 들어 PowerShell 스크립트는 [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe)버전을 사용 합니다. 설치 관리자를 IoT 장치의 동일한 폴더에 IoT Edge 파일로 저장 합니다.

5. 오프 라인 구성 요소를 사용 하 여 설치 하려면 [도트 원본](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) 에 PowerShell 스크립트의 로컬 복사본을 사용 합니다.

6. 매개 변수를 사용 하 여 [배포-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 명령을 실행 합니다 `-OfflineInstallationPath` . 파일 디렉터리에 대 한 절대 경로를 제공 합니다. 예를 들면 다음과 같습니다.

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   배포 명령은 제공 된 로컬 파일 디렉터리에 있는 모든 구성 요소를 사용 합니다. .Cab 파일 또는 Visual C++ 설치 관리자가 없으면 다운로드를 시도 합니다.

## <a name="update-iot-edge"></a>업데이트 IoT Edge

명령을 사용 `Update-IoTEdge` 하 여 보안 디먼을 업데이트 합니다. 이 스크립트는 보안 디먼의 최신 버전을 자동으로 가져옵니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Update-IoTEdge 명령을 실행 하면 두 개의 런타임 컨테이너 이미지와 함께 장치에서 보안 데몬이 제거 되 고 업데이트 됩니다. config.yaml 파일뿐 아니라 Moby 컨테이너 엔진의 데이터도 디바이스에서 유지됩니다. 구성 정보를 유지 하는 것은 업데이트 프로세스 중에 장치에 대 한 연결 문자열 또는 장치 프로 비전 서비스 정보를 다시 제공할 필요가 없음을 의미 합니다.

특정 버전의 보안 디먼으로 업데이트 하려면 [IoT Edge 릴리스의](https://github.com/Azure/azure-iotedge/releases)대상으로 지정할 1.1 릴리스 채널의 버전을 찾습니다. 해당 버전에서 **Microsoft-Azure-IoTEdge.cab** 파일을 다운로드 합니다. 그런 다음 `-OfflineInstallationPath` 매개 변수를 사용 하 여 로컬 파일 위치를 가리킵니다. 예를 들면 다음과 같습니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath`매개 변수는 제공 된 디렉터리에서 **Microsoft-Azure-IoTEdge.cab** 라는 파일을 찾습니다. 파일의 이름을 바꾸면 아키텍처 접미사 (있는 경우)가 제거 됩니다.

장치를 오프 라인으로 업데이트 하려면 [Azure IoT Edge 릴리스에서](https://github.com/Azure/azure-iotedge/releases)대상으로 지정할 버전을 찾습니다. 해당 버전에서 *IoTEdgeSecurityDaemon.ps1* 및 *Microsoft-Azure-IoTEdge.cab* 파일을 다운로드 합니다. 각 릴리스의 기능을 지원 하기 위해 기능이 변경 되기 때문에 사용 하는 .cab 파일과 동일한 릴리스의 PowerShell 스크립트를 사용 하는 것이 중요 합니다.

다운로드 한 .cab 파일의 아키텍처 접미사가 있는 경우 파일 이름을 **Microsoft-Azure-IoTEdge.cab** 으로 바꿉니다.

오프 라인 구성 요소로 업데이트 하려면 [도트 원본](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) 에 PowerShell 스크립트의 로컬 복사본을 사용 합니다. 그런 다음 `-OfflineInstallationPath` 매개 변수를 명령의 일부로 사용 `Update-IoTEdge` 하 고 파일 디렉터리에 대 한 절대 경로를 제공 합니다. 예를 들면 다음과 같습니다.

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

업데이트 옵션에 대 한 자세한 내용은 명령을 사용 `Get-Help Update-IoTEdge -full` 하거나 [Windows 컨테이너와 IoT Edge에 대 한 PowerShell 스크립트](reference-windows-scripts.md)를 참조 하세요.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

장치에서 IoT Edge 설치를 제거 하려면 다음 명령을 사용 합니다.

Windows 장치에서 IoT Edge 설치를 제거 하려면 관리 PowerShell 창에서 [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) 명령을 사용 합니다. 이 명령은 기존 구성 및 Moby 엔진 데이터와 함께 IoT Edge 런타임을 제거합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

제거 옵션에 대 한 자세한 내용을 보려면 명령을 사용 `Get-Help Uninstall-IoTEdge -full` 하세요.

## <a name="next-steps"></a>다음 단계

장치에 모듈을 배포 하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md) 를 계속 진행 하세요.
