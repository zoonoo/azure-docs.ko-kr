<properties
   pageTitle="미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치 연결 | Microsoft Azure"
   description="온도 및 습도와 관련된 예를 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
   services="iot-hub"
   documentationCenter="na"
   authors="hegate"
   manager="jamesosb"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="hegate"/>


# Azure IoT Suite 원격 모니터링 솔루션에 장치 연결


## 시나리오 개요

이 예에서는 3개의 시뮬레이션된 데이터 원본: 외부 온도, 내부 온도 및 습도를 갖습니다. 단순화를 위한 목적으로 실제 센서를 사용하지 않지만(데이터가 클라이언트 코드에서 자체 생성됨) 다음 단계로 선호하는 센서를 연결하고 실제 데이터를 보내는 것이 좋습니다. 자세한 내용은 솔루션 섹션 확장에 연결된 리소스를 참조하세요.

## 필수 조건

### IoT Suite 프로비전

미리 구성된 원격 모니터링 솔루션을 아직 프로비전하지 않은 경우 [여기](http://www.microsoft.com/ko-KR/server-cloud/internet-of-things/getting-started.aspx)에서 프로비전할 수 있습니다.


### 원격 모니터링 솔루션에서 장치 프로비전
```
Note: if you have already provisioned a device on your solution, you can skip this step.
```
미리 구성된 솔루션에 연결된 장치를 얻으려면 대시보드에서 장치 자격 증명을 가져와야 합니다. 장치를 식별할 수 있도록 해당 클라이언트 응용 프로그램에서 사용됩니다. 이 단계를 수행

1.  대시보드의 왼쪽 아래 모서리에서 “장치 추가"를 클릭합니다. ![][1]
2.  사용자 지정 장치에서 "새로 추가" 단추를 클릭합니다.

    ![][2]
3.  realdevice1과 같은 이름을 입력하여 고유한 장치 ID를 선택하고 ID 확인을 클릭하여 이름이 아직 사용되지 않았는지 확인합니다. ![][3]

5. 제공된 자격 증명(장치 ID, IoT Hub 호스트 이름 및 장치 키)을 복사하세요. 클라이언트 응용 프로그램에서 장치를 솔루션에 연결하려면 나중에 필요합니다. ![][4]
6. 장치 섹션에 장치가 올바르게 표시되는지 확인합니다. 상태는 "보류 중"이 됩니다. 장치에서 클라우드 연결이 설정될 때까지 예상됩니다.

    ![][5]

[1]: ./media/iot-suite-connecting-devices/suite0.png
[2]: ./media/iot-suite-connecting-devices/suite1.png
[3]: ./media/iot-suite-connecting-devices/suite2.png
[4]: ./media/iot-suite-connecting-devices/suite3.png
[5]: ./media/iot-suite-connecting-devices/suite5new.png

이제 샘플을 계속하기 위해 사용할 언어를 선택합니다. 이 자습서에서는 C 및 node.js에 대한 샘플 코드를 만들었지만 C# 및 Java에서도 구현할 수 있습니다.

## C를 사용하여 장치 데이터를 원격 모니터링 솔루션에 보내기


### Linux에서 장치 실행

1. 환경 설정: 장치 SDK를 사용한 적이 없는 경우 Linux에서 환경을 설정하는 방법에 대해 [여기](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux)에서 알아보세요.

1. 텍스트 편집기에서 파일 **c/serializer/samples/serializer/remote\_monitoring.c**를 엽니다.

2. 파일에서 다음 코드를 찾습니다.: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. "[장치 ID]", "[장치 키]를 장치 데이터와 대체합니다.

4. IoT Hub 호스트 이름 장치 데이터를 사용하여 IoTHub 이름 및 IoTHub 접미사를 입력합니다. 이렇게 하려면 IoTHub + IoTHubSuffix를 분할해야 합니다. 예: IoT Hub 호스트 이름이 "Contoso.azure devices.net"인 경우 "Contoso"는 IoTHub 이름이 되고 나머지는 접미사입니다. 다음과 같이 표시되어야 합니다.

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

5. 변경 내용을 저장하고 샘플을 빌드합니다. 샘플을 빌드하려면 **c/build\_all linux** 디렉터리의 build.sh 스크립트를 실행할 수 있습니다.

6. **c/serializer/samples/remote\_monitoring/linux/remote\_monitoring** 샘플 응용 프로그램을 실행합니다.

등록된 장치 및 데이터 시각화

7. 원격 모니터링 솔루션 대시보드로 돌아가기 장치의 상태를 장치 목록에서 실행 중으로 변경해야 합니다. ![][18]

8. 대시보드를 클릭하여 들어오는 데이터를 봅니다. 샘플은 내부 온도에 대해 50 단위, 외부 온도에 대해 55 단위 및 습도에 대해 50 단위를 전송하도록 구성됩니다. 대시보드가 기본으로 온도 및 습도만 표시하는지 확인합니다.

8. 이제 명령 및 컨트롤 [섹션](#command)으로 이동하여 원격 모니터링 솔루션에서 장치의 온도를 변경하는 방법을 배웁니다.


### Windows에서 장치 실행


1. 환경 설정: 장치 SDK를 사용한 적이 없는 경우 Windows에서 환경을 설정하는 방법에 대해 [여기](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows)에서 알아보세요.

1. Visual Studio 2015의 새 인스턴스를 시작합니다. 레포지토리의 로컬 복사본의 **c\\serializer\\build\\windows** 폴더에서 **remote\_monitoring.sln** 솔루션을 엽니다.

2. Visual Studio에서 **솔루션 탐색기**에서 샘플 폴더로 이동합니다. **remote\_monitoring** 프로젝트에서 **remote\_monitoring.c** 파일을 엽니다.

2. 파일에서 다음 코드를 찾습니다.: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. "[장치 ID]", "[장치 키]를 장치 데이터와 대체합니다.

4. IoT Hub 호스트 이름 장치 데이터를 사용하여 IoTHub 이름 및 IoTHub 접미사를 입력합니다. 이 작업을 수행하려면 다음과 같이 분할해야 합니다.

    IoT Hub 호스트 이름이 Contoso.azure devices.net인 경우 Contoso는 IoTHub 이름이 되고 나머지는 접미사가 됩니다. 다음과 같이 표시되어야 합니다.

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. **솔루션 탐색기**에서 **remote\_monitoring** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 클릭한 다음 **새 인스턴스 시작**을 클릭하여 빌드하고 샘플을 실행합니다. 콘솔은 응용 프로그램에서 장치-클라우드 메시지를 IoT Hub에 보낼 때 메시지를 표시합니다.

등록된 장치 및 데이터 시각화

7. 원격 모니터링 솔루션 대시보드로 돌아가기 장치의 상태를 장치 목록에서 실행 중으로 변경해야 합니다. ![][18]

8. 대시보드를 클릭하여 들어오는 데이터를 봅니다. 샘플은 내부 온도에 대해 50 단위, 외부 온도에 대해 55 단위 및 습도에 대해 50 단위를 전송하도록 구성됩니다. 대시보드가 기본으로 온도 및 습도만 표시하는지 확인합니다.

8. 이제 명령 및 컨트롤 [섹션](#command)으로 이동하여 원격 모니터링 솔루션에서 장치의 온도를 변경하는 방법을 배웁니다.


8. 이제 명령 및 컨트롤 섹션으로 이동하여 원격 모니터링 솔루션에서 장치의 온도를 변경하는 방법을 배웁니다.

### mbed에서 장치 실행

다음 지침은 [mbed 사용 가능 Freescale FRDM-K64F](https://developer.mbed.org/platforms/FRDM-K64F/) 장치를 Azure IoT Hub에 연결하기 위한 단계를 설명합니다.


요구 사항

- 필요한 하드웨어: [mbed 사용 가능 Freescale K64F ](https://developer.mbed.org/platforms/FRDM-K64F/) 또는 유사한 장치.

장치 연결

- 이더넷 케이블을 사용하여 보드를 네트워크에 연결합니다. 이 단계는 샘플이 인터넷 액세스에 의존하므로 필요합니다.

- 마이크로 USB 케이블을 사용하여 장치를 컴퓨터에 연결합니다. "시작" 섹션의 [여기](https://developer.mbed.org/platforms/frdm-k64f/)의 그림과 같이 케이블을 장치에서 올바른 USB 포트에 연결해야 합니다.

- [mbed handbook의 지침](https://developer.mbed.org/handbook/SerialPC)을 따라 개발 컴퓨터에서 장치의 직렬 연결을 설정합니다. Windows에 있는 경우 [여기](http://developer.mbed.org/handbook/Windows-serial-configuration#1-download-the-mbed-windows-serial-port)에 있는 Windows 직렬 포트 드라이버를 설치합니다.

mbed 프로젝트 만들기 및 샘플 코드 가져오기

- 웹 브라우저에서 mbed.org [개발자 사이트](https://developer.mbed.org/)로 이동합니다. 아직 등록하지 않은 경우 새 계정(무료)을 만들 수 있는 옵션이 표시됩니다. 그렇지 않은 경우 계정 자격 증명으로 로그인합니다. 그런 다음 페이지의 오른쪽 위 모서리에서 **컴파일러**를 클릭합니다. 그러면 작업 영역 관리 인터페이스로 이동합니다.

- 사용 중인 하드웨어 플랫폼이 창의 오른쪽 위 모서리에 표시되는지 확인하거나 오른쪽 모서리에 있는 아이콘을 클릭하여 하드웨어 플랫폼을 선택합니다.

- 주 메뉴에서 **가져오기**를 클릭합니다. 그런 다음 **여기를 클릭**을 클릭하여 mbed 구형 로고 옆에 있는 URL 링크에서 가져옵니다.

	![][6]

- 팝업 창에서 샘플 코드에 대한 링크 입력 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/

	![][7]

- 다양한 라이브러리에서 가져온 해당 프로젝트를 가져오는 mbed 컴파일러에서 볼 수 있습니다. 일부는 Azure IoT 팀([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [iothub\_http\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_http_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/))에서 제공 및 유지 관리되며 일부는 mbed 라이브러리 카탈로그에서 사용할 수 있는 타사 라이브러리입니다.

	![][8]

- remote\_monitoring\\remote\_monitoring.c를 열고 파일에서 다음 코드를 찾습니다. ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. [장치 ID] 및 [장치 키]를 장치 데이터와 대체합니다.

4. IoT Hub 호스트 이름 장치 데이터를 사용하여 IoTHub 이름 및 IoTHub 접미사를 입력합니다. 이 작업을 수행하려면 다음과 같이 분할해야 합니다.

    IoT Hub 호스트 이름이 Contoso.azure devices.net인 경우 Contoso는 IoTHub 이름이 되고 나머지는 접미사가 됩니다. 다음과 같이 표시되어야 합니다.

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```
    ![][9]

프로그램을 빌드 및 실행

- **컴파일**을 클릭하여 프로그램을 빌드합니다. 안전하게 모든 경고를 무시할 수 있지만 빌드가 오류를 생성하는 경우 계속하기 전에 해결하십시오.

- 빌드가 성공한 경우 프로젝트의 이름으로 .bin 파일이 생성됩니다. .bin 파일을 장치에 복사합니다. .bin 파일을 장치에 저장하면 장치에 대한 현재 터미널 세션을 다시 설정해야 합니다. 다시 연결되면 터미널을 수동으로 다시 설정하거나 새 터미널을 시작합니다. 이를 통해 mbed 장치를 다시 설정하고 프로그램 실행을 시작할 수 있습니다.

- PuTTY와 같은 SSH 클라이언트 응용 프로그램을 사용하여 장치에 연결합니다. Windows 장치 관리자를 확인하여 장치가 사용하는 직렬 포트를 확인할 수 있습니다.


- PuTTY에서 **직렬** 연결 형식을 클릭합니다. 장치는 115200에서 연결할 가능성이 크므로 **속도** 상자에 해당 값을 입력합니다. 그런 다음 **열기**를 클릭합니다.

	![][11]

프로그램이 실행을 시작합니다. 연결할 때 프로그램이 자동으로 시작되지 않는 경우 보드를 다시 설정해야 할 수 있습니다(CTRL + Break를 누르거나 보드의 다시 설정 단추를 누름).

등록된 장치 및 데이터 7을 시각화합니다. 원격 모니터링 솔루션 대시보드로 돌아가기 장치의 상태를 장치 목록에서 실행 중으로 변경해야 합니다. ![][18]

8. 대시보드를 클릭하여 들어오는 데이터를 봅니다. 샘플은 내부 온도에 대해 50 단위, 외부 온도에 대해 55 단위 및 습도에 대해 50 단위를 전송하도록 구성됩니다. 대시보드가 기본으로 온도 및 습도만 표시하는지 확인합니다.

8. 이제 명령 및 컨트롤 [섹션](#command)으로 이동하여 원격 모니터링 솔루션에서 장치의 온도를 변경하는 방법을 배웁니다.



[6]: ./media/iot-suite-connecting-devices/mbed1.png
[7]: ./media/iot-suite-connecting-devices/mbed2a.png
[8]: ./media/iot-suite-connecting-devices/mbed3a.png
[9]: ./media/iot-suite-connecting-devices/suite6.png
[10]: ./media/iot-suite-connecting-devices/mbed5a.png
[11]: ./media/iot-suite-connecting-devices/mbed6.png
[12]: ./media/iot-suite-connecting-devices/mbed7.png

명령 및 제어를 실행하는 방법을 알아보려면 이 자습서의 해당 섹션 아래로 이동하세요.

## node.js를 사용하여 장치 데이터를 원격 모니터링 솔루션에 보내기



-   azure iot-sdk repo에서 다음 파일: packages.json(/node/common 아래) 및 remote\_monitoring.js(node/device/samples/ 아래)를 찾습니다. 장치에 복사하고 동일한 폴더에 넣습니다.

- remote-monitoring.js 파일을 열고 다음 변수를 찾습니다.


   ```
   static const char* deviceId = "[Device Id]";
   static const char* deviceKey = "[Device Key]";
   static const char* hubName = "[IoTHub Name]";
   static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
   ```

-  "[장치 ID]", "[장치 키]를 장치 데이터와 대체합니다.

-  IoT Hub 호스트 이름 장치 데이터를 사용하여 IoTHub 이름 및 IoTHub 접미사를 입력합니다. 이 작업을 수행하려면 다음과 같이 분할해야 합니다.

   IoT Hub 호스트 이름이 Contoso.azure devices.net인 경우 Contoso는 IoTHub 이름이 되고 나머지는 접미사가 됩니다. 다음과 같이 표시되어야 합니다.


   ```
     static const char* deviceId = "mydevice";
   static const char* deviceKey = "mykey";
   static const char* hubName = "Contoso";
   static const char* hubSuffix = "azure-devices.net";
   ```


- 파일을 저장합니다. 대상 폴더에서 다음 명령을 실행합니다.

```
npm install
node .
```

3.  각 변수를 이전 단계에서 수집한 정보로 대체합니다. 변경 내용을 저장합니다.


4. 셸(Linux) 또는 Node.js 명령 프롬프트(Windows)를 열고 **node\\samples** 폴더로 이동합니다. 그런 다음 다음 명령을 사용하여 샘플 응용 프로그램을 실행합니다.

    ```
    node simple_sample_remotemonitoring.js
    ```

장치 및 들어오는 데이터 시각화

6. 미리 구성된 솔루션 포털에서 장치 섹션을 클릭하여 장치의 상태가 "실행 중"으로 변경되고 모든 제조업체 데이터를 볼 수 있는지 확인합니다.

7. 대시보드를 클릭하고 "볼 장치"에서 장치를 선택합니다. 이제 원격 모니터링 솔루션에서 모니터링되는 원격 분석 데이터가 표시됩니다.


## <a name="command"></a>대시보드에서 장치 명령 및 제어

이제 장치가 연결되었고 장치에서 자체 생성된 온도를 전송하므로 IoT Hub에서 원격으로 장치를 명령 및 제어할 수 있습니다. 비즈니스 응용 프로그램에 맞는 여러 종류의 명령을 구현할 수 있습니다. 이 경우 솔루션에서 제어할 필요가 있으므로 온도에 변경을 구현했습니다. 명령을 보내려면 다음을 수행해야 합니다.

-  장치 목록(왼쪽 메뉴에서 장치 섹션을 찾을 수 있음)의 장치 ID를 클릭합니다.

	![][13]

- 장치 세부 정보가 표시되어 있는 오른쪽 메뉴에서 "명령 전송"을 클릭합니다.


- 실행하려는 명령을 선택합니다. 이 경우 장치가 설정된 온도를 변경하려고 하므로 "온도 설정"을 선택합니다. 해당 명령을 선택하고 온도 값을 선택합니다. 명령 전송을 클릭하고 새 온도를 장치에 밀어 넣습니다. 참고: 명령 기록에서 명령의 결과가 "보류 중"으로 표시됩니다. 단순화하기 위해 이 샘플은 IoT Hub에 응답하도록 장치의 어떠한 논리도 구현하지 않았습니다. 솔루션을 확장하여 수행할 수 있습니다.

	![][14]
- 대시보드로 돌아가서 업데이트된 데이터가 들어오는지 확인합니다. 온도에서 업데이트된 통계 및 원격 분석 기록에서 표시되고 있는 새로운 데이터가 표시됩니다.




[13]: ./media/iot-suite-connecting-devices/suite4.png
[14]: ./media/iot-suite-connecting-devices/suite7-1.png
[15]: ./media/iot-suite-connecting-devices/suite8a.png
[16]: ./media/iot-suite-connecting-devices/mbed4a.png
[17]: ./media/iot-suite-connecting-devices/suite9.png
[18]: ./media/iot-suite-connecting-devices/suite10.png


## 다음 단계

이 샘플의 기능을 확장하는 방법은 실제 센서를 장치에 연결하여 실제 날짜를 보내고 명령을 구현하고 기능을 제어하는 등 다양합니다. 원격 모니터링 솔루션을 확장하는 방법에 대한 자세한 내용은 [지침](articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md)을 사용하세요.

<!---HONumber=Oct15_HO2-->