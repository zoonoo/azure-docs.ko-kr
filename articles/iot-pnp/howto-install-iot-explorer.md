---
title: Azure IoT 탐색기 설치 및 사용 | Microsoft Docs
description: Azure IoT 탐색기 도구를 설치 하 고이 도구를 사용 하 여 iot hub에 연결 된 IoT 플러그 앤 플레이 미리 보기 장치와 상호 작용 합니다.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4e23a440f46b52633a88d0212e08c7b584f61a38
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932466"
---
# <a name="install-and-use-azure-iot-explorer"></a>Azure IoT 탐색기 설치 및 사용

Azure IoT 탐색기는 IoT 플러그 앤 플레이 미리 보기 장치를 조작 하 고 테스트 하는 그래픽 도구입니다. 로컬 컴퓨터에 도구를 설치한 후에는이 도구를 사용 하 여 장치에 연결할 수 있습니다. 도구를 사용 하 여 장치에서 보내는 원격 분석을 확인 하 고, 장치 속성을 사용 하 고, 명령을 호출할 수 있습니다.

이 문서에서는 다음 방법을 안내합니다.

- Azure IoT 탐색기 도구를 설치 하 고 구성 합니다.
- 도구를 사용 하 여 장치를 조작 하 고 테스트 합니다.

## <a name="prerequisites"></a>전제 조건

Azure IoT 탐색기 도구를 사용 하려면 다음이 필요 합니다.

- Azure IoT Hub - [Azure CLI를 사용 하 여 iot hub를 만드는 등의](../iot-hub/iot-hub-create-using-cli.md)다양 한 방법으로 Azure 구독에 iot hub를 추가할 수 있습니다. Azure IoT 탐색기 도구를 실행 하려면 IoT hub 연결 문자열이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- IoT hub에 등록 된 장치입니다. 다음 Azure CLI 명령을 사용 하 여 장치를 등록할 수 있습니다. `{YourIoTHubName}` 및`{YourDeviceID}` 자리 표시자를 사용자의 값으로 바꾸어야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Azure IoT 탐색기 설치

[Azure IoT explorer 릴리스](https://github.com/Azure/azure-iot-explorer/releases) 로 이동 하 여 최신 릴리스에 대 한 자산 목록을 확장 합니다. 최신 버전의 응용 프로그램을 다운로드 하 여 설치 합니다.

## <a name="use-azure-iot-explorer"></a>Azure IoT 탐색기 사용

장치의 경우 장치를 연결 하거나 시뮬레이션 된 샘플 장치 중 하나를 사용할 수 있습니다. 시뮬레이션 된 장치 샘플을 실행 하려면 [다음 지침](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) 을 따르세요.

### <a name="connect-to-your-hub"></a>허브에 연결

Azure IoT 탐색기를 처음 실행 하면 IoT hub의 연결 문자열을 입력 하 라는 메시지가 표시 됩니다. 연결 문자열을 추가한 후 **연결**을 선택 합니다. 도구의 설정을 사용 하 여 연결 문자열을 업데이트 하 여 다른 IoT hub로 전환할 수 있습니다.

IoT 플러그 앤 플레이 장치에 대 한 모델 정의는 공용 리포지토리, 회사 리포지토리 또는 연결 된 장치에 저장 됩니다. 기본적으로이 도구는 공용 모델 리포지토리 및 연결 된 장치에서 모델 정의를 찾습니다. 원본 추가/제거 또는 **설정**에서 원본의 우선 순위를 구성할 수 있습니다.

소스를 추가 하려면:

1. **설정**으로 이동합니다.
1. **새로 만들기** 를 선택 하 고 소스를 선택 합니다.
1. 회사 모델 리포지토리를 추가 하는 경우 연결 문자열을 제공 합니다.

원본을 제거 하려면 다음을 수행 합니다.

1. **설정**으로 이동합니다.
1. 제거 하려는 원본을 찾습니다.
1. **X** 를 선택 하 여 제거 합니다. 공용 인터페이스 정의는이 리포지토리에서 제공 되므로 공용 모델 리포지토리를 제거할 수 없습니다.

원본 우선 순위 변경:

모델 정의 원본 중 하나를 목록에서 다른 순위에 끌어서 놓을 수 있습니다. 충돌이 발생 하는 경우 순위가 더 높은 정의 원본은 순위가 낮은 소스를 재정의 합니다.

### <a name="overview-page"></a>개요 페이지

#### <a name="device-overview"></a>장치 개요

도구는 IoT hub에 연결 된 후 Azure IoT hub에 등록 된 모든 장치 id의 목록을 표시 하는 개요 페이지를 표시 합니다. 자세한 내용을 보려면 장치를 선택 하세요.

#### <a name="device-management"></a>디바이스 관리

- 허브에 새 장치를 등록 하려면 **추가**를 선택 합니다. 장치 ID를 입력 합니다. 기본 설정을 사용 하 여 인증 키를 자동 생성 하 고 허브에 대 한 연결을 사용 하도록 설정 합니다.
- 장치 id를 삭제 하려면 **삭제**를 선택 합니다. 이 작업을 완료 하기 전에 장치 세부 정보를 검토 하 여 올바른 장치 id를 삭제 하 고 있는지 확인 합니다.
- 도구는 `capabilityID` 및 `interfaceID`의 쿼리를 지원 합니다. `capabilityID` 또는`interfaceID` 를 매개 변수로 추가 하 여 장치를 쿼리 합니다.

## <a name="interact-with-a-device"></a>장치와 상호 작용

개요 페이지에서 장치를 두 번 클릭 하면 다음 수준의 세부 정보를 볼 수 있습니다. 두 개의 섹션이 있습니다. **장치** 및 **디지털**쌍

### <a name="device"></a>디바이스

이 섹션에는 **장치 id**, **원격 분석**및 **장치** 쌍 탭이 포함 되어 있습니다.

- **장치 id 탭에서** 장치 id 정보를 확인 하 고 업데이트할 수 있습니다.
- 장치가 연결 되어 있으며 데이터를 적극적으로 전송 하는 경우 **원격 분석** 탭에서 원격 분석을 볼 수 있습니다.
- 장치 쌍 정보는 **장치** 쌍 탭에서 액세스할 수 있습니다.

### <a name="digital-twin"></a>디지털 쌍

이 도구를 사용 하 여 장치의 디지털 쌍 인스턴스를 볼 수 있습니다. IoT 플러그 앤 플레이 장치의 경우 장치 기능 모델과 연결 된 모든 인터페이스가이 문서에 표시 됩니다. 인터페이스를 선택 하 여 해당 [IoT 플러그 앤 플레이 기본 형식을](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)확장 합니다.

#### <a name="properties"></a>속성

**속성** 페이지의 인터페이스에 정의 된 읽기 전용 속성을 볼 수 있습니다. **쓰기 가능한 속성** 페이지의 인터페이스에 정의 된 쓰기 가능한 속성을 업데이트할 수 있습니다.

1. **쓰기 가능한 속성** 페이지로 이동 합니다.
1. 업데이트 하려는 속성을 클릭 합니다.
1. 속성의 새 값을 입력 합니다.
1. 장치에 전송할 페이로드를 미리 봅니다.
1. 변경 내용을 제출 합니다.

변경을 제출한 후 업데이트 상태 ( **동기화**, **성공**또는 **오류**)를 추적할 수 있습니다. 동기화가 완료 되 면 **보고 된 속성** 열에 속성의 새 값이 표시 됩니다. 동기화가 완료 되기 전에 다른 페이지로 이동 하는 경우에도 업데이트가 완료 되 면 도구에서 사용자에 게 알려줍니다. 도구의 알림 센터를 사용 하 여 알림 기록을 볼 수도 있습니다.

#### <a name="commands"></a>명령

장치에 명령을 보내려면 **명령** 페이지로 이동 합니다.

1. 명령 목록에서 트리거할 명령을 확장 합니다.
1. 명령에 필요한 값을 입력 합니다.
1. 장치에 전송할 페이로드를 미리 봅니다.
1. 명령을 제출 합니다.

#### <a name="telemetry"></a>원격 분석

선택한 인터페이스에 대 한 원격 분석을 보려면 해당 **원격 분석** 페이지로 이동 합니다.

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Azure IoT 탐색기를 설치 하 고 사용 하 여 IoT 플러그 앤 플레이 장치와 상호 작용 하는 방법을 배웠습니다. 제안 된 다음 단계는 [Azure CLI 확장을 설치 하 고 사용](./howto-install-pnp-cli.md)하는 방법을 배우는 것입니다.
