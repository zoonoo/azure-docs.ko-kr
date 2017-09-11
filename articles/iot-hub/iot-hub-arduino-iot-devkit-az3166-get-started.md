---
title: "IoT DevKit에서 클라우드로 - Azure IoT Hub에 IoT DevKit AZ3166 연결 | Microsoft Docs"
description: "이 자습서에서는 Azure 클라우드 플랫폼으로 데이터를 보내기 위해 DevKit AZ3166을 설정하고 해당 Azure IoT Hub에 연결하는 방법을 알아봅니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 122fac584ac5b954ef7b33a3121bee2c502ebc63
ms.contentlocale: ko-kr
ms.lasthandoff: 08/16/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>클라우드에서 Azure IoT Hub에 IoT DevKit AZ3166 연결

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션을 개발하고 프로토타입하는 데 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)를 사용할 수 있습니다. 풍부한 주변 장치 및 센서가 있는 Arduino 호환 보드, 오픈 소스 보드 패키지 및 증가하는 [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)를 포함합니다.

## <a name="what-you-do"></a>수행할 작업
사용자가 만드는 Azure IoT Hub에 [DevKit](https://microsoft.github.io/azure-iot-developer-kit/)을 연결하고 센서에서 온도 및 습도 데이터를 수집하고 데이터를 IoT 허브로 보냅니다.

DevKit가 아직 없으세요? [여기](https://aka.ms/iot-devkit-purchase)에서 새 DevKit를 받으세요.

## <a name="what-you-learn"></a>학습 내용

* IoT DevKit를 무선 액세스 지점에 연결하고 개발 환경을 준비하는 방법.
* IoT Hub를 만들고 MXChip IoT DevKit용 장치를 등록하는 방법.
* MXChip IoT DevKit에서 샘플 응용 프로그램을 실행하여 센서 데이터를 수집하는 방법.
* IoT Hub로 센서 데이터를 보내는 방법

## <a name="what-you-need"></a>필요한 항목

* 마이크로 USB 케이블을 사용하는 MXChip IoT DevKit 보드. [지금 사용](https://aka.ms/iot-devkit-purchase)
* Windows 10 또는 macOS 10.10+를 실행하는 컴퓨터
* 활성 Azure 구독
  * [30일 평가판 Microsoft Azure 계정](https://azureinfo.microsoft.com/us-freetrial.html) 활성화

## <a name="prepare-your-hardware"></a>하드웨어 준비

컴퓨터에 하드웨어를 연결합니다.

### <a name="hardware-you-need"></a>필요한 하드웨어

* DevKit 보드
* Micro USB 케이블

![getting-started-hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

### <a name="connect-devkit-to-your-computer"></a>DevKit를 컴퓨터에 연결

1. USB 끝을 PC에 연결
2. Micro USB 끝을 DevKit에 연결
3. 연결을 확인하는 전원 옆에 있는 녹색 LED

![getting-started-connect](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wifi"></a>WiFi 구성

IoT 프로젝트는 인터넷 연결에 의존합니다. 다음 지침을 사용하여 DevKit를 WiFi에 연결하도록 구성합니다.

### <a name="enter-ap-mode"></a>AP 모드로 전환

단추 B를 길게 누른 다음 다시 설정 단추를 눌러서 놓고 단추 B를 놓습니다. DevKit은 WiFi 구성을 위해 AP 모드로 전환합니다. 화면은 구성 포털 IP 주소와 함께 DevKit의 SSID(서비스 설정 식별자)를 표시합니다.

![getting-started-wifi-ap](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>DevKit AP에 연결

이제 다른 WiFi 사용 장치(PC 또는 휴대폰)를 사용하여 DevKit SSID(위 스크린샷에 강조 표시됨)에 연결합니다. 암호는 비워 둡니다.

![getting-started-ssid](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wifi-for-devkit"></a>DevKit용 WiFi 구성

PC 또는 모바일 폰 브라우저에서 DevKit 화면에 표시된 IP 주소를 열고, DevKit에서 연결하려는 WiFi 네트워크를 선택한 다음 암호를 입력합니다. **연결**을 클릭하여 완료합니다.

![getting-started-wifi-portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

연결에 성공하면 DevKit은 몇 초 후에 다시 부팅됩니다. 성공한 경우 화면에 WiFi 이름 및 IP 주소가 표시됩니다.

![getting-started-wifi-ip](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
사진에 표시된 IP 주소는 DevKit 화면에 할당되고 표시되는 실제 IP와 일치하지 않을 수 있습니다. WiFi는 DHCP를 사용하여 동적으로 IP를 할당하므로 이는 일반적입니다.

WiFi를 구성한 후 자격 증명은 분리되는 경우에도 해당 연결에 대해 장치에서 유지됩니다. 예를 들어 집에서 WiFi에 대해 DevKit를 구성한 다음 DevKit를 사무실에 가져간 경우 AP 모드를 다시 구성하여(**AP 모드로 전환** 단계에서 시작) DevKit를 사무실 WiFi에 연결해야 합니다. 

## <a name="start-using-devkit"></a>DevKit 사용 시작

DevKit에서 실행 중인 기본 앱은 최신 버전의 펌웨어를 확인하고 일부 센서 진단 데이터를 표시합니다.

### <a name="upgrade-to-the-latest-firmware"></a>최신 펌웨어로 업그레이드

필요한 업그레이드가 있는 경우 현재 및 최신 펌웨어 버전이 모두 화면에 나타납니다. [펌웨어 업그레이드](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) 가이드를 따라 업그레이드합니다.

![getting-started-firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
이는 일회성 작업입니다. DevKit 개발을 시작하고 앱을 업로드하면 앱과 함께 최신 펌웨어가 제공됩니다.

### <a name="test-various-sensors"></a>다양한 센서 테스트

단추 B를 눌러 센서를 테스트하고 단추 B를 계속해서 눌렀다 놓아 각 센서를 순환합니다.

![getting-started-sensors](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-development-environment"></a>개발 환경 준비

이제 IoT 응용 프로그램을 빌드하도록 개발 환경(도구 및 패키지)을 설정할 시간입니다. 운영 체제에 따라 Windows 또는 macOS 버전을 선택할 수 있습니다.


### <a name="windows"></a>Windows

설치 패키지를 사용하여 개발 환경을 준비하는 것이 좋습니다. 문제가 발생하는 경우 [수동 단계](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)를 따라 수행할 수 있습니다.

#### <a name="download-latest-package"></a>최신 패키지 다운로드

다운로드한 `.zip` 파일은 DevKit 개발에 필요한 모든 필요한 도구 및 패키지를 포함합니다.

> [!div class="button"]
[다운로드](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


> [!NOTE] 
> `.zip` 파일은 다음 도구 및 패키지를 포함합니다. 이미 일부 구성 요소를 설치한 경우 스크립트에서 이를 검색하고 건너뜁니다.
> * Node.js 및 Yarn: 설치 스크립트 및 자동화된 작업에 대한 런타임
> * [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows) - Azure 리소스를 관리하기 위한 플랫폼 간 명령줄 환경, MSI는 종속 Python 및 pip를 포함합니다.
> * [Visual Studio Code](https://code.visualstudio.com/): DevKit 개발을 위한 간단한 코드 편집기
> * [Arduino용 Visual Studio Code 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): VS Code에서 Arduino 개발 활성화
> * [Arduino IDE](https://www.arduino.cc/en/Main/Software): Arduino에 대한 확장은 이 도구에 의존함
> * DevKit 보드 패키지: DevKit에 대한 도구 체인, 라이브러리 및 프로젝트
> * ST 링크 유틸리티: 필수 유틸리티 및 드라이버

#### <a name="run-installation-script"></a>설치 스크립트 실행

Windows 파일 탐색기에서 `.zip`를 찾고 압축을 풀어 `install.cmd`를 찾고 **"관리자 권한으로 실행"**을 마우스 오른쪽 단추로 클릭하고 선택하여 시작합니다.

![getting-started-run-admin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

설치하는 동안 각 도구 또는 패키지의 진행 상태를 볼 수 있습니다.

![getting-started-install](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="confirm-to-install-drivers"></a>드라이버 설치 확인

Arduino 확장에 대한 VS Code는 Arduino IDE에 의존합니다. Arduino IDE를 처음 설치하는 경우 관련 드라이버를 설치하라는 메시지가 표시됩니다.

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

인터넷 속도에 따라 설치를 완료하는 데 약 10분이 걸릴 수 있습니다. 설치가 완료되면 바탕 화면에 Visual Studio Code 및 Arduino IDE 바로 가기가 표시됩니다.

> [!NOTE] 
경우에 따라서 VS Code를 시작할 때 Arduino IDE 또는 관련된 보드 패키지를 찾을 수 없다는 오류와 함께 메시지가 표시됩니다. 이를 해결하기 위해 VS Code를 닫고 Arduino IDE를 한 번 시작하면 VS Code는 Arduino IDE 경로를 올바르게 찾습니다.


### <a name="macos-preview"></a>macOS(미리 보기)

이 단계를 따라 macOS에서 개발 환경을 준비합니다.

#### <a name="install-azure-cli-20"></a>Azure CLI 2.0 설치

[공식 가이드](https://docs.microsoft.com//cli/azure/install-azure-cli)를 따라 Azure CLI 2.0을 설치합니다.

`curl` 명령 하나로 Azure CLI 2.0을 설치합니다.

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

변경 내용을 적용하기 위해 명령 셸을 다시 시작합니다.

```bash
exec -l $SHELL
```

#### <a name="install-arduino-ide"></a>Arduino IDE 설치

Visual Studio Code Arduino 확장은 Arduino IDE에 의존합니다. [macOS용 Arduino IDE](https://www.arduino.cc/en/Main/Software)를 다운로드하고 설치합니다.

#### <a name="install-visual-studio-code"></a>Visual Studio Code 설치

[macOS용 Visual Studio Code](https://code.visualstudio.com/)를 다운로드하고 설치합니다. 이는 DevKit IoT 응용 프로그램을 구축하기 위한 기본 개발 도구가 됩니다.

####  <a name="download-latest-package"></a>최신 패키지 다운로드

1. Node.js를 설치합니다. 인기 있는 macOS 패키지 관리자 [Homebrew](https://brew.sh/) 또는 [미리 빌드된 설치 관리자](https://nodejs.org/en/download/)를 사용하여 설치할 수 있습니다.

2. VS Code에서 DevKit 개발에 필요한 작업 스크립트를 포함하는 `.zip` 파일을 다운로드합니다.

   > [!div class="button"]
   [다운로드](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

   `.zip`를 찾고 압축을 풉니다. 그런 다음 **터미널** 앱을 실행하고 다음 명령을 실행하여 구성합니다.

   압축을 푼 폴더를 macOS 사용자 폴더로 이동합니다.
   ```bash
   mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
   ```
  
   `npm` 패키지 설치:
   ```
   npm install
   ```

#### <a name="install-vs-code-extension-for-arduino"></a>Arduino용 VS Code 확장 설치

Visual Studio Code를 사용하면 도구에서 직접 Marketplace 확장을 설치하고 왼쪽 메뉴 창에서 간단히 확장 아이콘을 클릭한 다음 `Arduino`를 검색하여 설치할 수 있습니다.

![installation-extensions](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-devkit-board-package"></a>DevKit 보드 패키지 설치

Visual Studio Code에서 보드 관리자를 사용하여 DevKit 보드를 추가해야 합니다.

1. `Cmd+Shift+P`를 사용하여 명령 팔레트를 호출하고 **Arduino**를 입력한 다음 찾아서 **Arduino: 보드 관리자**를 선택합니다.

2. 오른쪽 아래에서 **'추가 URL'**을 클릭합니다.
   ![installation-additional-urls](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. `settings.json` 파일에서 `USER SETTINGS` 창의 아래쪽에 줄을 추가하고 저장합니다.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![installation-settings-json](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. 이제 보드 관리자에서 'az3166'을 검색하고 최신 버전을 설치합니다.
   ![installation-az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

이제 macOS에 대해 필요한 모든 도구 및 패키지를 설치했습니다.


## <a name="open-project-folder"></a>프로젝트 폴더 열기

### <a name="launch-vs-code"></a>VS Code 시작

DevKit이 연결되어 있지 않은지 확인합니다. VS Code를 먼저 시작하고 DevKit를 컴퓨터에 연결합니다. VS Code는 자동으로 찾고 소개 페이지를 팝업합니다.

![mini-solution-vscode](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
경우에 따라서 VS Code를 시작할 때 Arduino IDE 또는 관련된 보드 패키지를 찾을 수 없다는 오류와 함께 메시지가 표시됩니다. 이를 해결하기 위해 VS Code를 닫고 Arduino IDE를 다시 한 번 시작하면 VS Code는 Arduino IDE 경로를 올바르게 찾습니다.

### <a name="open-arduino-examples-folder"></a>Arduino 예제 폴더 열기

**'Arduino 예제'** 탭으로 전환하고 `Examples for MXCHIP AZ3166 > AzureIoT`로 이동하고 `GetStarted`를 클릭합니다.

![mini-solution-examples](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

창이 닫힌 경우 다시 로드하려면 `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`)를 사용하여 명령 팔레트를 호출하고 **Arduino**를 입력하여 찾아서 **Arduino: 예제**를 선택합니다.

## <a name="provision-azure-services"></a>Azure 서비스 프로비전

솔루션 창에서 'task cloud-provision'을 입력하여 `Ctrl+P`(macOS: `Cmd+P`)를 통해 작업을 실행합니다.

VS Code 터미널에서 대화형 명령줄은 필요한 Azure 서비스를 프로비전하는 과정을 안내합니다.

![mini-solution-cloud-provision](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-arduino-sketch"></a>Arduino 스케치 빌드 및 업로드

### <a name="install-required-library"></a>필요한 라이브러리 설치

1. `F1` 또는 `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`)를 눌러 명령 팔레트를 호출하고 **Arduino**를 입력한 다음 찾아서 **Arduino: 라이브러리 관리자**를 선택합니다.

2. `ArduinoJson` 라이브러리를 검색하고 **설치**를 클릭합니다.

### <a name="build-and-upload-the-device-code"></a>장치 코드 빌드 및 업로드

`Ctrl+P`(macOS: `Cmd+P`)를 사용하여 'task device-upload'를 실행합니다. 터미널에서 구성 모드를 입력하라는 메시지를 표시합니다. 이렇게 하려면 단추 A를 누르고 있다가 다시 설정 단추를 밀어서 놓습니다. 화면에 '구성'이 표시됩니다. 이는 'task cloud-provision' 단계에서 검색하는 연결 문자열을 설정하기 위한 것입니다.

그런 다음 Arduino 스케치 확인 및 업로드를 시작합니다.

![mini-solution-device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit가 재부팅되고 코드 실행을 시작합니다.

## <a name="test-the-project"></a>프로젝트 테스트

VS Code에서 상태 표시줄의 전원 플러그 아이콘을 클릭하여 직렬 모니터를 엽니다.

다음과 같은 결과가 표시되면 샘플 응용 프로그램은 성공적으로 실행됩니다.

* 직렬 모니터는 아래 스크린샷의 내용으로 동일한 정보를 표시합니다.
* MXChip IoT DevKit의 LED가 깜박입니다.

![VS Code의 최종 출력](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생했거나 아래 채널에서 연락하려는 경우 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub에 MXChip IoT DevKit를 성공적으로 연결하고 캡처된 센서 데이터를 IoT Hub에 전송했습니다.

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

- [iothub-explorer를 사용하여 클라우드 장치 메시지 관리](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [IoT Hub 메시지를 Azure 데이터 저장소에 저장](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Azure Web Apps를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Azure Machine Learning에서 IoT Hub의 센서 데이터를 사용한 날씨 예측](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [iothub-explorer를 사용하여 장치 관리](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Logic Apps를 사용하여 원격 모니터링 및 알림](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
