---
title: Azure IoT Hub 빠른 시작(Android)에서 디바이스 제어 | Microsoft Docs
description: 이 빠른 시작에서는 두 개의 Java 응용 프로그램 샘플을 실행합니다. 그 중 한 애플리케이션은 허브에 연결된 디바이스를 원격으로 제어할 수 있는 서비스 애플리케이션입니다. 또 다른 애플리케이션은 원격으로 제어할 수 있는 허브에 연결된 물리적 디바이스 또는 시뮬레이션된 디바이스에서 실행됩니다.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/19/2018
ms.author: wesmc
ms.openlocfilehash: 28884b9b7d29a3c8da1fee0f0b54269bdaadf926
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427625"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>빠른 시작: IoT 허브(Android)에 연결된 디바이스 제어

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub는 IoT 디바이스에서 클라우드로 다량의 원격 분석 데이터를 수집하고 클라우드에서 디바이스를 관리할 수 있게 해주는 Azure 서비스입니다. 이 빠른 시작에서는 *직접 메서드*를 사용하여 IoT 허브에 연결된 시뮬레이션된 장치를 제어합니다. 직접 메서드를 사용하여 IoT 허브에 연결된 디바이스의 동작을 원격으로 변경할 수 있습니다.

빠른 시작에서는 미리 작성된 두 개의 Java 응용 프로그램을 사용합니다.

* 백 엔드 서비스 애플리케이션에서 호출된 직접 메서드에 응답하는 시뮬레이션된 디바이스 애플리케이션입니다. 직접 메서드 호출을 수신하기 위해 이 애플리케이션을 IoT 허브의 디바이스별 엔드포인트에 연결합니다.

* Android 디바이스에서 직접 메서드를 호출하는 서비스 애플리케이션입니다. 장치에서 직접 메서드를 호출하려면 이 응용 프로그램을 IoT 허브의 서비스 측 엔드포인트에 연결합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건


* https://developer.android.com/studio/의 Android Studio. Android Studio 설치에 대한 자세한 내용은 [android-installation](https://developer.android.com/studio/install)을 참조하세요. 

* 이 문서의 샘플에서는 Android SDK 27을 사용합니다. 

* 이 빠른 시작에서 필요한 두 가지 샘플 애플리케이션은 [디바이스 SDK 샘플 Android 애플리케이션](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) 및 [서비스 SDK 샘플 Android 애플리케이션](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample)입니다. 이 두 샘플은 Github에 있는 azure-iot-samples-java 리포지토리의 일부입니다. [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) 리포지토리를 다운로드하거나 복제합니다.


## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 [빠른 시작: 원격 분석을 디바이스에서 IoT 허브로 전송](quickstart-send-telemetry-android.md)을 완료한 경우 이 단계를 건너뛰고 이미 생성된 IoT 허브를 사용할 수 있습니다.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>장치 등록

이전 [빠른 시작: 원격 분석을 디바이스에서 IoT 허브로 전송](quickstart-send-telemetry-android.md)을 완료한 경우 이 단계를 건너뛰고 이전 빠른 시작에서 등록한 것과 동일한 디바이스를 사용할 수 있습니다.

연결을 위해 장치를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 IoT Hub CLI 확장을 추가하고 디바이스 ID를 만듭니다. 

   **YourIoTHubName**: 이 자리 표시자를 IoT Hub용으로 선택한 이름으로 바꿉니다.

   **MyAndroidDevice**: 이 값은 등록된 디바이스에 지정된 이름입니다. 표시된 것처럼 MyAndroidDevice를 사용합니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용해야 하고, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyAndroidDevice \
      --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

## <a name="retrieve-the-service-connection-string"></a>서비스 연결 문자열 검색

또한 백 엔드 서비스 애플리케이션을 IoT 허브에 연결하여 메서드를 실행하고 메시지를 검색할 수 있게 하려면 _서비스 연결 문자열_이 필요합니다. 다음 명령은 IoT Hub에 대한 서비스 연결 문자열을 검색합니다.
   
**YourIoTHubName**: 이 자리 표시자를 IoT Hub용으로 선택한 이름으로 바꿉니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIoTHubName --output table
```

다음과 같은 서비스 연결 문자열을 기록해 둡니다.

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

이 값은 빠른 시작의 뒷부분에서 사용합니다. 서비스 연결 문자열은 디바이스 연결 문자열과는 다릅니다.

## <a name="listen-for-direct-method-calls"></a>직접 메서드 호출 수신 대기

디바이스 SDK 샘플 애플리케이션은 물리적 Android 디바이스 또는 Android 에뮬레이터에서 실행됩니다. 이 샘플은 IoT 허브의 디바이스별 엔드포인트에 연결하고, 시뮬레이션된 원격 분석 데이터를 전송하고, 허브에서 직접 메서드 호출을 수신 대기합니다. 이 빠른 시작에서 허브의 직접 메서드 호출은 디바이스에 원격 분석을 보내는 간격을 변경하도록 지시합니다. 시뮬레이션된 디바이스는 직접 메서드를 실행한 후 승인을 다시 허브로 보냅니다.

1. Android Studio에서 github 샘플 Android 프로젝트를 엽니다. 프로젝트는 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 리포지토리의 복제된 또는 다운로드된 사본인 다음 디렉터리에 있습니다.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Android Studio에서 샘플 프로젝트의 *gradle.properties*를 열고 **Device_Connection_String** 자리 표시자를 앞서 언급한 디바이스 연결 문자열로 바꿉니다.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Android Studio에서 **파일** > **Gradle 파일과 프로젝트 동기화**를 클릭합니다. 빌드가 완료되는지 확인합니다.

4. 빌드가 완료되면 **실행** > **'앱' 실행**을 클릭합니다. 물리적 Android 디바이스 또는 Android 에뮬레이터에서 실행되도록 앱을 구성합니다. 물리적 디바이스 또는 에뮬레이터에서 Android 앱을 실행하는 방법에 대한 자세한 내용은 [앱 실행](https://developer.android.com/training/basics/firstapp/running-app)을 참조하세요.

5. 앱이 로드되면 **시작** 단추를 클릭하여 IoT Hub로 원격 분석 데이터를 보내기 시작합니다.

    ![응용 프로그램](media/quickstart-send-telemetry-android/sample-screenshot.png)

런타임 중에 원격 분석 간격을 업데이트하려면 서비스 SDK 샘플을 실행 중인 상태에서 물리적 디바이스 또는 에뮬레이터에서 앱이 실행 중인 상태여야 합니다.


## <a name="read-the-telemetry-from-your-hub"></a>허브에서 원격 분석 읽기

이 섹션에서는 [IoT 확장](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)과 함께 Azure Cloud Shell을 사용하여 Android 디바이스에서 보내는 디바이스 메시지를 모니터링합니다.

1. Azure Cloud Shell을 통해 다음 명령을 실행하여 IoT 허브의 메시지를 연결하고 읽습니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    다음 스크린샷은 Android 디바이스가 보낸 원격 분석 데이터를 IoT 허브가 수신할 때 출력을 보여줍니다.

      ![Azure CLI를 사용하여 디바이스 메시지 읽기](media/quickstart-send-telemetry-android/read-data.png)

기본적으로 원격 분석 앱은 Android 디바이스에서 5초마다 원격 분석 데이터를 보냅니다. 다음 섹션에서는 직접 메서드 호출을 사용하여 Android IoT 디바이스의 원격 분석 간격을 업데이트합니다.


## <a name="call-the-direct-method"></a>직접 메서드 호출

서비스 애플리케이션은 IoT Hub의 서비스 측 엔드포인트에 연결합니다. 애플리케이션은 IoT 허브를 통해 디바이스에 직접 메서드 호출을 하고 승인을 수신 대기합니다. 

별도의 물리적 Android 디바이스 또는 Android 에뮬레이터에서 이 앱을 실행합니다.

IoT Hub 백엔드 서비스 애플리케이션은 일반적으로 IoT Hub의 모든 디바이스를 제어하는 민감한 연결 문자열과 관련된 위험을 완화하기 용이한 클라우드에서 실행됩니다. 이 예제에서는 데모용 Android 앱으로 실행 중입니다. 이 빠른 시작의 다른 언어 버전에서는 백 엔드 서비스 애플리케이션과 더욱 긴밀하게 부합하는 다른 예제를 제공합니다. 

1. Android Studio에서 github 서비스 샘플 Android 프로젝트를 엽니다. 프로젝트는 [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) 리포지토리의 복제된 또는 다운로드된 사본인 다음 디렉터리에 있습니다.

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Android Studio에서 샘플 프로젝트의 *gradle.properties*를 열고 **ConnectionString** 및 **DeviceId** 속성의 값을 앞서 본 서비스 연결 문자열 및 등록된 Android 디바이스 ID로 업데이트합니다.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice    
    ```

3. Android Studio에서 **파일** > **Gradle 파일과 프로젝트 동기화**를 클릭합니다. 빌드가 완료되는지 확인합니다.

4. 빌드가 완료되면 **실행** > **'앱' 실행**을 클릭합니다. 별도의 물리적 Android 디바이스 또는 Android 에뮬레이터에서 실행되도록 앱을 구성합니다. 물리적 디바이스 또는 에뮬레이터에서 Android 앱을 실행하는 방법에 대한 자세한 내용은 [앱 실행](https://developer.android.com/training/basics/firstapp/running-app)을 참조하세요.

5. 앱이 로드되면**메시징 간격 설정** 값을 **1000**으로 업데이트하고 **호출**을 클릭합니다. 

    원격 분석 메시징 간격은 밀리초 단위입니다. 디바이스 샘플의 기본 원격 분석 간격은 5초로 설정됩니다. 이와 같이 변경하면 원격 분석 데이터를 1초마다 전송하도록 Android IoT 디바이스가 업데이트됩니다.

    ![원격 분석 간격 입력](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. 앱은 메서드가 성공적으로 실행되었는지 여부를 나타내는 승인을 받습니다.

    ![직접 메서드 승인](media/quickstart-control-device-android/direct-method-ack.png)



## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 백 엔드 애플리케이션에서 디바이스에 직접 메서드를 호출하고, 시뮬레이션된 디바이스 애플리케이션에서 직접 메서드 호출에 응답했습니다.

디바이스-클라우드 메시지를 클라우드의 다른 대상으로 라우팅하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> [자습서: 처리를 위해 다른 엔드포인트로 원격 분석 라우팅](tutorial-routing.md)