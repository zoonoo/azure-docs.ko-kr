---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 9baef8226ed44e7b11ce8108bacd2b686bee2d9d
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112040649"
---
## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독이 아직 없는 경우 시작하기 전에 [무료 구독을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Git](https://git-scm.com/downloads)
- Java SE Development Kit 8 이상이 포함된 개발 머신. 여러 플랫폼용 Java 8(LTS) JDK는 [OpenJDK의 Zulu 빌드 다운로드](https://www.azul.com/downloads/zulu-community/)에서 다운로드할 수 있습니다. 설치 관리자에서 **경로에 추가** 옵션을 선택합니다.
- [Apache Maven 3](https://maven.apache.org/download.cgi). 로컬 폴더로 다운로드를 추출한 후 Maven */bin* 폴더의 전체 경로를 Windows PATH 변수에 추가합니다.
- Azure CLI. 이 빠른 시작에서 Azure CLI 명령을 실행하기 위한 두 가지 옵션이 있습니다.
    - 브라우저에서 CLI 명령을 실행하는 대화형 셸인 Azure Cloud Shell을 사용합니다. 이 옵션은 아무 것도 설치할 필요가 없으므로 권장됩니다. 처음으로 Cloud Shell을 사용하는 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다. [Cloud Shell 빠른 시작](../articles/cloud-shell/quickstart.md)의 단계를 따라 **Cloud Shell을 시작하고** **Bash 환경을 선택합니다**.
    - 선택적으로 로컬 컴퓨터에서 Azure CLI를 실행합니다. Azure CLI가 이미 설치된 경우 `az upgrade`를 실행하여 CLI 및 확장을 현재 버전으로 업그레이드합니다. Azure CLI를 설치하는 방법은 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>시뮬레이트된 디바이스 실행
이 섹션에서는 Java SDK를 사용하여 시뮬레이션된 디바이스에서 IoT 허브로 메시지를 보냅니다.

### <a name="configure-your-environment"></a>환경 구성
1. 콘솔을 열어 Azure IoT Java 디바이스 SDK를 설치하고, 빌드하고, 코드 샘플을 실행합니다.
    > [!NOTE]
    > 이제 두 개의 콘솔 창이 열려 있습니다. 방금 연 것과, 이전에 CLI 명령을 입력하기 위해 사용한 Cloud Shell 또는 CLI 콘솔입니다.
1. 시뮬레이션된 디바이스에서 Azure IoT에 연결할 수 있도록 다음 환경 변수를 설정합니다.
    * `IOTHUB_DEVICE_CONNECTION_STRING`이라는 환경 변수를 설정합니다. 변수 값의 경우 이전 섹션에서 저장한 디바이스 연결 문자열을 사용합니다.
    * `IOTHUB_DEVICE_SECURITY_TYPE`이라는 환경 변수를 설정합니다. 변수의 경우 리터럴 문자열 값 `connectionString`을 사용합니다.

    **CMD**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > Windows CMD의 경우 각 변수의 문자열 값을 묶는 따옴표가 없습니다.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

### <a name="build-the-sample"></a>샘플 빌드
1. 콘솔에서 Azure IoT Java 디바이스 SDK를 로컬 컴퓨터에 복제합니다.
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-c.git
    ```
1. SDK의 루트 폴더로 이동하고 다음 명령을 실행하여 SDK를 빌드한 후 샘플을 업데이트합니다.
    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```
    이 작업은 몇 분이 걸립니다.

### <a name="run-the-code"></a>코드 실행
1. CLI 앱에서 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 명령을 실행하여 시뮬레이션된 IoT 디바이스의 이벤트 모니터링을 시작합니다.  이벤트 메시지가 도착하면 터미널에 출력됩니다.

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```
1. Java 콘솔에서 samples 디렉터리로 이동합니다.
    ```console
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```
1. Java 콘솔에서 다음 코드 샘플을 실행합니다. 샘플은 자동 온도 조절기 센서가 있는 시뮬레이션된 온도 컨트롤러를 만듭니다.
    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```
    > [!NOTE]
    > 이 코드 샘플에서는 수동 구성 없이 솔루션에 스마트 디바이스를 통합할 수 있도록 하는 Azure IoT 플러그 앤 플레이를 사용합니다.  기본적으로 이 설명서에 있는 대부분의 샘플은 IoT 플러그 앤 플레이를 사용합니다. IoT PnP의 장점과 사용 여부에 대한 자세한 내용은 [IoT 플러그 앤 플레이란?](../articles/iot-pnp/overview-iot-plug-and-play.md)을 참조하세요.

    시뮬레이션된 디바이스가 IoT Hub에 연결되면 애플리케이션에서 만든 디바이스 인스턴스에 연결하고 원격 분석 전송을 시작합니다. 몇 가지 초기 프로비저닝 세부 정보를 제공한 후 콘솔은 온도 컨트롤러에 대한 원격 분석을 출력하기 시작합니다.
    
    ```output
    2021-05-13 22:11:05.639 DEBUG TemperatureController:518 - Telemetry - Response from IoT Hub: message Id=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx, status=OK_EMPTY
    2021-05-13 22:11:10.229 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 22:11:10.229 INFO  IotHubTransport:1473 - Sending message ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 22:11:10.231 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 22.8░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    2021-05-13 22:11:10.234 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 22:11:10.236 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 35.3░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    ```