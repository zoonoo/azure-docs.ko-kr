---
title: Azure IoT Central 애플리케이션에서 원격 분석 규칙 만들기 및 관리 | Microsoft Docs
description: Azure IoT Central 원격 분석 규칙을 사용하여 디바이스를 거의 실시간으로 모니터링하고, 규칙이 트리거되면 이메일 보내기 등의 작업을 자동으로 호출합니다.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879943"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Azure IoT Central 응용 프로그램에서 원격 분석 규칙 만들기 및 알림 설정 (미리 보기 기능)

*이 문서는 운영자, 빌더 및 관리자에게 적용됩니다.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central을 사용하여 원격으로 연결된 디바이스를 모니터링할 수 있습니다. Azure IoT Central 규칙을 사용하여 디바이스를 거의 실시간으로 모니터링하고, 이메일 보내기 또는 Microsoft Flow 트리거 등의 작업을 자동으로 호출합니다. 클릭 몇 번으로 디바이스 데이터를 모니터링하는 조건을 정의하고 해당 작업을 구성할 수 있습니다. 이 문서에서는 디바이스에서 보낸 원격 분석을 모니터링하는 규칙을 만드는 방법을 설명합니다.

디바이스는 원격 분석 측정을 사용하여 디바이스에서 숫자 데이터를 보낼 수 있습니다. 선택한 디바이스가 지정된 임계값을 초과하면 원격 분석 규칙이 트리거됩니다.

## <a name="create-a-telemetry-rule"></a>원격 분석 규칙 만들기

원격 분석 규칙을 만들려면 장치 정의에 하나 이상의 원격 분석 측정이 정의 되어 있어야 합니다. 이 예제에서는 온도 및 습도 원격 분석 데이터를 전송하는 냉장 자동 판매기를 사용합니다. 이 규칙은 디바이스에서 보고한 온도를 모니터링하다가 온도가 80도를 초과하면 이메일을 보냅니다.

1. **규칙** 페이지로 이동 합니다.

1. 규칙을 아직 만들지 않은 경우 다음 화면이 표시 됩니다.

    ![규칙 없음](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. **+ 새 규칙** 을 선택 하 여 만들 수 있는 규칙의 유형을 확인 합니다.

1. **원격 분석** 을 선택 하 여 장치 원격 분석을 모니터링 하는 규칙을 만듭니다.

    ![규칙 유형](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. 규칙을 식별 하는 데 도움이 되는 이름을 입력 하 고 Enter 키를 누릅니다.

1. 범위 섹션에서이 규칙의 범위를 선택 하려는 장치 정의를 선택 합니다. 이 화면에서는 **+ 필터**를 사용 하 여 규칙이 적용 되는 장치를 필터링 할 수도 있습니다. 규칙은 디바이스 템플릿 아래에 있는 모든 디바이스에 자동으로 적용됩니다. 규칙을 사용 하지 않도록 설정 하려면 머리글에서 **사용 안 함** 단추를 선택 합니다.

### <a name="configure-the-rule-conditions"></a>규칙 조건 구성

조건은 규칙이 모니터링하는 기준을 정의합니다.

1. 집계를 on 또는 off로 **설정할지** 여부를 선택 합니다.

      - 집계는 선택 사항입니다. 집계를 사용하지 않으면 조건을 충족하는 각 원격 분석 데이터 요소에 대해 규칙이 트리거됩니다. 예를 들어 온도가 80를 초과 하는 경우를 트리거하기 위해 규칙이 구성 된 경우 장치에서 온도 > 80를 보고할 때 규칙이 거의 즉시 트리거됩니다.
      - Average, min, max, count 등의 집계 함수를 선택 하는 경우 사용자는 조건을 평가 해야 하는 **시간 창을** 제공 해야 합니다. 예를 들어 기간을 "5분"으로 설정할 경우, 평균 온도가 5분 이상 80도를 초과할 경우 규칙이 80도를 초과하는 평균 온도를 찾습니다. 규칙 평가 빈도는 **시간 창과**동일 합니다. 즉,이 예제에서 규칙은 5 분 마다 한 번씩 평가 됩니다.

1. **단위** 드롭다운에서 모니터링하려는 원격 분석을 선택합니다.

1. 그런 다음 **연산자** 를 선택 하 고 **값**을 제공 합니다.

     ![조건](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>**+ 조건**을 선택 하 여 두 개 이상의 원격 분석 측정을 추가할 수 있습니다. 조건을 여러 개 지정하는 경우 모든 조건을 충족해야 규칙이 트리거됩니다. 각 조건은 암시적으로 ' AND ' 절에 의해 조인 됩니다. 집계를 사용할 경우 모든 측정값이 집계되어야 합니다.

### <a name="configure-actions"></a>작업 구성

이 섹션에서는 규칙이 실행될 때 수행할 작업을 설정하는 방법을 보여줍니다. 규칙에 지정된 모든 조건이 true로 평가될 경우 작업이 호출됩니다.

1. **작업** 섹션에서 **+ 동작** 을 클릭 합니다. 사용 가능한 작업 목록을 표시됩니다.  

    ![작업 추가](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. **전자 메일** 작업을 선택 하 고, 작업에 대 한 표시 이름을 입력 하 고, **대상** 필드에 유효한 전자 메일 주소를 입력 하 고, 규칙이 트리거될 때 전자 메일의 본문에 표시 되는 메모를 제공 합니다.

    > [!NOTE]
    > 애플리케이션에 추가되어 한 번 이상 로그인한 사용자에게만 이메일이 발송됩니다. Azure IoT Central에서 [사용자 관리](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)에 대해 자세히 알아보세요.

   ![작업 구성](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. 작업을 저장 하려면 **완료**를 선택 합니다.

1. 규칙을 저장하려면 **저장**을 선택합니다. 몇 분 이내에 규칙이 적용되어 애플리케이션으로 전송되는 원격 분석 데이터의 모니터링이 시작됩니다. 규칙에 지정된 조건이 충족하는 경우 규칙이 구성된 이메일 작업을 트리거합니다.

## <a name="parameterize-the-rule"></a>규칙 매개 변수화

규칙은 **디바이스 속성**에서 특정 값을 매개 변수로 얻을 수 있습니다. 매개 변수를 사용하면 디바이스에 따라 원격 분석 임계값이 달라지는 시나리오에서 유용합니다. 규칙을 만드는 동안 80도와 같은 절대 값을 제공 하는 대신 **최대 이상적인 임계값**과 같은 임계값을 지정 하는 장치 속성을 선택 합니다. 규칙이 실행되면 규칙은 디바이스 원격 분석 데이터를 디바이스 속성에 설정된 값과 매칭합니다.

매개 변수를 사용 하는 것은 관리할 규칙의 수를 줄이는 효과적인 방법입니다.

**디바이스 속성**을 매개 변수로 사용하여 작업을 구성할 수도 있습니다. 이메일 주소가 속성으로 저장되는 경우 **받는 사람** 주소를 정의할 때 사용할 수 있습니다.

## <a name="delete-a-rule"></a>규칙 삭제

규칙이 더 이상 필요 없으면 해당 규칙을 열고 **삭제**를 선택하여 삭제할 수 있습니다. 규칙을 삭제하면 디바이스 템플릿 및 모든 관련 디바이스에서 해당 규칙이 제거됩니다.

## <a name="enable-or-disable-a-rule"></a>규칙 사용 또는 사용 안 함

사용 하거나 사용 하지 않을 규칙을 선택 합니다. 규칙에서 범위를 지정 하는 모든 장치에 대해 규칙을 사용 하거나 사용 하지 않도록 설정 하려면 규칙의 **사용** 또는 사용 **안 함** 단추를 설정/해제 합니다.

## <a name="enable-or-disable-a-rule-for-a-device"></a>디바이스에 규칙을 사용하도록 또는 사용하지 않도록 설정

사용 하거나 사용 하지 않을 규칙을 선택 합니다. **범위** 섹션에 필터를 추가 하 여 장치 템플릿에서 특정 장치를 포함 하거나 제외 합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에서 규칙을 만드는 방법을 배웠으므로 제안 된 다음 단계는 [장치를 관리 하는 방법을](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 배우는 것입니다.
