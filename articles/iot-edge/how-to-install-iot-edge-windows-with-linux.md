---
title: Windows에서 Linux 용 Azure IoT Edge 설치 | Microsoft Docs
description: Windows 10, Windows Server 및 Windows IoT Core에 Linux 컨테이너에 대 한 azure IoT Edge 설치 지침
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: bb47a1b828084673961a6d2c5657793b4437f294
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160577"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Windows IoT Edge에서 Linux 컨테이너 실행

Windows에서 Linux 장치용 IoT Edge 모듈을 테스트합니다. 

프로덕션 시나리오에서 Windows 장치는 Windows 컨테이너만 실행해야 합니다. 하지만, Windows에서 Linux 장치용 IoT Edge 모듈을 개발하는 것이 일반적입니다. IoT Edge Runtime for Windows를 통해 **테스트 및 개발** 을 위한 Linux Container를 사용할 수 있습니다. 

이 문서에서는 Windows x64 (Intel/AMD) 시스템에 Linux 컨테이너를 사용하여 Azure IoT Edge 런타임을 설치하는 단계를 나열합니다. 설치 매개 변수에 대한 세부 정보를 포함하는 IoT Edge 런타임 설치 관리자에 대한 자세한 내용은 [Windows에 Azure IoT Edge 런타임 설치](how-to-install-iot-edge-windows.md)를 참조합니다.

## <a name="prerequisites"></a>필수 조건

이 섹션을 사용하여 Windows 디바이스에서 IoT Edge를 지원할 수 있는지 여부를 검토하고 설치 전에 컨테이너 엔진에 대해 준비합니다. 

### <a name="supported-windows-versions"></a>지원되는 Windows 버전

Linux 컨테이너를 사용 하 여 azure IoT Edge는 Windows의 다음 버전에서 실행할 수 있습니다. 
* Windows 10 1주년 업데이트(빌드 14393) 이상
* Windows Server 2016 이상

최신 버전의 IoT Edge에 포함된 기능에 대한 자세한 내용은 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조하세요.

가상 컴퓨터에 IoT Edge를 설치하려면 중첩 가상화를 활성화하고 2GB 이상의 메모리를 할당합니다. 중첩 된 가상화를 사용 하는 방법에 사용 하 여 하이퍼바이저에 따라 달라 집니다. Hyper-V의 경우 2 세대 가상 컴퓨터는 기본적으로 중첩된 가상화를 사용할 수 있습니다. VMWare의 경우 가상 컴퓨터에 이 기능을 사용하도록 설정/해제할 수 있습니다. 

### <a name="prepare-the-container-engine"></a>컨테이너 엔진 준비 

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 엔진을 사용합니다. Windows 시스템에서 Windows 컨테이너와 Linux 컨테이너를 실행하는 가장 큰 차이점은 IoT Edge 설치에는 Windows 컨테이너 런타임이 포함되지만 IoT Edge를 설치하기 전에 Linux 컨테이너에 대한 자체 런타임을 제공해야 한다는 것입니다. 

Linux 장치용 컨테이너를 개발하고 테스트하기 위해 Windows 시스템을 설정하려면 [Docker 데스크톱](https://www.docker.com/docker-windows) 을 컨테이너 엔진으로 사용할 수 있습니다. IoT Edge를 설치하기 전에 Docker를 설치하고 [Linux 컨테이너](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) 를 사용하도록 구성해야 합니다.  

IoT Edge 디바이스가 Windows 컴퓨터인 경우 Hyper-V에 대한 [시스템 요구 사항](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)을 충족하는지 확인합니다.

## <a name="install-iot-edge-on-a-new-device"></a>새 디바이스에 IoT Edge 설치

>[!NOTE]
>Azure IoT Edge 소프트웨어 패키지에는 패키지(LICENSE 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보시기 바랍니다. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

PowerShell 스크립트가 Azure IoT Edge 보안 디먼을 다운로드하여 설치합니다. 그런 다음, 보안 디먼이 다른 모듈의 원격 배포를 가능하게 하는 두 가지 런타임 모듈 중 첫 번째인 IoT Edge 에이전트를 시작합니다. 

디바이스에 IoT Edge 런타임을 처음 설치하는 경우 IoT 허브의 ID를 사용하여 디바이스를 프로비전해야 합니다. 단일 IoT Edge 장치를 IoT hub에서 제공 하는 장치 연결 문자열을 사용 하 여 수동으로 프로 비전 할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 디바이스를 프로비전할 수 있습니다. 이 기능은 많은 디바이스를 설정해야 하는 경우에 유용합니다. 

자세한 내용은 여러 설치 옵션 및 매개 변수는 문서에서에 대 한 [Windows에 Azure IoT Edge 런타임을 설치](how-to-install-iot-edge-windows.md)합니다. 기본 설치 차이 사용 하 여 Linux를 선언 하 고 Docker 데스크톱 설치 하 고 Linux 컨테이너에 대 한 구성 했으면 합니다 **-ContainerOs** 매개 변수입니다. 예를 들면 다음과 같습니다. 

1. 아직 없는 경우, 새 IoT Edge 장치를 등록 하 고 장치 연결 문자열을 검색 합니다. 이 섹션에서 나중에 사용 하려면 연결 문자열을 복사 합니다. 다음 도구를 사용 하 여이 단계를 완료할 수 있습니다.

   * [Azure Portal](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. PowerShell을 관리자 권한으로 실행합니다.

3. 합니다 **배포 IoTEdge** 명령 인지 확인 하 Windows 컴퓨터에서 지원 되는 버전, 컨테이너 기능을 켭니다 (Linux 컨테이너에 사용 됨)는 모 비 런타임 및 IoT Edge 런타임에서 다운로드 합니다. 따라서 명령에 대 한 기본값으로 Windows 컨테이너는 원하는 컨테이너 운영 체제로 Linux를 선언 합니다. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. 이 시점에서 IoT Core 장치 수 자동으로 다시 시작 합니다. 다른 Windows 10 또는 Windows Server 장치에서 다시 시작할 것인지 묻는 메시지를 표시할 수 있습니다. 그렇다면 이제 장치 다시 시작 합니다. 일단 장치가 준비 되 면 관리자 권한으로 PowerShell을 다시 실행 합니다.

5. 합니다 **Initialize IoTEdge** 명령은 컴퓨터에서 IoT Edge 런타임을 구성 합니다. 이 명령은 장치 연결 문자열을 사용 하 여 수동 프로 비전 기본값으로 사용 됩니다. 원하는 컨테이너 운영 체제로 Linux를 다시 선언 합니다. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. 메시지가 표시되면 1단계에서 검색된 장치 연결 문자열을 제공합니다. 장치 연결 문자열은 물리적 장치를 IoT Hub의 장치 ID와 연결합니다. 

   장치 연결 문자열을 다음 형식으로 및 따옴표를 포함 하지 않아야 합니다. `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>성공적인 설치 확인

IoT Edge 서비스의 상태를 확인합니다. 실행 중으로 표시되어야 합니다.  

```powershell
Get-Service iotedge
```

최근 5분 간의 서비스 로그를 검사합니다. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

실행 중인 모듈을 나열합니다. 새로 설치를 실행 하는 것을 표시만 모듈 뒤 **edge 에이전트**합니다. 한 후 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md), 다른 사용자가 표시 됩니다. 

```powershell
iotedge list
```

## <a name="next-steps"></a>다음 단계

런타임을 설치하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge를 제대로 설치하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인합니다.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
