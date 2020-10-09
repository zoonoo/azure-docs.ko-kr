---
title: Azure IoT Hub에 IoT DevKit AZ3166 연결
description: 이 자습서에서는 Azure 클라우드 플랫폼으로 데이터를 보내도록 DevKit AZ3166을 설정하고 Azure IoT Hub에 연결하는 방법을 알아봅니다.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: 0d3afc8d24b95f170bd22b3dc2cf29e7f97e77c3
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839557"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Azure IoT Hub에 IoT DevKit AZ3166 연결

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션을 개발하고 프로토타입하는 데 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)를 사용할 수 있습니다. 풍부한 주변 장치 및 센서가 있는 Arduino 호환 보드, 오픈 소스 보드 패키지 및 풍부한 [샘플 갤러리](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)를 포함합니다.

## <a name="what-you-learn"></a>학습 내용

* IoT Hub를 만들고 MXChip IoT DevKit용 디바이스를 등록하는 방법
* IoT DevKit를 Wi-Fi에 연결하고 IoT Hub 연결 문자열을 구성하는 방법
* DevKit 센서 원격 분석 데이터를 IoT hub로 보내는 방법
* IoT DevKit에 대한 개발 환경을 준비하고 애플리케이션을 개발하는 방법을 설명합니다.

DevKit가 아직 없으세요? [DevKit 시뮬레이터](https://azure-samples.github.io/iot-devkit-web-simulator/)를 시도하거나 [DevKit를 구매](https://aka.ms/iot-devkit-purchase)합니다.

[코드 샘플 갤러리](https://docs.microsoft.com/samples/browse/?term=mxchip)에서 모든 DevKit 자습서에 대한 소스 코드를 찾을 수 있습니다.

## <a name="what-you-need"></a>필요한 항목

* 마이크로 USB 케이블을 사용하는 MXChip IoT DevKit 보드 [지금 사용해 보세요](https://aka.ms/iot-devkit-purchase).
* Windows 10, macOS 10.10+ 또는 Ubuntu 18.04+를 실행하는 컴퓨터
* 활성화된 Azure 구독. [30일 평가판 Microsoft Azure 계정](https://azureinfo.microsoft.com/us-freetrial.html) 활성화

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>하드웨어 준비

컴퓨터에 다음 하드웨어를 연결합니다.

* DevKit 보드
* 마이크로 USB 케이블

![필수 하드웨어](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit를 컴퓨터에 연결하려면 다음 단계를 수행합니다.

1. USB 끝을 컴퓨터에 연결합니다.

2. 마이크로 USB 끝을 DevKit에 연결합니다.

3. 전원의 녹색 LED는 연결을 확인합니다.

   ![하드웨어 연결](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>빠른 시작: DevKit에서 IoT Hub로 원격 분석 보내기

이 빠른 시작에서는 미리 컴파일된 DevKit 펌웨어를 사용하여 IoT Hub에 원격 분석을 보냅니다. 실행하기 전에 IoT Hub를 만들고 허브에 디바이스를 등록합니다.

### <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyNodeDevice**: 등록 중인 디바이스의 이름입니다. 표시된 것처럼 **MyNodeDevice**를 사용하세요. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용해야 하고, 애플리케이션 예제에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > `device-identity`를 실행하는 동안 오류가 발생하는 경우 [Azure CLI에 대한 Azure IoT 확장](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md)을 설치합니다.
   > 다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IoT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 고유의 명령을 추가합니다.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

### <a name="send-devkit-telemetry"></a>DevKit 원격 분석 보내기

DevKit는 IoT 허브의 디바이스 관련 엔드포인트에 연결하고 온도 및 습도 원격 분석을 전송합니다.

1. IoT DevKit에 대한 [GetStarted 펌웨어](https://aka.ms/devkit/prod/getstarted/latest) 최신 버전을 다운로드합니다.

1. IoT DevKit에서 USB를 통해 컴퓨터에 연결되는지 확인합니다. 파일 탐색기를 열면 **AZ3166**라는 USB 대용량 스토리지 디바이스가 있습니다.

    ![Windows 탐색기 열기](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. 다운로드한 펌웨어를 대용량 스토리지 디바이스에 끌어서 놓으면 자동으로 깜박입니다.

    ![펌웨어 복사](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. DevKit에서 **B** 단추를 누른 상태로 **다시 설정** 단추를 눌렀다 떼고, **B** 단추를 뗍니다. DevKit가 AP 모드로 전환됩니다. 확인을 위해 화면에는 DevKit의 SSID(서비스 설정 식별자) 및 구성 포털 IP 주소가 표시됩니다.

    ![단추, 단추 B 및 SSID 다시 설정](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![AP 모드 설정](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. 다른 Wi-Fi 지원 디바이스(컴퓨터 또는 휴대폰)의 웹 브라우저를 사용하여 이전 단계에서 표시된 IoT DevKit SSID에 연결합니다. 암호를 요청하는 경우 비워 둡니다.

    ![SSID 연결](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. 브라우저에서 **192.168.0.1**을 엽니다. IoT DevKit에서 연결할 Wi-Fi를 선택하고 Wi-Fi 암호를 입력한 다음, 이전에 기록해 둔 디바이스 연결 문자열을 붙여 넣습니다. 그런 다음 저장을 클릭합니다.

    ![구성 UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit는 2.4GHz 네트워크만 지원합니다. 자세한 내용은 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)를 참조하세요.

1. WiFi 정보 및 디바이스 연결 문자열은 결과 페이지가 표시될 때 IoT DevKit에 저장됩니다.

    ![구성 결과](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Wi-Fi를 구성한 후 디바이스가 분리되는 경우에도 자격 증명은 해당 연결에 대해 디바이스에서 유지됩니다.

1. IoT DevKit는 몇 초 안에 재부팅됩니다. DevKit 화면에서 DevKit의 IP 주소 다음에 Azure IoT Hub로 전송된 메시지 수와 함께 온도 및 습도 값을 포함하는 원격 분석 데이터가 표시되는 것을 볼 수 있습니다.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![데이터 전송](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Azure로 전송되는 원격 분석 데이터를 확인하려면 Azure Cloud Shell에서 다음 명령을 실행합니다.

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>개발 환경 준비

다음 단계를 따라 DevKit에서 개발 환경을 준비하세요.

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Azure IoT Tools 확장 패키지를 사용하여 Visual Studio Code 설치

1. [Arduino IDE](https://www.arduino.cc/en/Main/Software)를 설치합니다. Arduino 코드를 컴파일 및 업로드하는 데 필요한 도구 체인을 제공합니다.
    * **Windows**: Windows Installer 버전을 사용합니다. App Store에서 설치하지 마세요.
    * **macOS**: 추출된 **Arduino.app**을 `/Applications` 폴더로 끌어다 놓습니다.
    * **Ubuntu**: `$HOME/Downloads/arduino-1.8.8` 같은 폴더에 압축을 풉니다.

2. IntelliSense, 코드 완성 및 디버깅 지원을 제공하는 플랫폼 간 소스 코드 편집기인 [Visual Studio Code](https://code.visualstudio.com/)를 설치하고 Marketplace에서 풍부한 확장을 설치할 수 있습니다.

3. VS Code를 실행하고, 확장 마켓플레이스에서 **Arduino**를 검색하여 설치합니다. 이 확장은 Arduino 플랫폼에서 개발에 대한 향상된 환경을 제공합니다.

    ![Arduino 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 확장 마켓플레이스에서 [Azure IoT Tools](https://aka.ms/azure-iot-tools)를 검색하여 설치합니다.

    ![확장 marketplace에서 Azure IoT 도구를 보여 주는 스크린샷](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    또는 URL `vscode:extension/vsciot-vscode.azure-iot-tools`를 복사하여 브라우저 창에 붙여 넣습니다.

    > [!NOTE]
    > Azure IoT Tools 확장 팩에는 다양한 IoT devkit 디바이스에서 개발하고 디버그하는 데 사용되는 [Azure IoT 디바이스 워크벤치](https://aka.ms/iot-workbench)가 포함되어 있습니다. Azure IoT Tools 확장 팩에도 포함된 [Azure IoT Hub 확장](https://aka.ms/iot-toolkit)은 Azure IoT Hub를 관리하고 상호 작용하는 데 사용됩니다.

5. Arduino 설정을 사용하여 VS Code를 구성합니다.

    Visual Studio Code에서 **파일 > 기본 설정 > 설정**(macOS에서는 **설정 > 기본 설정 > 설정**)을 클릭합니다. 그런 다음, *설정* 페이지의 오른쪽 위 모서리에서 **설정 열기(JSON)** 아이콘을 클릭합니다.

    ![Azure IoT Tools 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    플랫폼에 따라 다음 줄을 추가하여 Arduino를 구성합니다. 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        **{username}** 을 사용자 이름 아래의 자리 표시자로 바꿉니다.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. `F1` 키를 클릭하여 명령 팔레트를 연 다음, **Arduino: 보드 관리자**를 입력하고 선택합니다. **AZ3166**을 검색하고 최신 버전을 설치합니다.

    ![DevKit SDK 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link 드라이버 설치

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html)는 IoT DevKit가 개발 머신과 통신하는 데 사용하는 USB 인터페이스입니다. 컴파일된 디바이스 코드를 DevKit로 플래시하려면 Windows에 설치해야 합니다. 해당 OS의 단계에 따라 머신이 디바이스에 액세스할 수 있도록 허용합니다.

* **Windows**: [STMicroelectronics 웹 사이트](https://www.st.com/en/development-tools/stsw-link009.html)에서 USB 드라이버를 다운로드하여 설치합니다.
* **macOS**: macOS는 드라이버가 필요 없습니다.
* **Ubuntu**: 터미널에서 명령을 실행하고 로그아웃했다가 다시 로그인하여 그룹 변경 내용을 적용합니다.

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

개발 환경을 준비하고 구성하기 위한 작업이 모두 끝났습니다. 방금 실행한 GetStarted 샘플을 빌드해 보겠습니다.

## <a name="build-your-first-project"></a>첫 번째 프로젝트 빌드

### <a name="open-sample-code-from-sample-gallery"></a>샘플 갤러리에서 샘플 코드 열기

IoT DevKit에는 다양한 Azure 서비스에 DevKit를 연결하는 방법을 배우는 데 사용할 수 있는 풍부한 샘플 갤러리가 포함되어 있습니다.

1. IoT DevKit가 컴퓨터에 연결되어 있지 **않은지** 확인합니다. VS Code를 시작하고 DevKit를 컴퓨터에 연결합니다.

1. `F1` 키를 클릭하여 명령 팔레트를 열고, **Azure IoT Device Workbench: 예제 열기...** 를 입력하고 선택합니다. 보드로 **IoT DevKit**를 선택합니다.

1. IoT Workbench 예제 페이지에서 **시작**을 찾아서 **샘플 열기**를 클릭합니다. 기본 경로를 선택하여 샘플 코드를 다운로드합니다.

    ![샘플 열기](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Azure IoT Hub 및 디바이스 프로비저닝

Azure Portal에서 Azure IoT Hub 및 디바이스를 프로비저닝하는 대신, 개발 환경에서 나가지 않고 VS Code에서 이 작업을 수행할 수 있습니다.

1. 새로 열린 프로젝트 창에서 `F1` 키를 클릭하여 명령 팔레트를 열고, **Azure IoT Device Workbench: Azure 서비스 프로비전...** 을 입력하고 선택합니다. 단계별 지침에 따라 Azure IoT Hub 프로비전을 완료하고 IoT Hub 디바이스를 만듭니다.

    ![프로비저닝 명령](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Azure에 로그인하지 않은 경우 로그인을 위한 팝업 알림을 따릅니다.

1. 사용할 구독을 선택합니다.

    ![구독 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. 새 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)을 선택하거나 만듭니다.

    ![리소스 그룹 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. 지정한 리소스 그룹에서 가이드에 따라 새 Azure IoT Hub를 선택하거나 만듭니다.

    ![IoT Hub 선택 단계](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT Hub 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![선택한 IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. 출력 창에 프로비저닝된 Azure IoT Hub가 표시됩니다.

    ![프로비저닝된 IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. 프로비저닝한 Azure IoT Hub에서 새 디바이스를 선택하거나 만듭니다.

    ![IoT 디바이스 선택 단계](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![프로비저닝된 IoT 디바이스 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. 이제 Azure IoT Hub가 프로비저닝되고 디바이스가 생성되었습니다. 또한 디바이스 연결 문자열은 나중에 IoT DevKit를 구성하기 위해 VS Code에 저장됩니다.

    ![프로비저닝 완료](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>디바이스 코드 구성 및 컴파일

1. 오른쪽 아래의 상태 표시줄에서 **MXCHIP AZ3166**이 보드로 선택되고 **STMicroelectronics**가 있는 직렬 포트가 사용되는지 확인합니다.

    ![보드 및 COM 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. `F1` 키를 클릭하여 명령 팔레트를 열고, **Azure IoT Device Workbench: 디바이스 설정 구성...** 을 입력하여 선택한 다음, **디바이스 연결 문자열 구성 > IoT Hub 디바이스 연결 문자열 선택**을 선택합니다.

1. DevKit에서 **A 단추**를 누른 상태로 **다시 설정** 단추를 눌렀다 떼고, **A 단추**를 뗍니다. DevKit가 구성 모드로 전환되고 연결 문자열이 저장됩니다.

    ![연결 문자열](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. `F1` 키를 다시 클릭하고, **Azure IoT Device Workbench: 디바이스 코드 업로드**를 입력하고 선택합니다. 컴파일을 시작하고 코드를 DevKit로 업로드합니다.

    ![Arduino 업로드](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

> [!NOTE]
> 오류 또는 중단이 있으면 언제든지 명령을 다시 실행하여 복구할 수 있습니다.

## <a name="test-the-project"></a>프로젝트 테스트

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Azure IoT Hub로 전송된 원격 분석 보기

상태 표시줄의 전원 플러그 아이콘을 클릭하여 직렬 모니터를 엽니다.

![직렬 모니터](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

다음과 같은 결과가 표시되면 샘플 애플리케이션은 성공적으로 실행됩니다.

* 직렬 모니터는 IoT Hub로 전송된 메시지를 표시합니다.
* MXChip IoT DevKit의 LED가 깜박입니다.

![직렬 모니터 출력](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> 테스트 중에 LED가 깜박이지 않고, Azure Portal에 디바이스에서 들어오는 데이터가 표시되지 않지만 디바이스 OLED 화면에 **실행 중...** 이 표시되는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 Azure Portal에서 IoT Hub의 디바이스로 이동한 후 해당 디바이스에 메시지를 보냅니다. VS Code의 직렬 모니터에 다음과 같은 응답이 표시되는 경우 디바이스의 직접 통신이 라우터 수준에서 차단된 것일 수 있습니다. 연결하는 디바이스에 대해 구성된 방화벽 및 라우터 규칙을 확인합니다. 또한 아웃바운드 포트 1833이 열려 있는지 확인합니다.
> 
> 오류: mqtt_client.c(ln 454): 오류: 엔드포인트에 대한 연결을 여는 동안 오류가 발생했습니다.  
> 정보: >>>연결 상태: 연결 끊김  
> 오류: tlsio_mbedtls.c(ln 604): 기본 IO를 열지 못했습니다.  
> 오류: mqtt_client.c(ln 1042): 오류: io_open 실패  
> 오류: iothubtransport_mqtt_common.c(ln 2283): 주소 atcsliothub.azure-devices.net에 연결하는 동안 오류가 발생했습니다.  
> 정보: >>>다시 연결합니다.  
> 정보: IoThub 버전: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Azure IoT Hub에서 수신한 원격 분석 보기

[Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)를 사용하여 IoT Hub에서 디바이스-클라우드(D2C) 메시지를 모니터링할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, 이전에 만든 IoT Hub를 찾습니다.

    ![Azure portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. **공유 액세스 정책** 창에서 **iothubowner** 정책을 클릭하고, IoT Hub의 연결 문자열을 기록해 둡니다.

    ![Azure IoT Hub 연결 문자열](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. VS Code에서 `F1` 키를 클릭하고, **Azure IoT Hub: IoT Hub 연결 문자열 설정**을 입력하고 선택합니다. 연결 문자열을 복사합니다.

    ![Azure IoT Hub 연결 문자열 설정](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 왼쪽의 **AZURE IOT HUB 디바이스** 창을 확장하고, 만든 디바이스 이름을 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택합니다.

    ![D2C 메시지 모니터링](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. **출력** 창에서, IoT Hub로 들어오는 D2C 메시지를 볼 수 있습니다.

    ![들어오는 D2C 메시지를 IoT Hub 표시 하는 스크린샷](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>코드 검토

`GetStarted.ino`는 기본 Arduino 스케치 파일입니다.

![D2C 메시지](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

디바이스 원격 분석을 Azure IoT Hub로 전송하는 방법을 확인하려면 동일한 폴더에서 `utility.cpp` 파일을 엽니다. IoT DevKit에서 센서 및 주변 디바이스를 사용하는 방법을 알아보려면 [API 참조](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/)를 참조하세요.

사용되는 `DevKitMQTTClient`가 Azure IoT Hub와 상호 작용하기 위해 [C용 Microsoft Azure IoT SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client)에서 가져온 **iothub_client**의 래퍼입니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 있는 경우 [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)에서 해결 방법을 찾아보거나 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit)에서 문의할 수 있습니다. 또한 이 페이지에서 의견을 남겨 피드백을 제공해 주실 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub에 MXChip IoT DevKit를 성공적으로 연결하고 캡처된 센서 데이터를 IoT Hub에 전송했습니다.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
