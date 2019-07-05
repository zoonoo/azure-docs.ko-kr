---
title: Azure IoT Hub 디바이스 스트림을 통한 SSH 및 RDP - C 빠른 시작(미리 보기) | Microsoft Docs
description: 이 빠른 시작에서는 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 시나리오를 활성화하는 프록시 역할을 하는 C 샘플 애플리케이션을 실행합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: b958711c498f0826f2a48d92d4892eb43ec8d18a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446073"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>빠른 시작: C 프록시 애플리케이션을 사용하여 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 사용(미리 보기)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub는 현재 디바이스 스트림을 [미리 보기 기능](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)으로 지원합니다.

[IoT Hub 디바이스 스트림](./iot-hub-device-streams-overview.md)은 서비스 및 디바이스 애플리케이션이 안전하고 방화벽 친화적인 방식으로 통신할 수 있도록 합니다. 설정 개요는 [로컬 프록시 샘플 페이지](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)를 참조하세요.

이 빠른 시작에서는 디바이스 스트림을 통해 SSH(Secure Shell) 트래픽을 터널링(22 포트 사용)하도록 설정하는 방법에 대해 설명합니다. RDP(원격 데스크톱 프로토콜) 트래픽에 대한 설정도 비슷하지만 구성을 간단하게 변경할 필요가 있습니다. 디바이스 스트림은 애플리케이션 및 프로토콜에 구속받지 않으므로 다른 유형의 애플리케이션 트래픽을 수용하도록 이 빠른 시작을 수정할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

아래 그림에서는 디바이스-로컬 프록시 프로그램 및 서비스-로컬 프록시 프로그램에서 SSH 클라이언트와 SSH 디먼 프로세스 간의 엔드투엔드 연결을 사용하도록 설정하는 방법을 보여 줍니다. 공개 미리 보기 동안 C SDK는 디바이스 쪽의 디바이스 스트림만 지원합니다. 결과적으로 이 빠른 시작에서는 디바이스-로컬 프록시 애플리케이션만 실행하는 지침에 대해 설명합니다. 다음 서비스 쪽 빠른 시작 중 하나를 실행해야 합니다.

* [C#을 사용하여 IoT Hub 디바이스 스트림을 통한 SSH/RDP](./quickstart-device-streams-proxy-csharp.md)
* [NodeJS 프록시를 사용하여 IoT Hub 디바이스 스트림을 통한 SSH/RDP](./quickstart-device-streams-proxy-nodejs.md)

![로컬 프록시 설정](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. 서비스-로컬 프록시에서 IoT Hub에 연결하고 대상 디바이스에 대한 디바이스 스트림을 시작합니다.

2. 디바이스-로컬 프록시에서 스트림 시작 핸드셰이크를 완료하고, IoT Hub의 스트리밍 엔드포인트를 통한 엔드투엔드 스트리밍 터널을 서비스 쪽에 설정합니다.

3. 디바이스-로컬 프록시에서 디바이스의 22 포트에서 수신 대기하는 SSH 디먼에 연결합니다. 이 설정은 "디바이스-로컬 프록시 애플리케이션 실행" 섹션에서 설명한 대로 구성할 수 있습니다.

4. 서비스-로컬 프록시에서 지정된 포트(이 경우 2222 포트)에서 수신 대기하여 사용자의 새 SSH 연결을 기다립니다. 이 설정은 "디바이스-로컬 프록시 애플리케이션 실행" 섹션에서 설명한 대로 구성할 수 있습니다. 사용자가 SSH 클라이언트를 통해 연결하면 터널을 통해 SSH 클라이언트와 서버 프로그램 간에 SSH 애플리케이션 트래픽을 전송할 수 있습니다.

> [!NOTE]
> 디바이스 스트림을 통해 보내는 SSH 트래픽은 서비스와 디바이스 간에 직접 보내는 것이 아니라 IoT Hub의 스트리밍 엔드포인트를 통해 터널링됩니다. 자세한 내용은 [IoT Hub 디바이스 스트림 사용의 이점](iot-hub-device-streams-overview.md#benefits)을 참조하세요. 또한 그림에서는 디바이스-로컬 프록시와 동일한 디바이스(또는 머신)에서 실행되는 SSH 디먼을 보여 줍니다. 이 빠른 시작에서 SSH 디먼 IP 주소를 제공하면 디바이스-로컬 프록시와 디먼이 다른 머신에서도 실행될 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* 디바이스 스트림 미리 보기는 현재 다음 Azure 지역에서 만든 IoT Hub만 지원합니다.

  * 미국 중부
  * 미국 중부 EUAP

* [C++를 사용한 데스크톱 개발](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 워크로드를 사용하도록 설정된 [Visual Studio 2019](https://www.visualstudio.com/vs/)를 설치합니다.
* 최신 버전의 [Git](https://git-scm.com/download/)을 설치합니다.

* 다음 명령을 실행하여 Azure CLI용 Azure IoT 확장을 Cloud Shell 인스턴스에 추가합니다. IoT 확장은 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 관련 명령을 Azure CLI에 추가합니다.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>개발 환경 준비

이 빠른 시작에서는 [C용 Azure IoT 디바이스 SDK](iot-hub-device-sdk-c-intro.md)를 사용합니다. GitHub에서 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 복제하고 빌드하는 데 사용되는 개발 환경을 준비합니다. GitHub의 SDK에는 이 빠른 시작에 사용되는 샘플 코드가 포함되어 있습니다.

1. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    CMake 설치를 시작하기 *전에* Visual Studio 필수 구성 요소(Visual Studio 및 *C++를 사용한 데스크톱 개발* 워크로드)가 머신에 설치되어 있어야 합니다. 필수 구성 요소가 준비되고 다운로드가 확인되면 CMake 빌드 시스템을 설치할 수 있습니다.

1. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 복제합니다.

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    이 작업은 몇 분 정도가 걸립니다.

1. 다음 명령과 같이 *cmake* 하위 디렉터리를 Git 리포지토리의 루트 디렉터리에 만들고, 해당 폴더로 이동합니다.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. *cmake* 디렉터리에서 다음 명령을 실행하여 개발 클라이언트 플랫폼과 관련된 SDK 버전을 빌드합니다.

   * Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Windows에서 Visual Studio 2015 또는 2017에 대해 개발자 명령 프롬프트에서 다음 명령을 실행합니다. 시뮬레이션된 디바이스용 Visual Studio 솔루션이 *cmake* 디렉터리에 생성됩니다.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 섹션에서는 [IoT 확장](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)에서 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. 디바이스 ID를 만들려면 Cloud Shell에서 다음 명령을 실행합니다.

   > [!NOTE]
   > * *YourIoTHubName* 자리 표시자를 IoT Hub에서 선택한 이름으로 바꿉니다.
   > * 다음과 같이 *MyDevice*를 사용합니다. 등록된 디바이스에 지정된 이름입니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용하고, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후에 실행하세요.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. 방금 등록한 디바이스의 *디바이스 연결 문자열*을 가져오려면 Cloud Shell에서 다음 명령을 실행합니다.

   > [!NOTE]
   > *YourIoTHubName* 자리 표시자를 IoT Hub에서 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    나중에 이 빠른 시작에서 사용할 수 있도록 디바이스 연결 문자열을 적어 두세요. 다음 예제와 유사합니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>디바이스 스트림을 통해 디바이스에 대한 SSH

이 섹션에서는 SSH 트래픽을 터널링하는 엔드투엔드 스트림을 설정합니다.

### <a name="run-the-device-local-proxy-application"></a>디바이스-로컬 프록시 애플리케이션 실행

1. *iothub_client/samples/iothub_client_c2d_streaming_sample* 폴더에 있는 *iotub_client_c2d_streaming_sample.c* 원본 파일을 편집하여 디바이스 연결 문자열, 대상 디바이스 IP/호스트 이름 및 22 SSH 포트를 제공합니다.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. 샘플 컴파일:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. 디바이스에서 컴파일된 프로그램을 실행합니다.

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>서비스-로컬 프록시 애플리케이션 실행

"작동 방법 섹션"에서 설명한 대로 SSH 트래픽을 터널링하도록 엔드투엔드 스트림을 설정하려면 서비스 쪽과 디바이스 쪽 모두의 각 끝에 로컬 프록시가 필요합니다. 공개 미리 보기 동안 IoT Hub C SDK는 디바이스 쪽의 디바이스 스트림만 지원합니다. 서비스-로컬 프록시를 빌드하고 실행하려면 다음 빠른 시작 중 하나의 지침을 따르세요.

   * [C# 프록시 앱을 사용하여 IoT Hub 디바이스 스트림을 통한 SSH/RDP](./quickstart-device-streams-proxy-csharp.md)
   * [Node.js 프록시 앱을 사용하여 IoT Hub 디바이스 스트림을 통한 SSH/RDP](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>SSH 세션 설정

디바이스- 및 서비스-로컬 프록시가 모두 실행되면, SSH 클라이언트 프로그램을 사용하여 포트 2222에서 서비스-로컬 프록시에 연결합니다(SSH 디먼에서 직접 연결하는 대신).

```cmd/sh
ssh <username>@localhost -p 2222
```

이 시점에서 자격 증명을 입력하라는 메시지가 SSH 로그인 창에 표시됩니다.

다음 이미지에서는 `IP_address:22`의 SSH 디먼에 연결되는 디바이스-로컬 프록시의 콘솔 출력을 보여 줍니다.

![디바이스-로컬 프록시 출력](./media/quickstart-device-streams-proxy-c/device-console-output.png)

다음 이미지에서는 SSH 클라이언트 프로그램의 콘솔 출력을 보여 줍니다. SSH 클라이언트는 서비스-로컬 프록시에서 수신 대기하는 22 포트에 연결하여 SSH 디먼과 통신합니다.

![SSH 클라이언트 출력](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT Hub를 설정하고, 디바이스를 등록하고, 디바이스-로컬 프록시 프로그램 및 서비스-로컬 프록시 프로그램을 배포하여 IoT Hub를 통한 디바이스 스트림을 설정하고, 프록시를 사용하여 SSH 트래픽을 터널링했습니다.

디바이스 스트림에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [디바이스 스트림 개요](./iot-hub-device-streams-overview.md)
