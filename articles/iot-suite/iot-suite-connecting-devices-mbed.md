<properties
   pageTitle="mbed에서 C를 사용하여 장치 연결 | Microsoft Azure"
   description="mbed 장치에서 실행되는 C로 작성된 응용 프로그램을 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2015"
   ms.author="dobett"/>


# 미리 구성된 IoT Suite 원격 모니터링 솔루션에 장치 연결

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## mbed에서 C 샘플 솔루션 빌드 및 실행

다음 지침은 [mbed 사용 가능 Freescale FRDM-K64F][lnk-mbed-home] 장치를 원격 모니터링 솔루션에 연결하기 위한 단계를 설명합니다.

### 네트워크 및 데스크톱 컴퓨터에 장치 연결

1. 이더넷 케이블을 사용하여 mbed 장치를 네트워크에 연결합니다. 이 단계는 샘플 응용 프로그램에서 인터넷에 액세스해야 하기 때문에 필요합니다.

2. [mbed 시작][lnk-mbed-getstarted]을 참조하여 mbed 장치를 데스크톱 PC에 연결합니다.

3. 데스크톱 PC에서 Windows를 실행 중인 경우 [PC 구성][lnk-mbed-pcconnect]을 참조하여 mbed 장치에 대한 직렬 포트 액세스를 구성합니다.

### mbed 프로젝트 만들기 및 샘플 코드 가져오기

1. 웹 브라우저에서 mbed.org [개발자 사이트](https://developer.mbed.org/)로 이동합니다. 아직 등록하지 않은 경우 새 계정(무료)을 만들 수 있는 옵션이 표시됩니다. 그렇지 않은 경우 계정 자격 증명으로 로그인합니다. 그런 다음 페이지의 오른쪽 위 모서리에서 **컴파일러**를 클릭합니다. 그러면 작업 영역 관리 인터페이스로 이동합니다.

2. 사용 중인 하드웨어 플랫폼이 창의 오른쪽 위 모서리에 표시되는지 확인하거나 오른쪽 모서리에 있는 아이콘을 클릭하여 하드웨어 플랫폼을 선택합니다.

3. 주 메뉴에서 **가져오기**를 클릭합니다. 그런 다음 **여기를 클릭**을 클릭하여 mbed 구형 로고 옆에 있는 URL 링크에서 가져옵니다.

    ![][6]

4. 팝업 창에서 샘플 코드 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/에 대한 링크를 입력한 다음 **가져오기**를 클릭합니다.

    ![][7]

5. 다양한 라이브러리에서 가져온 해당 프로젝트를 가져오는 mbed 컴파일러 창에서 볼 수 있습니다. 일부는 Azure IoT 팀([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/))에서 제공 및 유지 관리되며 일부는 mbed 라이브러리 카탈로그에서 사용할 수 있는 타사 라이브러리입니다.

    ![][8]

6. remote\_monitoring\\remote\_monitoring.c 파일을 열고 파일에서 다음 코드를 찾습니다.

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. [장치 ID] 및 [장치 키]를 장치 데이터와 대체합니다. IoT Hub 호스트 이름을 사용하여 [IoTHub 이름] 및 [IoTHub 접미사, 즉 azure-devices.net] 자리 표시자를 바꿉니다. 예를 들어 IoT Hub 호스트 이름이 contoso.azure-devices.net인 경우 contoso **hubName**이고 나머지는 **hubSuffix**입니다.

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### 프로그램을 빌드 및 실행

1. **컴파일**을 클릭하여 프로그램을 빌드합니다. 안전하게 모든 경고를 무시할 수 있지만 빌드가 오류를 생성하는 경우 계속하기 전에 해결하십시오.

2. 빌드가 성공한 경우 mbed 컴파일러 웹 사이트에서는 프로젝트 이름이 지정된 .bin 파일을 생성하여 로컬 컴퓨터에 다운로드합니다. .bin 파일을 장치에 복사합니다. .bin 파일을 장치에 저장하면 장치가 다시 시작하고 .bin 파일에 포함된 프로그램이 실행됩니다. 언제든지 mbed 장치의 리셋 단추를 눌러 프로그램을 수동으로 다시 시작할 수 있습니다.

3. PuTTY와 같은 SSH 클라이언트 응용 프로그램을 사용하여 장치에 연결합니다. Windows 장치 관리자를 확인하여 장치가 사용하는 직렬 포트를 확인할 수 있습니다.

    ![][11]

4. PuTTY에서 **직렬** 연결 형식을 클릭합니다. 장치는 일반적으로 115200 보드로 연결되므로 **속도** 상자에 115200을 입력합니다. 그런 다음 **열기**를 클릭합니다.

5. 프로그램이 실행을 시작합니다. 연결할 때 프로그램이 자동으로 시작되지 않는 경우 보드를 다시 설정해야 할 수 있습니다(CTRL + Break를 누르거나 보드의 다시 설정 단추를 누름).

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

<!---HONumber=AcomDC_0211_2016-->