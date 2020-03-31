---
title: Azure IoT 탐색기 설치 및 사용 | 마이크로 소프트 문서
description: Azure IoT 탐색기 도구를 설치하고 이를 사용하여 IoT 허브에 연결된 IoT 플러그 앤 플레이 미리 보기 장치와 상호 작용합니다.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159186"
---
# <a name="install-and-use-azure-iot-explorer"></a>Azure IoT 탐색기 설치 및 사용

Azure IoT 탐색기는 IoT 플러그 앤 플레이 미리 보기 장치와 상호 작용하고 테스트하기 위한 그래픽 도구입니다. 로컬 컴퓨터에 도구를 설치한 후 이 도구를 사용하여 장치에 연결할 수 있습니다. 이 도구를 사용하여 장치가 전송하는 원격 분석을 보고, 장치 속성으로 작업하고, 명령을 호출할 수 있습니다.

이 아티클에서는 다음을 수행하는 방법을 보여줍니다.

- Azure IoT 탐색기 도구를 설치하고 구성합니다.
- 이 도구를 사용하여 장치와 상호 작용하고 테스트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure IoT 탐색기 도구를 사용하려면 다음이 필요합니다.

- Azure IoT Hub - Azure [CLI를 사용하여 IoT](../iot-hub/iot-hub-create-using-cli.md)허브 만들기와 같이 Azure 구독에 IoT 허브를 추가하는 방법에는 여러 가지가 있습니다. Azure IoT 탐색기 도구를 실행하려면 IoT 허브 연결 문자열이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- IoT 허브에 등록된 장치입니다. 다음 Azure CLI 명령을 사용하여 장치를 등록할 수 있습니다. `{YourIoTHubName}` 및 `{YourDeviceID}` 자리 표시자를 값으로 바꿔야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Azure IoT 탐색기 설치

Azure [IoT 탐색기 릴리스로](https://github.com/Azure/azure-iot-explorer/releases) 이동하여 최신 릴리스의 자산 목록을 확장합니다. 최신 버전의 응용 프로그램을 다운로드하여 설치합니다.

## <a name="use-azure-iot-explorer"></a>Azure IoT 탐색기 사용

장치의 경우 사용자 고유의 장치를 연결하거나 샘플 시뮬레이션 장치 중 하나를 사용할 수 있습니다. [다음 지침에](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) 따라 시뮬레이션된 장치 샘플을 실행합니다.

### <a name="connect-to-your-hub"></a>허브에 연결

Azure IoT 탐색기를 처음 실행할 때 IoT 허브의 연결 문자열에 대한 메시지가 표시됩니다. 연결 문자열을 추가한 후 **연결을**선택합니다. 이 도구의 설정을 사용하여 연결 문자열을 업데이트하여 다른 IoT 허브로 전환할 수 있습니다.

IoT 플러그 앤 플레이 장치의 모델 정의는 공용 리포지토리, 회사 리포지토리 또는 연결된 장치에 저장됩니다. 기본적으로 이 도구는 공용 모델 리포지토리및 연결된 장치에서 모델 정의를 찾습니다. 소스를 추가 및 제거하거나 **설정에서**소스의 우선 순위를 구성할 수 있습니다.

소스를 추가하려면 다음을 수행하십시오.

1. **설정**으로 이동합니다.
1. **새로 하나를** 선택하고 소스를 선택합니다.
1. 회사 모델 리포지토리를 추가하는 경우 연결 문자열을 제공합니다.

소스를 제거하려면 다음을 수행하십시오.

1. **설정**으로 이동합니다.
1. 제거할 소스를 찾습니다.
1. **X를** 선택하여 제거합니다. 공용 인터페이스 정의는 이 리포지토리에서 제공되므로 공용 모델 리포지토리를 제거할 수 없습니다.

소스 우선 순위 변경:

모델 정의 소스 중 하나를 목록의 다른 순위로 끌어서 놓을 수 있습니다. 충돌이 있는 경우 순위가 높은 정의 소스는 순위가 낮은 소스를 재정의합니다.

### <a name="view-devices"></a>디바이스 보기

도구가 IoT 허브에 연결되면 IoT 허브에 등록된 장치 ID를 나열하는 **장치** 목록 페이지가 표시됩니다. 목록에서 항목을 확장하여 자세한 정보를 볼 수 있습니다.

**장치** 목록 페이지에서 다음을 수행할 수 있습니다.

- 허브에 새 장치를 등록하려면 **추가를** 선택합니다. 그런 다음 장치 ID를 입력합니다. 기본 설정을 사용하여 인증 키를 자동으로 생성하고 허브에 대한 연결을 사용하도록 설정합니다.
- 장치를 선택한 다음 **삭제를** 선택하여 장치 ID를 삭제합니다. 이 작업을 완료하기 전에 장치 세부 정보를 검토하여 올바른 장치 ID를 삭제하는지 확인합니다.
- 에 `capabilityID` 의해 `interfaceID`쿼리합니다. 또는 `capabilityID` 매개 `interfaceID` 변수로 추가하여 장치를 쿼리합니다.

## <a name="interact-with-a-device"></a>장치와 상호 작용

**장치** 목록 페이지에서 **장치 ID** 열에서 값을 선택하여 등록된 장치의 세부 정보 페이지를 봅니다. 각 장치에 대해 장치 **및** **디지털 트윈의**두 섹션이 있습니다.

### <a name="device"></a>디바이스

이 섹션에는 **장치 ID,** **장치 쌍,** **원격 분석,** **직접 메서드** 및 **클라우드-장치 메시지** 탭이 포함됩니다.

- **장치 ID** 탭에서 [장치 ID](../iot-hub/iot-hub-devguide-identity-registry.md) 정보를 보고 업데이트할 수 있습니다.
- **장치** 쌍 탭에서 [장치 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 정보에 액세스할 수 있습니다.
- 장치가 연결되어 있고 적극적으로 데이터를 보내는 경우 **원격 분석** 탭에서 [원격 분석을](../iot-hub/iot-hub-devguide-messages-read-builtin.md) 볼 수 있습니다.
- Direct 메서드 탭에서 장치에서 [직접](../iot-hub/iot-hub-devguide-direct-methods.md) **메서드를** 호출할 수 있습니다.
- [클라우드-장치 메시지](../iot-hub/iot-hub-devguide-messages-c2d.md) 탭에서 **클라우드-장치 메시지를** 보낼 수 있습니다.

### <a name="digital-twin"></a>디지털 트윈

이 도구를 사용하여 장치의 디지털 트윈 인스턴스를 볼 수 있습니다. IoT 플러그 앤 플레이 장치의 경우 장치 기능 모델과 연결된 모든 인터페이스가 도구의 이 섹션에 표시됩니다. 인터페이스를 선택하여 해당 [IoT 플러그 앤 플레이 프리미티브를 확장합니다.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)

### <a name="interface"></a>인터페이스

**인터페이스** 페이지에서 인터페이스의 JSON 정의를 볼 수 있습니다.

#### <a name="properties"></a>속성

**쓸 수 없는** 속성 페이지의 인터페이스에 정의된 읽기 전용 속성을 볼 수 있습니다. 쓰기 가능 속성 페이지의 인터페이스에 정의된 쓰기 **가능한 속성을 업데이트할** 수 있습니다.

1. 쓰기 가능한 속성 페이지로 **이동합니다.**
1. 업데이트할 속성을 클릭합니다.
1. 속성에 대한 새 값을 입력합니다.
1. 장치로 보낼 페이로드를 미리 봅시봅
1. 변경 사항을 제출합니다.

변경 사항을 제출한 후 업데이트 상태인 **동기화,** **성공**또는 **오류를**추적할 수 있습니다. 동기화가 완료되면 **보고된 속성** 열에 속성의 새 값이 표시됩니다. 동기화가 완료되기 전에 다른 페이지로 이동해도 업데이트가 완료되면 이 도구는 계속 알려주는 것입니다. 도구의 알림 센터를 사용하여 알림 기록을 볼 수도 있습니다.

#### <a name="commands"></a>명령

장치에 명령을 보내려면 명령 페이지로 **이동하십시오.**

1. 명령 목록에서 트리거할 명령을 확장합니다.
1. 명령에 필요한 값을 입력합니다.
1. 장치로 보낼 페이로드를 미리 봅시봅
1. 명령을 제출합니다.

#### <a name="telemetry"></a>원격 분석

선택한 인터페이스의 원격 분석을 보려면 **원격 분석** 페이지로 이동하십시오.

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Azure IoT 탐색기를 설치하고 사용하여 IoT 플러그 앤 플레이 장치와 상호 작용하는 방법을 배웠습니다. 다음 단계는 [Azure CLI 확장을 설치하고 사용하는](./howto-install-pnp-cli.md)방법을 알아보는 것입니다.
