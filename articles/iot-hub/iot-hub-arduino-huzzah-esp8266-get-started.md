---
title: "ESP8266에서 클라우드로 - Feather HUZZAH ESP8266을 Azure IoT Hub에 연결 | Microsoft Docs"
description: "Arduino 장치인 Adafruit Feather HUZZAH ESP8266을 IoT 자산을 관리하는 데 도움이 되는 Microsoft 클라우드 서비스인 Azure IoT Hub에 연결하는 가이드입니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 544f98afc1769f75bd4e06dc7b2bf8a1a0d91371
ms.lasthandoff: 03/30/2017


---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Adafruit Feather HUZZAH ESP8266을 클라우드의 Azure IoT Hub에 연결

![DHT22, Feather HUZZAH ESP8266 및 IoT Hub 간 연결](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-will-do"></a>수행할 사항

Adafruit Feather HUZZAH ESP8266을 IoT Hub에 연결합니다. 그런 다음 ESP8266에서 응용 프로그램 예제를 실행하여 DHT22 센서로부터 온도 및 습도 데이터를 수집합니다. 마지막으로, 이 센서 데이터를 IoT Hub로 보냅니다.

> [!NOTE]
>다른 ESP8266 보드를 사용하는 경우에도 이러한 단계에 따라 IoT Hub에 계속 연결할 수 있습니다. 사용하는 ESP8266 보드에 따라 `LED_PIN`을 다시 구성해야 할 수 있습니다. 예를 들어, AI-Thinker의 ESP8266을 사용하는 경우 `0`을 `2`로 변경할 수 있습니다. 보드를 아직 사용하지 않는가요? [Microsoft Azure IoT 시작 키트 가져오기](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>알아볼 내용

* IoT Hub를 만들고 Feather HUZZAH ESP8266에 대한 장치를 등록하는 방법
* Feather HUZZAH ESP8266을 센서와 컴퓨터에 연결하는 방법
* Feather HUZZAH ESP8266에서 응용 프로그램 예제를 실행하여 센서 데이터를 수집하는 방법
* IoT Hub로 센서 데이터를 보내는 방법

## <a name="what-you-will-need"></a>필요한 사항

![이 자습서에 필요한 부품](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

이 작업을 완료하려면 Feather HUZZAH ESP8266 시작 키트에서 다음 부품이 필요합니다.

* Feather HUZZAH ESP8266 보드
* Micro USB - A형 USB 케이블

또한 개발 환경에는 다음 사항도 필요합니다.

* Windows 또는 Ubuntu가 실행되는 Mac 또는 PC
* Feather HUZZAH ESP8266을 연결할 무선 네트워크
* 구성 도구를 다운로드하기 위한 인터넷 연결
* [Arduino IDE](https://www.arduino.cc/en/main/software) 버전 1.6.8(이상) - 이전 버전은 AzureIoT 라이브러리에서 작동하지 않음


센서가 없는 경우 다음 항목은 선택 사항입니다. 시뮬레이션된 센서 데이터를 사용하는 옵션도 있습니다.

* Adafruit DHT22 온도 및 습도 센서
* 실험용 회로판
* M/M 점퍼 와이어

## <a name="create-an-iot-hub-and-register-a-device-for-feather-huzzah-esp8266"></a>IoT Hub를 만들기 및 Feather HUZZAH ESP8266에 대한 장치 등록

### <a name="create-your-azure-iot-hub-in-the-azure-portal"></a>Azure Portal에 Azure IoT Hub 만들기

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
1. **새로 만들기** > **사물 인터넷** > **IoT Hub**를 차례로 클릭합니다.

   ![iot hub 만들기](media/iot-hub-arduino-huzzah-esp8266-get-started/3_iot-hub-creation.png)

1. **IoT Hub** 창에서 IoT Hub에 필요한 정보를 입력합니다.

   ![iot hub 생성을 위한 기본 정보](media/iot-hub-arduino-huzzah-esp8266-get-started/4_iot-hub-provide-basic-info.png)

   * **이름**: IoT Hub의 이름입니다. 입력한 이름이 올바른 경우 녹색 확인 표시가 나타납니다.
   * **가격 및 크기 계층**: 체험 F1 계층을 선택합니다. 이 데모의 경우 이 옵션이면 충분합니다. [가격 및 크기 계층](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

   * **리소스 그룹**: IoT Hub를 호스트할 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-portal.md)(영문)를 참조하세요.
   * **위치**: IoT Hub가 생성된 곳에서 가장 가까운 위치를 선택합니다.
   * **대시보드에 고정**: 대시보드에서 IoT Hub에 손쉽게 액세스하려면 이 옵션을 선택합니다.
1. **만들기**를 클릭합니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. **알림** 창에서 진행률을 볼 수 있습니다.

   ![알림 창에서 iot hub 만들기 진행률 모니터링](media/iot-hub-arduino-huzzah-esp8266-get-started/5_iot-hub-monitor-creation-progress-notification-pane.png)

1. IoT Hub를 만들었으면 대시보드에서 클릭합니다. **호스트 이름**을 기록해둔 다음 **공유 액세스 정책**을 클릭합니다.

   ![IoT Hub의 호스트 이름 가져오기](media/iot-hub-arduino-huzzah-esp8266-get-started/6_iot-hub-get-hostname.png)

1. **공유 액세스 정책** 창에서 **iothubowner** 정책을 클릭한 다음 나중에 사용할 IoT Hub의 **연결 문자열**을 복사하여 기록해 둡니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)를 참조하세요.

   ![iot hub 연결 문자열 가져오기](media/iot-hub-arduino-huzzah-esp8266-get-started/7_iot-hub-get-connection-string.png)

### <a name="register-a-device-for-feather-huzzah-esp8266-in-your-iot-hub"></a>IoT Hub에서 Feather HUZZAH ESP8266에 대한 장치 등록

모든 IoT Hub에는 IoT Hub에 연결이 허용된 장치에 대한 정보를 저장하는 ID 레지스트리가 있습니다. 장치를 IoT Hub에 연결할 수 있으려면 IoT Hub의 ID 레지스트리에 해당 장치에 대한 항목이 있어야 합니다.

이 섹션에서는 iothub explorer CLI 도구를 사용하여 IoT Hub의 ID 레지스트리에 Feather HUZZAH ESP8266에 대한 장치를 등록합니다.

> [!NOTE]
> iothub explorer가 제대로 작동하려면 Node.js 4.x 이상이 필요합니다.

Feather HUZZAH ESP8266에 대한 장치를 등록하려면 다음 단계를 따르세요.

1. 최신 LTS 버전의 Node.js(NPM 포함)를 [다운로드](https://nodejs.org/en/download/)하여 설치합니다.
1. NPM을 사용하여 iothub explorer를 설치합니다.

   * Windows 7 이상

     관리자로 명령 프롬프트를 시작합니다. 다음 명령을 실행하여 iothub explorer를 설치합니다.

     ```bash
     npm install -g iothub-explorer
     ```
   * Ubuntu 16.04 이상

     Ctrl+Alt+T 바로 가기 키를 사용하여 터미널을 연 후 다음 명령을 실행합니다.

     ```bash
     sudo npm install -g iothub-explorer
     ```
   * macOS 10.1 이상

     터미널을 연 후 다음 명령을 실행합니다.

     ```bash
     npm install -g iothub-explorer
     ```
1. 다음 명령을 실행하여 IoT Hub에 로그인합니다.

   ```bash
   iothub-explorer login [your iot hub connection string]
   ```
1. `deviceID`가 있는 새 장치를 `new-device`로 등록하고 다음 명령을 실행하여 연결 문자열을 가져옵니다.

   ```bash
   iothub-explorer create new-device --connection-string
   ```

등록된 장치의 연결 문자열을 기록해 둡니다.

> [!NOTE]
> 연결 문자열의 등록된 장치를 보려면 `iothub-explorer list` 명령을 실행합니다.

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Feather HUZZAH ESP8266을 센서와 컴퓨터에 연결

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Feather HUZZAH ESP8266에 DHT22 온도 및 습도 센서 연결

다음과 같이 실험용 회로판과 점퍼 와이어를 사용하여 연결합니다. 센서가 없는 경우 시뮬레이션된 센서 데이터를 사용할 수 있으므로 이 섹션을 건너뜁니다.

![연결 참조](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)

센서 핀에는 다음 배선을 사용합니다.

| 시작(센서)           | 끝(보드)           | 케이블 색   |
| -----------------------  | ---------------------- | ------------: |
| VDD(핀 31F)            | 3V(핀 58H)           | 빨간색 케이블     |
| 데이터(핀 32F)           | GPIO 2(핀 46A)       | 파란색 케이블    |
| GND(핀 34F)            | GND(핀 56I)          | 검은색 케이블   |

자세한 내용은 [Adafruit DHT22 센서 설치](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor)(영문) 및 [Adafruit Feather HUZZAH Esp8266 핀 배열](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts)(영문)을 참조하세요.

이제 Feather Huzzah ESP8266을 작동 센서와 연결해야 합니다.

![dht22를 feather huzzah와 연결](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Feather HUZZAH ESP8266을 컴퓨터에 연결

다음과 같이 Micro USB - A형 USB 케이블을 사용하여 Feather HUZZAH ESP8266을 컴퓨터에 연결합니다.

![feather huzzah를 컴퓨터에 연결](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>직렬 포트 권한 추가 – Ubuntu만

Ubuntu를 사용하는 경우 Feather HUZZAH ESP826의 USB 포트에서 작동할 권한이 있는지 확인합니다. 직렬 포트 권한을 추가하려면 다음 단계를 수행합니다.

1. 터미널에서 다음 명령을 실행합니다.

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   다음 출력 중 하나가 표시됩니다.

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   출력에서 `uucp` 또는 `dialout`이 USB 포트의 그룹 소유자 이름임을 확인합니다.

1. 다음 명령을 실행하여 그룹에 사용자를 추가 합니다.

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>`은 이전 단계에서 얻은 그룹 소유자 이름입니다. `<username>`은 Ubuntu 사용자 이름입니다.

1. 변경 내용을 적용하려면 Ubuntu에서 로그아웃했다가 다시 로그인합니다.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>센서 데이터를 수집하여 IoT Hub에 보내기

이 섹션에서는 Feather HUZZAH ESP8266에 대한 응용 프로그램 예제를 배포하고 실행합니다. 응용 프로그램 예제는 Feather HUZZAH ESP8266의 LED를 깜박이고 DHT22 센서에서 수집된 온도 및 습도 데이터를 IoT Hub로 보냅니다.

### <a name="get-the-sample-application-from-github"></a>GitHub에서 샘플 응용 프로그램 가져오기

샘플 응용 프로그램은 GitHub에서 호스팅됩니다. GitHub에서 응용 프로그램 예제가 포함된 샘플 리포지토리를 복제합니다. 샘플 리포지토리를 복제하려면 다음 단계를 수행합니다.

1. 명령 프롬프트 또는 터미널 창을 엽니다.
1. 응용 프로그램 예제를 저장하려는 폴더로 이동합니다.
1. 다음 명령을 실행합니다.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Arduino IDE에 Feather HUZZAH ESP8266에 대한 패키지를 설치합니다.

1. 응용 프로그램 예제가 저장된 폴더를 엽니다.
1. Arduino IDE의 앱 폴더에서 app.ino 파일을 엽니다.

   ![arduino ide에서 응용 프로그램 예제 열기](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. Arduino IDE에서 **파일** > **기본 설정**을 클릭합니다.
1. **기본 설정** 대화 상자에서 **Additional Boards Manager URLs**(추가 보드 관리자 URL) 텍스트 상자 옆에 있는 아이콘을 클릭합니다.
1. 팝업 창에 다음 URL을 입력한 다음 **확인**을 클릭합니다.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![arduino ide에서 패키지 url 가리키기](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. **기본 설정** 대화 상자에서 **확인**을 클릭합니다.
1. **도구** > **보드** > **보드 관리자**를 클릭한 후 esp8266을 검색합니다.

   [보드 관리자]에서 ESP8266 2.2.0 이상 버전으로 설치되었음을 나타냅니다.

   ![esp8266 패키지 설치됨](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. **도구** > **보드** > **Adafruit HUZZAH ESP8266**를 클릭합니다.

### <a name="install-necessary-libraries"></a>필요한 라이브러리 설치

1. Arduino IDE에서 **스케치** > **라이브러리 포함** > **라이브러리 관리**를 클릭합니다.
1. 다음 라이브러리 이름을 하나씩 검색합니다. 라이브러리를 찾을 때마다 **설치**를 클릭합니다.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>실제 DHT22 센서가 없나요?

실제 DHT22 센서가 없는 경우 응용 프로그램 예제에서 온도 및 습도 데이터를 시뮬레이션할 수 있습니다. 시뮬레이션된 데이터를 사용하도록 응용 프로그램 예제를 설정하려면 다음 단계를 따릅니다.

1. `app` 폴더에서 `config.h` 파일을 엽니다.
1. 다음 코드 줄을 찾아 값을 `false`에서 `true`로 변경합니다.
   ```c
   define SIMULATED_DATA true
   ```
   ![시뮬레이션된 데이터를 사용하도록 응용 프로그램 예제 구성](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. `Control-s`로 파일을 저장합니다.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Feather HUZZAH ESP8266에 응용 프로그램 예제 배포

1. Arduino IDE에서 **도구** > **포트**를 클릭한 후 Feather HUZZAH ESP8266에 대한 직렬 포트를 클릭합니다.
1. **스케치** > **업로드**를 클릭하여 응용 프로그램 예제를 빌드하여 Feather HUZZAH ESP8266에 배포합니다.

### <a name="enter-your-credentials"></a>자격 증명 입력

업로드를 성공적으로 완료했으면 단계에 따라 자격 증명을 입력합니다.

1. Arduino IDE에서 **도구** > **직렬 모니터**를 클릭합니다.
1. 직렬 모니터 창의 오른쪽 아래에서 드롭다운 목록 두 개를 확인합니다.
1. 왼쪽 드롭다운 목록에서 **No line ending**(줄 끝 없음)을 선택합니다.
1. 오른쪽 드롭다운 목록에서 **115200 baud**를 선택합니다.
1. 직렬 모니터 창 맨 위에 있는 입력 상자에 정보를 입력하라는 메시지가 표시되면 다음 정보를 입력한 후 **보내기**를 클릭합니다.
   * Wi-Fi SSID
   * Wi-Fi 암호
   * 장치 연결 문자열

> [!Note]
> 자격 증명 정보는 Feather HUZZAH ESP8266의 EEPROM에 저장됩니다. Feather HUZZAH ESP8266 보드에서 초기화 단추를 클릭하면 응용 프로그램 예제에서 정보를 지울지 묻는 메시지가 표시됩니다. 정보를 지우고 정보를 입력하라는 메시지를 다시 표시하도록 하려면 `Y`를 입력합니다.

### <a name="verify-the-sample-application-is-running-successfully"></a>응용 프로그램 예제가 성공적으로 실행 중인지 확인합니다.

직렬 모니터 창에 다음 출력이 표시되고 Feather HUZZAH ESP8266의 LED가 깜박이면 응용 프로그램 예제가 성공적으로 실행 중입니다.

![arduino ide에서 최종 출력](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>다음 단계

IoT Hub에 Feather HUZZAH ESP8266를 성공적으로 연결하고 캡처된 센서 데이터를 IoT Hub에 전송했습니다. 

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

- [iothub-explorer를 사용하여 클라우드 장치 메시지 관리](iot-hub-explorer-cloud-device-messaging.md)
- [IoT Hub 메시지를 Azure 데이터 저장소에 저장](iot-hub-store-data-in-azure-table-storage.md)
- [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)
- [Azure Web Apps를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-web-apps.md)

