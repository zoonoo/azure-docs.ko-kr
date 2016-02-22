<properties
   pageTitle="Linux에서 C를 사용하여 장치 연결 | Microsoft Azure"
   description="Linux에서 실행되는 C로 작성된 응용 프로그램을 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
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
   ms.date="02/04/2016"
   ms.author="dobett"/>


# 미리 구성된 IoT Suite 원격 모니터링 솔루션에 장치 연결

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Linux에서 C 샘플 솔루션 빌드 및 실행

1. *Microsoft Azure IoT SDK* GitHub 리포지토리를 복제하고 Linux 데스크톱 환경에 *C용 Microsoft Azure IoT 장치 SDK*를 설치하려면 [Linux 개발 환경 설정][lnk-setup-linux] 지침을 따르세요.

2. 텍스트 편집기에서 **c/serializer/samples/remote\_monitoring/remote\_monitoring.c** 파일을 엽니다.

3. 파일에서 다음 코드를 찾습니다.

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. 원격 모니터링 솔루션 대시보드에서 **[Device Id]** 및 **[Device Key]**를 장치에 대한 값으로 바꿉니다.

5. 대시보드에서 **IoT Hub 호스트 이름**을 사용하여 **[IoTHub Name]** 및 **[IoTHub Suffix, i.e. azure-devices.net]**을 바꿉니다. 예를 들어 **IoT Hub 호스트 이름**이 **contoso.azure-devices.net**인 경우 아래와 같이 **[IoTHub Name]**을 **contoso**로 바꾸고 **[IoTHub Suffix, i.e. azure-devices.net]**을 **azure-devices.net**으로 바꿉니다.

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. 변경 내용을 저장하고 샘플을 빌드합니다. 샘플을 빌드하려면 **c/build\_all linux** 디렉터리의 build.sh 스크립트를 실행하면 됩니다. 이 빌드 스크립트는 컴파일된 샘플 프로그램을 저장할 **cmake** 폴더를 생성합니다.

7. **cmake/serializer/samples/remote\_monitoring/remote\_monitoring** 응용 프로그램 예제를 실행합니다.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=AcomDC_0211_2016-->