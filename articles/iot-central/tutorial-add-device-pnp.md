---
title: Azure IoT Central 애플리케이션에 실제 디바이스 추가 | Microsoft Docs
description: 운영자로서 Azure IoT Central 애플리케이션에 실제 디바이스를 추가합니다.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878864"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>자습서: Azure IoT Central 애플리케이션에 시뮬레이션된 디바이스 추가(미리 보기 기능)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

이 자습서에서는 Microsoft Azure IoT Central 애플리케이션에 시뮬레이션된 디바이스를 추가하고 구성하는 방법을 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 시뮬레이션된 새 디바이스 추가
> * 건물 환경에서 시뮬레이션된 디바이스 사용

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하려는 개발자는 Azure IoT Central 애플리케이션을 만드는 첫 번째 개발자 자습서를 먼저 완료해야 합니다.

* [새 디바이스 유형 정의](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)(필수)

## <a name="add-a-simulated-device"></a>시뮬레이트된 디바이스 추가

애플리케이션에 실제 디바이스를 추가하려면 [새 디바이스 유형을 정의](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 자습서에서 만든 **환경 센서** 디바이스 템플릿을 사용합니다.

1. 운영자로서 새 디바이스를 추가하려면 왼쪽 탐색 메뉴에서 **디바이스**를 선택합니다. **디바이스** 탭에는 **모든 디바이스** 및 **환경 센서** 디바이스 템플릿이 표시됩니다.

1. 시뮬레이션된 센서 디바이스를 추가하려면 **+ 새로 만들기**를 선택합니다. 제안된 **디바이스 ID**를 사용하거나 원하는 **디바이스 ID**를 소문자로 입력합니다. 새 디바이스의 이름을 입력할 수도 있습니다. **시뮬레이션됨** 토글을 **켜기**로 전환한 다음, **만들기**를 선택합니다.

    ![시뮬레이션된 디바이스](./media/tutorial-add-device-pnp/simulated-device.png)

이제 시뮬레이션된 데이터를 사용하여 개발자가 만든 보기와 상호 작용할 수 있습니다.

## <a name="use-a-simulated-device-to-improve-views"></a>시뮬레이션된 디바이스를 사용하여 보기 개선

시뮬레이션된 디바이스를 새로 만든 후에는 개발자가 이 디바이스를 사용하여 디바이스 템플릿의 보기를 지속적으로 개선하고 향상할 수 있습니다.

1. 디바이스 보기에 있는 동안 시뮬레이션된 디바이스의 **디바이스 ID**를 복사합니다.

1. 왼쪽 탐색 메뉴에서 **디바이스 템플릿** 탭을 선택하고 **환경 센서** 템플릿을 선택합니다.

1. 편집하려는 보기를 선택하거나 새 보기를 만듭니다. **미리 보기 디바이스 구성**을 클릭합니다. 여기서는 미리 보기 디바이스를 사용하지 않거나, 테스트용으로 구성할 수 있는 실제 디바이스를 사용하거나, IoT Central에 추가한 기존 디바이스를 사용하는 세 가지 방법 중에 선택할 수 있습니다.

1. **실행 중인 디바이스에서 선택**을 선택하고, 앞에서 복사한 시뮬레이션된 디바이스의 **디바이스 ID**를 입력합니다.

1. **적용**을 선택합니다. 이제 디바이스 템플릿 보기 빌드 환경에 동일한 시뮬레이션된 디바이스가 표시됩니다. 이 보기는 특히 차트 및 기타 시각화에 유용합니다.

    ![미리 보기 디바이스 구성](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

* 시뮬레이션된 새 디바이스 추가
* 건물 환경에서 시뮬레이션된 디바이스 사용

시뮬레이션된 디바이스를 Azure IoT Central 애플리케이션에 연결했으므로, 이제 다음과 같은 단계를 제안합니다.

운영자로서 방법을 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [규칙 구성](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

디바이스 개발자로서 다음 작업 방법을 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스 연결](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



