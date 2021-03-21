---
title: V3로 V2 Azure IoT Central 응용 프로그램 마이그레이션 | Microsoft Docs
description: 관리자는 V2 Azure IoT Central 응용 프로그램을 V3로 마이그레이션하는 방법에 대해 알아봅니다.
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 735ad7ad9ded6baded59ab3f08e239d1c8376b74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702728"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>V2 IoT Central 응용 프로그램을 V3로 마이그레이션

*이 문서는 관리자에 게 적용 됩니다.*

현재 새 IoT Central 응용 프로그램을 만들 때이 응용 프로그램은 V3 응용 프로그램입니다. 이전에 응용 프로그램을 만든 경우 해당 응용 프로그램을 만든 시기에 따라 V2가 될 수 있습니다. 이 문서에서는 최신 IoT Central 기능을 사용 하 고 있도록 V2를 V3 응용 프로그램으로 마이그레이션하는 방법을 설명 합니다.

IoT Central 응용 프로그램의 버전을 확인 하는 방법을 알아보려면 [응용 프로그램 정보](howto-get-app-info.md)를 참조 하세요.

V2에서 V3로 응용 프로그램을 마이그레이션하는 단계는 다음과 같습니다.

1. V2 응용 프로그램에서 새 V3 응용 프로그램을 만듭니다.
1. V3 응용 프로그램을 구성 합니다.
1. V2 응용 프로그램으로 삭제 합니다.

## <a name="create-a-new-v3-application"></a>새 V3 응용 프로그램 만들기

마이그레이션 마법사를 사용 하 여 새 V3 응용 프로그램을 만듭니다.

IoT Central는 기존 V3 응용 프로그램으로의 마이그레이션을 지원 하지 않습니다. 기존 장치를 자동으로 이동 하려면 마이그레이션 마법사를 사용 하 여 V3 응용 프로그램을 만듭니다.

마이그레이션 마법사:

- 새 V3 응용 프로그램을 만듭니다.
- V3과의 호환성을 위해 장치 템플릿을 확인 합니다.
- 모든 장치 템플릿을 새 V3 응용 프로그램에 복사 합니다.
- 모든 사용자 및 역할 할당을 새 V3 응용 프로그램에 복사 합니다.

> [!NOTE]
> 장치와 V3의 호환성을 보장 하기 위해 장치 템플릿의 기본 형식은 개체 속성이 됩니다. 장치 코드를 변경할 필요가 없습니다.

응용 프로그램을 V3로 마이그레이션하려면 관리자 여야 합니다.

1. **관리** 메뉴에서 **V3 응용 프로그램으로 마이그레이션** 을 선택 합니다.

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="응용 프로그램 마이그레이션 마법사를 보여 주는 스크린샷":::

1. 새 **응용 프로그램 이름을** 입력 하 고 선택적으로 자동 생성 된  **URL** 을 변경 합니다. URL은 현재 V2 응용 프로그램의 URL과 같을 수 없습니다. 그러나 V2 응용 프로그램을 삭제 한 후에는 나중에 URL을 변경할 수 있습니다.

1. **새 V3 앱 만들기를** 선택 합니다.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="응용 프로그램 마이그레이션 마법사의 옵션을 보여 주는 스크린샷":::

    장치 템플릿의 수와 복잡도에 따라이 프로세스를 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

    > [!Warning]
    > 장치 템플릿에 숫자로 시작 하거나 다음 문자 (,,,,,,,,,,,,)를 포함 하는 필드가 있으면 V3 응용 프로그램 만들기가 실패 합니다 `+` `*` `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` `\` . V3 응용 프로그램에서 사용 하는 DTDL 장치 템플릿 스키마는 이러한 문자를 허용 하지 않습니다. V3로 마이그레이션하기 전에이 문제를 해결 하기 위해 장치 템플릿을 업데이트 합니다.

1. 새 V3 앱이 준비 되 면 **새 앱 열기** 를 선택 하 여 엽니다.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="응용 프로그램 마이그레이션 후 응용 프로그램 마이그레이션 마법사를 보여 주는 스크린샷":::

## <a name="configure-the-v3-application"></a>V3 응용 프로그램 구성

새 V3 응용 프로그램을 만든 후에는 V2 응용 프로그램에서 V3 응용 프로그램으로 장치를 이동 하기 전에 구성 변경을 수행 합니다.

> [!TIP]
> 이전 버전과 약간의 차이가 있으므로 [V3에 익숙해질](overview-iot-central-tour.md#navigate-your-application) 수 있습니다.

고려해 야 할 몇 가지 권장 구성 단계는 다음과 같습니다.

- [대시보드 구성](howto-add-tiles-to-your-dashboard.md)
- [데이터 내보내기 구성](howto-export-data.md)
- [규칙 및 작업 구성](quick-configure-rules.md)
- [응용 프로그램 UI 사용자 지정](howto-customize-ui.md)

사용자의 요구에 맞게 V3 응용 프로그램이 구성 된 경우 V2 응용 프로그램에서 V3 응용 프로그램으로 장치를 이동할 준비가 되었습니다.

## <a name="move-your-devices-to-the-v3-application"></a>장치를 V3 응용 프로그램으로 이동

이 단계가 완료 되 면 모든 장치가 새 V3 응용 프로그램과만 통신 합니다.

> [!IMPORTANT]
> 장치를 V3 응용 프로그램으로 이동 하기 전에 V3 응용 프로그램에서 만든 모든 장치를 삭제 합니다.

이 단계에서는 기존 장치를 모두 새로운 V3 응용 프로그램으로 이동 합니다. 장치 데이터는 복사 되지 않습니다.

**모든 장치 이동** 을 선택 하 여 장치 이동을 시작 합니다. 이 단계를 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="장치 이동 옵션을 사용 하 여 응용 프로그램 마이그레이션 마법사를 보여 주는 스크린샷":::

이동이 완료 되 면 모든 장치를 다시 시작 하 여 새 V3 응용 프로그램에 연결 하도록 합니다.

> [!WARNING]
> 이제 V2 응용 프로그램이 작동 하지 않으므로 V3 응용 프로그램을 삭제 하지 마세요.

## <a name="delete-your-old-v2-application"></a>이전 V2 응용 프로그램 삭제

새 V3 응용 프로그램에서 모든 항목이 예상 대로 작동 하는지 확인 한 후에는 이전 V2 응용 프로그램을 삭제 합니다. 이 단계를 통해 더 이상 사용 하지 않는 응용 프로그램에 대 한 요금이 청구 되지 않습니다.

> [!Note]
> 응용 프로그램을 삭제 하려면 응용 프로그램을 만들 때 선택한 Azure 구독에서 리소스를 삭제할 수 있는 권한이 있어야 합니다. 자세한 내용은 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

1. V2 응용 프로그램의 메뉴에서 **관리** 탭을 선택 합니다.
2. IoT Central 응용 프로그램을 영구적으로 삭제 하려면 **삭제** 를 선택 합니다. 이 옵션을 선택 하면 해당 응용 프로그램과 연결 된 모든 데이터가 영구적으로 삭제 됩니다.

## <a name="next-steps"></a>다음 단계

응용 프로그램을 마이그레이션하는 방법을 배웠으므로 이제 권장 되는 다음 단계는 [Azure IOT CENTRAL UI](overview-iot-central-tour.md) 를 검토 하 여 V3에서 변경 된 내용을 확인 하는 것입니다.