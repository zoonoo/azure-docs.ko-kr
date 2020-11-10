---
title: Azure IoT Edge용 모듈 개발 | Microsoft Docs
description: 런타임 및 IoT Hub과 통신할 수 있는 Azure IoT Edge용 사용자 지정 모듈을 개발합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8907af07fff7b315eec263d38b686c17218ed9d2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445475"
---
# <a name="develop-your-own-iot-edge-modules"></a>사용자 고유의 IoT Edge 모듈 개발

Azure IoT Edge 모듈은 다른 Azure 서비스와 연결하고 더 큰 클라우드 데이터 파이프라인에 속할 수 있습니다. 이 문서에서는 IoT Edge 런타임 및 IoT Hub, 나머지 Azure 클라우드와 통신하기 위한 모듈을 개발하는 방법을 설명합니다.

## <a name="iot-edge-runtime-environment"></a>IoT Edge 런타임 환경

IoT Edge 런타임은 여러 IoT Edge 모듈의 기능을 통합하고 이를 IoT Edge 디바이스에 배포하기 위한 인프라를 제공합니다. 모든 프로그램은 IoT Edge 모듈로 패키지할 수 있습니다. IoT Edge 통신 및 관리 기능을 완벽 하 게 활용 하기 위해 모듈에서 실행 되는 프로그램은 Azure IoT 장치 SDK를 사용 하 여 로컬 IoT Edge 허브에 연결할 수 있습니다.
::: moniker range=">=iotedge-2020-11"
모듈은 모든 MQTT 클라이언트를 사용 하 여 로컬 IoT Edge 허브 MQTT broker에 연결할 수도 있습니다.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>프로그램을 IoT Edge 모듈로 패키징

IoT Edge 장치에 프로그램을 배포 하려면 먼저 해당 프로그램을 컨테이너 화 된 하 고 Docker 호환 엔진과 함께 실행 해야 합니다. IoT Edge는 docker를 사용 하는 오픈 소스 프로젝트인 [Moby](https://github.com/moby/moby)를 docker 호환 엔진으로 사용 합니다. Docker를 사용 하 여 사용 하는 것과 동일한 매개 변수를 IoT Edge 모듈로 전달할 수 있습니다. 자세한 내용은 [IoT Edge 모듈의 컨테이너 만들기 옵션을 구성 하는 방법](how-to-use-create-options.md)을 참조 하세요.

## <a name="using-the-iot-edge-hub"></a>IoT Edge 허브 사용

IoT Edge 허브는 IoT Hub에 프록시 및 로컬 통신이라는 두 가지 주요 기능을 제공합니다.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>모듈에서 IoT Edge 허브에 연결

모듈에서 로컬 IoT Edge 허브에 연결 하려면 모든 클라이언트와 동일한 연결 단계를 수행 해야 합니다. 자세한 내용은 [IoT Edge 허브에 연결](iot-edge-runtime.md#connecting-to-the-iot-edge-hub)을 참조 하세요.

AMQP 또는 MQTT를 통해 IoT Edge 라우팅을 사용 하려면 Azure IoT SDK에서 ModuleClient를 사용할 수 있습니다. DeviceClient 인스턴스가 IoT Hub에 IoT 디바이스를 연결하는 방법과 비슷하게 디바이스에서 실행 중인 IoT Edge 허브에 모듈을 연결하는 ModuleClient 인스턴스를 만듭니다. ModuleClient 클래스 및 해당 통신 방법에 대 한 자세한 내용은 [c #](/dotnet/api/microsoft.azure.devices.client.moduleclient), [c](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)또는 [Node.js](/javascript/api/azure-iot-device/moduleclient)의 기본 SDK 언어에 대 한 API 참조를 참조 하세요.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT broker IoT Edge을 사용 하려면 사용자 고유의 MQTT 클라이언트를 가져와서 IoT Edge 디먼 워크 로드 API에서 검색 한 정보를 사용 하 여 연결을 직접 시작 해야 합니다. <!--Need to add details here-->

라우팅 또는 MQTT broker를 사용 하 여 게시/구독 중 하나를 선택 하는 방법에 대 한 자세한 내용은 [로컬 통신](iot-edge-runtime.md#local-communication)을 참조 하세요.

### <a name="mqtt-broker-primitives"></a>MQTT broker 기본 형식

#### <a name="send-a-message-on-a-user-defined-topic"></a>사용자 정의 항목에 대 한 메시지 보내기

IoT Edge MQTT broker를 사용 하 여 사용자 정의 항목에 메시지를 게시할 수 있습니다. 이렇게 하려면 특정 항목에 대 한 게시를 위해 모듈에 권한을 부여 하 고, MQTT broker에 연결할 때 암호를 사용 하도록 작업 API에서 토큰을 가져온 다음, 마지막으로 선택한 MQTT 클라이언트를 사용 하 여 권한 있는 항목에 메시지를 게시 합니다.

#### <a name="receive-messages-on-a-user-defined-topic"></a>사용자 정의 항목에 대 한 메시지 받기

IoT Edge MQTT broker를 사용 하 여 메시지를 받는 것도 유사 합니다. 먼저 모듈에 특정 항목을 구독할 수 있는 권한이 있는지 확인 한 다음, MQTT broker에 연결할 때 작업 API에서 암호로 사용할 토큰을 가져오고, 마지막으로 선택한 MQTT 클라이언트를 사용 하 여 승인 된 항목의 메시지를 구독 합니다.

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub 기본 형식

IoT Hub는 다음과 같은 점에서 디바이스와 비슷하게 모듈 인스턴스를 표시합니다.

* [디바이스-클라우드 메시지](../iot-hub/iot-hub-devguide-messaging.md)을 보낼 수 있습니다.
* 특별히 해당 ID에서 대상으로 지정된 [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)를 받을 수 있습니다.
* [장치](../iot-hub/iot-hub-devguide-device-twins.md) 쌍 및 해당 장치의 다른 모듈 쌍에서 분리 되 고 격리 된 모듈 쌍이 있습니다.

현재 모듈은 클라우드-장치 메시지를 받거나 파일 업로드 기능을 사용할 수 없습니다.

모듈을 작성 하는 경우 장치 응용 프로그램에서 IoT Hub를 사용할 때와 마찬가지로 IoT Edge 허브에 연결 하 고 IoT Hub 기본 형식을 사용할 수 있습니다. IoT Edge 모듈과 IoT 장치 응용 프로그램 간의 유일한 차이점은 장치 id 대신 모듈 id를 참조 해야 한다는 것입니다.

#### <a name="device-to-cloud-messages"></a>디바이스-클라우드 메시지

IoT Edge 모듈은 로컬 브로커 역할을 하는 IoT Edge 허브를 통해 클라우드로 메시지를 보내고 메시지를 클라우드로 전파할 수 있습니다. 장치-클라우드 메시지를 복잡 하 게 처리할 수 있도록 하기 위해 IoT Edge 모듈은 다른 모듈 또는 장치에서 로컬 IoT Edge 허브로 전송 된 메시지를 가로채서 처리 하 고 처리 된 데이터로 새 메시지를 보낼 수도 있습니다. 따라서 IoT Edge 모듈 체인을 만들어 로컬 처리 파이프라인을 빌드할 수 있습니다.

라우팅을 사용 하 여 장치-클라우드 원격 분석 메시지를 보내려면 Azure IoT SDK의 ModuleClient를 사용 합니다. Azure IoT SDK를 사용 하 여 각 모듈에는 특수 MQTT 토픽에 매핑되는 모듈 *입력* 및 *출력* 끝점의 개념이 있습니다. 메서드를 사용 하 여 `ModuleClient.sendMessageAsync` 모듈의 출력 끝점에서 메시지를 보냅니다. 그런 다음 edgeHub에서 경로를 구성 하 여이 출력 끝점을 IoT Hub 보냅니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT broker를 사용 하 여 장치-클라우드 원격 분석 메시지를 전송 하는 것은 사용자 정의 항목에 메시지를 게시 하는 것과 유사 하지만, 모듈에 대해 다음과 같은 IoT Hub 특수 항목을 사용 `devices/<device_name>/<module_name>/messages/events` 합니다. 권한 부여는 적절히 설정 해야 합니다. 또한이 항목의 메시지를 클라우드로 전달 하도록 MQTT 브리지가 구성 되어야 합니다.

::: moniker-end

라우팅을 사용 하 여 메시지를 처리 하려면 먼저 다른 끝점 (모듈 또는 장치)에서 들어오는 메시지를 모듈의 입력 끝점으로 보내는 경로를 설정한 다음 모듈의 입력 끝점에서 메시지를 수신 합니다. 새 메시지가 다시 들어올 때마다 Azure IoT SDK에서 콜백 함수가 트리거됩니다. 이 콜백 함수를 사용 하 여 메시지를 처리 하 고 필요에 따라 모듈 끝점 큐에서 새 메시지를 보냅니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT broker를 사용 하 여 메시지를 처리 하는 것은 사용자 정의 항목에 대 한 메시지를 구독 하는 것과 유사 하지만, 모듈의 출력 큐의 특별 한 항목 IoT Edge 사용 `devices/<device_name>/<module_name>/messages/events` 됩니다. 권한 부여는 적절히 설정 해야 합니다. 필요에 따라 선택한 항목에 대 한 새 메시지를 보낼 수 있습니다.

::: moniker-end

#### <a name="twins"></a>쌍

Twins는 IoT Hub에서 제공 하는 기본 형식 중 하나입니다. 메타 데이터, 구성 및 조건을 포함 하 여 상태 정보를 저장 하는 JSON 문서가 있습니다. 각 모듈 또는 장치에는 고유한 쌍이 있습니다.

Azure IoT SDK를 사용 하 여 모듈 쌍을 가져오려면 메서드를 호출 `ModuleClient.getTwin` 합니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT 클라이언트를 사용 하 여 모듈 쌍을 가져오려면 쌍을 가져오는 것이 일반적인 MQTT 패턴이 아니기 때문에 약간 더 많은 작업이 필요 합니다. 모듈은 먼저 IoT Hub 특수 항목을 구독 해야 합니다 `$iothub/twin/res/#` . 이 항목 이름은 IoT Hub에서 상속 되며 모든 장치/모듈은 같은 항목을 구독 해야 합니다. 장치가 서로 쌍을 받는 것을 의미 하지는 않습니다. IoT Hub 및 edgeHub에서는 모든 장치가 동일한 토픽 이름을 수신 하는 경우에도 어떤 쌍이 전달 되어야 하는지 알 수 있습니다. 구독을 만든 후에는 모듈에서 요청 ID를 사용 하 여 다음 IoT Hub 특수 항목에 메시지를 게시 하 여 쌍을 요청 해야 합니다 `$iothub/twin/GET/?$rid=1234` . 이 요청 ID는 요청 된 데이터와 함께 IoT Hub으로 다시 전송 되는 임의의 ID (즉, GUID)입니다. 클라이언트에서 응답과 요청을 연결 하는 방법입니다. 결과 코드는 HTTP와 유사한 상태 코드입니다. 성공은 200로 인코딩됩니다.

::: moniker-end

Azure IoT SDK를 사용 하 여 모듈 쌍 패치를 수신 하려면 콜백 함수를 구현 하 고 Azure IoT SDK의 메서드에 등록 하 여 쌍 `ModuleClient.moduleTwinCallback` 패치가 제공 될 때마다 콜백 함수가 트리거됩니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT 클라이언트를 사용 하 여 모듈 쌍 패치를 수신 하기 위해 프로세스는 전체 쌍을 수신 하는 것과 매우 유사 합니다. 클라이언트는 특수 IoT Hub 항목을 구독 해야 `$iothub/twin/PATCH/properties/desired/#` 합니다. 구독이 생성 된 후에는 IoT Hub에서 쌍의 원하는 영역에 대 한 변경 내용을 보내면 클라이언트에서 해당 쌍을 받습니다.

::: moniker-end

#### <a name="receive-direct-methods"></a>직접 메서드 받기

Azure IoT SDK를 사용 하 여 직접 메서드를 수신 하려면 콜백 함수를 구현 하 고 `ModuleClient.methodCallback` Azure IOT sdk의 메서드에 등록 하 여 직접 메서드가 들어올 때마다 콜백 함수가 트리거됩니다.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT 클라이언트를 사용 하 여 직접 메서드를 수신 하기 위해이 프로세스는 쌍 패치를 받는 것과 매우 비슷합니다. 클라이언트는 호출을 수신 하 고 일부 정보를 동시에 전송할 수 있는지 확인 해야 합니다. 구독할 특수 IoT Hub 토픽은 `$iothub/methods/POST/#` 입니다.

::: moniker-end

## <a name="language-and-architecture-support"></a>언어 및 아키텍처 지원

IoT Edge는 요구 사항과 일치 하는 시나리오를 구축할 수 있도록 여러 운영 체제, 장치 아키텍처 및 개발 언어를 지원 합니다. 이 섹션을 사용 하 여 사용자 지정 IoT Edge 모듈을 개발 하기 위한 옵션을 이해할 수 있습니다. [IoT Edge에 대 한 개발 및 테스트 환경 준비](development-environment.md)에서 각 언어에 대 한 도구 지원 및 요구 사항에 대해 자세히 알아볼 수 있습니다.

### <a name="linux"></a>Linux

다음 표의 모든 언어에 대해 IoT Edge는 AMD64 및 ARM32 Linux 장치에 대 한 개발을 지원 합니다.

| 개발 언어 | 개발 도구 |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>ARM64 Linux 장치에 대 한 개발 및 디버깅은 [공개 미리 보기로](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)제공 됩니다. 자세한 내용은 [Visual Studio Code(미리 보기)에서 ARM64 IoT Edge 모듈 개발 및 디버그](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)를 참조하세요.

### <a name="windows"></a>Windows

다음 표의 모든 언어에 대해 IoT Edge는 AMD64 Windows 장치 개발을 지원 합니다.

| 개발 언어 | 개발 도구 |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (디버깅 기능 없음)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>다음 단계

[IoT Edge를 위한 개발 및 테스트 환경 준비](development-environment.md)

[Visual Studio를 사용 하 여 IoT Edge에 대 한 c # 모듈 개발](how-to-visual-studio-develop-module.md)

[Visual Studio Code를 사용 하 여 IoT Edge 모듈 개발](how-to-vs-code-develop-module.md)

[Azure IoT Hub SDK 이해 및 사용](../iot-hub/iot-hub-devguide-sdks.md)
