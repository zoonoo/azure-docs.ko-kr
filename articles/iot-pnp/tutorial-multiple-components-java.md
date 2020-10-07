---
title: IoT 플러그 앤 플레이 샘플 Java 구성 요소 디바이스 코드를 IoT Hub에 연결 | Microsoft Docs
description: 여러 구성 요소를 사용하고 IoT 허브에 연결하는 IoT 플러그 앤 플레이 샘플 Java 디바이스 코드를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a7c1f0d207a113b2c12010cbc0a8876edd9269bc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577257"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-java"></a>자습서: 샘플 IoT 플러그 앤 플레이 여러 구성 요소 디바이스 애플리케이션을 IoT Hub에 연결(Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

이 자습서에서는 여러 구성 요소 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure CLI를 사용하여 전송되는 원격 분석을 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 Java로 작성되었으며 Java용 Azure IoT 디바이스 SDK에 포함되어 있습니다. Azure CLI를 사용하면 솔루션 빌더가 디바이스 코드를 보지 않고도 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

이 자습서에서는 구성 요소를 사용하여 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 허브로 전송된 정보를 확인하는 방법을 보여 줍니다. 샘플 애플리케이션은 Java로 작성되었으며 Java용 Azure IoT 디바이스 SDK에 포함되어 있습니다. Azure IoT 탐색기 도구를 사용하면 솔루션 빌더가 디바이스 코드를 보지 않고도 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Windows에서 이 자습서를 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* Java SE Development Kit 8. [Azure 및 Azure Stack에 대한 Java 장기 지원](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)의 **장기 지원** 에서 **Java 8**을 선택합니다.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>코드 다운로드

[빠른 시작: Windows에서 실행되는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 IoT Hub(Java)에 연결](quickstart-connect-device-java.md)을 완료한 경우 이미 리포지토리를 복제한 것입니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Azure IoT Java SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-java) GitHub 리포지토리를 이 위치에 복제합니다.

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

이 작업을 완료하는 데 몇 분 정도가 걸립니다.

## <a name="build-the-code"></a>코드 빌드

Windows에서 복제된 Java SDK 리포지토리의 루트 폴더로 이동합니다. 다음 명령을 실행하여 종속성을 빌드합니다.

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>디바이스 샘플 실행

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

샘플 애플리케이션을 실행하려면 *\device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample* 폴더로 이동하고 다음 명령을 실행합니다.

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었으며, 허브로 원격 분석 데이터를 보내기 시작했습니다. 다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>코드 검토

이 샘플은 IoT 플러그 앤 플레이 온도 컨트롤러 디바이스를 구현합니다. 이 샘플에서 구현하는 모델은 [여러 구성 요소](concepts-components.md)를 사용합니다. [온도 디바이스용 DTDL(Digital Twins 정의 언어) 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

디바이스 코드는 표준 `DeviceClient` 클래스를 사용하여 IoT 허브에 연결합니다. 디바이스는 연결 요청에서 구현하는 DTDL 모델의 모델 ID를 보냅니다. 모델 ID를 보내는 디바이스는 IoT 플러그 앤 플레이 디바이스입니다.

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

모델 ID는 다음 코드 조각과 같이 코드에 저장됩니다.

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

디바이스가 IoT 허브에 연결되면 코드에 명령 처리기를 등록합니다.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

두 개의 자동 온도 조절기 구성 요소에 원하는 속성 업데이트에 대한 별도의 처리기가 있습니다.

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

샘플 코드는 각 자동 온도 조절기 구성 요소에서 원격 분석을 보냅니다.

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

`sendTemperatureReading` 메서드는 `PnpHhelper` 클래스를 사용하여 각 구성 요소에 대한 메시지를 만듭니다.

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

`PnpHelper` 클래스에는 여러 구성 요소 모델에서 사용할 수 있는 다른 예제 메서드가 포함되어 있습니다.

Azure IoT 탐색기 도구를 사용하여 두 가지 자동 온도 조절기 구성 요소의 원격 분석 및 속성을 볼 수 있습니다.

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Azure IoT 탐색기의 여러 구성 요소 디바이스":::

Azure IoT 탐색기 도구를 사용하여 두 가지 자동 온도 조절기 구성 요소 중 하나 또는 기본 구성 요소에서 명령을 호출할 수도 있습니다.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT 플러그 앤 플레이 디바이스를 구성 요소와 IoT 허브에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스 모델에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT 플러그 앤 플레이 모델링 개발자 가이드](concepts-developer-guide-device-csharp.md)
