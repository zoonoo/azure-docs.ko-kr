---
title: DPS를 사용하여 Azure IoT Edge 장치 자동 프로비전 - Linux | Microsoft Docs
description: Linux VM에서 시뮬레이션된 TPM을 사용하여 Azure IoT Edge에 대한 장치 프로 비전 테스트
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6d0d2adfb4a727ec93db6d44e6a3e8f923760b91
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739671"
---
# <a name="create-and-provision-an-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Linux 가상 머신에서 가상 TPM을 사용하여 Edge 장치 만들기 및 프로비전

Edge를 사용하지 않는 장치와 마찬가지로 [Device Provisioning Service](../iot-dps/index.yml)를 사용하여 Azure IoT Edge 장치를 자동 프로비전할 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](../iot-dps/concepts-auto-provisioning.md)을 검토하세요. 

이 문서에서는 다음 단계를 사용하여 시뮬레이션된 Edge 장치에서 자동 프로비전을 테스트하는 방법을 보여줍니다. 

* 하드웨어 보안을 위해 시뮬레이션된 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Hyper-v에서 Linux 가상 머신(VM)을 만듭니다.
* IoT Hub DPS(Device Provisioning Service)의 인스턴스를 만듭니다.
* 장치에 대한 개별 등록 만들기
* IoT Edge 런타임 설치 및 IoT Hub에 장치 연결

이 문서의 단계는 테스트 목적으로 사용됩니다.

## <a name="prerequisites"></a>필수 조건

* [Hyper-V를 사용하도록 설정된](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 개발 컴퓨터. 이 문서에서는 Ubuntu Server VM을 실행하는 Windows 10을 사용합니다. 
* 활성 IoT Hub. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>가상 TPM을 사용하여 Linux 가상 머신 만들기

이 섹션에서는 IoT Edge를 사용한 자동 프로비전 작동 방식을 테스트하는 데 사용할 수 있도록 시뮬레이션된 TPM이 있는 Hyper-V에서 새 Linux 가상 머신을 만듭니다. 

### <a name="create-a-virtual-switch"></a>가상 스위치 만들기

가상 스위치를 사용하면 가상 머신을 실제 네트워크에 연결할 수 있습니다.

1. Windows 컴퓨터에서 Hyper-V를 엽니다. 

2. **작업** 메뉴에서 **가상 스위치 관리자**를 선택합니다. 

3. **외부** 가상 스위치를 선택한 다음, **가상 스위치 만들기**를 선택합니다. 

4. 새 가상 스위치에 이름을 지정합니다(예를 들어 **EdgeSwitch**). 연결 형식이 **외부 네트워크**에 설정되어 있는지 확인한 다음, **확인**을 선택합니다.

5. 팝업은 네트워크 연결이 중단될 수 있다는 경고 메시지를 표시합니다. **예**를 선택하여 계속합니다. 

새 가상 스위치를 만드는 동안 오류가 표시되는 경우 다른 스위치가 이더넷 어댑터를 사용하고 있지 않은지 그리고 동일한 이름을 사용하지 않는지 확인합니다. 

### <a name="create-virtual-machine"></a>가상 머신 만들기

1. 가상 머신에 사용하기 위해 디스크 이미지 파일을 다운로드하고 로컬로 저장합니다. 예를 들어 [Ubuntu server](https://www.ubuntu.com/download/server). 

2. Hyper-V를 다시 엽니다. **작업** 메뉴에서 **새로 만들기** > **Virtual Machine**을 선택합니다.

3. 다음 특정 구성으로 **새 Virtual Machine 마법사**를 완료합니다.

   1. **세대 지정**: **2세대**를 선택합니다.
   2. **네트워킹 구성**: 이전 섹션에서 만든 가상 스위치에 **연결** 값을 설정합니다. 
   3. **설치 옵션**: **부팅 가능한 이미지 파일에서 운영 체제 설치**를 선택하고, 로컬에 저장한 디스크 이미지 파일을 찾습니다.

새 VM을 만드는 데 몇 분이 걸릴 수 있습니다. 

### <a name="enable-virtual-tpm"></a>가상 TPM 사용

1. VM이 만들어지면 해당 설정을 엽니다. 
2. **보안**으로 이동합니다. 
3. **보안 부팅 사용**을 선택 취소합니다.
4. **신뢰할 수 있는 플랫폼 모듈 사용**을 확인합니다. 
5. **확인**을 클릭합니다.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>가상 머신 시작 및 TPM 데이터 수집

가상 머신에서 장치의 **등록 ID** 및 **인증 키**를 검색하는 데 사용할 수 있는 C SDK 도구를 빌드합니다. 

1. VM을 시작하고 연결하여 설치 프로세스를 완료합니다. 

2. VM에서 [Linux 개발 환경 설정](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)의 단계를 C에 대한 Azure IoT 장치 SDK를 설치 및 빌드합니다. 

3. 장치 프로비전 정보를 검색하는 C SDK 도구를 빌드하려면 다음 명령을 실행합니다. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

3. **등록 ID** 및 **인증 키**의 값을 복사합니다. 이러한 값을 사용하여 DPS에서 장치에 대한 개별 등록을 만듭니다. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 설정

Azure에서 IoT Hub Device Provisioning Service의 새 인스턴스를 만들어 IoT hub에 연결합니다. [IoT Hub DPS 설정](../iot-dps/quick-setup-auto-provision.md) 지침을 따르면 됩니다.

Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다. 

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

가상 머신에서 프로비전 정보를 검색하고 이를 사용하여 Device Provisioning Service에서 개별 등록을 만듭니다. 

DPS에서 등록을 만들 때 **초기 장치 쌍 상태**를 선언할 기회가 있습니다. 장치 쌍에서 지역, 환경, 위치 또는 장치 유형 같은 솔루션에 필요한 모든 메트릭을 기준으로 장치 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-monitor.md)를 만드는 데 사용됩니다. 


1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다. 

2. **설정**에서 **등록 관리**를 선택합니다. 

3. 등록을 구성하려면 **개별 등록 추가**를 선택한 다음, 다음 단계를 완료합니다.  

   1. **메커니즘**의 경우 **TPM**을 선택합니다. 
   2. 가상 머신에서 복사한 **인증 키** 및 **등록 ID**를 삽입합니다.
   3. **사용**을 선택하여 이 가상 머신이 IoT Edge 장치임을 선언합니다. 
   4. 장치를 연결하려는 링크된 **IoT Hub**를 선택합니다. 
   5. 원하는 경우 장치에 대한 ID를 제공합니다. 장치 ID를 사용하여 모듈 배포에 대한 개별 장치를 대상으로 할 수 있습니다. 
   6. 원하는 경우 **초기 장치 쌍 상태**에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 장치 그룹을 대상으로 할 수 있습니다. 
   7. **저장**을 선택합니다. 


## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 장치에 추가 컨테이너의 배포를 허용합니다. IoT Edge 런타임을 가상 머신에 설치합니다. 

장치 유형과 일치하는 문서를 시작하기 전에 DPS **ID 범위** 및 장치 **등록 ID**를 알아야 합니다. 예제 Ubuntu 서버를 설치했으면 **x64** 지침을 사용합니다. 수동이 아닌 자동 프로비전에 대한 IoT Edge 런타임을 구성해야 합니다. 

* [Linux(x64)](how-to-install-iot-edge-linux.md)
* [Linux(ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>TPM에 대한 IoT Edge 액세스 권한 부여

IoT Edge 런타임이 장치를 자동으로 프로비전하려면 TPM에 대한 액세스가 필요합니다. 

TPM 액세스 권한을 부여하려면 다음 단계를 사용합니다. 또는 *iotedge* 서비스가 루트로 실행될 수 있도록 systemd 설정을 재정의하여 동일한 작업을 완수할 수 있습니다. 

1. 장치에서 TPM 하드웨어 모듈에 대한 파일 경로를 찾아 로컬 변수로 저장합니다. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. tpm0에 IoT Edge 런타임 액세스 권한을 부여할 새 규칙을 만듭니다. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. 규칙 파일을 엽니다. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. 규칙 파일에 다음과 같은 액세스 정보를 복사합니다. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. 파일을 저장하고 종료합니다. 

6. 새 규칙을 평가하려면 udev 시스템을 트리거합니다. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. 규칙이 성공적으로 적용됐는지 확인합니다.

   ```bash
   ls -l /dev/tpm0
   ```

   출력이 성공하면 다음과 같이 표시됩니다.

   ```output
   crw------- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

8. 파일을 재정의하는 IoT Edge 런타임을 엽니다. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. TPM 환경 변수를 설정하려면 다음 코드를 추가합니다.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

9. 재정의가 성공적으로 수행되었는지 확인합니다.

   ```bash
   sudo systemctl cat iotedge.service
   ```

   출력이 성공하면 **iotedge** 기본 서비스 변수를 표시한 다음, **override.conf**에서 설정한 환경 변수를 보여줍니다. 

12. 설정을 다시 로드합니다.

   ```bash
   sudo systemctl daemon-reload
   ```

## <a name="restart-the-iot-edge-runtime"></a>IoT Edge 런타임 다시 시작

장치에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다. 

   ```bash
   sudo systemctl restart iotedge
   ```

IoT Edge 런타임이 실행되고 있는지 확인합니다. 

   ```bash
   sudo systemctl status iotedge
   ```

프로비전 오류가 표시되면 구성 변경 내용이 아직 적용되지 않았을 수 있습니다. IoT Edge 디먼을 다시 시작합니다. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
또는 가상 머신을 다시 시작하여 변경 내용이 새로 시작 시 적용되는지 확인합니다. 

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작된 경우 IoT Hub로 이동하여 새 장치가 자동으로 프로비전되고 IoT Edge 모듈을 실행할 준비가 되었는지 확인할 수 있습니다. 

IoT Edge 디먼의 상태를 확인합니다.

```cmd/sh
systemctl status iotedge
```

디먼 로그를 검사합니다.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

실행 중인 모듈을 나열합니다.

```cmd/sh
iotedge list
```


## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 장치를 프로비전할 때 장치 ID 및 장치 쌍 태그를 동시에 설정할 수 있습니다. 자동 장치 관리를 사용하여 개별 장치 또는 장치 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-monitor-cli.md)하거나 [Azure Portal을 사용하여 대규모 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-monitor.md)하는 방법을 알아봅니다.
