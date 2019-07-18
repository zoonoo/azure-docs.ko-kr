---
title: Azure IoT Hub 디바이스 스트림을 통한 SSH 및 RDP - C# 빠른 시작(미리 보기) | Microsoft Docs
description: 이 빠른 시작에서는 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 시나리오를 활성화하는 두 개의 C# 샘플 애플리케이션을 실행합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 914568ee4b669605807c8a0e386cd540145c9522
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446088"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>빠른 시작: C# 프록시 애플리케이션을 사용하여 IoT Hub 디바이스 스트림을 통해 SSH 및 RDP 사용(미리 보기)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub는 현재 디바이스 스트림을 [미리 보기 기능](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)으로 지원합니다.

[IoT Hub 디바이스 스트림](iot-hub-device-streams-overview.md)은 서비스 및 디바이스 애플리케이션이 안전하고 방화벽 친화적인 방식으로 통신할 수 있도록 합니다. 이 빠른 시작 가이드에는 IoT Hub를 통해 설정된 디바이스 스트림을 통해 클라이언트-서버 애플리케이션 트래픽(예: SSH[Secure Shell] 및 RDP[원격 데스크톱 프로토콜])을 보낼 수 있는 두 개의 C# 애플리케이션이 포함되어 있습니다. 설정 개요는 [SSH 또는 RDP용 로컬 프록시 애플리케이션 샘플](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)을 참조하세요.

이 문서에서는 먼저 SSH 설정(22 포트 사용)을 설명한 다음, 설정의 RDP 포트를 수정하는 방법을 설명합니다. 디바이스 스트림은 애플리케이션 및 프로토콜에 구속받지 않으므로 다른 유형의 애플리케이션 트래픽을 수용하도록 동일한 샘플을 수정할 수 있습니다. 이 수정은 일반적으로 통신 포트를 원하는 애플리케이션에서 사용하는 포트로만 변경하는 것과 관련이 있습니다.

## <a name="how-it-works"></a>작동 방법

다음 그림에서는 이 샘플의 디바이스-로컬 프록시 애플리케이션 및 서비스-로컬 프록시 애플리케이션에서 SSH 클라이언트와 SSH 디먼 간의 엔드투엔드 연결을 사용하도록 설정하는 방법을 보여 줍니다. 여기서는 디먼이 디바이스-로컬 프록시 애플리케이션과 동일한 디바이스에서 실행되고 있다고 가정합니다.

![로컬 프록시 애플리케이션 설정](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. 서비스-로컬 프록시 애플리케이션에서 IoT Hub에 연결하고 대상 디바이스에 대한 디바이스 스트림을 시작합니다.

1. 디바이스-로컬 프록시 애플리케이션에서 스트림 시작 핸드셰이크를 완료하고, IoT Hub의 스트리밍 엔드포인트를 통한 엔드투엔드 스트리밍 터널을 서비스 쪽에 설정합니다.

1. 디바이스-로컬 프록시 애플리케이션에서 디바이스의 22 포트에서 수신 대기하는 SSH 디먼에 연결합니다. 이 설정은 "디바이스-로컬 프록시 애플리케이션 실행" 섹션에서 설명한 대로 구성할 수 있습니다.

1. 서비스-로컬 프록시 애플리케이션에서 지정된 포트(이 경우 2222 포트)에서 수신 대기하여 사용자의 새 SSH 연결을 기다립니다. 이 설정은 "서비스-로컬 프록시 애플리케이션 실행" 섹션에서 설명한 대로 구성할 수 있습니다. 사용자가 SSH 클라이언트를 통해 연결하면 터널을 통해 SSH 클라이언트와 서버 애플리케이션 간에 SSH 애플리케이션 트래픽을 전송할 수 있습니다.

> [!NOTE]
> 디바이스 스트림을 통해 보내는 SSH 트래픽은 서비스와 디바이스 간에 직접 보내는 것이 아니라 IoT Hub의 스트리밍 엔드포인트를 통해 터널링됩니다. 자세한 내용은 [IoT Hub 디바이스 스트림 사용의 이점](iot-hub-device-streams-overview.md#benefits)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* 디바이스 스트림 미리 보기는 현재 다음 Azure 지역에서 만든 IoT Hub만 지원합니다.

  * 미국 중부
  * 미국 중부 EUAP

* 이 빠른 시작에서 실행하는 두 개의 샘플 애플리케이션은 C#을 사용하여 작성되었습니다. 개발 머신에는 .NET Core SDK 2.1.0 이상이 필요합니다.

  [.NET](https://www.microsoft.com/net/download/all)에서 여러 플랫폼에 대한 .NET Core SDK를 다운로드할 수 있습니다.

* 다음 명령을 사용하여 개발 머신에서 C#의 현재 버전을 확인합니다.

    ```
    dotnet --version
    ```

* 다음 명령을 실행하여 Azure CLI용 Azure IoT 확장을 Cloud Shell 인스턴스에 추가합니다. IoT 확장은 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service) 특정의 명령을 Azure CLI에 추가합니다.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* [C# 샘플 프로젝트를 다운로드](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)하고 ZIP 보관 파일을 추출합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

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

1. IoT Hub에 연결하고 디바이스 스트림을 설정하려면 서비스 쪽 애플리케이션을 사용하도록 설정하기 위해 IoT Hub의 *서비스 연결 문자열*도 필요합니다. 다음 명령은 IoT Hub에 대한 이 값을 검색합니다.

   > [!NOTE]
   > *YourIoTHubName* 자리 표시자를 IoT Hub에서 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    나중에 이 빠른 시작에서 사용할 수 있도록 반환된 값을 적어 두세요. 다음 예제와 유사합니다.

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>디바이스 스트림을 통해 디바이스에 대한 SSH

이 섹션에서는 SSH 트래픽을 터널링하는 엔드투엔드 스트림을 설정합니다.

### <a name="run-the-device-local-proxy-application"></a>디바이스-로컬 프록시 애플리케이션 실행

압축을 푼 프로젝트 폴더의 *device-streams-proxy/device* 디렉터리로 이동합니다. 다음 정보를 편리하게 보관합니다.

| 인수 이름 | 인수 값 |
|----------------|-----------------|
| `deviceConnectionString` | 이전에 만든 디바이스의 연결 문자열입니다. |
| `targetServiceHostName` | SSH 서버에서 수신 대기하는 IP 주소입니다. 디바이스-로컬 프록시 애플리케이션이 실행되는 IP와 동일한 경우 주소는 `localhost`입니다. |
| `targetServicePort` | 애플리케이션 프로토콜에서 사용하는 포트입니다(SSH의 경우 기본적으로 22 포트임).  |

다음과 같이 코드를 컴파일하고 실행합니다.

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $deviceConnectionString localhost 22

# In Windows
dotnet run %deviceConnectionString% localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>서비스-로컬 프록시 애플리케이션 실행

압축을 푼 프로젝트 폴더에서 `device-streams-proxy/service`로 이동합니다. 다음 정보가 필요합니다.

| 매개 변수 이름 | 매개 변수 값 |
|----------------|-----------------|
| `iotHubConnectionString` | IoT Hub의 서비스 연결 문자열입니다. |
| `deviceId` | 이전에 만든 디바이스의 식별자입니다. |
| `localPortNumber` | SSH 클라이언트에서 연결하는 로컬 포트입니다. 이 샘플에서는 2222 포트를 사용하지만 다른 임의의 숫자를 사용할 수도 있습니다. |

다음과 같이 코드를 컴파일하고 실행합니다.

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-the-ssh-client"></a>SSH 클라이언트 실행

이제 SSH 클라이언트 애플리케이션을 사용하고, 2222 포트에서 서비스-로컬 프록시 애플리케이션에 연결합니다(SSH 디먼에서 직접 연결하는 대신).

```
ssh <username>@localhost -p 2222
```

이 시점에서 자격 증명을 입력하라는 메시지가 SSH 로그인 창에 표시됩니다.

서비스 쪽의 콘솔 출력(서비스-로컬 프록시 애플리케이션이 2222 포트에서 수신 대기함):

![서비스-로컬 프록시 애플리케이션 출력](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

*IP_address:22*에서 SSH 디먼에 연결하는 디바이스-로컬 프록시 애플리케이션의 콘솔 출력:

![디바이스-로컬 프록시 애플리케이션 출력](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

SSH 클라이언트 애플리케이션의 콘솔 출력입니다. SSH 클라이언트는 서비스-로컬 프록시 애플리케이션에서 수신 대기하는 22 포트에 연결하여 SSH 디먼과 통신합니다.

![SSH 클라이언트 애플리케이션 출력](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>디바이스 스트림을 통해 디바이스에 대한 RDP

RDP 설정은 위에서 설명한 SSH 설정과 매우 비슷합니다. RDP 대상 IP 및 3389 포트를 대신 사용하고, SSH 클라이언트 대신 RDP 클라이언트를 사용합니다.

### <a name="run-the-device-local-proxy-application-rdp"></a>디바이스-로컬 프록시 애플리케이션(RDP) 실행

압축을 푼 프로젝트 폴더의 *device-streams-proxy/device* 디렉터리로 이동합니다. 다음 정보를 편리하게 보관합니다.

| 인수 이름 | 인수 값 |
|----------------|-----------------|
| `DeviceConnectionString` | 이전에 만든 디바이스의 연결 문자열입니다. |
| `targetServiceHostName` | RDP 서버가 실행되는 호스트 이름 또는 IP 주소입니다. 디바이스-로컬 프록시 애플리케이션이 실행되는 IP와 동일한 경우 주소는 `localhost`입니다. |
| `targetServicePort` | 애플리케이션 프로토콜에서 사용하는 포트입니다(RDP의 경우 기본적으로 3389 포트임).  |

다음과 같이 코드를 컴파일하고 실행합니다.

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>서비스-로컬 프록시 애플리케이션(RDP) 실행

압축을 푼 프로젝트 폴더에서 `device-streams-proxy/service`로 이동합니다. 다음 정보가 필요합니다.

| 매개 변수 이름 | 매개 변수 값 |
|----------------|-----------------|
| `iotHubConnectionString` | IoT Hub의 서비스 연결 문자열입니다. |
| `deviceId` | 이전에 만든 디바이스의 식별자입니다. |
| `localPortNumber` | SSH 클라이언트에서 연결하는 로컬 포트입니다. 이 샘플에서는 2222 포트를 사용하지만 다른 임의의 숫자로 수정할 수 있습니다. |

다음과 같이 코드를 컴파일하고 실행합니다.

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-rdp-client"></a>RDP 클라이언트 실행

이제 RDP 클라이언트 애플리케이션을 사용하고 2222 포트(이전에 선택한 임의의 사용 가능한 포트)의 서비스-로컬 프록시 애플리케이션에 연결합니다.

![RDP에서 서비스-로컬 프록시 애플리케이션에 연결](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT Hub를 설정하고, 디바이스를 등록하고, 디바이스-로컬 프록시 애플리케이션 및 서비스-로컬 프록시 애플리케이션을 배포하여 IoT Hub를 통한 디바이스 스트림을 설정하고, 프록시 애플리케이션을 사용하여 SSH 또는 RDP 트래픽을 터널링했습니다. 동일한 패러다임에서 서버가 디바이스(예: SSH 디먼)에서 실행되는 다른 클라이언트-서버 프로토콜을 수용할 수 있습니다.

디바이스 스트림에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [디바이스 스트림 개요](./iot-hub-device-streams-overview.md)
