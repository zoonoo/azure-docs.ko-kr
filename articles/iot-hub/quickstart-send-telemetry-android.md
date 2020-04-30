---
title: Azure IoT Hub에 원격 분석 데이터 전송 빠른 시작(Android) | Microsoft Docs
description: 이 빠른 시작에서는 샘플 Android 애플리케이션을 실행하여 시뮬레이션된 원격 분석 데이터를 IoT 허브로 보내고, 클라우드에서 처리할 원격 분석 데이터를 IoT 허브에서 읽습니다.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: ab947dd5485c68e8b971b1cf98fc389945a69914
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769307"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>빠른 시작: Android 디바이스에서 IoT 원격 분석 데이터 전송

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

이 빠른 시작에서는 실제 또는 시뮬레이션된 디바이스에서 실행 중인 Android 애플리케이션에서 Azure IoT Hub로 원격 분석 데이터를 보냅니다. IoT Hub는 스토리지 또는 처리를 위해 IoT 디바이스에서 클라우드로 다량의 원격 분석 데이터를 수집할 수 있게 해주는 Azure 서비스입니다. 이 빠른 시작에서는 미리 작성된 Android 애플리케이션을 사용하여 원격 분석 데이터를 보냅니다. Azure Cloud Shell을 사용하여 IoT Hub에서 원격 분석 데이터가 읽힙니다. 애플리케이션을 실행하기 전에 IoT 허브를 만들고 허브에 디바이스를 등록합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android SDK 27이 설치된 Android Studio](https://developer.android.com/studio/). 자세한 내용은 [android-installation](https://developer.android.com/studio/install)을 참조하세요. 이 문서의 샘플에서는 Android SDK 27을 사용합니다.

* [샘플 Android 애플리케이션](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample). 이 샘플은 [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) 리포지토리에 속합니다.

* 방화벽에서 포트 8883이 열려 있습니다. 이 빠른 시작의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT 확장 추가

다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IOT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 고유의 명령을 추가합니다.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyAndroidDevice**: 등록 중인 디바이스의 이름입니다. 표시된 대로 **MyAndroidDevice**를 사용하는 것이 좋습니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서도 해당 이름을 사용해야 하며, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

    **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    이 빠른 시작의 뒷부분에서 이 값을 사용하여 원격 분석 데이터를 보냅니다.

## <a name="send-simulated-telemetry"></a>시뮬레이션된 원격 분석 전송

1. Android Studio에서 GitHub 샘플 Android 프로젝트를 엽니다. 프로젝트는 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 리포지토리의 복제된 또는 다운로드된 사본인 다음 디렉터리에 있습니다.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Android Studio에서 샘플 프로젝트의 *gradle.properties*를 열고 **Device_Connection_String** 자리 표시자를 이전에 기록한 디바이스 연결 문자열로 바꿉니다.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Android Studio에서 **파일** > **Gradle 파일과 프로젝트 동기화**를 클릭합니다. 빌드가 완료되는지 확인합니다.

   > [!NOTE]
   > 프로젝트 동기화에 실패하는 경우는 다음 이유 중 하나일 수 있습니다.
   >
   > * 프로젝트에서 참조한 Android Gradle 플러그 인 및 Gradle의 버전이 사용자의 Android Studio 버전과 맞지 않습니다. [이러한 지침](https://developer.android.com/studio/releases/gradle-plugin)에 따라 설치용 플러그 인 및 Gradle의 올바른 버전을 참조하고 설치합니다.
   > * Android SDK에 대한 사용권 계약에 서명하지 않았습니다. 빌드 출력의 지침에 따라 사용권 계약에 서명하고 SDK를 다운로드합니다.

4. 빌드가 완료되면 **실행** >  **'앱' 실행**을 클릭합니다. 물리적 Android 디바이스 또는 Android 에뮬레이터에서 실행되도록 앱을 구성합니다. 물리적 디바이스 또는 에뮬레이터에서 Android 앱을 실행하는 방법에 대한 자세한 내용은 [앱 실행](https://developer.android.com/training/basics/firstapp/running-app)을 참조하세요.

5. 앱이 로드되면 **시작** 단추를 클릭하여 IoT Hub로 원격 분석 데이터를 보내기 시작합니다.

    ![애플리케이션](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>허브에서 원격 분석 읽기

이 섹션에서는 [IoT 확장](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)과 함께 Azure Cloud Shell을 사용하여 Android 디바이스에서 보내는 디바이스 메시지를 모니터링합니다.

1. Azure Cloud Shell을 통해 다음 명령을 실행하여 IoT 허브의 메시지를 연결하고 읽습니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    다음 스크린샷은 Android 디바이스가 보낸 원격 분석 데이터를 IoT 허브가 수신할 때 출력을 보여줍니다.

      ![Azure CLI를 사용하여 디바이스 메시지 읽기](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 IoT 허브를 설치하고, 디바이스를 등록하고, Android 애플리케이션을 사용하여 허브에 시뮬레이션된 원격 분석 데이터를 보내고, Azure Cloud Shell을 사용하여 허브에서 원격 분석 데이터를 읽었습니다.

백 엔드 애플리케이션에서 시뮬레이션된 디바이스를 제어하는 방법을 알아보려면 다음 빠른 시작을 계속합니다.

> [!div class="nextstepaction"]
> [빠른 시작: IoT 허브에 연결된 디바이스 제어](quickstart-control-device-android.md)