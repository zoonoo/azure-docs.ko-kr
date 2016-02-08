<properties
   pageTitle="Windows에서 C를 사용하여 장치 연결 | Microsoft Azure"
   description="Windows에서 실행되는 C로 작성된 응용 프로그램을 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
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
   ms.date="11/10/2015"
   ms.author="dobett"/>


# 미리 구성된 IoT Suite 원격 모니터링 솔루션에 장치 연결

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Windows에서 C 샘플 솔루션 빌드 및 실행

1. *Microsoft Azure IoT SDK* GitHub 리포지토리를 복제하고 Windows 데스크톱 환경에 *C용 Microsoft Azure IoT 장치 SDK*를 설치하려면 [Windows 개발 환경 설정][lnk-setup-windows] 지침을 따르세요.

2. Visual Studio 2015의 경우 리포지토리의 로컬 복사본에서 **c\\serializer\\samples\\remote\_monitoring\\windows** 폴더에 있는 **remote\_monitoring.sln** 솔루션을 엽니다.

3. **솔루션 탐색기**에서 **remote\_monitoring** 프로젝트의 **remote\_monitoring.c** 파일을 엽니다.

4. 파일에서 다음 코드를 찾습니다.

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

5. 원격 모니터링 솔루션 대시보드에서 **[Device Id]** 및 **[Device Key]**를 장치에 대한 값으로 바꿉니다.

6. 대시보드에서 **IoT Hub 호스트 이름**을 사용하여 **[IoTHub Name]** 및 **[IoTHub Suffix, i.e. azure-devices.net]**을 바꿉니다. 예를 들어 **IoT Hub 호스트 이름**이 **contoso.azure-devices.net**인 경우 아래와 같이 **[IoTHub Name]**을 **contoso**로 바꾸고 **[IoTHub Suffix, i.e. azure-devices.net]**을 **azure-devices.net**으로 바꿉니다.

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

7. **솔루션 탐색기**에서 **remote\_monitoring** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 클릭한 다음 **새 인스턴스 시작**을 클릭하여 빌드하고 샘플을 실행합니다. 응용 프로그램에서 샘플 원격 분석을 IoT Hub로 보낼 때 나타나는 메시지가 콘솔에 표시됩니다.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=AcomDC_0128_2016-->