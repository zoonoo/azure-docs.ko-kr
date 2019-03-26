---
title: IoT DevKit에서 클라우드로 - Azure IoT Hub에 IoT DevKit AZ3166 연결 | Microsoft Docs
description: 이 자습서에서는 Azure 클라우드 플랫폼으로 데이터를 보내도록 DevKit AZ3166을 설정하고 Azure IoT Hub에 연결하는 방법을 알아봅니다.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/21/2019
ms.author: wesmc
ms.openlocfilehash: 0c6189dfd02a4bdd3662f4fa50dbb812fe995884
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438485"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Azure IoT Hub에 IoT DevKit AZ3166 연결

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션을 개발하고 프로토타입하는 데 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)를 사용할 수 있습니다. 풍부한 주변 장치 및 센서가 있는 Arduino 호환 보드, 오픈 소스 보드 패키지 및 증가하는 [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)를 포함합니다.

## <a name="what-you-do"></a>수행할 작업

이 문서에서는 사용 하 여 [Visual Studio Code](https://code.visualstudio.com/)를 함께 코드 편집기, 플랫폼 간 원본 합니다 [Azure IoT 도구](https://aka.ms/azure-iot-tools) 확장 팩입니다.

DevKit 사용자가 만든 Azure IoT hub에 연결 합니다. 그런 다음, 센서에서 온도 및 습도 데이터를 수집하고 데이터를 IoT Hub로 보냅니다.

DevKit가 아직 없으세요? [DevKit 시뮬레이터](https://azure-samples.github.io/iot-devkit-web-simulator/)를 시도하거나 [DevKit를 구매](https://aka.ms/iot-devkit-purchase)합니다.

## <a name="what-you-learn"></a>학습 내용

* IoT DevKit를 무선 액세스 지점에 연결하고 개발 환경을 준비하는 방법
* IoT Hub를 만들고 MXChip IoT DevKit용 디바이스를 등록하는 방법
* MXChip IoT DevKit에서 샘플 애플리케이션을 실행하여 센서 데이터를 수집하는 방법
* IoT Hub로 센서 데이터를 보내는 방법

## <a name="what-you-need"></a>필요한 항목

* 마이크로 USB 케이블을 사용하는 MXChip IoT DevKit 보드 [지금 사용해 보세요](https://aka.ms/iot-devkit-purchase).
* Windows 10 또는 macOS 10.10+을 실행하는 컴퓨터
* 활성 Azure 구독. [30일 평가판 Microsoft Azure 계정](https://azureinfo.microsoft.com/us-freetrial.html) 활성화
  
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

## <a name="configure-wi-fi"></a>Wi-Fi 구성

IoT 프로젝트는 인터넷 연결을 사용합니다. 다음 지침을 사용하여 DevKit를 Wi-Fi에 연결하도록 구성합니다.

### <a name="enter-ap-mode"></a>AP 모드로 전환

단추 B를 길게 누른 다음 다시 설정 단추를 눌러서 놓고 단추 B를 놓습니다. DevKit은 Wi-Fi 구성을 위해 AP 모드로 전환합니다. 화면은 DevKit의 SSID(서비스 설정 식별자) 및 구성 포털 IP 주소를 표시합니다.

![단추, 단추 B 및 SSID 다시 설정](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

![AP 모드 설정](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>DevKit AP에 연결

이제 다른 Wi-Fi 사용 디바이스(컴퓨터 또는 휴대폰)를 사용하여 DevKit SSID(위의 이미지에 강조 표시됨)에 연결합니다. 암호를 비워 둡니다.

![네트워크 정보 및 연결 단추](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>DevKit에 Wi-Fi 구성

컴퓨터 또는 모바일 폰 브라우저에서 DevKit 화면에 표시된 IP 주소를 열고, DevKit에서 연결하려는 Wi-Fi 네트워크를 선택한 다음 암호를 입력합니다. **연결**을 선택합니다.

![암호 상자 및 연결 단추](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

연결에 성공하는 경우 DevKit은 몇 초 내에 다시 부팅됩니다. 그러면 화면에 Wi-Fi 이름 및 IP 주소가 표시됩니다.

![Wi-Fi 이름 및 IP 주소](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE]
> 작업 하는 IoT DevKit 용 2.4ghz 네트워크를 해야 합니다. IoT DevKit의 WiFi 모듈 5ghz 네트워크와 호환 되지 않습니다. 자세한 내용은 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)를 참조하세요.

Wi-Fi를 구성한 후 디바이스가 분리되는 경우에도 자격 증명은 해당 연결에 대해 디바이스에서 유지됩니다. 예를 들어 집에서 Wi-Fi에 대해 DevKit를 구성한 다음 DevKit를 사무실에 가져간 경우 AP 모드를 다시 구성하여("AP 모드로 전환" 섹션의 단계에서 시작) DevKit를 사무실 Wi-Fi에 연결해야 합니다.

## <a name="start-using-the-devkit"></a>DevKit를 사용하여 시작

DevKit에서 실행 중인 기본 앱은 최신 버전의 펌웨어를 확인하고 일부 센서 진단 데이터를 표시합니다.

### <a name="upgrade-to-the-latest-firmware"></a>최신 펌웨어로 업그레이드

> [!NOTE]
> v1.1부터 DevKit은 부팅 로더에서 ST-SAFE를 활성화합니다. v1.1 이전 버전을 실행하는 경우 펌웨어를 업그레이드해야 합니다.

펌웨어를 업그레이드해야 하는 경우 화면에는 최신 펌웨어 버전이 표시됩니다. 업그레이드하려면 [펌웨어 업그레이드](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) 가이드를 따릅니다.

![최신 펌웨어 버전 표시](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> 이는 일회성 작업입니다. DevKit 개발을 시작한 후에 앱을 업로드하면 앱과 함께 최신 펌웨어가 제공됩니다.

### <a name="test-various-sensors"></a>다양한 센서 테스트

센서를 테스트하려면 단추 B를 누릅니다. 단추 B를 계속해서 눌렀다 놓아 각 센서를 순환합니다.

![단추 B 및 센서 표시](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>개발 환경 준비

### <a name="install-azure-iot-tools"></a>Azure IoT Tools 설치

이 섹션에서는 설치를 [Arduino IDE](https://www.arduino.cc/en/Main/Software) 와 함께 [Visual Studio Code](https://code.visualstudio.com/), 플랫폼 간 소스 코드 편집기입니다.

도 설치 합니다 [Azure IoT 도구](https://aka.ms/azure-iot-tools) for Visual Studio Code 확장 팩입니다. 사용 하는 것이 좋습니다 [Azure IoT 도구](https://aka.ms/azure-iot-tools) DevKit의 응용 프로그램을 개발 하려면 Visual Studio Code 용 확장 팩입니다. Azure IoT 도구 확장 팩을 포함 합니다 [Azure IoT 장치 Workbench](https://aka.ms/iot-workbench) 는 개발 및 다양 한 IoT devkit 장치에서 디버그 하는 데 사용 됩니다. 합니다 [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit)도 Azure IoT 도구 확장 팩을 사용 하 여, 관리 및 Azure IoT Hub와 상호 작용 하는 데 사용 됩니다.

수행하는 작업에 대한 개요를 보려면 다음의 [Channel 9](https://channel9.msdn.com/) 비디오를 볼 수 있습니다.
* [VS Code에 대한 새 IoT Workbench 확장 소개](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code)
* [VS Code에 대한 IoT Toolkit 확장의 새로운 기능](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code)

다음 단계를 따라 DevKit에서 개발 환경을 준비하세요.

1. [Arduino IDE](https://www.arduino.cc/en/Main/Software)를 설치합니다. Arduino 코드를 컴파일 및 업로드하는 데 필요한 도구 체인을 제공합니다.
    * **Windows**: Windows Installer 버전을 사용합니다. 앱 스토어에서 설치 하지 마세요.
    * **macOS**: 추출된 **Arduino.app**을 `/Applications` 폴더로 끌어다 놓습니다.
    * **Ubuntu**: `$HOME/Downloads/arduino-1.8.8` 같은 폴더에 압축을 풉니다.

2. IntelliSense 코드 완성 및 디버깅 같은 강력한 개발자 도구를 제공하는 플랫폼 간 소스 코드 편집기인 [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.

3. VS Code를 실행하고, 확장 마켓플레이스에서 **Arduino**를 검색하여 설치합니다. 이 확장은 Arduino 플랫폼에서 개발에 대한 향상된 환경을 제공합니다.
    ![Arduino 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 확장 마켓플레이스에서 **Azure IoT Tools**를 검색하여 설치합니다.
    ![Azure IoT Tools 설치](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    > [!div class="nextstepaction"]
    > [Azure IoT 도구 확장 팩 설치](vscode:extension/vsciot-vscode.azure-iot-tools)

5. Arduino 설정을 사용하여 VS Code를 구성합니다.

    Visual Studio Code에서 **파일 > 기본 설정 > 설정**을 클릭합니다. 그런 후 **...** 를 클릭하고 **settings.json을 엽니다**.
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

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html)는 IoT DevKit가 개발 머신과 통신하는 데 사용하는 USB 인터페이스입니다. DevKit에 컴파일된 장치 코드를 Windows에 설치 해야 합니다. 해당 OS의 단계에 따라 머신이 디바이스에 액세스할 수 있도록 허용합니다.

* **Windows**: USB 드라이버를 다운로드 및 설치 [STMicroelectronics 웹 사이트](https://www.st.com/en/development-tools/stsw-link009.html) 에 대 한 [직접 링크](https://aka.ms/stlink-v2-windows)합니다.
* **macOS**: macOS는 드라이버가 필요 없습니다.
* **Ubuntu**: 터미널에서 명령을 실행 하 고 로그 아웃 하 고 그룹 변경 내용을 적용 하려면 로그인 합니다.
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

개발 환경을 준비하고 구성하기 위한 작업이 모두 끝났습니다. 이제부터 Azure IoT Hub로 온도 분석 데이터 보내기 IoT에 대한 "Hello World" 샘플을 빌드하겠습니다.

## <a name="build-your-first-project"></a>첫 번째 프로젝트 빌드

### <a name="open-sample-code-from-sample-gallery"></a>샘플 갤러리에서 샘플 코드 열기

1. IoT DevKit가 컴퓨터에 연결되어 있지 **않은지** 확인합니다. VS Code를 시작하고 DevKit를 컴퓨터에 연결합니다.

1. `F1` 키를 클릭하여 명령 팔레트를 열고, **Azure IoT Device Workbench: 예제 열기...** 를 입력하고 선택합니다. 보드로 **IoT DevKit**를 선택합니다.

1. IoT Workbench 예제 페이지에서 **시작**을 찾아서 **샘플 열기**를 클릭합니다. 기본 경로를 선택하여 샘플 코드를 다운로드합니다.
    ![샘플 열기](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>프로 비전 Azure IoT Hub 및 장치

1. 새로 열린 프로젝트 창에서 `F1` 키를 클릭하여 명령 팔레트를 열고, **Azure IoT Device Workbench: Azure 서비스 프로비전...** 을 입력하고 선택합니다. 단계별 지침에 따라 Azure IoT Hub 프로비전을 완료하고 IoT Hub 디바이스를 만듭니다.
    ![프로 비전 명령](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Azure 로그인 하지 않은 경우. 로그인에 대 한 팝업 알림을 수행 합니다.

1. 사용할 구독을 선택합니다.
    ![Sub를 선택 합니다.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. 다음을 선택 하거나 새로 만듭니다 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)합니다.
    ![리소스 그룹 선택](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. 지정한 리소스 그룹을 선택 하거나 새 Azure IoT Hub를 만드는 가이드를 따릅니다.
    ![IoT Hub 단계를 선택 합니다.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT Hub를 선택 합니다.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![선택한 IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. 출력 창에 프로 비전 하 여 Azure IoT Hub 나타납니다 ![IoT Hub는 프로 비전](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. 선택 하거나 새 장치를 프로 비전 하는 Azure IoT Hub에 만듭니다.
    ![IoT 장치는 단계를 선택 합니다.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![프로 비전 된 IoT 장치를 선택 합니다.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. 이제 프로 비전 하는 Azure IoT Hub 및 장치를 만들 수 있습니다. 또한 장치 연결 문자열을 나중에 IoT DevKit를 구성 하는 것에 대 한 VS Code에서 저장 됩니다.
    ![프로 비전 완료](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>구성 및 장치 코드 컴파일

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

상태 표시줄의 전원 플러그 아이콘을 클릭하여 직렬 모니터를 엽니다. ![직렬 모니터](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

다음과 같은 결과가 표시되면 샘플 애플리케이션은 성공적으로 실행됩니다.

* 직렬 모니터는 IoT Hub로 전송된 메시지를 표시합니다.
* MXChip IoT DevKit의 LED가 깜박입니다.

![직렬 모니터 출력](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Azure IoT Hub에서 수신한 원격 분석 보기

[Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)를 사용하여 IoT Hub에서 디바이스-클라우드(D2C) 메시지를 모니터링할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, 이전에 만든 IoT Hub를 찾습니다.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. **공유 액세스 정책** 창에서 **iothubowner** 정책을 클릭하고, IoT Hub의 연결 문자열을 기록해 둡니다.
    ![Azure IoT Hub 연결 문자열](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. VS Code에서 `F1` 키를 클릭하고, **Azure IoT Hub: IoT Hub 연결 문자열 설정**을 입력하고 선택합니다. 연결 문자열을 복사합니다.
    ![Azure IoT Hub 연결 문자열 설정](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 오른쪽의 **AZURE IOT HUB 디바이스** 창을 확장하고, 만든 디바이스 이름을 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.
    ![D2C 메시지 모니터링](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. **출력** 창에서, IoT Hub로 들어오는 D2C 메시지를 볼 수 있습니다.
    ![D2C 메시지](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>코드 검토

`GetStarted.ino` 주 Arduino 스케치 파일입니다.

![D2C 메시지](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Azure IoT Hub에 장치 원격 분석이 전송 되는 방식을 보려면를 `utility.cpp` 동일한 폴더에 파일입니다. 뷰 [API 참조](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) 에 IoT DevKit에서 센서 및 주변 장치를 사용 하는 방법을 알아봅니다.

`DevKitMQTTClient` 의 래퍼인 사용 합니다 **iothub_client** 에서 [Microsoft Azure IoT Sdk 및 C 라이브러리](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) Azure IoT Hub와 상호 작용 하 합니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 있는 경우 [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)에서 해결 방법을 찾아보거나 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit)에서 문의할 수 있습니다. 또한 이 페이지에서 의견을 남겨 피드백을 제공해 주실 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub에 MXChip IoT DevKit를 성공적으로 연결하고 캡처된 센서 데이터를 IoT Hub에 전송했습니다.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
