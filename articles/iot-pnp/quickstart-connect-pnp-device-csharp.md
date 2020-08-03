---
title: IoT 플러그 앤 플레이 미리 보기 코드를 IoT Hub에 연결 - C# | Microsoft Docs
description: C#(.NET)을 사용하여 IoT 허브에 연결하는 IoT 플러그 앤 플레이 미리 보기 샘플 디바이스 코드를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d598bbb792c071db281ebdc562402ba0198305ab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044189"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>빠른 시작: 샘플 IoT 플러그 앤 플레이 미리 보기 디바이스 애플리케이션을 IoT Hub(C#)에 연결

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

이 빠른 시작에서는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT Hub에 연결하고, Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 C#(.NET)으로 작성되었으며, C#(.NET)용 Azure IoT 샘플 컬렉션의 일부로 제공됩니다. 솔루션 개발자는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 개발 머신에 .NET Core 3.0을 설치해야 합니다. [.NET Core 3.0 다운로드](https://dotnet.microsoft.com/download/dotnet-core/3.0)에서 여러 플랫폼에 대한 .NET Core SDK의 이 버전을 다운로드할 수 있습니다.

로컬 터미널 창에서 다음 명령을 실행하여 개발 머신에 있는 .NET 버전을 확인할 수 있습니다. 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Azure IoT 탐색기 설치

최신 업데이트에 대한 "자산"에서 .msi 파일을 선택하여 도구의 [리포지토리](https://github.com/Azure/azure-iot-explorer/releases) 페이지에서 최신 버전의 **Azure IoT 탐색기**를 다운로드하고 설치합니다.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브의 _IoT 허브 연결 문자열_을 가져옵니다(나중에 사용하기 위해 참고).

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>개발 환경 준비

이 빠른 시작에서는 C#(.NET).용 Azure IoT 샘플을 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [C#(.NET)용 Azure IoT 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 리포지토리를 다음 위치에 복제합니다.

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="run-the-device-sample"></a>디바이스 샘플 실행

복제된 샘플 코드를 사용하여 IoT 허브에 연결하는 디바이스를 시뮬레이트하는 애플리케이션을 빌드합니다. 이 애플리케이션은 원격 분석 데이터 및 속성을 보내고 명령을 수신합니다.

1. 로컬 터미널 창에서 복제된 리포지토리의 폴더로 이동하고, **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample** 폴더로 이동합니다. 

1. 다음과 같이 _디바이스 연결 문자열_을 구성합니다.

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 샘플 애플리케이션을 실행하여 IoT 허브에 원격 분석을 전송하는 IoT 플러그 앤 플레이 디바이스를 시뮬레이트합니다. 동일한 터미널 창에서 필요한 패키지를 빌드하고 샘플 애플리케이션을 실행하려면 다음 명령을 사용합니다.

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

디바이스가 성공적으로 등록되고 클라우드에서 업데이트를 기다린다는 메시지가 표시됩니다. 이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었고, 원격 분석 데이터를 허브로 보내기 시작했다는 것을 나타냅니다. 다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. 도구가 디바이스의 인터페이스 모델 정의를 읽을 수 있도록 **설정**을 선택합니다. [설정] 메뉴의 [플러그 앤 플레이 구성]에 **연결된 디바이스**가 이미 표시되어 있을 수도 있습니다. 표시되지 있지 않으면 **+ 모듈 정의 원본 새로 만들기**를 선택한 다음, **연결된 디바이스**를 선택하여 추가합니다.

1. **디바이스** 개요 페이지로 돌아가서 이전에 만든 디바이스 ID를 찾습니다. 디바이스 애플리케이션이 명령 프롬프트에서 계속 실행 중인 상태에서 Azure IoT 탐색기에 디바이스의 **연결 상태**가 _연결됨_으로 보고되고 있는지 확인합니다(그렇지 않은 경우 연결될 때까지 **새로 고침**을 누릅니다). 디바이스를 선택하여 자세한 내용을 봅니다.

1. ID가 **urn:csharp_sdk_sample:EnvironmentalSensor:1**인 인터페이스를 확장하여 인터페이스 및 IoT 플러그 앤 플레이 기본 형식인 속성, 명령 및 원격 분석을 표시합니다.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT Hub에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [방법: IoT 플러그 앤 플레이 미리 보기 디바이스에 연결하고 상호 작용](howto-develop-solution.md)
