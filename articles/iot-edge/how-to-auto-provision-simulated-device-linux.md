---
title: 리눅스 VM에 가상 TPM을 가진 장치 프로비저닝 - Azure IoT 가장자리
description: Linux VM에서 시뮬레이션된 TPM을 사용하여 Azure IoT Edge에 대한 Azure Device Provisioning Service 테스트
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511062"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Linux 가상 컴퓨터에서 가상 TPM을 사용하여 IoT Edge 장치 생성 및 프로비전

Azure IoT Edge 장치는 [장치 프로비전 서비스를](../iot-dps/index.yml)사용하여 자동으로 프로비저닝할 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](../iot-dps/concepts-auto-provisioning.md)을 검토하세요.

이 문서에서는 다음 단계를 사용하여 시뮬레이션된 IoT Edge 장치에서 자동 프로비전을 테스트하는 방법을 보여 주며 다음과 같은 단계를 수행합니다.

* 하드웨어 보안을 위해 시뮬레이션된 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Hyper-v에서 Linux 가상 머신(VM)을 만듭니다.
* IoT Hub DPS(Device Provisioning Service)의 인스턴스를 만듭니다.
* 디바이스에 대한 개별 등록 만들기
* IoT Edge 런타임 설치 및 IoT Hub에 디바이스 연결

> [!TIP]
> 이 문서에서는 TPM 시뮬레이터를 사용하여 DPS 프로비저닝을 테스트하는 방법을 설명하지만 대부분은 IoT 장치에 대한 Azure 인증 장치인 [Infineon&trade; OPTIGA TPM과](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)같은 물리적 TPM 하드웨어에 적용됩니다.
>
> 실제 장치를 사용하는 경우 이 문서의 실제 장치 섹션에서 [프로비저닝 정보 검색으로](#retrieve-provisioning-information-from-a-physical-device) 건너뛸 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Hyper-V를 사용하도록 설정된](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 개발 컴퓨터. 이 문서에서는 Ubuntu Server VM을 실행하는 Windows 10을 사용합니다.
* 활성 IoT Hub
* 시뮬레이션된 TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 이상을 ['C++가 있는 데스크톱 개발' 워크로드를](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 사용하도록 설정한 경우.

> [!NOTE]
> TPM 2.0은 DPS와 함께 TPM 증명을 사용할 때 필요하며 그룹이 아닌 개별 등록을 만드는 데만 사용할 수 있습니다.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>가상 TPM을 사용하여 Linux 가상 머신 만들기

이 섹션에서는 Hyper-V에서 새 Linux 가상 컴퓨터를 만듭니다. IoT Edge에서 자동 프로비저닝이 어떻게 작동하는지 테스트하는 데 사용할 수 있도록 시뮬레이션된 TPM으로 이 가상 컴퓨터를 구성했습니다.

### <a name="create-a-virtual-switch"></a>가상 스위치 만들기

가상 스위치를 사용하면 가상 머신을 실제 네트워크에 연결할 수 있습니다.

1. Windows 컴퓨터에서 하이퍼 V 관리자를 엽니다.

2. **작업** 메뉴에서 **가상 스위치 관리자**를 선택합니다.

3. **외부** 가상 스위치를 선택한 다음, **가상 스위치 만들기**를 선택합니다.

4. 새 가상 스위치에 이름을 지정합니다(예를 들어 **EdgeSwitch**). 연결 형식이 **외부 네트워크**에 설정되어 있는지 확인한 다음, **확인**을 선택합니다.

5. 팝업은 네트워크 연결이 중단될 수 있다는 경고 메시지를 표시합니다. **예**를 선택하여 계속합니다.

새 가상 스위치를 만드는 동안 오류가 표시되는 경우 다른 스위치가 이더넷 어댑터를 사용하고 있지 않은지 그리고 동일한 이름을 사용하지 않는지 확인합니다.

### <a name="create-virtual-machine"></a>가상 머신 만들기

1. 가상 머신에 사용하기 위해 디스크 이미지 파일을 다운로드하고 로컬로 저장합니다. 예를 들어 [Ubuntu server](https://www.ubuntu.com/download/server).

2. 하이퍼-V 관리자에서 다시 **작업** 메뉴에서 **새** > **가상 컴퓨터를** 선택합니다.

3. 다음 특정 구성으로 **새 Virtual Machine 마법사**를 완료합니다.

   1. **세대 지정**: **2세대**를 선택합니다. 2세대 가상 머신에는 가상 화가 활성화되어 있으며, 가상 머신에서 IoT Edge를 실행하는 데 필요합니다.
   2. **네트워킹 구성**: 이전 섹션에서 만든 가상 스위치에 **연결** 값을 설정합니다.
   3. **설치 옵션**: **부팅 가능한 이미지 파일에서 운영 체제 설치**를 선택하고, 로컬에 저장한 디스크 이미지 파일을 찾습니다.

4. 마법사에서 **완료를** 선택하여 가상 컴퓨터를 만듭니다.

새 VM을 만드는 데 몇 분이 걸릴 수 있습니다.

### <a name="enable-virtual-tpm"></a>가상 TPM 사용

VM이 만들어지면 해당 설정을 열어 장치를 자동으로 프로비전할 수 있는 TPM(가상 신뢰할 수 있는 플랫폼 모듈)을 활성화합니다.

1. 가상 컴퓨터를 선택한 다음 **설정을**엽니다.

2. **보안**으로 이동합니다.

3. **보안 부팅 사용**을 선택 취소합니다.

4. **신뢰할 수 있는 플랫폼 모듈 사용**을 확인합니다.

5. **확인**을 클릭합니다.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>가상 머신 시작 및 TPM 데이터 수집

가상 컴퓨터에서 장치의 **등록 ID** 및 **인증 키를**검색하는 데 사용할 수 있는 도구를 빌드합니다.

1. 가상 컴퓨터를 시작하고 연결합니다.

1. 가상 컴퓨터 내의 프롬프트에 따라 설치 프로세스를 완료하고 컴퓨터를 재부팅합니다.

1. VM에 로그인한 다음 [Linux 개발 환경 설정의](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) 단계를 수행하여 C용 Azure IoT 장치 SDK를 설치하고 빌드합니다.

   >[!TIP]
   >이 문서의 과정에서 는 Hyper-V Manager 연결 응용 프로그램을 통해 쉽지 않은 가상 컴퓨터에서 복사하여 붙여 넣습니다. 하이퍼 V 관리자를 통해 가상 시스템에 한 번 연결하여 IP `ifconfig`주소를 검색할 수 있습니다. 그런 다음 IP 주소를 사용하여 SSH를 `ssh <username>@<ipaddress>`통해 연결할 수 있습니다.

1. 다음 명령을 실행하여 TPM 시뮬레이터에서 장치 프로비저닝 정보를 검색하는 SDK 도구를 빌드합니다.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 명령 창에서 `azure-iot-sdk-c` 디렉터리로 이동하여 TPM 시뮬레이터를 실행합니다. 포트 2321 및 2322에서 소켓을 수신 대기합니다. 이 명령 창을 닫지 마십시오. 이 시뮬레이터를 계속 실행해야 합니다.

   `azure-iot-sdk-c` 디렉터리에서 다음 명령을 실행하여 시뮬레이터를 시작합니다.

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Visual `cmake` Studio를 사용하여 명명된 `azure_iot_sdks.sln`디렉터리에서 생성된 솔루션을 열고 **빌드** 메뉴에서 빌드 **솔루션** 명령을 사용하여 빌드합니다.

1. Visual Studio의 **솔루션 탐색기** 창에서 **Provision\_Tools** 폴더로 이동합니다. **tpm_device_provision** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 선택합니다.

1. **디버그** 메뉴에서 **시작** 명령 중 하나를 사용하여 솔루션을 실행합니다. 출력 창에는 TPM 시뮬레이터의 **등록 ID와** **인증 키가**표시되며, 나중에 장치에 대한 개별 등록을 만들 때 복사하여 이 창을 닫을 수 있지만(등록 ID 및 인증 키를 사용하여) TPM 시뮬레이터 창을 실행 상태로 둘 수 있습니다.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>물리적 장치에서 프로비저닝 정보 검색

장치에서 장치의 프로비저닝 정보를 검색하는 데 사용할 수 있는 도구를 빌드합니다.

1. Linux 개발 [환경 설정의](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) 단계를 수행하여 C용 Azure IoT 장치 SDK를 설치하고 빌드합니다.

1. 다음 명령을 실행하여 TPM 장치에서 장치 프로비저닝 정보를 검색하는 SDK 도구를 빌드합니다.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. **등록 ID** 및 인증 키의 값을 **복사합니다.** 이러한 값을 사용하여 DPS에서 디바이스에 대한 개별 등록을 만듭니다.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 설정

Azure에서 IoT Hub Device Provisioning Service의 새 인스턴스를 만들어 IoT hub에 연결합니다. [IoT Hub DPS 설정](../iot-dps/quick-setup-auto-provision.md) 지침을 따르면 됩니다.

Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

가상 머신에서 프로비전 정보를 검색하고 이를 사용하여 Device Provisioning Service에서 개별 등록을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태**를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-monitor.md)를 만드는 데 사용됩니다.

1. Azure [포털에서](https://portal.azure.com)IoT 허브 장치 프로비저닝 서비스의 인스턴스로 이동합니다.

2. **설정**에서 **등록 관리**를 선택합니다.

3. 등록을 구성하려면 **개별 등록 추가**를 선택한 다음, 다음 단계를 완료합니다.  

   1. **메커니즘**의 경우 **TPM**을 선택합니다.

   2. 가상 컴퓨터에서 복사한 **인증 키** 및 **등록 ID를** 제공합니다.

      > [!TIP]
      > 실제 TPM 장치를 사용하는 경우 각 TPM 칩에 고유하며 연결된 TPM 칩 제조업체에서 가져온 **인증 키를**확인해야 합니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 장치에 대한 고유한 **등록 ID를** 도출할 수 있습니다.

   3. 이 가상 컴퓨터가 IoT Edge 장치임을 선언하려면 **True를** 선택합니다.

   4. 디바이스를 연결하려는 링크된 **IoT Hub**를 선택합니다. 여러 허브를 선택할 수 있으며 선택한 할당 정책에 따라 장치가 해당 허브 중 하나에 할당됩니다.

   5. 원하는 경우 디바이스에 대한 ID를 제공합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 장치 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   6. 원하는 경우 **초기 디바이스 쌍 상태**에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들어:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. **저장**을 선택합니다.

이제 이 장치에 대한 등록이 있으므로 IoT Edge 런타임은 설치 하는 동안 장치를 자동으로 프로비전할 수 있습니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 디바이스에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 디바이스에 추가 컨테이너의 배포를 허용합니다. IoT Edge 런타임을 가상 머신에 설치합니다.

디바이스 유형과 일치하는 문서를 시작하기 전에 DPS **ID 범위** 및 디바이스 **등록 ID**를 알아야 합니다. 예제 Ubuntu 서버를 설치했으면 **x64** 지침을 사용합니다. 수동이 아닌 자동 프로비전에 대한 IoT Edge 런타임을 구성해야 합니다.

[리눅스에 Azure IoT 에지 런타임 설치](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>TPM에 대한 IoT Edge 액세스 권한 부여

IoT Edge 런타임이 디바이스를 자동으로 프로비전하려면 TPM에 대한 액세스가 필요합니다.

**iotedge** 서비스에 루트 권한이 있도록 systemd 설정을 재정의하여 IoT Edge 런타임에 TPM 액세스 권한을 부여할 수 있습니다. 서비스 권한을 승격시키지 않으려는 경우 다음 단계를 사용하여 수동으로 TPM 액세스 권한을 제공할 수도 있습니다.

1. 디바이스에서 TPM 하드웨어 모듈에 대한 파일 경로를 찾아 로컬 변수로 저장합니다.

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
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   올바른 사용 권한이 적용됐는지 확인할 수 없는 경우 머신을 재부팅하여 udev를 새로 고칩니다.

## <a name="restart-the-iot-edge-runtime"></a>IoT Edge 런타임 다시 시작

디바이스에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다.

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

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하여 새 디바이스를 자동으로 프로비전했는지 확인할 수 있습니다. 이제 디바이스가 IoT Edge 모듈을 실행할 준비가 되었습니다.

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

장치 프로비저닝 서비스에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure 포털에서 장치 프로비저닝 서비스 인스턴스로 이동합니다. 만든 개별 등록에 대한 등록 세부 정보를 엽니다. 등록 상태가 **할당되고** 장치 ID가 나열됩니다.

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. Azure 포털을 사용하거나 [Azure CLI를 사용하여](how-to-deploy-monitor-cli.md)규모에 따라 [IoT Edge 모듈을 배포하고 모니터링하는](how-to-deploy-monitor.md) 방법에 대해 알아봅니다.
