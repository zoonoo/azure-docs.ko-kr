---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 8b48aacf29666536a360af34b5cbc0f6a2dcf0f2
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111993731"
---
## <a name="create-the-cloud-components"></a>클라우드 구성 요소 만들기

### <a name="create-the-iot-central-application"></a>IoT Central 애플리케이션 만들기

디바이스를 Azure IoT에 연결하는 방법에는 여러 가지가 있습니다. 이 섹션에서는 Azure IoT Central을 사용하여 디바이스를 연결하는 방법에 대해 알아봅니다. IoT Central은 IoT 솔루션을 만들고 관리하는 데 드는 비용과 복잡성을 줄이는 IoT 애플리케이션 플랫폼입니다.

새 애플리케이션을 만들려면
1. [Azure IoT Central 포털](https://apps.azureiotcentral.com/)의 측면 탐색 메뉴에서 **빌드** 를 선택합니다.

    > [!NOTE]
    > 기존 IoT Central 애플리케이션이 있는 경우 이를 사용하여 새 애플리케이션을 만드는 대신 이 문서의 단계를 완료할 수 있습니다. 이 경우 기존 디바이스 ID를 사용하려는 경우 새 디바이스를 만들거나 디바이스를 삭제한 후 다시 만드는 것이 좋습니다.

1. **사용자 지정 앱** 타일에서 **앱 만들기** 를 선택합니다.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-select-custom.png" alt-text="Azure IoT Central에서 사용자 지정 앱 만들기 스크린샷":::

1. 애플리케이션 이름 및 URL을 추가합니다.
1. **체험** 가격 책정 계획을 선택하여 7일 평가판을 활성화합니다.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-custom.png" alt-text="Azure IoT Central에서 새 앱에 대한 정보를 입력하는 스크린샷":::

1. **만들기** 를 선택합니다. IoT Central에서 애플리케이션을 프로비저닝하면 자동으로 새 애플리케이션 대시보드로 리디렉션됩니다.


### <a name="create-a-new-device"></a>새 디바이스 만들기

이 섹션에서는 IoT Central 애플리케이션 대시보드를 사용하여 새 디바이스를 만듭니다. 이후 섹션에서 새로 만든 디바이스에 대한 연결 정보를 사용하여 물리적 디바이스를 안전하게 연결합니다.

디바이스를 만들려면
1. 애플리케이션 대시보드의 측면 탐색 메뉴에서 **디바이스** 를 선택합니다.
1. **모든 디바이스** 창에서 **디바이스 만들기** 를 선택하여 **새 디바이스 만들기** 창을 엽니다. 이미 하나 이상의 디바이스가 있는 기존 애플리케이션을 다시 사용하는 경우 **+ 새로 만들기** 를 선택하여 창을 엽니다.
1. 디바이스 템플릿을 **할당되지 않음** 으로 둡니다.
1. 원하는 디바이스 이름 및 디바이스 ID를 입력합니다.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-device.png" alt-text="Azure IoT Central에서 새 디바이스에 대한 정보를 입력하는 스크린샷":::

1. **만들기** 단추를 선택합니다.
1. **모든 디바이스** 목록에 새로 만든 디바이스가 표시됩니다.  디바이스 이름을 선택하여 세부 정보를 표시합니다.
1. 오른쪽 위의 메뉴 모음에서 **연결** 을 선택하여 다음 섹션에서 디바이스를 구성하는 데 사용되는 연결 정보를 표시합니다.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-device-connection-info.png" alt-text="Azure IoT Central의 디바이스 연결 세부 정보 스크린샷":::

1. **연결** 대화 상자에 표시된 다음 연결 문자열 매개 변수에 대한 연결 값을 적어 둡니다. 이러한 값은 다음 단계에서 구성 파일에 추가합니다.

    * `ID scope`
    * `Device ID`
    * `Primary key`