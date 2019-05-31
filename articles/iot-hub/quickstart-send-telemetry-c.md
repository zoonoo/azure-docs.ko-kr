---
title: Azure IoT Hub에 원격 분석 전송 빠른 시작(C) | Microsoft Docs
description: 이 빠른 시작에서는 두 가지 C 애플리케이션 예제를 실행하여 시뮬레이트된 원격 분석을 IoT Hub로 보내고, 클라우드에서 처리할 원격 분석을 IoT Hub에서 읽습니다.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 92575f2fc8e6dbcfc5767a179ddf60df1bce0c83
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872596"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>빠른 시작: 디바이스에서 IoT Hub로 원격 분석을 보내고 백 엔드 애플리케이션(C#)으로 읽습니다.

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub는 저장 또는 처리를 위해 IoT 디바이스에서 클라우드로 다량의 원격 분석 데이터를 수집할 수 있게 해주는 Azure 서비스입니다. 이 빠른 시작에서는 시뮬레이션된 장치 애플리케이션에서 IoT Hub를 통해 백 엔드 애플리케이션으로 원격 분석을 처리를 위해 보냅니다.

빠른 시작에서는 [C용 Azure IoT 장치 SDK](iot-hub-device-sdk-c-intro.md)의 C 애플리케이션 예제를 사용하여 원격 분석을 IoT 허브로 보냅니다. Azure IoT 디바이스 SDK는 이식성과 광범위한 플랫폼 호환성을 위해 [ANSI C(C99)](https://wikipedia.org/wiki/C99)로 작성되었습니다. 샘플 코드를 실행하기 전에 IoT 허브를 만들고 시뮬레이트한 디바이스를 해당 허브에 등록합니다.

이 문서는 Windows용으로 작성되었지만 Linux에서도 이 빠른 시작을 완료할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* [‘C++를 사용한 데스크톱 개발’](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 워크로드를 사용하도록 설정하고 [Visual Studio 2019](https://www.visualstudio.com/vs/)를 설치합니다.
* 최신 버전의 [Git](https://git-scm.com/download/)을 설치합니다.
* 다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IOT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 고유의 명령을 추가합니다.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>개발 환경 준비

이 빠른 시작에서는 [C용 Azure IoT 디바이스 SDK](iot-hub-device-sdk-c-intro.md)를 사용합니다. 

다음 환경용 패키지 및 라이브러리를 설치하여 SDK를 사용할 수 있습니다.

* **Linux**: apt-get 패키지는 CPU 아키텍처인 amd64, arm64, armhf 및 i386을 사용하여 Ubuntu 16.04 및 18.04에서 사용할 수 있습니다. 자세한 내용은 [Using apt-get to create a C device client project on Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md)(apt-get을 사용하여 Ubuntu에서 C 디바이스 클라이언트 프로젝트 만들기)를 참조하세요.

* **mbed**: mbed 플랫폼에서 디바이스 애플리케이션을 만드는 개발자를 위해 Azure IoT Hub를 통해 몇 분 안에 시작할 수 있는 라이브러리 및 샘플을 게시했습니다. 자세한 내용은 [Use the mbed library](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed)(mbed 라이브러리 사용)를 참조하세요.

* **Arduino**: Arduino에서 개발 중이라면 Arduino IDE 라이브러리 관리자에서 제공되는 Azure IoT 라이브러리를 사용할 수 있습니다. 자세한 내용은 [The Azure IoT Hub library for Arduino](https://github.com/azure/azure-iot-arduino)(Arduino용 Azure IoT Hub 라이브러리)를 참조하세요.

* **iOS**: IoT Hub 디바이스 SDK는 Mac 및 iOS 디바이스 개발용 CocoaPods로 사용할 수 있습니다. 자세한 내용은 [iOS Samples for Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient)(Microsoft Azure IoT용 iOS 샘플)를 참조하세요.

그러나 이 빠른 시작에서는 GitHub에서 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 복제하고 빌드하는 데 사용되는 개발 환경을 준비합니다. GitHub의 SDK에는 이 빠른 시작에 사용된 샘플 코드가 포함됩니다. 

1. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    `CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소(Visual Studio 및 'C++를 사용한 데스크톱 개발' 워크로드)를 머신에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다.

2. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 복제합니다.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    이 작업을 완료하는 데 몇 분 정도가 걸립니다.


3. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. 개발 클라이언트 플랫폼에 관련된 SDK 버전을 빌드하는 다음 명령을 실행합니다. 또한 시뮬레이션된 디바이스에 대한 Visual Studio 솔루션이 `cmake` 디렉터리에서 생성됩니다. 

    ```cmd
    cmake ..
    ```
    
    `cmake`에서 C++ 컴파일러를 찾을 수 없으면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다. 

    빌드가 성공되면 마지막 몇몇 출력 줄은 다음 출력과 유사하게 표시됩니다.

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 섹션에서는 [IoT 확장](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)과 함께 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyCDevice**: 등록된 디바이스에 지정된 이름입니다. 표시된 것처럼 MyCDevice를 사용합니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용해야 하고, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyCDevice --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

## <a name="send-simulated-telemetry"></a>시뮬레이션된 원격 분석 전송

시뮬레이트된 장치 애플리케이션은 IoT 허브의 장치별 엔드포인트에 연결하고 시뮬레이트된 원격 분석으로 문자열을 보냅니다.

1. 텍스트 편집기를 사용하여 iothub_convenience_sample.c 소스 파일을 열고 원격 분석을 보내기 위한 샘플 코드를 검토합니다. 파일은 다음 위치에 있습니다.

    ```
    \azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. `connectionString` 상수의 선언을 찾습니다.

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```
    `connectionString` 상수의 값을 이전에 적어둔 디바이스 연결 문자열로 바꿉니다. 그런 다음, 변경 내용을 **iothub_convenience_sample.c**에 저장합니다.

3. 로컬 터미널 창에서 Azure IoT C SDK로 만든 CMake 디렉터리의 *iothub_convenience_sample* 프로젝트 디렉터리로 이동합니다.

    ```
    cd /azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. 로컬 터미널 창에서 CMake를 실행하여 업데이트된 `connectionString` 값으로 샘플을 빌드합니다.

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 장치 애플리케이션을 실행합니다.

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    다음 스크린샷에서는 시뮬레이트된 장치 애플리케이션에서 IoT 허브에 원격 분석을 보낼 때의 출력을 보여 줍니다.

    ![시뮬레이션된 디바이스 실행](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>허브에서 원격 분석 읽기


이 섹션에서는 [IoT 확장](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)과 함께 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스에서 보내는 디바이스 메시지를 모니터링합니다.

1. Azure Cloud Shell을 통해 다음 명령을 실행하여 IoT 허브의 메시지를 연결하고 읽습니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    ![Azure CLI를 사용하여 디바이스 메시지 읽기](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

    

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 IoT 허브를 설치하고, 장치를 등록하고, C# 애플리케이션을 사용하여 허브에 시뮬레이션된 원격 분석을 보내고, Azure Cloud Shell을 사용하여 허브에서 원격 분석을 읽었습니다.

Azure IoT Hub C SDK를 사용하여 개발하는 방법을 자세히 알아보려면 다음 방법 가이드를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure IoT Hub C SDK를 사용하여 개발](iot-hub-devguide-develop-for-constrained-devices.md)