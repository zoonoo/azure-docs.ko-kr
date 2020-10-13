---
title: Azure IoT Edge | 설치 Microsoft Docs
description: Windows 또는 Linux 장치에 대 한 설치 지침 Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: e5dc5601be5bd6d8003b196b5e7768e66bc48478
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979786"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>Azure IoT Edge 런타임 설치 또는 제거

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. 디바이스가 IoT Edge 런타임을 사용하여 구성되면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 자세히 알아보려면 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조 하세요.

IoT Edge 장치를 설정 하는 두 단계는 다음과 같습니다. 첫 번째 단계는이 문서에서 설명 하는 런타임 및 해당 종속성을 설치 하는 것입니다. 두 번째 단계는 장치를 클라우드의 id에 연결 하 고 IoT Hub를 사용 하 여 인증을 설정 하는 것입니다. 이러한 단계는 다음 문서에 나와 있습니다.

이 문서에는 Linux 또는 Windows 장치에 Azure IoT Edge 런타임을 설치 하는 단계가 나와 있습니다. Windows 장치의 경우 Linux 컨테이너 또는 Windows 컨테이너를 추가로 선택할 수 있습니다. 현재 Windows의 Windows 컨테이너는 프로덕션 시나리오에 권장 됩니다. Windows의 linux 컨테이너는 개발 및 테스트 시나리오에 유용 합니다. 특히 Linux 장치에 배포 하기 위해 Windows PC에서 개발 하는 경우에 유용 합니다.

## <a name="prerequisites"></a>필수 구성 요소

현재 프로덕션 시나리오에 대해 지원 되는 운영 체제에 대 한 최신 정보는 [지원 되는 Azure IoT Edge 시스템](support.md#operating-systems) 을 참조 하세요.

# <a name="linux"></a>[Linux](#tab/linux)

X64, ARM32 또는 ARM64 Linux 장치가 있어야 합니다. Microsoft는 Ubuntu Server 16.04, Ubuntu Server 18.04 및 Raspbian Stretch 운영 체제에 대 한 설치 패키지를 제공 합니다.

>[!NOTE]
>ARM64 장치에 대 한 지원은 [공개 미리 보기로](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)제공 됩니다.

Microsoft 설치 패키지에 액세스할 수 있도록 장치를 준비 합니다.

1. 장치 운영 체제와 일치 하는 리포지토리 구성을 설치 합니다.

   * **Ubuntu Server 16.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspbian Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. 생성 된 목록을 sources. list. d 디렉터리에 복사 합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Microsoft GPG 공개 키를 설치 합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Windows 버전

Windows 컨테이너에 IoT Edge Windows 버전 1809/build 17762이 필요 하며,이는 최신 [windows 장기 지원 빌드](/windows/release-information/)입니다. 개발 및 테스트 시나리오의 경우 컨테이너 기능을 지 원하는 모든 SKU (Pro, Enterprise, 서버 등)가 작동 합니다. 그러나 프로덕션으로 이동 하기 전에 지원 되는 시스템 목록을 검토 해야 합니다.

Linux 컨테이너를 사용 하는 IoT Edge는 [Docker Desktop에 대 한 요구 사항을](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)충족 하는 모든 버전의 Windows에서 실행할 수 있습니다.

### <a name="container-engine-requirements"></a>컨테이너 엔진 요구 사항

Azure IoT Edge은 [OCI 호환](https://www.opencontainers.org/) 컨테이너 엔진에 의존 합니다. 장치가 컨테이너를 지원할 수 있는지 확인 합니다.

가상 컴퓨터에 IoT Edge을 설치 하는 경우 중첩 된 가상화를 사용 하도록 설정 하 고 2gb 이상의 메모리를 할당 합니다. Hyper-v의 경우 2 세대 가상 컴퓨터는 기본적으로 중첩 된 가상화를 사용 하도록 설정 합니다. VMware의 경우 가상 머신에서 기능을 사용 하도록 설정 하는 토글이 있습니다.

---

Azure IoT Edge 소프트웨어 패키지에는 각 패키지 ( `usr/share/doc/{package-name}` 또는 디렉터리)에 있는 사용 조건이 적용 됩니다 `LICENSE` . 패키지를 사용 하기 전에 사용 조건을 읽어 보십시오. 패키지를 설치 하 고 사용 하면 이러한 조건에 동의 하는 것입니다. 사용 조건에 동의 하지 않는 경우 해당 패키지를 사용 하지 마십시오.

## <a name="install-a-container-engine"></a>컨테이너 엔진 설치

Azure IoT Edge는 OCI 호환 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우에는 Moby 기반 엔진을 사용 하는 것이 좋습니다. Moby 엔진은 Azure IoT Edge에서 공식적으로 지원 되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

# <a name="linux"></a>[Linux](#tab/linux)

장치에서 패키지 목록을 업데이트 합니다.

   ```bash
   sudo apt-get update
   ```

Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

Moby 컨테이너 엔진을 설치할 때 오류가 발생 하는 경우에는 Linux 커널을 Moby 호환성을 확인 하세요. 일부 임베디드 장치 제조업체는 컨테이너 엔진 호환성에 필요한 기능이 없는 사용자 지정 Linux 커널을 포함 하는 장치 이미지를 제공 합니다. Moby에서 제공한 [체크 인 스크립트](https://github.com/moby/moby/blob/master/contrib/check-config.sh) 를 사용 하 여 커널 구성을 확인 하는 다음 명령을 실행 합니다.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

스크립트의 출력에서 및 아래의 모든 항목을 `Generally Necessary` 사용할 수 있는지 확인 `Network Drivers` 합니다. 누락 된 기능을 사용 하는 경우 소스에서 커널을 다시 작성 하 고 적절 한 커널 .config에 포함 하기 위해 관련 모듈을 선택 하 여 사용 하도록 설정 합니다. 마찬가지로 또는와 같은 커널 구성 생성기를 사용 하는 `defconfig` 경우 `menuconfig` 해당 기능을 찾아서 사용 하도록 설정 하 고 그에 따라 커널을 다시 빌드합니다. 새로 수정 된 커널을 배포한 후에는 확인-구성 스크립트를 다시 실행 하 여 필요한 모든 기능이 성공적으로 설정 되었는지 확인 합니다.

# <a name="windows"></a>[Windows](#tab/windows)

프로덕션 시나리오의 경우 설치 스크립트에 포함 된 Moby 기반 엔진을 사용 합니다. 엔진을 설치 하는 추가 단계는 없습니다.

Linux 컨테이너를 사용 하는 IoT Edge의 경우 사용자 고유의 컨테이너 런타임을 제공 해야 합니다. 장치에 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) 을 설치 하 고 계속 하기 전에 [Linux 컨테이너를 사용](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) 하도록 구성 합니다.

---

## <a name="install-the-iot-edge-security-daemon"></a>IoT Edge 보안 데몬을 설치 합니다.

IoT Edge 보안 디먼은 IoT Edge 장치에서 보안 표준을 제공 하 고 유지 관리 합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이러한 섹션의 단계는 인터넷에 연결 된 장치에 최신 버전을 설치 하는 일반적인 프로세스를 나타냅니다. 시험판 버전과 같은 특정 버전을 설치 해야 하거나 오프 라인 상태에서 설치 해야 하는 경우 다음 섹션에서 [오프 라인 또는 특정 버전의 설치](#offline-or-specific-version-installation) 단계를 따르세요.

# <a name="linux"></a>[Linux](#tab/linux)

장치에서 패키지 목록을 업데이트 합니다.

   ```bash
   sudo apt-get update
   ```

사용할 수 있는 IoT Edge 버전을 확인 합니다.

   ```bash
   apt list -a iotedge
   ```

최신 버전의 보안 디먼을 설치 하려는 경우 다음 명령을 사용 하 여 최신 버전의 **libiothsm** 패키지를 설치 합니다.

   ```bash
   sudo apt-get install iotedge
   ```

특정 버전의 보안 디먼을 설치 하려는 경우 apt 목록 출력의 버전을 지정 합니다. 또한 **libiothsm** 패키지에 대해 동일한 버전을 지정 합니다. 그렇지 않으면 최신 버전이 설치 됩니다. 예를 들어 다음 명령은 1.0.8 릴리스의 최신 버전을 설치 합니다.

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

설치 하려는 버전이 나열 되지 않은 경우 다음 섹션에서 [오프 라인 또는 특정 버전의 설치](#offline-or-specific-version-installation) 단계를 따르세요. 이 섹션에서는 이전 버전의 IoT Edge 보안 디먼 또는 릴리스 후보 버전을 대상으로 하는 방법을 보여 줍니다.

# <a name="windows"></a>[Windows](#tab/windows)

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

   이 `Deploy-IoTEdge` 명령은 기본적으로 Windows 컨테이너를 사용 합니다. Linux 컨테이너를 사용 하려는 경우 매개 변수를 추가 합니다 `ContainerOs` .

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. 이 시점에서 출력은 다시 시작 하 라는 메시지를 표시할 수 있습니다. 그렇다면 장치를 지금 다시 시작 하세요.

장치에 IoT Edge를 설치 하는 경우 추가 매개 변수를 사용 하 여 다음을 포함 하 여 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 설치 관리자가 오프 라인 설치를 위해 로컬 디렉터리를 가리키도록 합니다.

이러한 추가 매개 변수에 대 한 자세한 내용은 [Windows의 IoT Edge에 대 한 PowerShell 스크립트](reference-windows-scripts.md)를 참조 하세요.

---

이제 컨테이너 엔진과 IoT Edge 런타임이 장치에 설치 되었으므로 다음 단계를 수행할 수 있습니다 .이 단계에서는 장치를 IoT Hub에 등록 하 고 해당 클라우드 id 및 인증 정보를 사용 하 여 장치를 설정 합니다.

사용할 인증 유형에 따라 다음 문서를 선택 합니다.

* [대칭 키 인증](how-to-manual-provision-symmetric-key.md) 을 시작 하는 것이 더 빠릅니다.
* [X.509 인증서 인증은](how-to-manual-provision-x509.md) 프로덕션 시나리오에 더 안전 합니다.

## <a name="offline-or-specific-version-installation"></a>오프 라인 또는 특정 버전 설치

이 섹션의 단계는 표준 설치 단계에서 다루지 않는 시나리오에 대 한 것입니다. 여기에는 다음이 포함 될 수 있습니다.

* 오프 라인 상태에서 IoT Edge 설치
* 릴리스 후보 버전 설치
* Windows에서 최신 버전이 아닌 버전을 설치 합니다.

# <a name="linux"></a>[Linux](#tab/linux)

에서 제공 하지 않는 특정 버전의 Azure IoT Edge 런타임을 설치 하려는 경우이 섹션의 단계를 사용 `apt-get install` 합니다. Microsoft 패키지 목록에는 제한 된 최신 버전과 해당 하위 버전 집합만 포함 되어 있으므로 이러한 단계는 이전 버전 또는 릴리스 후보 버전을 설치 하려는 모든 사용자를 위한 것입니다.

말아 넘기기 명령을 사용 하면 IoT Edge GitHub 리포지토리에서 직접 구성 요소 파일을 대상으로 지정할 수 있습니다.

<!-- TODO: Offline installation? -->

1. [Azure IoT Edge 릴리스로](https://github.com/Azure/azure-iotedge/releases)이동 하 고 대상으로 지정할 릴리스 버전을 찾습니다.

2. 해당 버전에 대 한 **자산** 섹션을 확장 합니다.

3. 모든 릴리스에는 IoT Edge 보안 디먼 및 hsmlib에 대 한 새 파일이 있어야 합니다. 다음 명령을 사용 하 여 해당 구성 요소를 업데이트 합니다.

   1. IoT Edge 장치의 아키텍처와 일치 하는 **libiothsm** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭 하 고 링크 주소를 복사 합니다.

   2. 다음 명령의 복사 된 링크를 사용 하 여 해당 버전의 hsmlib를 설치 합니다.

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. IoT Edge 장치의 아키텍처와 일치 하는 **iotedge** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭 하 고 링크 주소를 복사 합니다.

   4. 다음 명령의 복사 된 링크를 사용 하 여 해당 버전의 IoT Edge 보안 디먼을 설치 합니다.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

설치 하는 동안 세 개의 파일이 다운로드 됩니다.

* 설치 지침을 포함 하는 PowerShell 스크립트
* Microsoft Azure IoT Edge cab (IoT Edge 보안 디먼), Moby 컨테이너 엔진 및 Moby CLI를 포함 합니다.
* Visual C++ 재배포 가능 패키지 (VC 런타임) 설치 관리자

설치 하는 동안 장치가 오프 라인 상태가 되거나 특정 버전의 IoT Edge를 설치 하려는 경우 장치에 미리 이러한 파일을 다운로드할 수 있습니다. 를 설치할 때 다운로드 한 파일이 포함 된 디렉터리의 설치 스크립트를 가리킵니다. 설치 관리자는 해당 디렉터리를 먼저 확인 한 다음 찾을 수 없는 구성 요소만 다운로드 합니다. 모든 파일을 오프 라인으로 사용할 수 있는 경우 인터넷에 연결 되지 않은를 설치할 수 있습니다.

1. 이전 버전과 함께 최신 IoT Edge 설치 파일은 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조 하세요.

2. 설치 하려는 버전을 찾고 릴리스 정보의 **자산** 섹션에서 다음 파일을 IoT 장치로 다운로드 합니다.

   * IoTEdgeSecurityDaemon.ps1
   * 릴리스 1.0.9 이상에서 Microsoft-Azure-IoTEdge-amd64.cab 하거나 1.0.8 이전 버전에서 Microsoft-Azure-IoTEdge.cab 합니다.

   Microsoft-Azure-IotEdge-arm32.cab는 테스트 목적 으로만 1.0.9에서 사용할 수 있습니다. IoT Edge는 현재 Windows ARM32 장치에서 지원 되지 않습니다.

   각 릴리스의 기능을 지원 하기 위해 기능이 변경 되기 때문에 사용 하는 .cab 파일과 동일한 릴리스의 PowerShell 스크립트를 사용 하는 것이 중요 합니다.

3. 다운로드 한 .cab 파일의 아키텍처 접미사가 있는 경우 파일 이름을 **Microsoft-Azure-IoTEdge.cab**으로 바꿉니다.

4. 필요에 따라 Visual C++ 재배포 가능 패키지에 대 한 설치 관리자를 다운로드 합니다. 예를 들어 PowerShell 스크립트는 [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe)버전을 사용 합니다. 설치 관리자를 IoT 장치의 동일한 폴더에 IoT Edge 파일로 저장 합니다.

5. 오프 라인 구성 요소를 사용 하 여 설치 하려면 [도트 원본](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) 에 PowerShell 스크립트의 로컬 복사본을 사용 합니다. 

6. 매개 변수를 사용 하 여 [배포-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 명령을 실행 합니다 `-OfflineInstallationPath` . 파일 디렉터리에 대 한 절대 경로를 제공 합니다. 예를 들면 다음과 같습니다.

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   배포 명령은 제공 된 로컬 파일 디렉터리에 있는 모든 구성 요소를 사용 합니다. .Cab 파일 또는 Visual C++ 설치 관리자가 없으면 다운로드를 시도 합니다.

---

컨테이너 엔진과 IoT Edge 런타임이 장치에 설치 되었으므로 다음 단계를 수행할 준비가 되었습니다 .이 단계에서는 [IoT Hub에서 IoT Edge 장치를 인증](how-to-manual-provision-symmetric-key.md)합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

장치에서 IoT Edge 설치를 제거 하려면 다음 명령을 사용 합니다.

# <a name="linux"></a>[Linux](#tab/linux)

IoT Edge 런타임을 제거합니다.

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge 런타임을 제거 하면 만든 컨테이너가 모두 중지 되지만 장치에는 계속 존재 합니다. 모든 컨테이너를 보고 남아 있는 항목을 확인합니다.

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

# <a name="windows"></a>[Windows](#tab/windows)

Windows 장치에서 IoT Edge 설치를 제거 하려면 관리 PowerShell 창에서 [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) 명령을 사용 합니다. 이 명령은 기존 구성 및 Moby 엔진 데이터와 함께 IoT Edge 런타임을 제거합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

제거 옵션에 대 한 자세한 내용을 보려면 명령을 사용 `Get-Help Uninstall-IoTEdge -full` 하세요.

---

## <a name="next-steps"></a>다음 단계

IoT Edge 런타임을 설치한 후 IoT Hub에 연결 하도록 장치를 구성 합니다. 다음 문서에서는 클라우드에서 새 장치를 등록 한 다음 해당 id 및 인증 정보를 사용 하 여 장치를 제공 하는 과정을 안내 합니다.

사용 하려는 인증 유형에 따라 다음 문서를 선택 합니다.

* **대칭 키**: IoT Hub 및 IoT Edge 장치에는 모두 보안 키의 복사본이 있습니다. 장치가 IoT Hub에 연결 되 면 키가 일치 하는지 확인 합니다. 이 인증 방법은 시작 하기 빠르지만 안전 하지 않습니다.

  [대칭 키 인증을 사용 하 여 Azure IoT Edge 장치 설정](how-to-manual-provision-symmetric-key.md)

* **X.509 자체 서명**: IoT Edge 장치에는 x.509 id 인증서가 있고 IoT Hub에는 인증서 지문이 제공 됩니다. 장치가 IoT Hub에 연결 되 면 해당 지문과 인증서를 비교 합니다. 이 인증 방법은 더 안전 하며 프로덕션 시나리오에 권장 됩니다.

  [X.509 인증서 인증을 사용 하 여 Azure IoT Edge 장치 설정](how-to-manual-provision-x509.md)