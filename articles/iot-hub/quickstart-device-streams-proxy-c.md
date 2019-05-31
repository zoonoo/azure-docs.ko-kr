---
title: SSH/RDP에 대한 Azure IoT Hub 디바이스 스트림 C 빠른 시작(미리 보기) | Microsoft Docs
description: 이 빠른 시작에서는 IoT Hub 디바이스 스트림을 통해 SSH/RDP 시나리오를 활성화하는 프록시 역할을 하는 샘플 C 애플리케이션을 실행합니다.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: ae5db52d7ac00080c2a740820debe6384cfa8dff
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872658"
---
# <a name="quickstart-sshrdp-over-an-iot-hub-device-stream-using-a-c-proxy-application-preview"></a>빠른 시작: C 프록시 애플리케이션을 사용하여 IoT Hub 디바이스 스트림을 통한 SSH/RDP(미리 보기)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub는 현재 디바이스 스트림을 [미리 보기 기능](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)으로 지원합니다.

[IoT Hub 디바이스 스트림](./iot-hub-device-streams-overview.md)은 서비스 및 디바이스 애플리케이션이 안전하고 방화벽 친화적인 방식으로 통신할 수 있도록 합니다. 설정 개요는 [로컬 프록시 샘플 페이지](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)를 참조하세요.

이 문서에서는 디바이스 스트림을 통해 SSH 트래픽 터널링(포트 22 사용)에 대한 설정을 설명합니다. RDP 트래픽에 대한 설정은 유사하며 간단한 구성 변경이 필요합니다. 디바이스 스트림은 애플리케이션이며 프로토콜에 구속 받지 않으므로 다른 종류의 애플리케이션 트래픽에 맞춰 현재 빠른 시작을 수정할 수 있습니다(통신 포트를 변경하여).

## <a name="how-it-works"></a>작동 방법

아래 그림에서는 디바이스- 및 서비스-로컬 프록시 프로그램에서 SSH 클라이언트와 SSH 디먼 프로세스 간 엔드투엔드 연결을 활성화하는 방법을 보여줍니다. 공개 미리 보기 동안 C SDK는 디바이스 쪽에서 디바이스 스트림을 지원합니다. 결과적으로 이 빠른 시작에서는 디바이스-로컬 프록시 애플리케이션을 실행하는 지침만 설명합니다. 다음 서비스 쪽 빠른 시작 중 하나를 실행해야 합니다.

* [C#을 사용하여 IoT Hub 디바이스 스트림을 통한 SSH/RDP](./quickstart-device-streams-proxy-csharp.md)

* [NodeJS 프록시를 사용하여 IoT Hub 디바이스 스트림을 통한 SSH/RDP](./quickstart-device-streams-proxy-nodejs.md)

![로컬 프록시 설정](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. 서비스-로컬 프록시는 IoT 허브에 연결하고 대상 디바이스에 대한 디바이스 스트림을 시작합니다.

2. 디바이스-로컬 프록시는 스트림 시작 핸드셰이크를 완료하고 IoT Hub의 스트리밍 엔드포인트를 통해 서비스 측에 엔드투엔드 스트리밍 터널을 설정합니다.

3. 디바이스-로컬 프록시는 디바이스의 포트 22에서 수신 대기하는 SSHD(SSH 디먼)에 연결합니다([*디바이스 로컬 프록시 애플리케이션 실행* 섹션](#run-the device-local-proxy-application)에 설명된 대로 구성 가능).

4. 서비스-로컬 프록시는 이 경우 포트 2222인 지정된 포트에서 수신 대기하여 사용자의 새 SSH 연결을 기다립니다([디바이스-로컬 프록시 애플리케이션 실행](#run-the-device-local-proxy-application) 섹션에 설명된 대로 역시 구성 가능함). 사용자가 SSH 클라이언트를 통해 연결하면 터널은 SSH 클라이언트와 서버 프로그램 간에 SSH 애플리케이션 트래픽이 전송되도록 활성화합니다.

> [!NOTE]
> 디바이스 스트림을 통해 전송되는 SSH 트래픽은 서비스와 디바이스 간에 직접 전송되는 것이 아니라 IoT Hub의 스트리밍 엔드포인트를 통해 터널링됩니다. 자세한 내용은 [IoT Hub 디바이스 스트림 사용의 이점](iot-hub-device-streams-overview.md#benefits)에 대해 읽어보세요. 또한 그림은 디바이스-로컬 프록시로 동일한 디바이스(또는 머신)에서 실행되는 SSH 디먼을 보여줍니다. 이 빠른 시작에서는 SSH 디먼 IP 주소를 제공하여 디바이스-로컬 프록시 및 디먼이 다른 머신에서 실행될 수 있도록 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* 디바이스 스트림의 미리 보기는 현재 다음 지역에서 만든 IoT Hub에 대해서만 지원됩니다.

   * **미국 중부**

   * **미국 중부 EUAP**

* [‘C++를 사용한 데스크톱 개발’](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 워크로드를 사용하도록 설정하고 [Visual Studio 2019](https://www.visualstudio.com/vs/)를 설치합니다.
* 최신 버전의 [Git](https://git-scm.com/download/)을 설치합니다.

* 다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IOT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 고유의 명령을 추가합니다.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>개발 환경 준비

이 빠른 시작에서는 [C용 Azure IoT 디바이스 SDK](iot-hub-device-sdk-c-intro.md)를 사용합니다. GitHub에서 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 복제하고 빌드하는 데 사용되는 개발 환경을 준비합니다. GitHub의 SDK에는 이 빠른 시작에 사용된 샘플 코드가 포함됩니다. 


1. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    `CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소(Visual Studio 및 'C++를 사용한 데스크톱 개발' 워크로드)를 머신에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다.

2. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 복제합니다.

    
   ```
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
   ```

   이 작업을 완료하는 데 몇 분 정도가 걸립니다.

3. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. 

   ```
   cd azure-iot-sdk-c
   mkdir cmake
   cd cmake
   ```

4. 개발 클라이언트 플랫폼과 관련된 SDK 버전을 빌드하려면 `cmake` 디렉터리에서 다음 명령을 실행합니다.

   * Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Windows에서 Visual Studio 2015 또는 2017에 대해 개발자 명령 프롬프트에서 다음 명령을 실행합니다. 또한 시뮬레이션된 디바이스에 대한 Visual Studio 솔루션이 `cmake` 디렉터리에서 생성됩니다.

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

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 섹션에서는 [IoT 확장](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)과 함께 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyDevice**: 등록된 디바이스에 지정된 이름입니다. 표시된 것처럼 MyDevice를 사용합니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서도 해당 이름을 사용해야 하며, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    다음 예제와 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

## <a name="ssh-to-a-device-via-device-streams"></a>디바이스 스트림을 통해 디바이스에 대한 SSH

이 섹션에서는 SSH 트래픽을 터널링하는 엔드투엔드 스트림을 설정합니다.

### <a name="run-the-device-local-proxy-application"></a>디바이스-로컬 프록시 애플리케이션 실행

1. `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/` 폴더의 원본 파일 `iothub_client_c2d_streaming_proxy_sample.c`를 편집하고 디바이스 연결 문자열, 대상 디바이스 IP/호스트 이름 및 SSH 포트 22를 제공합니다.

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. 샘플 컴파일:

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

3. 디바이스에서 컴파일된 프로그램을 실행합니다.

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

[작동 방법 섹션](#how-it-works)에서 설명했듯이 SSH 트래픽을 터널링하도록 엔드투엔드 스트림을 설정하려면 양쪽 끝(서비스와 디바이스 모두)에 대한 로컬 프록시가 필요합니다. 공개 미리 보기 중에는 IoT Hub C SDK가 디바이스 쪽에서 디바이스 스트림만 지원합니다. 서비스-로컬 프록시를 빌드하고 실행하려면 다음 빠른 시작 중 하나에서 서비스-로컬 프록시를 실행하는 데 사용할 수 있는 단계를 따릅니다.

   * [C# 프록시 앱을 사용하여 IoT Hub 디바이스 스트림을 통한 SSH/RDP](./quickstart-device-streams-proxy-csharp.md)

   * [Node.js 프록시 앱을 사용하여 IoT Hub 디바이스 스트림을 통한 SSH/RDP](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>SSH 세션 설정

디바이스- 및 서비스-로컬 프록시가 모두 실행되면, SSH 클라이언트 프로그램을 사용하여 포트 2222에서 서비스-로컬 프록시에 연결합니다(SSH 디먼에서 직접 연결하는 대신).

```cmd/sh
ssh <username>@localhost -p 2222
```

이 시점에서 자격 증명을 입력하라는 SSH 로그인 프롬프트가 표시됩니다.

`IP_address:22`에서 SSH 디먼에 연결하는 디바이스-로컬 프록시의 콘솔 출력: ![디바이스-로컬 프록시 출력](./media/quickstart-device-streams-proxy-c/device-console-output.png)

SSH 클라이언트 프로그램의 콘솔 출력(SSH 클라이언트는 서비스-로컬 프록시가 수신 대기하는 포트 22에 연결하여 SSH 디먼과 통신함): ![SSH 클라이언트 출력](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 허브를 설정했고, 디바이스를 등록했으며, IoT 허브를 통해 디바이스 스트림을 설정하는 디바이스- 및 서비스-로컬 프록시 프로그램을 배포했고, SSH 트래픽을 터널링하는 데 프록시를 사용했습니다.

디바이스 스트림에 대해 자세히 알아보려면 아래 링크를 사용합니다.

> [!div class="nextstepaction"]
> [디바이스 스트림 개요](./iot-hub-device-streams-overview.md)
