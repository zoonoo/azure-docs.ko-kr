---
title: DPS를 사용하여 Azure IoT Edge 장치 자동 프로비전 - Windows | Microsoft Docs
description: Windows 컴퓨터에서 시뮬레이트된 장치를 사용하여 Device Provisioning Service로 Azure IoT Edge에 대한 자동 장치 프로비전 테스트
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e149886e1ade80d7751f58eb1f77031c4e432b75
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307946"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Windows에서 시뮬레이션된 TPM 에지 장치 만들기 및 프로비전

Edge를 사용하지 않는 장치와 마찬가지로 [Device Provisioning Service](../iot-dps/index.yml)를 사용하여 Azure IoT Edge 장치를 자동 프로비전할 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](../iot-dps/concepts-auto-provisioning.md)을 검토하세요. 

이 문서에서는 다음 단계를 사용하여 시뮬레이션된 Edge 장치에서 자동 프로비전을 테스트하는 방법을 보여줍니다. 

* IoT Hub DPS(Device Provisioning Service)의 인스턴스를 만듭니다.
* 하드웨어 보안을 위해 시뮬레이트된 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Windows 컴퓨터에서 시뮬레이트된 장치를 만듭니다.
* 장치에 대한 개별 등록을 만듭니다.
* IoT Edge 런타임을 설치하고 IoT Hub에 장치를 연결합니다.

## <a name="prerequisites"></a>필수 조건

* Windows 개발 컴퓨터. 이 문서에서는 Windows 10을 사용합니다. 
* 활성 IoT Hub 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 설정

Azure에서 IoT Hub Device Provisioning Service의 새 인스턴스를 만들어 IoT hub에 연결합니다. [IoT Hub DPS 설정](../iot-dps/quick-setup-auto-provision.md) 지침을 따르면 됩니다.

Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다. 

## <a name="simulate-a-tpm-device"></a>TPM 장치 시뮬레이션

Windows 개발 컴퓨터에서 시뮬레이트된 TPM 장치를 만듭니다. 장치의 **등록 ID** 및 **인증 키**를 검색한 후 DPS에서 개별 등록 항목을 만드는 데 사용합니다. 

DPS에서 등록을 만들 때 **초기 장치 쌍 상태**를 선언할 기회가 있습니다. 장치 쌍에서 지역, 환경, 위치 또는 장치 유형 같은 솔루션에 필요한 모든 메트릭을 기준으로 장치 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-monitor.md)를 만드는 데 사용됩니다. 

시뮬레이트된 장치를 만드는 데 사용할 SDK 언어를 선택하고 개별 등록을 만들 때까지 단계를 진행합니다. 

개별 등록을 만들 때 **사용**을 선택하여 이 가상 머신을 **IoT Edge 장치**로 선언합니다.

시뮬레이트된 장치 및 개별 등록 가이드: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

개별 등록을 만든 후 **등록 ID** 값을 저장합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다. 

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 장치에 추가 컨테이너의 배포를 허용합니다. Windows를 실행하는 장치에서 Windows 컨테이너 또는 Linux 컨테이너를 사용하도록 선택할 수 있습니다. 사용하려는 컨테이너의 유형을 선택하고 단계를 수행합니다. 수동이 아닌 자동 프로비전에 대한 IoT Edge 런타임을 구성해야 합니다. 

지침에 따라 이전 섹션에서 시뮬레이트된 TPM이 실행되는 장치에서 IoT Edge 런타임을 설치합니다. 

이러한 문서를 시작하기 전에 DPS **ID 범위** 및 장치 **등록 ID**를 알아야 합니다. 

* [Windows 컨테이너](how-to-install-iot-edge-windows-with-windows.md)
* [Linux 컨테이너](how-to-install-iot-edge-windows-with-linux.md)

## <a name="create-a-tpm-environment-variable"></a>TPM 환경 변수 만들기

시뮬레이트된 장치를 실행하는 컴퓨터에서 **iotedge** 서비스 레지스트리를 수정하여 환경 변수를 설정합니다.

1. **시작** 메뉴에서 **regedit**을 엽니다. 
2. **컴퓨터\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\iotedge**로 이동합니다. 
3. **편집** > **새로 만들기** > **다중 문자열 값**을 선택합니다. 
4. 이름 **Environment**를 입력합니다. 
5. 새 변수를 두 번 클릭하고 값 데이터를 **IOTEDGE_USE_TPM_DEVICE=ON**으로 설정합니다. 
6. **확인**을 클릭하여 변경 내용을 저장합니다. 

## <a name="restart-the-iot-edge-runtime"></a>IoT Edge 런타임 다시 시작

장치에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다. 

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작된 경우 IoT Hub로 이동하여 새 장치가 자동으로 프로비전되고 IoT Edge 모듈을 실행할 준비가 되었는지 확인할 수 있습니다. 

IoT Edge 서비스의 상태를 확인합니다.

```powershell
Get-Service iotedge
```

최근 5분 간의 서비스 로그를 검사합니다.

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

실행 중인 모듈을 나열합니다.

```powershell
iotedge list
```

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 장치를 프로비전할 때 장치 ID 및 장치 쌍 태그를 동시에 설정할 수 있습니다. 자동 장치 관리를 사용하여 개별 장치 또는 장치 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-monitor-cli.md)하거나 [Azure Portal을 사용하여 대규모 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-monitor.md)하는 방법을 알아봅니다.