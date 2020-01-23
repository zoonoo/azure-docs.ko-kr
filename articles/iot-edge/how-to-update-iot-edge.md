---
title: 디바이스에서 IoT Edge 버전 업데이트 - Azure IoT Edge | Microsoft Docs
description: 최신 버전의 보안 디먼 및 IoT Edge 런타임을 실행하도록 IoT Edge 디바이스를 업데이트하는 방법
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f31dec1d654124f9071c01c0e5d033db4b65a1d3
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509668"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>IoT Edge 보안 디먼 및 런타임 업데이트

IoT Edge 서비스는 새 버전을 릴리스할 때 최신 기능과 보안 기능 향상을 위해 IoT Edge 장치를 업데이트 하려고 합니다. 이 문서에서는 새 버전을 사용할 수 있을 때 IoT Edge 디바이스를 업데이트하는 방법에 대한 정보를 제공합니다.

새 버전으로 전환하려면 IoT Edge 디바이스의 두 가지 구성 요소를 업데이트해야 합니다. 첫 번째는 디바이스에서 실행되고 디바이스가 시작될 때 런타임 모듈을 시작하는 보안 디먼입니다. 현재, 보안 디먼은 디바이스 자체에서만 업데이트할 수 있습니다. 두 번째 구성 요소는 런타임으로, IoT Edge 허브 및 IoT Edge 에이전트 모듈로 구성됩니다. 배포 구성 방법에 따라 디바이스에서 또는 원격으로 런타임을 업데이트할 수 있습니다.

최신 버전의 Azure IoT Edge를 찾으려면 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조하세요.

## <a name="update-the-security-daemon"></a>보안 디먼 업데이트

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 패키지 관리자를 사용하여 업데이트해야 하는 네이티브 구성 요소입니다.

`iotedge version` 명령을 사용하여 디바이스에서 실행 중인 보안 디먼의 버전을 확인합니다.

### <a name="linux-devices"></a>Linux 디바이스

Linux x64 장치에서 apt-get 또는 적절 한 패키지 관리자를 사용 하 여 보안 데몬을 업데이트 합니다.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Linux ARM32 장치에서 [linux에 Azure IoT Edge 런타임 설치 (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) 의 단계를 사용 하 여 최신 버전의 보안 디먼을 설치 합니다.

### <a name="windows-devices"></a>Windows 디바이스

Windows 장치에서 PowerShell 스크립트를 사용 하 여 보안 디먼을 업데이트 합니다. 이 스크립트는 보안 디먼의 최신 버전을 자동으로 가져옵니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

업데이트-IoTEdge 명령을 실행 하면 두 개의 런타임 컨테이너 이미지와 함께 장치에서 보안 데몬이 제거 되 고 업데이트 됩니다. Config.xml 파일은 Windows 컨테이너를 사용 하는 경우 Moby 컨테이너 엔진의 데이터 뿐만 아니라 장치에도 유지 됩니다. 구성 정보를 유지 하는 것은 업데이트 프로세스 중에 장치에 대 한 연결 문자열 또는 장치 프로 비전 서비스 정보를 다시 제공할 필요가 없음을 의미 합니다.

특정 버전의 보안 디먼을 설치 하려는 경우 [IoT Edge 릴리스에서](https://github.com/Azure/azure-iotedge/releases)적절 한 Microsoft-Azure-IoTEdge 파일을 다운로드 합니다. 그런 다음, `-OfflineInstallationPath` 매개 변수를 사용하여 파일 위치를 가리킵니다. 자세한 내용은 [오프라인 설치](how-to-install-iot-edge-windows.md#offline-installation)를 참조하세요.

## <a name="update-the-runtime-containers"></a>런타임 컨테이너 업데이트

IoT Edge 에이전트 및 IoT Edge 허브 컨테이너를 업데이트 하는 방법은 배포에서 롤링 태그 (예: 1.0) 또는 특정 태그 (예: 1.0.7)를 사용 하는지에 따라 달라 집니다.

`iotedge logs edgeAgent` 또는 `iotedge logs edgeHub` 명령을 사용하여 현재 사용 중인 디바이스에 있는 IoT Edge 에이전트 및 IoT Edge 허브 모듈 버전을 확인합니다.

  ![로그의 컨테이너 버전 찾기](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge 태그 이해

IoT Edge 에이전트 및 IoT Edge 허브 이미지에는 연결된 IoT Edge 버전으로 태그가 지정됩니다. 런타임 이미지에 태그를 사용하는 방법에는 다음 두 가지가 있습니다.

* **롤링 태그** - 버전 번호의 처음 두 개 값만 사용하여 해당 숫자와 일치하는 최신 이미지를 가져옵니다. 예를 들어, 최신 1.0.x 버전을 가리키는 새 릴리스가 있을 때마다 1.0이 업데이트됩니다. IoT Edge 디바이스의 컨테이너 런타임이 이미지를 다시 끌어오면 런타임 모듈은 최신 버전으로 업데이트됩니다. 이 접근 방법은 개발 목적으로 제안됩니다. Azure Portal에서 배포할 때는 기본적으로 롤링 태그가 사용됩니다.

* **특정 태그** - 버전 번호의 세 값을 모두 사용하여 이미지 버전을 명시적으로 설정합니다. 예를 들어 1.0.7는 초기 릴리스 후에 변경 되지 않습니다. 업데이트할 준비가 되면 배포 매니페스트에서 새 버전 번호를 선언할 수 있습니다. 이 접근 방법은 프로덕션 목적으로 제안됩니다.

### <a name="update-a-rolling-tag-image"></a>롤링 태그 이미지 업데이트

배포에 롤링 태그를 사용하는 경우(예: mcr.microsoft.com/azureiotedge-hub:  **1.0**) 디바이스의 컨테이너 런타임이 강제로 최신 버전의 이미지를 가져오도록 해야 합니다.

IoT Edge 디바이스에서 이미지의 로컬 버전을 삭제합니다. Windows 머신에서 보안 디먼을 제거하면 런타임 이미지도 제거되므로 이 단계를 다시 수행할 필요가 없습니다.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

이미지를 제거하기 위해 강제 `-f` 플래그를 사용해야 할 수 있습니다.

IoT Edge 서비스는 최신 버전의 런타임 이미지를 끌어오고 해당 이미지를 디바이스에서 자동으로 다시 시작합니다.

### <a name="update-a-specific-tag-image"></a>특정 태그 이미지 업데이트

배포에서 특정 태그를 사용 하는 경우 (예: mcr.microsoft.com/azureiotedge-hub:**1.0.8**), 배포 매니페스트의 태그를 업데이트 하 고 장치에 변경 내용을 적용 하기만 하면 됩니다.

1. Azure Portal의 IoT Hub에서 IoT Edge 장치를 선택 하 고 **모듈 설정**을 선택 합니다.

1. **IoT Edge 모듈** 섹션에서 **런타임 설정**을 선택 합니다.

   ![런타임 설정 구성](./media/how-to-update-iot-edge/configure-runtime.png)

1. **런타임 설정**에서 원하는 버전으로 **Edge Hub** 에 대 한 **이미지** 값을 업데이트 합니다. 아직 **저장** 안 함을 선택 합니다.

   ![Edge 허브 이미지 버전 업데이트](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. **Edge 허브** 설정을 축소 하거나 아래로 스크롤하고 **edge 에이전트** 에 대 한 **이미지** 값을 원하는 버전으로 업데이트 합니다.

   ![Edge 허브 에이전트 버전 업데이트](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. **저장**을 선택합니다.

1. **검토 + 만들기**를 선택 하 고 배포를 검토 한 다음 **만들기**를 선택 합니다.

## <a name="update-to-a-release-candidate-version"></a>릴리스 후보 버전으로 업데이트

Azure IoT Edge은 IoT Edge 서비스의 새 버전을 정기적으로 릴리스 합니다. 안정적인 각 릴리스 전에는 RC (릴리스 후보) 버전이 하나 이상 있습니다. RC 버전은 릴리스에 대해 계획 된 모든 기능을 포함 하지만 안정적인 릴리스에 필요한 테스트 및 유효성 검사 프로세스를 계속 진행 하 고 있습니다. 초기에 새 기능을 테스트 하려는 경우 RC 버전을 설치 하 고 GitHub를 통해 피드백을 제공할 수 있습니다.

릴리스 후보 버전은 릴리스의 동일한 번호 매기기 규칙을 따르고 끝에 **-rc** 와 증분 숫자가 추가 됩니다. 안정적인 버전과 동일한 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases) 목록에서 릴리스 후보를 확인할 수 있습니다. 예를 들어 **1.0.7**이전에 제공 된 두 개의 릴리스 후보로 **1.0.7-rc1** 및 **1.0.7**를 찾습니다. RC 버전이 **시험판** 레이블로 표시 된 것을 확인할 수도 있습니다.

미리 보기로 제공 되는 릴리스 후보 버전은 일반 설치 관리자가 대상으로 하는 최신 버전으로 포함 되지 않습니다. 대신 테스트 하려는 RC 버전의 자산을 수동으로 대상으로 해야 합니다. IoT Edge 장치 운영 체제에 따라 다음 섹션을 사용 하 여 특정 버전으로 IoT Edge를 업데이트 합니다.

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>다음 단계

최신 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 확인합니다.

[IoT(사물 인터넷) 블로그](https://azure.microsoft.com/blog/topics/internet-of-things/)에서 최신 업데이트 및 알림을 받아 보세요.
