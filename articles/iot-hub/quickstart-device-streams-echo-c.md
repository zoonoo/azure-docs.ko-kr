---
title: Azure IoT Hub 디바이스 스트림 C 빠른 시작(미리 보기) | Microsoft Docs
description: 이 빠른 시작에서는 디바이스 스트림을 통해 IoT 디바이스와 통신하는 C 서비스 쪽 애플리케이션을 실행합니다.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 24b00a589454bfa8413cd98407c2022671cb92ce
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887957"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>빠른 시작: IoT Hub 디바이스 스트림을 통해 C에서 디바이스 애플리케이션과 통신(미리 보기)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[IoT Hub 디바이스 스트림](./iot-hub-device-streams-overview.md)은 서비스 및 디바이스 애플리케이션이 안전하고 방화벽 친화적인 방식으로 통신할 수 있도록 합니다. 공개 미리 보기 동안 C SDK는 디바이스 쪽에서 디바이스 스트림을 지원합니다. 결과적으로 이 빠른 시작에서는 디바이스 쪽 애플리케이션을 실행하는 지침만 설명합니다. [C# 빠른 시작](./quickstart-device-streams-echo-csharp.md) 또는 [Node.js 빠른 시작](./quickstart-device-streams-echo-nodejs.md) 가이드에서 사용할 수 있는 함께 제공되는 서비스 쪽 애플리케이션을 실행해야 합니다.

이 빠른 시작의 디바이스 쪽 C 애플리케이션에는 다음과 같은 기능이 있습니다.

* IoT 디바이스에 디바이스 스트림을 설정합니다.

* 서비스 쪽에서 전송된 데이터를 수신하고 다시 에코합니다.

코드에서는 데이터를 받고 보내는 데 사용하는 방법뿐만 아니라 디바이스 스트림의 시작 프로세스를 보여줍니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* [‘C++를 사용한 데스크톱 개발’](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 워크로드가 사용하도록 설정하고 [Visual Studio 2017](https://www.visualstudio.com/vs/)을 설치합니다.
* 최신 버전의 [Git](https://git-scm.com/download/)을 설치합니다.

## <a name="prepare-the-development-environment"></a>개발 환경 준비

이 빠른 시작에서는 [C용 Azure IoT 디바이스 SDK](iot-hub-device-sdk-c-intro.md)를 사용합니다. GitHub에서 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 복제하고 빌드하는 데 사용되는 개발 환경을 준비합니다. GitHub의 SDK에는 이 빠른 시작에 사용된 샘플 코드가 포함됩니다. 


1. [CMake 빌드 시스템](https://cmake.org/download/)의 버전 3.11.4를 다운로드합니다. 해당하는 암호화 해시 값을 사용하여 다운로드된 이진 파일을 확인합니다. 다음 예제에서는 Windows PowerShell을 사용하여 x64 MSI 배포의 3.11.4 버전에 대한 암호화 해시를 확인했습니다.

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    3.11.4 버전에 대한 다음 해시 값이 작성 시 CMake 사이트에 나열되었습니다.

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

2. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 복제합니다.
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    이 리포지토리 크기는 현재 약 220MB입니다.

3. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. 개발 클라이언트 플랫폼에 관련된 SDK 버전을 빌드하는 다음 명령을 실행합니다. Windows에서 시뮬레이션된 디바이스에 대한 Visual Studio 솔루션이 `cmake` 디렉터리에서 생성됩니다. 

```
    # In Linux
    cmake ..
    make -j
```

Windows에서 Visual Studio 2015 또는 2017 프롬프트에 대해 개발자 명령 프롬프트에서 다음 명령을 실행합니다.

```
    # In Windows
    # For VS2015
    cmake .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    cmake .. -G "Visual Studio 15 2017

    # Then build the project
    cmake --build . -- /m /p:Configuration=Release
```

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 섹션에서는 [IoT 확장](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)과 함께 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 IoT Hub CLI 확장을 추가하고 디바이스 ID를 만듭니다. 

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyDevice**: 등록된 디바이스에 지정된 이름입니다. 표시된 것처럼 MyDevice를 사용합니다. 다른 장치 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용해야 하고, 샘플 애플리케이션에서 장치 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    다음 예제와 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.


## <a name="communicate-between-device-and-service-via-device-streams"></a>디바이스 스트림을 통해 디바이스와 서비스 간의 통신

### <a name="run-the-device-side-application"></a>디바이스 쪽 애플리케이션 실행

디바이스 쪽 애플리케이션을 실행하려면 다음 단계를 수행해야 합니다.
- 이 [디바이스 스트림에 대한 문서](https://github.com/Azure/azure-iot-sdk-c-tcpstreaming/blob/master/iothub_client/readme.md#compiling-the-device-sdk-for-c)의 지침을 사용하여 개발 환경을 설정합니다.

- 원본 파일 `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c`를 편집하여 디바이스 자격 증명을 제공하고 디바이스 연결 문자열을 제공합니다.
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[device connection string]";
```

- 다음과 같이 코드를 컴파일합니다.

```
  # In Linux
  # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  make -j


  # In Windows
  # Go to the cmake folder at the root of repo
  cmake --build . -- /m /p:Configuration=Release
```

- 컴파일된 프로그램을 실행합니다.

```
  # In Linux
  # Go to sample's folder
  cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  ./iothub_client_c2d_streaming_sample


  # In Windows
  # Go to sample's release folder
  cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
  iothub_client_c2d_streaming_sample.exe
```

### <a name="run-the-service-side-application"></a>서비스 쪽 애플리케이션 실행

앞에서 설명한 대로 IoT Hub C SDK는 디바이스 쪽에서 디바이스 스트림을 지원합니다. 서비스 쪽 애플리케이션의 경우 [C# 빠른 시작](./quickstart-device-streams-echo-csharp.md) 또는 [Node.js 빠른 시작](./quickstart-device-streams-echo-nodejs.md) 가이드에서 사용할 수 있는 함께 제공되는 서비스 프로그램을 사용합니다.


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 IoT 허브를 설치하고, 디바이스를 등록하고, C 애플리케이션을 사용하여 허브에 시뮬레이션된 원격 분석을 보내고, Azure Cloud Shell을 사용하여 허브에서 원격 분석을 읽었습니다.

디바이스 스트림에 대해 자세히 알아보려면 아래 링크를 사용합니다.

> [!div class="nextstepaction"]
> [디바이스 스트림 개요](./iot-hub-device-streams-overview.md)