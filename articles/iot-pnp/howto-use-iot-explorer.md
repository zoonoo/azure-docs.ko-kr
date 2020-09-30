---
title: Azure IoT 탐색기 설치 및 사용 | Microsoft Docs
description: Azure IoT 탐색기 도구를 설치 하 고이 도구를 사용 하 여 iot hub에 연결 된 IoT 플러그 앤 플레이 장치와 상호 작용 합니다.
author: rido-min
ms.author: rmpablos
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e64586ced7a619de02ae3a9d6beda35660ca04a9
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577580"
---
# <a name="install-and-use-azure-iot-explorer"></a>Azure IoT 탐색기 설치 및 사용

Azure IoT 탐색기는 IoT 플러그 앤 플레이 장치를 조작 하 고 테스트 하는 그래픽 도구입니다. 로컬 컴퓨터에 도구를 설치한 후 허브에 연결 하는 데 사용할 수 있습니다. 도구를 사용 하 여 장치에서 보내는 원격 분석을 보고, 장치 속성을 사용 하 고, 명령을 호출할 수 있습니다.

이 아티클에서는 다음을 수행하는 방법을 보여줍니다.

- Azure IoT 탐색기 도구를 설치 하 고 구성 합니다.
- 도구를 사용 하 여 장치를 조작 하 고 테스트 합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure IoT 탐색기 도구를 사용 하려면 다음이 필요 합니다.

- Azure IoT Hub - [Azure CLI를 사용 하 여 iot hub를 만드는 등의](../iot-hub/iot-hub-create-using-cli.md)다양 한 방법으로 Azure 구독에 iot hub를 추가할 수 있습니다. Azure IoT 탐색기 도구를 실행 하려면 IoT hub 연결 문자열이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- IoT Hub에 등록된 디바이스. IoT 탐색기를 사용 하 여 IoT Hub에서 장치 등록을 만들고 관리할 수 있습니다.

## <a name="install-azure-iot-explorer"></a>Azure IoT 탐색기 설치

[Azure IoT explorer 릴리스](https://github.com/Azure/azure-iot-explorer/releases) 로 이동 하 여 최신 릴리스에 대 한 자산 목록을 확장 합니다. 최신 버전의 응용 프로그램을 다운로드 하 여 설치 합니다.

>[!Important]
>버전 0.11.0 IoT Explorer는 IoT 플러그 앤 플레이 GA 릴리스 (9 월 2020 일)만 지원 합니다. 8 월 2019의 이전 미리 보기 릴리스에서 제공 되는 기능을 사용 하려면 0.10 버전을 설치 합니다.

## <a name="use-azure-iot-explorer"></a>Azure IoT 탐색기 사용

장치의 경우 장치를 연결 하거나 시뮬레이션 된 샘플 장치 중 하나를 사용할 수 있습니다. 시뮬레이션 된 장치 샘플을 실행 하려면 [다음 지침](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples) 을 따르세요.

### <a name="connect-to-your-hub"></a>허브에 연결

Azure IoT 탐색기를 처음 실행 하면 IoT hub의 연결 문자열을 입력 하 라는 메시지가 표시 됩니다. 연결 문자열을 추가한 후 **연결**을 선택 합니다. 도구의 설정을 사용 하 여 연결 문자열을 업데이트 하 여 다른 IoT hub로 전환할 수 있습니다.

IoT 플러그 앤 플레이 장치에 대 한 모델 정의는 공용 리포지토리, 연결 된 장치 또는 로컬 폴더에 저장 됩니다. 기본적으로이 도구는 공용 리포지토리와 연결 된 장치에서 모델 정의를 찾습니다. 원본 추가/제거 또는 **설정**에서 원본의 우선 순위를 구성할 수 있습니다.

소스를 추가 하려면:

1. **Home/IoT 플러그 앤 플레이 설정** 으로 이동
2. **추가** 를 선택 하 고 리포지토리 또는 로컬 폴더에서 소스를 선택 합니다.

원본을 제거 하려면 다음을 수행 합니다.

1. **Home/IoT 플러그 앤 플레이 설정** 으로 이동
2. 제거 하려는 원본을 찾습니다.
3. **X** 를 선택 하 여 제거 합니다.

원본 우선 순위 변경:

모델 정의 원본 중 하나를 목록에서 다른 순위에 끌어서 놓을 수 있습니다.

### <a name="view-devices"></a>디바이스 보기

도구는 IoT hub에 연결 된 후 IoT hub에 등록 된 장치 id를 나열 하는 **장치** 목록 페이지를 표시 합니다. 목록에서 항목을 선택 하 여 자세한 정보를 볼 수 있습니다.

**장치** 목록 페이지에서 다음을 수행할 수 있습니다.

- 새 장치를 허브에 등록 하려면 **새로 만들기** 를 선택 합니다. 그런 다음 장치 ID를 입력 합니다. 기본 설정을 사용 하 여 인증 키를 자동으로 생성 하 고 허브에 대 한 연결을 사용 하도록 설정 합니다.
- 장치를 선택 하 고 **삭제** 를 선택 하 여 장치 id를 삭제 합니다. 이 작업을 완료 하기 전에 장치 세부 정보를 검토 하 여 올바른 장치 id를 삭제 하 고 있는지 확인 합니다.

## <a name="interact-with-a-device"></a>디바이스와 상호 작용

**장치 목록 페이지** 의 **장치 ID** 열에서 값을 선택 하 여 등록 된 장치에 대 한 세부 정보 페이지를 표시 합니다. 각 장치에는 **장치** 및 **디지털**쌍의 두 섹션이 있습니다.

### <a name="device"></a>디바이스

이 섹션에는 **장치 id**,  **장치**쌍, **원격 분석**, **직접 메서드**, **클라우드-장치 메시지**, **모듈 id**  탭이 포함 되어 있습니다.

- **장치 id 탭에서** [장치 id](../iot-hub/iot-hub-devguide-identity-registry.md) 정보를 확인 하 고 업데이트할 수 있습니다.
- [장치 쌍 정보는](../iot-hub/iot-hub-devguide-device-twins.md) **장치** 쌍 탭에서 액세스할 수 있습니다.
- 장치가 연결 되어 있으며 데이터를 적극적으로 전송 하는 경우 **원격 분석** 탭에서 [원격 분석](../iot-hub/iot-hub-devguide-messages-read-builtin.md) 을 볼 수 있습니다.
- **직접 메서드** 탭에서 장치에 대 한 [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md) 를 호출할 수 있습니다.
- 클라우드- **장치 메시지** 탭에서 [클라우드-장치 메시지](../iot-hub/iot-hub-devguide-messages-c2d.md) 를 보낼 수 있습니다.
- [모듈](../iot-hub/iot-hub-devguide-module-twins.md) 쌍 정보에 액세스할 수 있습니다.

### <a name="iot-plug-and-play-components"></a>IoT 플러그 앤 플레이 구성 요소

장치가 **모델 id**를 사용 하 여 허브에 연결 된 경우이 도구는 **모델 id**를 볼 수 있는 **IoT 플러그 앤 플레이 구성 요소** 탭 ' '을 표시 합니다.

구성 된 원본-공용 리포지토리 또는 로컬 폴더 중 하나에서 **모델 ID** 를 사용할 수 있는 경우 구성 요소 목록이 표시 됩니다. 구성 요소를 선택 하면 사용할 수 있는 속성, 명령 및 원격 분석이 표시 됩니다.

**구성 요소** 페이지에서 읽기 전용 속성을 보고, 쓰기 가능한 속성을 업데이트 하 고, 명령을 호출 하 고,이 구성 요소에서 생성 한 원격 분석 메시지를 볼 수 있습니다.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Azure IoT 탐색기에서 구성 요소 보기":::

#### <a name="properties"></a>속성

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Azure IoT 탐색기에서 구성 요소 보기":::

**속성 (읽기 전용)** 탭의 인터페이스에 정의 된 읽기 전용 속성을 볼 수 있습니다. **속성 (쓰기 가능)** 탭의 인터페이스에 정의 된 쓰기 가능한 속성을 업데이트할 수 있습니다.

1. **속성 (쓰기 가능)** 탭으로 이동 합니다.
1. 업데이트 하려는 속성을 클릭 합니다.
1. 속성의 새 값을 입력 합니다.
1. 장치에 전송할 페이로드를 미리 봅니다.
1. 변경 내용을 제출 합니다.

변경을 제출한 후 업데이트 상태 ( **동기화**, **성공**또는 **오류**)를 추적할 수 있습니다. 동기화가 완료 되 면 **보고 된 속성** 열에 속성의 새 값이 표시 됩니다. 동기화가 완료 되기 전에 다른 페이지로 이동 하는 경우에도 업데이트가 완료 되 면 도구에서 사용자에 게 알려줍니다. 도구의 알림 센터를 사용 하 여 알림 기록을 볼 수도 있습니다.

#### <a name="commands"></a>명령

장치에 명령을 보내려면 **명령** 탭으로 이동 합니다.

1. 명령 목록에서 트리거할 명령을 확장 합니다.
1. 명령에 필요한 값을 입력 합니다.
1. 장치에 전송할 페이로드를 미리 봅니다.
1. 명령을 제출 합니다.

#### <a name="telemetry"></a>원격 분석

선택한 인터페이스에 대 한 원격 분석을 보려면 해당 **원격 분석** 탭으로 이동 합니다.

#### <a name="known-issues"></a>알려진 문제

- IoT Edge 지원: 현재 버전은 장치 목록에 IoT Edge 장치를 표시 하지 않습니다.
- DTDL 언어 기능: IoT 탐색기 0.12는 완전 한 DTDL v2 규격이 아닙니다. 지원 되지 않는 기능은 다음과 같습니다.
  - 인터페이스 상속 `extends`
  - Map in map (중첩 맵)
  - 배열 형식
  - 사용자 지정 스키마
  - 사용자 지정 의미 유형

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Azure IoT 탐색기를 설치 하 고 사용 하 여 IoT 플러그 앤 플레이 장치와 상호 작용 하는 방법을 배웠습니다. 제안 된 다음 단계는 [Azure CLI IoT 플러그 앤 플레이 명령을](./howto-use-iot-pnp-cli.md)사용 하는 방법을 설명 하는 것입니다.
